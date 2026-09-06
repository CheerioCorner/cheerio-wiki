# Deep Research 報告 — rc-20260906-001

- 研究主題：.NET Aspire 作為企業級 Distributed Application Platform 的技術深度研究：核心能力（Application Model、Local Orchestration、Service Discovery、Observability）、Container Runtime 整合邊界（Docker Engine / Podman / containerd）、部署路徑技術細節（aspire publish → Docker Compose / Helm Chart）、多語言支援成熟度（C# / TypeScript / Python / Java / Go）、Aspire 9 新功能與 API 變化、與既有 DevOps 工具鏈整合（Azure DevOps Pipeline / GitHub Actions），以及企業導入實務考量（授權、安全、觀測、CI/CD），用於支撐「企業能否降低 Docker Desktop 依賴」的架構決策提案
- Notebook：https://notebooklm.google.com/notebook/e256d9e3-e60f-42d3-ae17-da872507403e
- 產生時間：2026-09-06T09:32:56.911Z
- 品質過濾：97 → 93 筆（移除 4 筆）
- Recheck：共 2 輪補充研究
- 蒸餾必要性：93 → 90 筆（移除 3 筆對研究問題無貢獻的來源）
- 來源分類/重新命名：0 / 88 筆成功

## 研究結果

### 1. Aspire 核心四大能力（Application Model / Local Orchestration / Service Discovery / Observability）各自的機制、API 與限制

### .NET Aspire 核心能力深度研究：機制、API 與限制

.NET Aspire 是微軟專為雲端原生（Cloud-native）與微服務架構設計的**編排與應用程式模型平台**。它並非全新的執行階段，而是透過標準化的整合工具與 opinionated 的預設配置，來解決分散式系統在開發、偵錯與部署上的痛點 [1-3]。

以下為您深度解析 .NET Aspire 四大核心能力的運作機制、API 規格與技術限制：

---

### 一、 Application Model (應用程式模型)

**1. 運作機制**
*   **程式碼即拓撲 (Topology as Code)**：.NET Aspire 採用 code-first 的方式，透過 C# (或 TypeScript) 程式碼在名為 **AppHost** 的專案中宣告式地定義整個分散式系統的架構 [1, 4, 5]。
*   **資源圖 (Resource Graph)**：在 AppHost 中，每次呼叫 `builder.AddX(...)` 都會在應用程式模型中建立一個**資源節點 (Resource Node)** [6, 7]；而 `.WithReference(...)` 或 `.WaitFor(...)` 則會建立**關係邊 (Edges)** [6, 8]。這套依賴關係圖可讓 Aspire 自動計算正確的啟動順序、自動解析並安全地注入連線組態 [6, 9, 10]。
*   **強型別專案生成**：當 AppHost 引進其他專案的 `ProjectReference` 時，系統內建的 Source Generator 會在編譯期為每個專案生成 `Projects` 命名空間下的強型別元數據（Metadata）類別，進而使 `AddProject<Projects.MyApi>()` 的調用具有型別安全與 IDE 自動完成優勢 [11, 12]。
*   **建置與部署分開**：**AppHost 專案本身在生產環境中是不運行的** [13, 14]。在部署時，`aspire publish` 會解析這套應用程式模型並將其導出為 JSON 資訊清單（Deployment Manifest）[13, 15, 16]，接著由 Azure Developer CLI (`azd`)、Aspirate 等工具將其轉譯為生產環境所需的架構定義檔（例如 ACA 的 Bicep 範本、Kubernetes Manifests / Helm Charts，或 Docker Compose YAML 檔） [13, 17-19]。

**2. 核心 API**
*   `DistributedApplication.CreateBuilder(args)`：初始化應用程式主機的 builder [20, 21]。
*   `builder.AddProject<Projects.TResource>("name")`：向 AppHost 註冊一個 .NET 專案資源 [11, 12, 20]。
*   `builder.AddContainer("name", "image")`：向 AppHost 註冊一個 OCI/Docker 容器資源 [22-24]。
*   `builder.AddExecutable("name", "command", "workingDir", args)`：加入自訂的可執行檔或原生處理序（Process） [25, 26]。
*   `WithReference(resource)`：建立相依關係，自動計算連接字串，並於**程序啟動時**將其以環境變數注入（如 `ConnectionStrings__*` 或 `services__*`） [9, 27]。
*   `WaitFor(resource)`：阻塞啟動，直到被相依資源報告 **Healthy** (健康) 後才啟動當前資源，但此 API 自身**不**注入任何組態 [9]。

**3. 技術限制**
*   **無執行期鎖定限制**：由於 AppHost 專案僅存在於開發期與建置期 [13, 14]，所有動態的彈性擴充與自訂事件（如資料庫 seeding）必須依賴特定生命週期事件（如 `BeforeStartEvent` 或 `AfterResourcesCreatedEvent`）或是撰寫自訂的 `IDistributedApplicationLifecycleHook`，這需要開發人員具備較高的擴充 API 知識 [28-31]。
*   **專案本機非容器化偵錯**：雖然 .NET 支援免 Dockerfile 容器化打包 [32, 33]，但 Aspire 在本地開發時，預設是以**本機原生進程 (Process)** 來啟動您自己的專案，而外部相依（如 Redis, Postgres）則以容器啟動 [34-36]。此舉旨在優化 IDE 的中斷點偵錯與熱重載（Hot-Reload）體驗，但也意味著本地開發與生產環境的容器基礎作業系統並不完全相同 [35, 37]。

---

### 二、 Local Orchestration (本地編排)

**1. 運作機制**
*   **Developer Control Plane (DCP)**：.NET Aspire 的本地編排核心並非 Docker Compose，而是微軟開發的 **Developer Control Plane (DCP)** 引擎（由 `dcp.exe`、`dcpctrl.exe` 和 `dcpd.exe` 等二進位程式組成） [38-40]。
*   **微型 Kubernetes 結構**：DCP 的運作架構本質上是一個**微型的本地 Kubernetes** [39]。當您點擊 F5 啟動 AppHost 時，Aspire 會為 DCP 產生一個臨時的 `kubeconfig` 設定檔，並建立自訂的 API Server 端點 [41, 42]。Aspire 接著會透過 C# Kubernetes 用戶端將應用程式模型的資源發送給 DCP，由其對應的控制器（Controllers）負責管理 `Container` 與 `Executable` 的執行生命週期、指派隨機連接埠、管理多副本（Replicas）與程序監控 [43-45]。

**2. 核心 API**
*   `WithReplicas(int count)`：指定該資源在本地啟動多個執行處理副本，並由 DCP 分流 [45, 46]。
*   `WithLifetime(ContainerLifetime.Persistent)`：自訂容器在 AppHost 停止時是否應該被保留，設為 `Persistent` 可以讓資料庫等重型容器免於重複重建、加速 Inner-loop 的重啟效率 [47, 48]。
*   環境變數 `DOTNET_ASPIRE_CONTAINER_RUNTIME`：可設定為 `docker` 或 `podman`，DCP 會自動偵測並調用對應的本地容器引擎 [49, 50]。

**3. 技術限制**
*   **WSL 與 Podman 容器到主機網路 (Container-to-Host)**：當您的 .NET 專案以本地原生處理序運行，而特定容器（如 Keycloak、Dapr 或 Nginx）需要連線回您的 .NET 專案時，DCP 必須依賴 `host.docker.internal` 或 `host.containers.internal` 來做迴圈網路對應 [51-53]。此機制在自建的 WSL2 Docker 引擎、Rancher Desktop 或 Podman 5+ 下極易因為作業系統虛擬網卡設定不一致而發生 `Connection refused` 等網絡故障 [51, 54-57]。
*   **憑證不信任問題**：在本地開發時，Dashboard 與 DCP 的通訊通常由 ASP.NET Core 的本機自簽開發憑證（Development Certificate）加密保護 [58, 59]。但對於在容器內部運行的應用程式來說，容器內部並非預設信任宿主機的開發憑證，這經常導致容器內部的 OpenTelemetry 匯出器無法透過 HTTPS 將 Telemetry 傳送到 Dashboard [58]。雖然 .NET 10 已在此方面進行自動憑證信任改善，但在複雜環境下仍是一大限制 [60]。
*   **僅限 Linux 容器**：DCP 的容器化編排僅支援本地 Linux 容器，並不支援 Windows 容器 [61]。
*   **參數處理回歸 (Regression)**：在某些非 Docker Desktop 環境（如批次檔 CLI 代理下），若傳入多個 `WithEnvironment`，DCP 產生的 multi-line 中繼環境變數標籤可能在 shell 解析中被截斷，導致容器無法啟動 [62, 63]。

---

### 三、 Service Discovery (服務發現)

**1. 運作機制**
*   **端點環境變數注入**：當在 AppHost 中宣告 `api.WithReference(db)` 時，Aspire 不會把固定的連接字串寫入 appsettings.json [64]。相反地，DCP 會在專案啟動時，直接將對應資源的**邏輯端點位址**注入到進程環境變數中（格式例如 `services__<resourcename>__http__0`） [9, 27, 65]。
*   **用戶端名稱解析 (Client-side Resolution)**：專案中引用的 `Microsoft.Extensions.ServiceDiscovery` 函式庫會接手這套機制 [66, 67]。當服務透過 `HttpClient` 發出例如 `http://apiservice/api` 的調用時，服務發現用戶端會攔截此請求，並自環境變數中找到對應的本地隨機 Port 進行 IP/Port 解析與 Polly 式的重試和斷路器保護（Resilience） [67-70]。
*   **過濾非預設端點**：自 13.3 起，Aspire 在調用 `WithReference` 時，會自動識別並排除像 Keycloak 或 CosmosDB Emulator 這類外部資源的「管理/Admin 端點」，防止其被誤注入為主要的通訊端點 [71]。

**2. 核心 API**
*   `builder.Services.AddServiceDiscovery()`：在專案內部的 `IServiceCollection` 註冊服務發現元件 [67, 70]。
*   `builder.Services.ConfigureHttpClientDefaults(...)`：設定全域的 `HttpClient` 預設啟用服務發現與 Polly 彈性原則 [67, 70]。
*   `WithHttpEndpoint(port: X, env: "ASPNETCORE_HTTP_PORTS")`：在 AppHost 中，若有外部 client（如前端 SPA 或手機 App）需要存取特定專案，可透過此 API 將該專案在宿主機的連接埠 Pin 住，並使進程內部 Kestrel 綁定相同連接埠 [72]。

**3. 技術限制**
*   **TCP 端點序列化 Gotcha**：當使用 `WithEndpoint(...)` 定義 TCP 端點時，其序列化會產生 `tcp://localhost:4317`。但像 .NET OTel OTLP 匯出器這類協定需要的是 `http://` 或 `https://` 的 HTTP 端點（即使底層是 gRPC 也是 HTTP/2 承載） [73]。如果用錯了 `WithEndpoint` 而非 `WithHttpEndpoint(...)`，會導致 OTel Telemetry 因為無法解析 `tcp://` 連接協定而無法正常匯出 [73-75]。
*   **動態與靜態連接埠衝突**：雖然 Aspire 鼓勵開發人員使用動態連接埠以避免 "port already in use" 衝突 [76]，但如果外部系統 (如瀏覽器或行動裝置、未與 Aspire 一同編排的本機程式) 需要直接連入此服務，則必須顯式 pinning 連接埠，這需要開發人員手動去處理潛在的 port 佔用衝突 [76]。

---

### 四、 Observability (可觀測性)

**1. 運作機制**
*   **無縫 OpenTelemetry 整合**：可觀測性是 .NET Aspire 的核心優勢。專案範本中的 `ServiceDefaults` 類別庫封裝了對 **OpenTelemetry (OTel)** 標準的調用 [21, 77]。呼叫 `ConfigureOpenTelemetry()` 時，系統會自動註冊對 ASP.NET Core、HttpClient、EntityFrameworkCore 等核心函式庫的 Metrics（指標）和 Tracing（追蹤）儀器化（Instrumentation） [77, 78]。
*   **OTLP 傳輸協定**：所有 Telemetry 訊號均透過標準 OTLP/gRPC 或 OTLP/HTTP 協定，發送到 `OTEL_EXPORTER_OTLP_ENDPOINT` 所指定的目標 [21, 77, 79]。
*   **即時開發人員儀表板 (Developer Dashboard)**：本地運行時，Aspire 會為您開啟一個功能強大的互動式 Dashboard [78, 80, 81]。它可以即時將控制台日誌（Logs）、結構化日誌、跨多程序服務邊界的分布式追蹤（Distributed Traces）、以及系統與自訂指標（Metrics）整合到單一的視覺化介面中 [81-83]。

**2. 核心 API**
*   `ConfigureOpenTelemetry()`：註冊 logs、traces 和 metrics 供應器 [77]。
*   `AddSource(string name)` / `AddMeter(string name)`：在配置中手動註冊您自訂的 `ActivitySource`（商業 Span）與 `Meter`（自訂效能計數器），**微軟限制：未在此顯式註冊的自訂 ActivitySource 或 Meter 會被 SDK 默默丟棄**，不會匯出 [74, 77, 84, 85]。
*   `ActivityTrackingOptions`：配置日誌記錄器，將當前 Activity 的 `TraceId` 與 `SpanId` 蓋章至每筆 ILogger 紀錄，以便在 Dashboard 進行跨 log 與 trace 的雙向關聯查詢 [77, 84]。

**3. 技術限制**
*   **純內存儲存限制 (In-memory Storage Limits)**：Aspire Dashboard 的定位是「本機開發與短期診斷工具」而非生產級 APM [86]。因此，所有 Telemetry 數據均**儲存在內存 (RAM) 中** [86, 87]。當 AppHost 重啟時，所有歷史追蹤數據會消失 [86, 87]。此外，為防止本機內存耗盡（Memory Exhaustion），Dashboard 設有嚴格的容量限制（例如：最大 10,000 筆 logs、10,000 條 traces、以及每個維度最大 50,000 點 metrics），一旦超限就會自動丟棄最舊的數據 [87, 88]。
*   **不支援作為 APM 分流器**：微軟官方明確表態，Dashboard 的工作**並非**作為 OTel Collector 來將數據分發 (Fan-out) 至 Grafana、InfluxDB 等生產級監控工具 [89]。若在生產環境需要分流，您必須自行編排並配置獨立的 OpenTelemetry Collector 容器節點，將資料轉發至生產平台 [79, 89-91]。
*   **非 .NET 語言的 Telemetry 障礙**：當您在 Aspire 中編排 Python 等非 .NET 服務時，Python 預設的 gRPC Exporter 需要 ALPN 的 HTTP/2 支持，而本地 Aspire 儀表板的 gRPC 端點可能因此發生相容性衝突 [92]。開發人員必須手動在 Python 中將傳輸協定強制降級為 `http/protobuf` 並指向 HTTP 連接埠才能正常匯出 Telemetry [93]。此外，Dashboard 暫不支援 exponential histograms 渲染，需要您將 metrics 收集器調整為 Explicit Bucket 模式 [93]。

---

💡 **建議下一步**：
我們是否要為您的微服務解決方案中的專案建立一個自訂且高度優化的 **`ServiceDefaults` 擴充方法**，並將自訂的 `ActivitySource` 與自訂的健康檢查項目（例如資料庫與 Redis 的連線驗證）一次配置到位？

**引用來源：**
- [1] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [2] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [3] [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)
- [4] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [5] [[Microsoft 官方] 產品介紹 - .NET Aspire 分散式系統編排平台之核心定位與願景](https://aspire.dev/get-started/what-is-aspire/)
- [6] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [7] [[mstack 部落格] 技術指引 - 自訂 .NET Aspire 元件與整合測試開發指南](https://mstack.nl/blogs/wiremock-net-aspire-component/)
- [8] [[Microsoft 官方] 步驟指南 - 從零開始建立第一個 .NET Aspire 分散式應用程式](https://dotnet-aspire-52.mintlify.app/quickstart)
- [9] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [10] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [11] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [12] [[Microsoft 官方] 技術文件 - 用於建置分散式應用程式的 .NET Aspire SDK 規格](https://aspire.dev/get-started/aspire-sdk/)
- [13] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [14] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [15] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [16] [[Microsoft 官方] 架構文件 - .NET Aspire 發布資訊清單與多路徑部署模型概覽](https://dotnet-aspire-52.mintlify.app/deployment/overview)
- [17] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [18] [[Reddit 社群] 案例研究 - 從開發容器移轉至生產環境託管的實務架構討論](https://www.reddit.com/r/dotnet/comments/1kxau0s/aspire_is_amazing_how_to_go_from_dev_containers/)
- [19] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [20] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [21] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [22] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [23] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [24] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [25] [[Microsoft 官方] 技術文件 - 在 .NET Aspire 之中託管與執行外部原生執行檔](https://aspire.dev/app-host/executable-resources/)
- [26] [[Microsoft 官方] 技術文件 - 在 .NET Aspire 之中託管與執行外部原生執行檔](https://aspire.dev/app-host/executable-resources/)
- [27] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [28] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [29] [[Microsoft 官方] 技術指引 - 透過 Custom Resources API 於 Aspire 中引用既有 Docker 容器](https://anthonysimmon.com/referencing-external-docker-containers-dotnet-aspire-custom-resources/)
- [30] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [31] [[mstack 部落格] 技術指引 - 自訂 .NET Aspire 元件與整合測試開發指南](https://mstack.nl/blogs/wiremock-net-aspire-component/)
- [32] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [33] [[Microsoft 官方] 技術指南 - 使用 .NET SDK 內建容器化功能打包應用程式](https://milanjovanovic.tech/blog/containerize-your-dotnet-applications-without-a-dockerfile)
- [34] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [35] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [36] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [37] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [38] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [39] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [40] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [41] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [42] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [43] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [44] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [45] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [46] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [47] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [48] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [49] [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [50] [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)
- [51] [[Microsoft GitHub] 故障排除 - 使用 Podman 時 Container-to-Host 網路連線故障處理](https://github.com/microsoft/aspire/issues/6846)
- [52] [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)
- [53] [[Microsoft 官方] 技術指引 - 讓 Aspire 容器能夠解析主機之自訂本機網域設定](https://anthonysimmon.com/dotnet-aspire-containers-share-custom-hosts/)
- [54] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [55] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [56] [[Microsoft GitHub] 故障排除 - 使用 Podman 時容器無法連線至 Aspire 主機服務之解決方案](https://github.com/microsoft/aspire/issues/4136)
- [57] [[Microsoft GitHub] 故障排除 - 使用 Podman 時容器無法連線至 Aspire 主機服務之解決方案](https://github.com/microsoft/aspire/issues/4136)
- [58] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [59] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [60] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [61] [[mstack 部落格] 技術指引 - 自訂 .NET Aspire 元件與整合測試開發指南](https://mstack.nl/blogs/wiremock-net-aspire-component/)
- [62] [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)
- [63] [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)
- [64] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [65] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [66] [[Microsoft 官方] 技術文件 - C# 專案中 Service Defaults 的預設功能與擴充設定](https://aspire.dev/get-started/csharp-service-defaults/)
- [67] [[Microsoft 官方] 技術文件 - C# 專案中 Service Defaults 的預設功能與擴充設定](https://aspire.dev/get-started/csharp-service-defaults/)
- [68] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [69] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [70] [[Microsoft 官方] 技術文件 - C# 專案中 Service Defaults 的預設功能與擴充設定](https://aspire.dev/get-started/csharp-service-defaults/)
- [71] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [72] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [73] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [74] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [75] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [76] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [77] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [78] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [79] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [80] [[Kalle Marjokorpi 部落格] 案例研究 - .NET Aspire 對企業微服務技術選型與架構之深遠影響](https://www.kallemarjokorpi.fi/blog/how-net-aspire-changed-my-way-of-building-software/)
- [81] [[Microsoft 官方] 步驟指南 - 從零開始建立第一個 .NET Aspire 分散式應用程式](https://dotnet-aspire-52.mintlify.app/quickstart)
- [82] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [83] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [84] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [85] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [86] [[Microsoft 官方] 技術指引 - 如何獨立運行 (Standalone) .NET Aspire 觀測儀表板](https://aspire.dev/dashboard/standalone/)
- [87] [[Microsoft GitHub] 架構文件 - .NET Aspire Dashboard 原始碼結構、架構與構建說明](https://github.com/dotnet/aspire/blob/main/src/Aspire.Dashboard/README.md)
- [88] [[Microsoft GitHub] 架構文件 - .NET Aspire Dashboard 原始碼結構、架構與構建說明](https://github.com/dotnet/aspire/blob/main/src/Aspire.Dashboard/README.md)
- [89] [[Microsoft GitHub] 技術討論 - 將 .NET Aspire 觀測數據導出至 Grafana 與 InfluxDB 的技術方案](https://github.com/microsoft/aspire/issues/9861)
- [90] [[Microsoft GitHub] 技術討論 - 將 .NET Aspire 觀測數據導出至 Grafana 與 InfluxDB 的技術方案](https://github.com/microsoft/aspire/issues/9861)
- [91] [[Reddit 社群] 技術探討 - 提取 .NET Aspire 遙測數據並匯出為 CSV 之實作方法](https://www.reddit.com/r/dotnet/comments/1oqpm21/exporting_net_aspire_telemetry_traces_logs/)
- [92] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [93] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)

### 2. Aspire 與 Container Runtime 的責任邊界：它做什麼、不做什麼、跟 Docker Engine / Podman / containerd 的確切分工

### 1. 核心整合機制：AppHost 與 Developer Control Plane (DCP)
在了解 Aspire 與容器執行期（Container Runtime）的分工前，必須先理解其底層的核心編排元件：**Developer Control Plane (DCP)** [1, 2]。

* **資源圖 (Resource Graph)**：我們在 AppHost 中寫的 C# 或 TypeScript 程式碼，本質上只是在建立一個代表服務與基礎設施依賴關係的「資源圖」（包含節點與邊） [3, 4]。
* **DCP (Developer Control Plane)**：當我們在本機啟動 AppHost 時，Aspire 會在背景叫起一個名為 DCP 的輕量化協調行程（通常以 Go 語言編譯） [5-7]。DCP 本質上像是一個「本機版的 Kubernetes API 伺服器」 [2]。
* **宣告式通訊**：Aspire 透過 .NET Kubernetes Client 與 DCP 通訊，將我們宣告的資源轉譯為類似 Kubernetes 樣式的規格（如 Executable、Container 資源類型） [8-10]。DCP 隨後負責實際與作業系統或容器引擎進行通訊，並控制這些資源的生命週期 [8, 11]。

---

### 2. .NET Aspire 做什麼（Responsibility of Aspire）
Aspire 是一個「code-first 的應用程式模型與開發期編排器」，主要負責應用程式的高階拓撲、服務發現與遙測整合 [12-14]：

* **拓撲解析與順序編排**：它負責解讀服務之間的相依性，並利用 `WaitFor()` 與 `WithReference()` 來決定啟動順序（例如：等待資料庫容器就緒且健康後，才允許 API 專案啟動） [3, 15, 16]。
* **動態環境變數與自動服務發現**：在開發階段，Aspire 預設會為每個服務指派動態埠號，並動態產生連線字串，然後在服務啟動時將這些資訊以環境變數的形式（如 `ConnectionStrings__mydb`）直接注入對應的行程或容器中 [17-19]。這消除了手動硬編碼埠號與複製憑證的痛點 [20-22]。
* **IDE 整合與原生行程偵錯代理**：在 IDE 偵錯模式下，DCP 會與 IDE（如 Visual Studio 或 JetBrains Rider）協同工作 [23, 24]。DCP 會將專案資源的啟動權「委託（Delegate）」給 IDE 執行，使得開發者能像平常一樣無縫下中斷點偵錯，同時維持對容器化資料庫、快取等外部資源的生命週期監控 [23-25]。
* **統一觀測與儀表板（Observability & Dashboard）**：透過 Service Defaults 專案自動為所有專案配置 OpenTelemetry [21, 26, 27]。Aspire Dashboard 在本機接收 OTLP 資料，整合呈現所有服務與容器的 Logs、Traces 與 Metrics [13, 28, 29]。
* **部署架構翻譯器（Publisher Model）**：當準備走向生產環境時（呼叫 `aspire publish`），Aspire 會將 AppHost 所描述的資源圖翻譯成生產環境所需的定義檔，例如 Docker Compose YAML、Kubernetes Manifest、或 Azure Container Apps 的 Bicep 範本 [18, 30-32]。

---

### 3. .NET Aspire 不做什麼（What Aspire DOES NOT Do）
為了保持輕量並避免重造輪子，Aspire 劃定了清晰的「不做」邊界：

* **不具備容器執行期與虛擬化能力**：Aspire 本身**沒有**容器引擎、沒有虛擬檔案系統、也沒有拉取映像檔的能力 [33-35]。它完全依賴本機已運作的 Docker 或 Podman 實體來處理容器底層 [33, 34, 36]。
* **本機開發時預設「不」容器化開發者專案**：這是一個極為關鍵的設計抉擇 [24]！為了極速的啟動速度、Hot Reload 以及原生 IDE 偵錯體驗，Aspire 在本機開發時，會將您自己寫的 .NET 或 Node.js 專案作為**主機上的原生行程（Bare-metal Processes）**直接執行，只有資料庫、快取等外部第三方基礎設施會以容器方式執行 [18, 24, 37, 38]。
* **不負責生產環境的運行**：AppHost 專案與 DCP 僅存在於本機開發期與建置期 [39, 40]。在生產環境中，完全沒有 Aspire 編排行程在運作，而是由雲端託管服務（如 Azure Container Apps）、Kubernetes 或獨立的 Docker Compose 負責執行 [39-41]。

---

### 4. 與 Docker Engine / Podman / containerd 的確切分工
當我們在 C# 中調用 `builder.AddContainer`、`builder.AddPostgres` 等方法時，DCP 與容器執行期的分工極為明確 [8, 11, 42]：

| 功能維度 | .NET Aspire (AppHost + DCP) 的責任 | 容器執行期 (Docker Engine / Podman) 的責任 |
| :--- | :--- | :--- |
| **映像檔管理** | 聲明需要哪個映像檔（如 `redis:7.4`）與拉取策略（`WithImagePullPolicy`） [9, 43, 44]。 | 實際向遠端 Registry 進行驗證、下載、解壓映像檔，並建立本機映像檔快取 [35, 43, 45]。 |
| **容器生命週期** | 發送 API 指令建立、啟動、停止與銷毀容器。監測容器是否正常運行 [8, 11]。 | 執行底層的 OCI 容器建立，配置核心隔離、控制組（cgroups）以及疊加檔案系統。 |
| **網路管理** | 規劃邏輯網路，指定需要對外暴露或內部對應的 Port [9, 46]。注入連線環境變數。 | 建立虛擬網橋（如 `aspire` 橋接網路）、分配內部 IP、處理主機與容器間的埠號對應（Port Forwarding） [44, 47]。 |
| **儲存管理** | 宣告需要持久化的磁碟卷（如 `.WithDataVolume()`）或綁定掛載路徑 [42, 48]。 | 在主機實體檔案系統中建立/管理 Volume 空間，並將其 mount 到容器內指定的目錄中 [42, 44]。 |
| **多語言行程混部** | 除了容器外，能同時原生啟動 Node.js/TypeScript、Python、Java 等程式碼行程，並讓它們與容器進行網路通訊 [18, 49, 50]。 | 僅專注於容器化資源，無法直接管理或感知主機上的裸機開發行程。 |

#### containerd 的角色定位
* **containerd** 是一個符合 OCI 標準的低階容器執行期守護行程（Container Runtime Daemon），主要負責管理容器生命週期、映像檔傳輸與儲存。它通常作為 Docker Engine、Kubernetes (CRI) 或高階 Podman 內部的底層驅動運作。
* **Aspire 不會直接與 containerd 通訊** [36]。DCP 在本機執行時，必須透過 PATH 上的高階用戶端 CLI（如 `docker` 或 `podman`）作為媒介 [51, 52]。DCP 會探測並呼叫這些高階工具的 CLI 或 API 來驅動 Docker/Podman，進而由它們調用底層的 containerd 來建立並運作容器 [33, 34, 36]。

---

### 5. 本機開發時的網路邊界挑戰
由於 Aspire 本機開發時採用「專案走裸機行程、依賴走容器」的混部模式 [18, 24, 37, 38]，這會引入網路邊界的溝通挑戰：

1. **容器到容器（Container-to-Container）**：若在 AppHost 中將多個容器資源互相 reference（例如 pgAdmin 連接 Postgres），DCP 會將它們放進同一個虛擬網橋（如 `aspire` 網路），讓它們利用容器名稱進行標準的容器間通訊 [44, 53]。
2. **容器到主機/行程（Container-to-Host / Process）**：如果容器內的服務需要存取主機上的 .NET 專案行程（例如 Dapr sidecar 存取主機 API），這會跨越虛擬網路與主機實體網路的邊界 [53, 54]。此時，容器內的服務必須透過主機的特殊 DNS 網域來進行連線（例如在 Docker 中解析 `host.docker.internal`，或在 Podman 中解析 `host.containers.internal`） [55, 56]。
   * *常見故障排查*：如果開發者使用自建的 WSL2 裸機 Docker 引擎，並透過自訂的批次檔（如 `wsl docker %*`）來代理指令，常會因為參數傳遞不完整，導致 DCP 在建立容器時無法正確配置這些 host 對應，進而發生 Container-to-Host 連線中斷 [55, 57, 58]。這也是為什麼微軟官方強烈建議本機開發使用標準 Docker Desktop 或 Podman Desktop 的關鍵原因 [35, 57]。

---

💡 **您接下來可能想了解：**
既然 Aspire 採用了「本機專案跑裸機行程、依賴走容器」的設計，您想深入看看這套混部架構在實務上面對「HTTPS 開發憑證信任」或「跨容器/主機 DNS 解析」時，微軟與社群是如何透過 Lifecycle Hooks 進行自動化配置與處理的嗎？

**引用來源：**
- [1] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [2] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [3] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [4] [[Microsoft 官方] 技術指引 - 透過 Custom Resources API 於 Aspire 中引用既有 Docker 容器](https://anthonysimmon.com/referencing-external-docker-containers-dotnet-aspire-custom-resources/)
- [5] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [6] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [7] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [8] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [9] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [10] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [11] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [12] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [13] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [14] [[Microsoft 官方] 產品介紹 - .NET Aspire 分散式系統編排平台之核心定位與願景](https://aspire.dev/get-started/what-is-aspire/)
- [15] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [16] [[C# Corner] 技術實作 - 基於 .NET Aspire 的端對端 (E2E) 整合測試架構與實踐](https://www.c-sharpcorner.com/article/end-to-end-integration-testing-with-net-aspire/)
- [17] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [18] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [19] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [20] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [21] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [22] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [23] [[JetBrains 官方] 產品功能 - 在 JetBrains Rider 中使用 .NET Aspire 插件進行開發](https://blog.jetbrains.com/dotnet/2024/02/19/jetbrains-rider-and-the-net-aspire-plugin/)
- [24] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [25] [[JetBrains 官方] 產品功能 - 在 JetBrains Rider 中使用 .NET Aspire 插件進行開發](https://blog.jetbrains.com/dotnet/2024/02/19/jetbrains-rider-and-the-net-aspire-plugin/)
- [26] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [27] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [28] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [29] [[Microsoft 官方] 技術指引 - 如何獨立運行 (Standalone) .NET Aspire 觀測儀表板](https://aspire.dev/dashboard/standalone/)
- [30] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [31] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [32] [[Microsoft 官方] 架構文件 - .NET Aspire 發布資訊清單與多路徑部署模型概覽](https://dotnet-aspire-52.mintlify.app/deployment/overview)
- [33] [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [34] [[Microsoft 官方] 開發教學 - 深入解析 dotnet publish 容器化打包技術之參數與規格](https://learn.microsoft.com/en-us/dotnet/core/containers/sdk-publish)
- [35] [[Microsoft 官方] 步驟指南 - .NET Aspire SDK 本機開發環境安裝與設定](https://dotnet-aspire-52.mintlify.app/installation)
- [36] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [37] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [38] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [39] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [40] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [41] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [42] [[Packt 部落格] 開發教學 - 從零建置生產就緒的 .NET Aspire 應用程式](https://juliocasal.com/blog/net-aspire-tutorial-build-production-ready-apps-from-day-1)
- [43] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [44] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [45] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [46] [[mstack 部落格] 技術指引 - 自訂 .NET Aspire 元件與整合測試開發指南](https://mstack.nl/blogs/wiremock-net-aspire-component/)
- [47] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [48] [[Context7 部落格] 架構分析 - .NET Aspire 平台與 Orchestration 核心元件拆解](https://context7.com/websites/aspire_dev)
- [49] [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [50] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [51] [[Microsoft GitHub] 故障排除 - 自訂 Docker Host 設定在 .NET Aspire 中無效的問題排解](https://github.com/microsoft/aspire/issues/1650)
- [52] [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)
- [53] [[Microsoft GitHub] 故障排除 - 使用 Podman 時容器無法連線至 Aspire 主機服務之解決方案](https://github.com/microsoft/aspire/issues/4136)
- [54] [[Microsoft GitHub] 故障排除 - 使用 Podman 時 Container-to-Host 網路連線故障處理](https://github.com/microsoft/aspire/issues/6846)
- [55] [[Microsoft GitHub] 故障排除 - 使用 Podman 時 Container-to-Host 網路連線故障處理](https://github.com/microsoft/aspire/issues/6846)
- [56] [[Microsoft 官方] 技術指引 - 讓 Aspire 容器能夠解析主機之自訂本機網域設定](https://anthonysimmon.com/dotnet-aspire-containers-share-custom-hosts/)
- [57] [[DEV Community] 技術文章 - .NET Aspire 於 WSL2 與 Docker 環境下的整合設定](https://dev.to/glsolaria/net-aspire-and-wsl-docker-21k4)
- [58] [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)

### 3. aspire publish 與 aspire deploy 的部署產物：Docker Compose YAML / Helm Chart / Kubernetes Resource 映射的具體細節與已知限制

在 .NET Aspire 的設計哲學中，**「發布（Publish）」與「部署（Deploy）」被明確拆分為兩個不同的階段**，這兩個階段的部署產物（Docker Compose 與 Kubernetes 資源）有著非常清晰的映射規則，同時也伴隨著一些不可忽視的已知限制 [1, 2]。

以下為您針對 **Docker Compose YAML**、**Helm Chart 與 Kubernetes 資源映射** 的具體映射細節與已知限制進行技術深度拆解：

---

### 1. `aspire publish` 與 `aspire deploy` 的根本設計差異

在理解具體產物前，必須先釐清這兩個 CLI 指令的核心運作模式：
*   **`aspire publish` (發布)**：進入「發布進入點」。它會評估 AppHost 中的應用模型，並**僅生成參數化的中間部署產物**（如 Docker Compose 檔、Bicep 範本、Kubernetes Manifests/Helm Charts 等）[3, 4]。最關鍵的細節在於：為了達成「一次發布、多處部署」，**所有的機密資訊與環境特定設定都會以 `${VAR}` 的佔位符（Placeholders）保留**，實行無狀態、安全的單向交付 [3, 5]。
*   **`aspire deploy` (部署)**：進入「部署進入點」。它會重新評估 AppHost，不僅在幕後生成產物，還會**自動解析並填充參數（Resolve Parameters）**、呼建與推送容器鏡像、並直接套用（Apply）部署至目標環境 [3, 4, 6, 7]。`deploy` 並不消耗先前 `publish` 出來的本地產物 [7]。
*   **已知通用限制（AppHost 不存在於生產環境）**：
    *   **本地與生產環境切換的守衛陷阱**：在生產環境中，**AppHost 專案是不作為運行程序存在的** [8, 9]。
    *   如果您在專案程式碼中使用了 `if (app.Environment.IsDevelopment())` 等本地環境守衛（例如註冊 `/health` 節點或自動執行資料庫移轉 `MigrateAsync()`），當發布部署到 Production 環境時，該守衛會判定為 false，從而導致健康檢查失效（Probe 失敗導致容器重啟）或資料庫完全未被初始化的嚴重 regression [10]。

---

### 2. Docker Compose YAML 映射細節與已知限制

若在 AppHost 中註冊了 `AddDockerComposeEnvironment` [11, 12]，執行發布或部署時，Aspire 將會在 `aspire-output` 目錄下生成以下映射產物 [13, 14]：

#### 映射產物與環境變數處理
*   **`docker-compose.yaml`**：定義所有服務、共享網路（預設為 `bridge` 驅動的 `aspire` 網路）及磁碟卷（如 Npgsql 的 data volumes）[14, 15]。
*   **`.env` 檔案**：放置所有尚未填充的環境變數佔位符 [14]。例如資料庫的密碼會映射為 `POSTGRES_PASSWORD=${DATABASE_PASSWORD}`，而 API 專案則會自動被注入適應 Compose 網路的連線字串 [5, 15]。
*   **`.env.{environment}` 檔案**：在執行 `aspire deploy --environment {env}` 時，系統會根據特定環境分支（例如 staging/production）自動生成已填充具體數值的變數檔案 [14, 16]。

#### 鏡像與端口映射規則
*   **SDK Container 內建容器化 vs. Dockerfile 模式**：
    *   預設情況下，.NET 專案會透過 .NET SDK 的內建容器化功能 `dotnet publish /t:PublishContainer` 直接打包為 Linux 容器鏡像，完全不需要手寫 Dockerfile [17, 18]。
    *   若專案有非 .NET 特有建置需求（例如包含 Node.js 前端 build 步驟、或需要執行 `apt-get install` 裝載 OS 套件），則可在 AppHost 調用 `.PublishAsDockerFile()`，此時 Aspire 發布時會自動為該專案生成實體的 `Dockerfile` [14, 19-21]。
    *   **限制（docker-compose build 的不相容性）**：`docker-compose build` 需要實體 Dockerfile，因此**無法直接支援 SDK 內建容器化打包方式** [22]。若要使用 SDK 打包鏡像，正確做法是先透過 .NET SDK 發布鏡像至本地 Daemon，然後在 Compose 的 `image:` 欄位中直接引用它，而非使用 `build:` 屬性 [22]。
*   **客製化 API**：
    *   可使用 `.WithRemoteImageName("myorg/myapi")` 及 `.WithRemoteImageTag("v1.0.0")` 自訂 Compose 發布時使用的遠端鏡像屬性 [23]。
    *   可在 AppHost 使用 `.ConfigureComposeFile(async compose => { ... })` 程式化客製輸出的 Compose YAML 模型（如修改 restart 策略、強指引 `pull_policy` 等） [24, 25]。
    *   調用 `.WithImagePullPolicy()` 可以自動將 Pull 策略映射為 Docker Compose 的 `pull_policy` 欄位 [26]。

#### 已知網路與運作限制
*   **Container-to-Host 本地網路連線故障（WSL2 / Podman）**：
    *   在 Windows 搭配自建 WSL2 或使用 Podman 環境下，容器到宿主機（Container-to-Host）的網路連線不夠穩定 [27-30]。當使用非官方 Docker Desktop 驅動（例如自製批次檔包裝 `wsl docker`）時，若在 AppHost 對同一 Container 連續呼叫二次 `.WithEnvironment`，在部分 Aspire 9.x 版本會引發 Developer Control Plane (DCP) 無法建立容器的 regression [31-33]。
    *   容器連回 Windows 宿主機（如 `host.docker.internal` 或 `host.containers.internal`）在 Podman 下常因 proxy 僅綁定在本地 `127.0.0.1` 導致超時故障 [34-37]。
*   **單向部署限制**：雖然 Aspire 13.x 開始針對 Docker Compose 的 `aspire deploy` 引進了 Podman/Docker 的自動偵測與部署支援 [38]，但在生產環境或複雜的 VPS 環境下，社群普遍仍建議僅採用 `aspire publish` 產生 parameterized YAML 與 `.env` 檔案，再交由團隊既有的 CI/CD 以 `docker compose up -d` 部署 [39-42]。

---

### 3. Helm Chart 與 Kubernetes Resource 映射細節與已知限制

若在 AppHost 中配置了 `AddKubernetesEnvironment` 物件 [43]，發布時 Aspire 將會根據拓撲結構將 App Model 原生翻譯為 Kubernetes 物件，並**預設以 Helm 作為部署與封裝引擎** [38, 44]：

#### 核心映射規則表
| Aspire 資源模型 | Kubernetes 資源類型 | 映射細節 |
| :--- | :--- | :--- |
| **Project 專案 / Container 容器** | **Deployments** 或 **StatefulSets** | 預設為 Deployment。若該服務綁定了 Persistent Volume，會被自動晉升（Promote） [45]。 |
| **Connection Strings / Env Variables** | **ConfigMaps** 和 **Secrets** | 提供無狀態環境參數解耦 [45]。 |
| **Endpoints 終端節點** | **Services** | 用於內部 Pod-to-Pod 網路通訊 [45]。 |
| **Volumes 儲存卷** | **PersistentVolumes (PV)** / **PersistentVolumeClaims (PVC)** | 透過 `AddPersistentVolume` 聲明與繫結 [45, 46]。 |

#### 極為關鍵的映射規則：StatefulSet 的自動晉升
*   當您在 K8s 環境下為 Durable Storage 建模第一類 K8s 物件時，需透過 `AddPersistentVolume` 配置 Storage Class、Capacity 與 Access Mode [46, 47]。
*   **在 Publish 階段，該 Volume 會被渲染成 `v1.PersistentVolumeClaim`，並且任何繫結了該 PVC 的服務工作負載（Workload），都會被 Aspire 自動由預設的 `Deployment` 晉升為 `StatefulSet`** [45, 46]。

#### Helm 整合、Ingress 與 Gateway API
*   **自訂 Helm 配置**：可透過 `.WithHelm()` 擴充方法自訂產出的 Helm Chart 名稱、版本、Release Name、Description 及目標 K8s Namespace，這些自訂值會被寫入產出的 `Chart.yaml` 中 [38, 48-50]。
*   **進階網路路由支援**：K8s 環境原生支援 **Ingress** (`networking.k8s.io/v1`) 與 **Gateway API** (`gateway.networking.k8s.io/v1`) 資源，並可使用 fluent API 配置 `.WithRoute()`、`.WithTls()`、`.WithHostname()` 等 [51]。
    *   其 Gateway TLS 支援 **FQDN 自動發現**：若調用 `.WithTls()` 但未指定 Hostname，Aspire 會在部署時輪詢並自動修補控制器分配的地址 [51]。
*   **自訂 Manifest 與 CRD 擴充**：
    *   透過 `PublishAsKubernetesService` 回呼，可客製化單一服務產生的 YAML（例如將 Replicas 調整為 3） [52, 53]。
    *   在 C# AppHost 中，您可以直接繼承 `BaseKubernetesResource` 來定義任何 **自訂 CRD (如 cert-manager 的 Certificate 物件)**，並直接將其添加至 `AdditionalResources` 集合中 [54, 55]。
    *   在 TypeScript AppHost 中，則可調用 `addManifest` 並傳入 `apiVersion` 與 `kind`，再利用 dot-notation 動態修補自訂欄位的值 [54, 56]。

#### 已知限制與 Gotchas
*   **Helm 版本強烈依賴**：Aspire 的 K8s 部署管線在幕後會直接調用 `helm upgrade --install` 指令，**系統環境變數 PATH 中必須安裝有 Helm v4.2.0 或更高版本**，否則部署步驟會立刻失敗中斷 [57, 58]。
*   **多計算環境歧義**：若您在同一個 AppHost 中同時註冊了 K8s 環境與 Docker Compose 環境，必須在各個 Project/Container 資源上調用 `.WithComputeEnvironment(...)` 明確指定目標，否則在執行 `aspire publish` 時會立刻擲回 **Ambiguous Compute Environment 異常** [59, 60]。

---

### 4. 關鍵的通用部署限制（Data Migrations 的重大改變）

在企業級部署中，最容易踩坑的已知限制在於 **資料庫移轉（Database Migrations）在 `aspire deploy` 中預設是完全不會被執行的** [61]：

*   **行為回歸與權責劃分**：在採用官方新版 `AddEFMigrations` 管理遷移時，`RunDatabaseUpdateOnStart()` 僅在本地開發期（IsRunMode）有效 [62, 63]。而 `PublishAsMigrationBundle()` 在 publish 時會將遷移編譯並打包為一個 **Self-contained Native 執行檔**（輸出至 `efmigrations/migrations`） [63-65]。**Aspire 的職責在此處便宣告結束，將其指向目標資料庫並執行的工作，必須由團隊的 CI/CD Pipeline 步驟完全手動接管** [61, 63, 65]。
*   **建置平台的系統架構強耦合限制**：由於產出的 Migration Bundle 是一個編編出來的**二進位原生執行檔** [66, 67]。若您在 Apple Silicon (M1/M2/M3 Mac) 上執行 `aspire publish`，將會產生一個 ARM64 格式的 Mach-O 執行檔，這意味著該檔案**完全無法**在 Linux CI/CD Agent (例如 Github Actions 預設的 x64 Ubuntu runner) 上執行 [66, 67]。
    *   **應對方案**：如果部署目標是 Kubernetes/AKS，強烈建議不要採用預設的 Bundle，改為調用 `.PublishAsAzureContainerAppJob()`（它會將遷移封裝成 K8s Job，並在 deploy 時自動執行），或是採用 `.PublishAsMigrationScript()` 生成冪等的 `.sql` 腳本，透過傳統的 DB 工具套用 [63, 68]。

💡 **建議下一步**：想了解如何透過設定 Github Actions 或 Azure DevOps，來解決 Migration Bundle 在 CI/CD 執行器上的 Linux/Mac 平台架構衝突，並安全地在 K8s 中套用資料庫 Schema 更新嗎？

**引用來源：**
- [1] [[RaspeR87 部落格] 技術指引 - .NET Aspire 自訂發布與部署流水線實作](https://rasper87.blog/2025/11/05/net-aspire-custom-publish-deployment-pipelines/)
- [2] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [3] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [4] [[Microsoft 官方] 架構文件 - .NET Aspire 內部部署機制與 Manifest 設計原理](https://aspire.dev/deployment/deploy-with-aspire/)
- [5] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [6] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [7] [[Microsoft 官方] 架構文件 - .NET Aspire 內部部署機制與 Manifest 設計原理](https://aspire.dev/deployment/deploy-with-aspire/)
- [8] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [9] [[Reddit 社群] 案例研究 - 從開發容器移轉至生產環境託管的實務架構討論](https://www.reddit.com/r/dotnet/comments/1kxau0s/aspire_is_amazing_how_to_go_from_dev_containers/)
- [10] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [11] [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [12] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [13] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [14] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [15] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [16] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [17] [[Microsoft 官方] 技術指引 - 使用 .NET Aspire 建置自訂發布與部署流水線](https://aspire.dev/deployment/custom-deployments/)
- [18] [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [19] [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [20] [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [21] [[Microsoft 官方] 技術文件 - 在 .NET Aspire 之中託管與執行外部原生執行檔](https://aspire.dev/app-host/executable-resources/)
- [22] [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [23] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [24] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [25] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [26] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [27] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [28] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [29] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [30] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [31] [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)
- [32] [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)
- [33] [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)
- [34] [[Microsoft GitHub] 故障排除 - 本機容器連線至主機 Aspire 專案資源的網路對應](https://github.com/microsoft/aspire/discussions/1808)
- [35] [[Microsoft GitHub] 故障排除 - 本機容器連線至主機 Aspire 專案資源的網路對應](https://github.com/microsoft/aspire/discussions/1808)
- [36] [[Microsoft GitHub] 故障排除 - 使用 Podman 時 Container-to-Host 網路連線故障處理](https://github.com/microsoft/aspire/issues/6846)
- [37] [[Microsoft GitHub] 故障排除 - 使用 Podman 時容器無法連線至 Aspire 主機服務之解決方案](https://github.com/microsoft/aspire/issues/4136)
- [38] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [39] [[RaspeR87 部落格] 技術指引 - .NET Aspire 自訂發布與部署流水線實作](https://rasper87.blog/2025/11/05/net-aspire-custom-publish-deployment-pipelines/)
- [40] [[RaspeR87 部落格] 技術指引 - .NET Aspire 自訂發布與部署流水線實作](https://rasper87.blog/2025/11/05/net-aspire-custom-publish-deployment-pipelines/)
- [41] [[RaspeR87 部落格] 技術指引 - .NET Aspire 自訂發布與部署流水線實作](https://rasper87.blog/2025/11/05/net-aspire-custom-publish-deployment-pipelines/)
- [42] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [43] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [44] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 專案發布並部署至 Kubernetes 集群](https://aspire.dev/deployment/kubernetes/)
- [45] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 專案發布並部署至 Kubernetes 集群](https://aspire.dev/deployment/kubernetes/)
- [46] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [47] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [48] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [49] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [50] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [51] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [52] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [53] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [54] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [55] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [56] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [57] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 專案發布並部署至 Kubernetes 集群](https://aspire.dev/deployment/kubernetes/)
- [58] [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [59] [[RaspeR87 部落格] 技術指引 - .NET Aspire 自訂發布與部署流水線實作](https://rasper87.blog/2025/11/05/net-aspire-custom-publish-deployment-pipelines/)
- [60] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [61] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [62] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [63] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [64] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [65] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [66] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [67] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [68] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)

### 4. Aspire 9 的新功能與 API 變化（相比 Aspire 8），以及對企業採用的影響

### 1. **核心編排與 API 機制的關鍵變化**

從 .NET Aspire 8 升級至 Aspire 9 及後續版本，其核心 API 與系統編排機制發生了數項重大變革：

*   **`WaitFor` 啟動順序編排的引入**：在 Aspire 8 中，開發團隊主要依賴 `.WithReference()` 來定義資源關係，但其僅負責「配置注入」（如傳遞連接字串），並不會阻止啟動時的競合 [1]。**Aspire 9 引入了 `.WaitFor(resource)` API，能夠真正阻塞 dependent 資源的啟動，直到被依賴的資源（如資料庫或 Redis 容器）回報為健康（Healthy）狀態** [1, 2]。這解決了微服務在冷啟動時因依賴未就緒而發生的啟動崩潰 [1, 3]。
*   **生命週期事件訂閱升級（IDistributedApplicationEventingSubscriber）**：原先的 `IDistributedApplicationLifecycleHook` 在後續版本中已被聲明棄用，**全面改用 `IDistributedApplicationEventingSubscriber` 訂閱機制** [4]。此外，Azure、Docker Compose、Kubernetes 與 AKS 的啟動邏輯被重構為具名的 **`BeforeStart` 管道步驟（BeforeStart pipeline steps）**，讓開發者能宣告更複雜、有序的環境初始化任務 [5]。
*   **非 C# AppHost 環境變數 API 統一（`withEnvironment`）**：在 polyglot（多語言）環境下，非 C#（如 TypeScript 寫成的 AppHost）將原先多種 environment 設置 API 統一整合至 **`withEnvironment(name, value)`** 單一模式中，簡化了 TypeScript AppHost 的編寫複雜度並不再提供舊有別名 [6, 7]。
*   **資源端點（Endpoint）覆寫行為優化**：當呼叫 `WithEndpoint`、`WithHttpEndpoint` 或 `WithHttpsEndpoint` 時，**若已存在同名端點，系統現在會直接進行「更新/覆寫」而非拋出異常**，極大簡化了對自動產生端點的第三方元件（如 Vite 應用、Keycloak）進行連接埠自訂的過程 [8]。

---

### 2. **全新功能亮點**

.NET Aspire 9（包含 9.2 至 9.5 增量升級 [9, 10]）推出了一系列重磅功能，將原本僅限於 .NET 的生態系擴展到真正的多語言雲端原生架構中：

#### **A. 多語言（Polyglot）編排平台升級**
*   **不再侷限於 .NET**：Aspire 逐步演進為一個**不限語言的編排引擎（Polyglot Orchestrator）** [11, 12]，原生支援 C# 與 **TypeScript** 編寫 AppHost，並對 Python [13]、Java [14] 提供與 C# AppHost 幾乎對等的一等公民支援。
*   **單檔案 AppHost（File-Based AppHost）**：在 Aspire 9.5 推出預覽，支援 .NET 10 的單檔案應用特性。**開發者現在無須建立完整的 `.csproj` 專案檔，僅需一個單一 C# 檔案即可定義並啟動整個系統的資源圖表**，降低了非 .NET 開發者的入手門檻 [15, 16]。

#### **B. 全新發布與部署模型（Publishers）**
*   **擺脫單一 manifest 限制**：自 9.2 起，原先單一的部署 json 資訊清單逐步退役 [17]。
*   **「Publishers」模型整合**：與開源工具 Aspir8 作者 David Sekula 合作，**將其發布模型直接內建於 .NET Aspire 中** [18, 19]。開發者現在能直接使用 `aspire publish` / `aspire deploy` 命令發布至 **Docker Compose、Kubernetes（以 Helm 為預設引擎 [6]）以及 Azure 平台** [18-20]。
*   **`aspire destroy` 拆除命令**：一鍵將已部署的環境拆除（例如刪除 Azure 資源群組、卸載 Kubernetes Helm release、或下線 Docker Compose 容器） [21]。

#### **C. 企業級安全合規增強**
*   **獨立 Managed Identity（行為變更）**：在部署至 Azure Container Apps 時，先前版本中所有應用共享同一個 Managed Identity [18, 22]。**9.2 版起，預設為每個應用程式分配「專屬的託管識別（Managed Identity）」** [18, 22]。
    *   *影響*：此一變更強化了安全隔離（最小權限原則），但也帶來破壞性變更。例如 **Azure SQL Server** 預設僅支援單一 AD 管理員，多個獨立身份需要顯式為其他應用設定使用者角色 [9, 18, 22]；而 **Azure PostgreSQL** 的 DB 建立者自動成為 owner，其餘微服務需手動 GRANT 權限 [9, 18, 22]。
*   **企業網路隔離**：支援 **Azure 網路安全邊界（Azure Network Security Perimeters）** [6]、Azure 容器註冊表（ACR）與 Azure AI Foundry 的**私有端點（Private Endpoint）** 設置 [5, 6]，讓 PaaS 服務完全隱藏於企業內部網路中。

#### **D. 儀表板（Dashboard）與觀測體驗大跨步**
*   **GitHub Copilot 深度整合**：可在儀表板中一鍵分析日誌、釐清分佈式追蹤（Traces）中的效能瓶頸，並提供錯誤代碼的 AI 解釋 [23-25]。
*   **`WithHttpCommand()` 自訂命令**：允許在儀表板上建立自訂的 HTTP 開發按鈕（例如一鍵為資料庫進行 Seed 填入測試資料、或清空快取） [26-28]。
*   **GenAI 遙測視覺化工具（GenAI Visualizer）**：在 Aspire 9.5 推出，當偵測到 AI 的 SDK（如 OpenAI）調用時，會以「✨」圖示整合 LLM 的輸入與輸出 Payload，甚至支援多模態內容（如生成圖像）與 Markdown 預覽 [29, 30]。
*   **「All」多資源日誌串流**：提供一鍵串流所有微服務日誌的統一檢視，並帶有顏色標記以便調試 [29]。

---

### 3. **對企業採用的技術深度與架構影響**

.NET Aspire 9 的重大更新對企業的技術架構與團隊協作，產生了深遠的正面影響：

*   **加速內部開發迴圈，破除「容器化調試」的痛點**：以往微服務開發，團隊需要手動維護複雜的 Docker Compose 來模擬環境，這會導致偵錯（Attach debugger）困難、電腦資源（RAM）消耗巨大且容易發生連接埠衝突 [31-34]。**Aspire 允許在本地直接以原生 Process（Plain Process）運行自己的代碼以利 F5 偵錯，僅將外部第三方依賴（如 Postgres/Kafka）容器化** [32, 35-37]。這顯著降低了新員工的入職與環境建置成本（從數天縮短至數分鐘） [38]。
*   **DevOps 邊界對齊與「程式碼即基礎設施 (IaC)」**：以前開發者在本地透過 `.env` 寫死 `localhost:5432`，而 Ops 團隊在生產環境則要設定 Bicep / Terraform 和安全性憑證，這經常在交付時產生「本地可以跑，到雲端就壞掉」的摩擦 [39, 40]。**透過 Aspire，同一套 C# 描述檔在本地運行時拉起 Docker 容器，在部署時則能自動合成 Bicep/YML 並利用 Managed Identity、私有端點對接雲端 PaaS（如 Azure SQL/Cosmos）** [41-43]。將架構關係統一在代碼（C# 或 TS）中，實現了 Dev 與 Ops 對系統拓撲的單一事實來源（Single Source of Truth） [41, 44, 45]。
*   **降低非 Azure 部署與私有雲遷移的阻力**：早期企業擔心 Aspire 有強烈的 Azure 綁定（Cloud Lock-in） [46]。**隨著 9.x 系列中 Docker Compose、Kubernetes (Helm) 部署管道的成熟與 Standalone Dashboard 獨立化** [6, 14, 18-20, 47]，企業可以在其現有的私有 K8s 叢集或 AWS 基礎設施中，享受 ServiceDefaults 所帶來的 OpenTelemetry 監控、Resilience（Polly）以及 Service Discovery 優勢，而不必強制遷移雲端平台 [38, 48-51]。
*   **行業領先指標的權威認可**：在 2025 年 4 月，**Thoughtworks 將 .NET Aspire 列入其知名的技術雷達（Technology Radar）** [28, 52]。這極大地提升了架構師和技術選型決策者在企業內推廣 .NET Aspire 的信心，將其視為架構微服務與雲端原生應用的業界最佳實踐。

💡 **您是否希望了解如何將現有的 .NET 8 / 8.0 Aspire 專案順暢升級至 Aspire 9 甚至最新的 SDK 版本，或需要對特定的資料庫移轉機制進行深入研究？**

**引用來源：**
- [1] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [2] [[iT 邦幫忙 部落格] 技術文章 - 利用 .NET Aspire 簡化與統一團隊本機開發環境之實踐](https://dev.to/baoduy2412/net-aspire-simplifying-local-development-environment-and-testing-an8)
- [3] [[C# Corner] 技術實作 - 基於 .NET Aspire 的端對端 (E2E) 整合測試架構與實踐](https://www.c-sharpcorner.com/article/end-to-end-integration-testing-with-net-aspire/)
- [4] [[Microsoft 官方] 技術指引 - 讓 Aspire 容器能夠解析主機之自訂本機網域設定](https://anthonysimmon.com/dotnet-aspire-containers-share-custom-hosts/)
- [5] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [6] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [7] [[Microsoft 官方] 技術文件 - 在 .NET Aspire 之中託管與執行外部原生執行檔](https://aspire.dev/app-host/executable-resources/)
- [8] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [9] [[InfoQ 媒體] 技術新聞 - .NET Aspire 9.2 部署功能擴展與儀表板優化](https://www.infoq.com/news/2025/04/dotnet-aspire-92-release/)
- [10] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [11] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [12] [[Microsoft 官方] 產品介紹 - .NET Aspire 分散式系統編排平台之核心定位與願景](https://aspire.dev/get-started/what-is-aspire/)
- [13] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [14] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [15] [[Microsoft 官方] 技術公告 - .NET Aspire 9.5 新特性與效能改進](https://devblogs.microsoft.com/dotnet/announcing-dotnet-aspire-95/)
- [16] [[Microsoft 官方] 技術公告 - .NET Aspire 9.5 新特性與效能改進](https://devblogs.microsoft.com/dotnet/announcing-dotnet-aspire-95/)
- [17] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [18] [[InfoQ 媒體] 技術新聞 - .NET Aspire 9.2 部署功能擴展與儀表板優化](https://www.infoq.com/news/2025/04/dotnet-aspire-92-release/)
- [19] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [20] [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [21] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [22] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [23] [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [24] [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [25] [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [26] [[InfoQ 媒體] 技術新聞 - .NET Aspire 9.2 部署功能擴展與儀表板優化](https://www.infoq.com/news/2025/04/dotnet-aspire-92-release/)
- [27] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [28] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [29] [[Microsoft 官方] 技術公告 - .NET Aspire 9.5 新特性與效能改進](https://devblogs.microsoft.com/dotnet/announcing-dotnet-aspire-95/)
- [30] [[Microsoft 官方] 技術公告 - .NET Aspire 9.5 新特性與效能改進](https://devblogs.microsoft.com/dotnet/announcing-dotnet-aspire-95/)
- [31] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [32] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [33] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [34] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [35] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [36] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [37] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [38] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [39] [[Agramont.net 部落格] 技術指引 - 使用 GitHub Actions 部署 .NET Aspire 至 Azure](https://agramont.net/blog/devops-intro-deploy-net-aspire-azure-github-actions/)
- [40] [[Microsoft 官方] 產品介紹 - .NET Aspire 分散式系統編排平台之核心定位與願景](https://aspire.dev/get-started/what-is-aspire/)
- [41] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [42] [[Reddit 社群] 案例研究 - 從開發容器移轉至生產環境託管的實務架構討論](https://www.reddit.com/r/dotnet/comments/1kxau0s/aspire_is_amazing_how_to_go_from_dev_containers/)
- [43] [[Kalle Marjokorpi 部落格] 案例研究 - .NET Aspire 對企業微服務技術選型與架構之深遠影響](https://www.kallemarjokorpi.fi/blog/how-net-aspire-changed-my-way-of-building-software/)
- [44] [[Microsoft 官方] 產品介紹 - .NET Aspire 分散式系統編排平台之核心定位與願景](https://aspire.dev/get-started/what-is-aspire/)
- [45] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [46] [[Reddit 社群] 案例研究 - 從開發容器移轉至生產環境託管的實務架構討論](https://www.reddit.com/r/dotnet/comments/1kxau0s/aspire_is_amazing_how_to_go_from_dev_containers/)
- [47] [[InfoQ 媒體] 技術新聞 - .NET Aspire 9.4 CLI 正式版與互動式儀表板新功能](https://www.infoq.com/news/2025/08/dotnet-aspire-9-4-release/)
- [48] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [49] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [50] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [51] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [52] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)

### 5. 多語言支援現況：C#、TypeScript、Python、Java、Go 各語言在 Aspire AppHost 中的整合成熟度與已知限制

自 **Aspire 13**（自此版本起更名為簡稱 **Aspire**）起，微軟已將其定位為「**多語言分散式應用程式平台**」（Polyglot Distributed Application Platform）[1-3]。這代表 AppHost（應用程式主機）除了原生編排 .NET 服務外，也全面支援 Node.js、Python、Go、Java 等多元語言與執行階段，不論是在本機開發的行程（Bare Metal），還是部署至雲端的容器，都能在同一個資源圖（Resource Graph）中統一編排 [2-5]。

以下為 **C#、TypeScript、Python、Java、Go** 各語言在 Aspire AppHost 中的整合現況、成熟度以及已知限制的深度研究：

---

### 1. C#：原生核心支援（最高成熟度）
C# 是 Aspire 的起點，具有最完整且最成熟的功能整合與生態系支持 [6, 7]。
* **整合現況與核心能力**：
  * **強型別專案編排**：使用 `AddProject<Projects.MyService>()` 可在編譯時自動產生專案命名空間，並獲得強型別的程式碼提示與型別安全保障 [6, 8, 9]。
  * **開箱即用的基礎設施**：透過專屬的 `ServiceDefaults` 專案一鍵調用 `AddServiceDefaults()` [10-12]。這能自動配置 OpenTelemetry（日誌、追蹤、度量）、健康檢查（`/health`、`/alive` 端點）、服務發現（Service Discovery）以及基於 Polly 的標準彈性與重試原則 [12-15]。
  * **IDE 與偵錯優勢**：在 Visual Studio、VS Code 或 JetBrains Rider 中按下 `F5` 鍵即可啟動整個分散式架構、自動掛載偵錯器，且本機預設以效能極高的「非容器化」本機行程運行 [2, 16, 17]。
* **已知限制**：
  * **`dotnet ef` CLI 工具衝突**：由於 AppHost 僅作為編排中介，其參考專案屬於中繼資料（Metadata-only）參考，並不會將應用程式實體 DLL 複製到 AppHost 的輸出目錄，這會導致 `dotnet ef` 無法在 AppHost 專案上正常執行 [18]。目前官方推薦的做法是**直接對個別的 API 專案執行移轉指令** [18]，或者採用 prerelease 的 `AddEFMigrations` 原生機制管理資料庫遷移 [19, 20]。

### 2. TypeScript / JavaScript (Node.js)：官方第一類公民（高度成熟）
從 Aspire 13 開始，JavaScript 與 TypeScript 已經從普通的外部執行檔，躍升為與 C# 平起平坐的第一類公民 [2, 5]。
* **整合現況與核心能力**：
  * **TypeScript 原生 AppHost**：不僅能用 C#，現在開發者可以直接編寫 `apphost.mts` 專案 [21]。透過 Aspire CLI 的 `aspire init --language typescript` 即可建立 TypeScript AppHost [22, 23]，並利用自動產生的 TypeScript SDK（ATS）進行型別安全的資源編排 [24, 25]。
  * **專屬前端與後端整合**：AppHost 內建 `AddNpmApp` [26, 27]、`AddNodeApp` [28, 29]、`AddViteApp` [30, 31] 等整合，並支援自動偵測與使用 npm、pnpm、Bun 與 Yarn 4+ 作為套件管理器 [32, 33]。
  * **自動環境變數注入**：呼叫 `.WithReference(api)` 會自動產生並向 Node.js 行程中注入如 `services__apiservice__http__0` 的環境變數，解決本機動態分配 Port 的連線問題 [26, 34]。
* **已知限制**：
  * **拒絕 Yarn Classic (v1)**：自 Aspire 13.3 起，CLI 會主動**拒絕 Yarn Classic (v1)** 並中斷執行 [35]。這是因為 Yarn v1 的參數轉發行為 (argument-forwarding) 與 Aspire 的發布/部署管線不相容，曾造成部署時無預警卡死 (silent hangs) [35]。
  * **遙測需手動配置**：Node.js 服務無法像 C# 一樣自動藉由一個方法注入所有 defaults，開發者需在專案內手動導入 `@opentelemetry/sdk-node`，並將 OTLP 匯出端點指回 Aspire 提供的手動或動態端點 [36, 37]。

### 3. Python：官方第一級 hosting 整合（高度成熟）
Python 被廣泛應用於人工智慧（AI）與資料科學中，Aspire 13 提供了非常精緻且高度自動化的 Python Hosting 整合 [38]。
* **整合現況與核心能力**：
  * **官方 `Aspire.Hosting.Python` 套件**：官方套件已取代過往的社群工具包成為標準推薦 [39]。支援 `AddPythonApp` (運行 Python 腳本) [40, 41]、`AddUvicornApp` (針對 FastAPI/Flask 等 ASGI 應用) [42-44] 等 API。
  * **虛擬環境自動建立**：當 Aspire 偵測到專案目錄中含有 `requirements.txt` 或 `pyproject.toml`，會自動在本機建立並啟用 `.venv` 虛擬環境，開發人員無需手動建立 [45]。
  * **支援極速包管理器 uv**：透過在 AppHost 中鏈結 `.WithUv()`，即可調用極速的 Python 包管理器 **uv** 自動執行 `uv sync`，這比傳統 `pip` 快上數倍 [44, 46]。
  * **Dockerfile 自動生成**：部署時（如呼叫 `aspire publish`），Python Hosting 整合會依據本機 Python 版本與依賴配置，**自動生成 production-ready 的 Dockerfile** [47]。
* **已知限制**：
  * **gRPC OTLP 與 ALPN 限制**：Python 的 OpenTelemetry SDK 預設採用 gRPC 來傳送遙測數據 [48]。然而，Aspire 的 gRPC 端點要求 HTTP/2 必須帶有 ALPN 支持，而 Python 的 `grpcio` 在本機通常無法支援，會導致遙測發送失敗 [48]。
  * **解決方案**：必須將 Python 端的 OTLP Exporter 配置強制切換為 **HTTP/protobuf** 通訊協定（即透過動態環境變數 `DOTNET_DASHBOARD_OTLP_HTTP_ENDPOINT_URL`，將其導向 OTLP/HTTP 接收端）才能正常回傳追蹤與度量 [48, 49]。

### 4. Java：已從社群工具包畢業至官方核心（中高成熟度）
Java 微服務在企業中非常普遍，微軟與社群合作已將 Java 的支援整合入主線 [50, 51]。
* **整合現況與核心能力**：
  * **官方 `Aspire.Hosting.Java` 支援**：在最新的 Aspire 13.5 中，Java 整合已正式從 Community Toolkit 中畢業，改為官方一等套件提供 [52, 53]。
  * **Wrapper 與 JAR 支援**：提供 `AddJavaApp` 方法，原生支援 Maven Wrapper（`mvnw`）與 Gradle Wrapper（`gradlew`）的工作流，也支援直接調用 `java -jar` 運行預編譯的 JAR [50, 54]。
  * **零代碼 Auto-Instrumentation**：內建 `.WithOtelAgent()` 機制，可直接配置 OpenTelemetry Java Agent 的 jar 包路徑，Aspire 會透過 `JAVA_TOOL_OPTIONS` 自動注入 JVM，無須修改 Java 代碼即可回傳分散式追蹤與度量 [50, 54, 55]。
  * **VS Code 偵錯支持**：在 VS Code 中開發時，DCP 提供了 `WithDebugSupport` 機制，支援透過 Java 偵錯擴充功能，以 JDWP 附加 (Attach) 的方式來直接偵錯 Wrapper 所啟動的 Java 專案 [56]。
* **已知限制**：
  * **特化 API 還在規劃中**：雖然核心的執行、Dockerfile 生成、測試和發布已經具備 [56]，但目前的 JVM 與編譯器設定仍屬於通用型別 [54]。如自動設定 Spring Boot 常見設定（如自動偵測設定為 `SERVER_PORT` 或 `SPRING_PROFILES_ACTIVE` 等特化 Fluent API）仍被排在 Backlog 中 [57, 58]。目前通常需要手動透過 `WithHttpEndpoint(env: "SERVER_PORT")` 對應環境變數 [57]。

### 5. Go：官方核心整合，並加入 Go AppHost 探索（中高成熟度）
Go 語言因編譯快、容器體積小而深受雲端原生歡迎，其整合成熟度與 Go 生態系契合度很高 [53, 59]。
* **整合現況與核心能力**：
  * **官方 `Aspire.Hosting.Go` 套件**：自 Aspire 13.4 起，Go 整合已由官方套件接管，原有的 Community Toolkit 宣告棄用 [53, 59, 60]。
  * **Go 工具鏈本機調用**：在開發環境下直接調用本機的 Go 工具鏈（執行 `go run .` 或 `go run ./cmd/server`）而非使用容器執行 [53]，這大幅加快了編譯和本機調試效率。
  * **自動 Dockerfile 與部署**：會解析專案依賴並在發布/部署時，自動生成多階段（Multi-stage）的 Go Dockerfile 進行容器構建 [53]。
  * **實驗性 Go AppHost**：除了在 C# / TypeScript 中編排 Go 專案外，13.4+ 版本起加入了實驗性的**用 Go 語言編寫 AppHost 本身**的支援（提供 `aspire new aspire-go-starter` 模板），讓 Go 開發者可以純 Go 語言管理整個系統的拓撲 [61, 62]。
* **已知限制 / 特殊現況**：
  * 用 Go / Java 撰寫 AppHost（與編排 Go / Java Workloads 區隔）目前屬於 experimental 狀態，需在環境中明確啟用特定的 CLI 特性標記，例如 Java AppHost：`aspire config set -g features.experimentalPolyglot:java true` [32]。

---

### 💡 多語言整合成熟度總覽表

| 語言 | 第一方官方支援版本 | 本機開發執行模式 | 遙測與可觀測性（OTel） | 自動生成 Dockerfile |
| :--- | :--- | :--- | :--- | :--- |
| **C#** | 原生 (1.0) | 本機 `.exe`/行程 [2] | 開箱即用 (`ServiceDefaults`) [12, 13] | 內建 .NET SDK 容器化 [63, 64] |
| **TypeScript** | Aspire 13 [2, 5] | 本機 Node.js 行程 [65] | 需手動引入 SDK 匯出 [36] | 自動生成 [56] |
| **Python** | Aspire 13 [39] | 本機 Python / venv [38, 45] | 需切換為 **HTTP/protobuf** [48, 49] | 自動生成 [47] |
| **Java** | Aspire 13.5 [52, 53] | 本機 JVM / Wrapper [50, 54] | 支援 **OTel Agent** 零代碼注入 [50, 54, 55] | 自動生成 [56] |
| **Go** | Aspire 13.4 [53, 60] | 本機 Go toolchain [53] | 需手動引入 SDK 匯出 [53, 66] | 自動生成 [53] |

---

🤖 **想要試試看建置一個混合語言的實體案例嗎？** 我們可以撰寫一段簡短的 AppHost 程式碼，向您展示如何在 C# 中同時編排並連接 **C# API、Python 機器學習服務與 Next.js 前端**，並完美處理它們之間的「服務發現」動態連線！如果您有需要，我可以馬上提供一份基礎設定範本給您。

**引用來源：**
- [1] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [2] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [3] [[Microsoft 官方] 產品介紹 - .NET Aspire 分散式系統編排平台之核心定位與願景](https://aspire.dev/get-started/what-is-aspire/)
- [4] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [5] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [6] [[Microsoft 官方] 技術文件 - 用於建置分散式應用程式的 .NET Aspire SDK 規格](https://aspire.dev/get-started/aspire-sdk/)
- [7] [[JetBrains 官方] 產品功能 - 在 JetBrains Rider 中使用 .NET Aspire 插件進行開發](https://blog.jetbrains.com/dotnet/2024/02/19/jetbrains-rider-and-the-net-aspire-plugin/)
- [8] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [9] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [10] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [11] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [12] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [13] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [14] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [15] [[Microsoft 官方] 技術文件 - C# 專案中 Service Defaults 的預設功能與擴充設定](https://aspire.dev/get-started/csharp-service-defaults/)
- [16] [[mstack 部落格] 技術指引 - 自訂 .NET Aspire 元件與整合測試開發指南](https://mstack.nl/blogs/wiremock-net-aspire-component/)
- [17] [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [18] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [19] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [20] [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [21] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [22] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [23] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [24] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [25] [[Microsoft 官方] 技術文件 - .NET Aspire 各個整合服務之間的端到端安全通訊架構](https://aspire.dev/integrations/custom-integrations/secure-communication/)
- [26] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [27] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [28] [[Microsoft 官方] 步驟指南 - 從零開始建立第一個 .NET Aspire 分散式應用程式](https://dotnet-aspire-52.mintlify.app/quickstart)
- [29] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [30] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [31] [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [32] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [33] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [34] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [35] [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [36] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [37] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [38] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [39] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [40] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [41] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [42] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [43] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [44] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [45] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [46] [[Context7 部落格] 架構分析 - .NET Aspire 平台與 Orchestration 核心元件拆解](https://context7.com/websites/aspire_dev)
- [47] [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [48] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [49] [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [50] [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [51] [[Microsoft 官方] 步驟指南 - 使用 .NET Aspire 開發與整合 Java 應用程式](https://aspire.dev/integrations/frameworks/java/java-get-started/)
- [52] [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [53] [[Microsoft 官方] 步驟指南 - 使用 .NET Aspire 整合與管理 Go 語言微服務](https://aspire.dev/integrations/frameworks/go/go-get-started/)
- [54] [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [55] [[Microsoft 官方] 步驟指南 - 使用 .NET Aspire 開發與整合 Java 應用程式](https://aspire.dev/integrations/frameworks/java/java-get-started/)
- [56] [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [57] [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [58] [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [59] [[Microsoft 官方] 步驟指南 - 使用 .NET Aspire 整合與管理 Go 語言微服務](https://aspire.dev/integrations/frameworks/go/go-get-started/)
- [60] [[Context7 部落格] 架構分析 - .NET Aspire 平台與 Orchestration 核心元件拆解](https://context7.com/websites/aspire_dev)
- [61] [[Context7 部落格] 架構分析 - .NET Aspire 平台與 Orchestration 核心元件拆解](https://context7.com/websites/aspire_dev)
- [62] [[Microsoft 官方] 步驟指南 - 使用 .NET Aspire 整合與管理 Go 語言微服務](https://aspire.dev/integrations/frameworks/go/go-get-started/)
- [63] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [64] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [65] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [66] [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)

### 6. Aspire 與 Azure DevOps Pipeline / GitHub Actions 的整合方式：CI/CD 流程中 Aspire 扮演的角色

在企業級分散式系統中，將 **.NET Aspire** 與 **GitHub Actions** 或 **Azure DevOps Pipelines** 整合，能為開發（Dev）與維運（Ops）建立順暢且安全的協作橋樑 [1]。

以下為 .NET Aspire 在 CI/CD 流程中所扮演的角色、核心整合機制，以及具體的實作方式：

---

### 一、 核心架構定位：雙層流水線的分工與責任

在 CI/CD 中，整合的核心邏輯在於**「雙層流水線（Two Pipelines）的分工」** [2]：

| 流水線層級 | 負責的事物（維運與排程） | 具體職責與範例 [2, 3] |
| :--- | :--- | :--- |
| **CI/CD 流水線**<br>*(GitHub Actions / Azure DevOps)* | **決定「何時」執行與「如何」保護** | 負責原始碼檢出（Checkout）、執行測試、審批流程（Approvals）、身分識別與憑證管理、產物保留、環境晉升與作業邊界。 |
| **Aspire 流水線**<br>*(AppHost & Aspire CLI)* | **決定「應用程式」的內部定義與步驟** | 負責 AppHost 資源圖表的評估、環境選擇、參數解析、容器建置與推送、服務啟動順序、以及目標平台（如 Bicep、Helm）的產物生成。 |

**.NET Aspire 的 AppHost 專案成為應用程式架構的「單一真理源」（Single Source of Truth）** [4, 5]。它將應用程式拓撲與關係（如資料庫連線、Redis 依賴）以 C# 或 TypeScript 程式碼定義，避免在 CI/CD 的 YAML 檔中重複編寫複雜的資源連接或參數對應邏輯 [6, 7]。

---

### 二、 .NET Aspire 在 CI/CD 流程中扮演的角色

在 CI/CD 流程的不同階段，.NET Aspire 扮演了以下重要角色：

#### 1. 產物與架構定義生成 (Publishing)
當 CI/CD 調用 `aspire publish` 時，Aspire 會評估 AppHost 中的資源配置，並將其翻譯為目標平台專用的參數化部署產物（例如：Azure 的 **Bicep 範本**、Kubernetes 的 **Helm charts**，或自託管環境的 **Docker Compose YAML** 與參數化 `.env` 檔） [8-11]。這讓維運人員不需手寫複雜的 IaC 程式碼 [5]。

#### 2. 自動化容器封裝與推送 (Build & Push)
CI/CD 無需為每個微服務手寫個別的 Dockerfile（除非有特殊需求） [12, 13]。Aspire 的發布管線可以使用 .NET SDK 內建的容器化功能（`dotnet publish /t:PublishContainer`）或本機容器執行階段，自動為服務打包映像檔，並安全地推送至容器登錄表（如 ACR 或 GHCR） [14, 15]。在 GitHub Actions 中，只需呼叫 `aspire do push`，即可一鍵完成所有服務映像檔的建置與推送 [16, 17]。

#### 3. 基礎設施配置與部署 (Deploy)
* **直連部署模式（`aspire deploy`）**：在 CI/CD 代理程式已取得目標環境授權時，由 Aspire 接管完整流程——解析參數、佈署雲端基礎設施、建置並推送映像檔，並套用部署變更 [18, 19]。
* **一向手交模式（`aspire publish`）**：若企業維運有嚴格的安全審查（如金融、醫療等受控環境），則可在 CI 階段使用 `aspire publish` 生成產物，交由安全防護網或 GitOps 工具（如 ArgoCD、kubectl、Helm）於 CD 階段套用 [7, 20-22]。

---

### 三、 與 GitHub Actions & Azure DevOps 的具體整合路徑

#### 路徑 A：整合 Azure Developer CLI (azd) —— 針對 Azure 的最佳實踐
對於部署至 Azure Container Apps (ACA) 或 Azure App Service 的生產環境，使用 **Azure Developer CLI (azd)** 是微軟最推薦、也最成熟的整合路徑 [23, 24]：

1. **一鍵初始化**：在 AppHost 目錄下運行 `azd init`，工具會自動掃描 Aspire 拓撲，並生成配置檔（如 `azure.yaml`） [23, 25]。
2. **自動生成 CI/CD 管線**：運行 `azd pipeline config` 命令 [26]。它會引導維運人員在 Azure 中建立安全的主體服務（Service Principal）或設定同盟認證（Federated Credentials），並自動在 GitHub 或 Azure DevOps 存放庫中註冊加密的機敏資訊與變數，最後**自動生成對應的 YAML 工作流檔案**（如 GitHub Actions 的 `azure-dev.yaml`） [5, 26-28]。
3. **管線執行邏輯**：在 CI/CD 執行時，工作流只需呼叫 `azd up --no-prompt` [29, 30]。該命令會調用 Aspire 機制，以 Bicep 進行無感基礎設施佈署、編譯服務容器、將映像檔安全推入 Azure Container Registry (ACR)，並藉由受控識別（Managed Identity）直接套用至 Container Apps 運作 [15, 23]。

#### 路徑 B：通用 CI/CD 路徑 —— 呼叫 Aspire CLI 部署 Docker Compose 或 Kubernetes
如果目標是部署至自託管伺服器或 Kubernetes，CI/CD 可以安裝 Aspire CLI 作為全域工具，並執行標準 CLI 工作流 [31, 32]：

1. **安裝工作負載與工具**：
   在 GitHub Actions 步驟中：
   ```yaml
   - name: Install Aspire CLI
     run: |
       curl -sSL https://aspire.dev/install.sh | bash
       echo "$HOME/.aspire/bin" >> $GITHUB_PATH
   ```
   在 Azure DevOps 步驟中：
   ```yaml
   - script: 'dotnet workload install aspire'
     displayName: 'Install .NET Aspire workload'
   ``` [33]
2. **生成與套用部署**：
   * **對於 Docker Compose**：執行 `aspire publish -o ./artifacts`，Aspire 會為 AppHost 定義的服務生成合規的 `docker-compose.yaml` 及 `.env` 檔 [9, 34]。接著管線便能直接呼叫 `docker compose up --build -d` 部署於目標主機上 [35, 36]。
   * **對於 Kubernetes**：調用 `aspire publish` 生成對應的 Helm charts 產物，再使用 CI 流程中的 `helm` 或 `kubectl` 將其套用至當前的叢集上下文（Cluster Context） [11, 22]。

---

### 四、 CI/CD 管線範例與安全整合測試 (Integration Testing)

除了資源部署外，.NET Aspire 也簡化了 **CI/CD 流程中的端對端（E2E）整合測試** [37]。

在 CI 執行期，測試代理程式（Runner）通常不具備與本機開發相同的開發者認證，此時必須利用服務主體（Service Principal）進行授權。只要在管線中設定標準的 Azure SDK 環境變數，測試專案的 `DefaultAzureCredential` 就會自動讀取並完成驗證 [38]：

#### 1. GitHub Actions 整合測試與建置部署工作流範例
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]

jobs:
  test-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
      id-token: write  # 同盟認證 (OIDC) 授權所需

    steps:
      - uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '10.x'

      - name: Restore tools & dependencies
        run: |
          dotnet workload install aspire
          dotnet restore
        # 在 CI 環境中，平台預設會傳遞 CI=true，
        # 整合測試機制 (如 DistributedApplicationTestingBuilder) 
        # 會自動檢測此變數並自動拉長逾時限制，避免代理程式執行過慢導致超時 [39]。

      - name: Run Aspire Integration Tests
        env:
          AZURE_CLIENT_ID: ${{ secrets.AZURE_CLIENT_ID }}
          AZURE_TENANT_ID: ${{ secrets.AZURE_TENANT_ID }}
          AZURE_CLIENT_SECRET: ${{ secrets.AZURE_CLIENT_SECRET }}
          AZURE_SUBSCRIPTION_ID: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
        run: dotnet test --no-restore

      # 部署至 Azure (使用推薦的 AZD 工作流)
      - name: Install azd
        uses: Azure/setup-azd@v2

      - name: Log in to Azure (Federated Credentials)
        run: |
          azd auth login \
            --client-id "${{ vars.AZURE_CLIENT_ID }}" \
            --federated-credential-provider "github" \
            --tenant-id "${{ vars.AZURE_TENANT_ID }}"

      - name: Provision and Deploy
        run: azd up --no-prompt
        env:
          AZURE_ENV_NAME: ${{ vars.AZURE_ENV_NAME }}
          AZURE_LOCATION: ${{ vars.AZURE_LOCATION }}
          AZURE_SUBSCRIPTION_ID: ${{ vars.AZURE_SUBSCRIPTION_ID }}
``` [29, 33, 39, 40]

#### 2. Azure DevOps Pipelines YAML 工作流範例
```yaml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  - group: AzureConnectionSecrets # 包含 AZURE_CLIENT_ID, AZURE_CLIENT_SECRET 等安全變數

steps:
  - task: UseDotNet@2
    displayName: 'Setup .NET SDK'
    inputs:
      version: '10.x'

  - script: 'dotnet workload install aspire'
    displayName: 'Install .NET Aspire Workload' [33]

  - task: DotNetCoreCLI@2
    displayName: 'Run Aspire Integration Tests'
    inputs:
      command: test
      projects: '**/*.Tests.csproj'
    env:
      AZURE_CLIENT_ID: $(AZURE_CLIENT_ID)
      AZURE_TENANT_ID: $(AZURE_TENANT_ID)
      AZURE_CLIENT_SECRET: $(AZURE_CLIENT_SECRET)
      AZURE_SUBSCRIPTION_ID: $(AZURE_SUBSCRIPTION_ID) [40]
```

---

### 五、 維運注意事項與 Gotchas [41, 42]
* **基礎設施更新機制**：若在 CI/CD 管線中調用 `azd deploy` 前沒有執行 `azd provision`，可能會遇到 Bicep 狀態與容器參數不符的問題 [42]。`azd provision` 非常聰明，它能自動偵測架構是否有異動，若無變更，則只會刷新部署狀態而不會重複發送重複部署資源的請求 [42]。
* **刪除資源組的連帶效應**：如果必須清理部署，建議在流水線中透過 `azd down` 刪除資源組 [41]。若從 Azure Portal 手動強制刪除資源組，將會使 GitHub 端的部署狀態快取發生衝突，導致下一次 CI/CD 流水線運作失敗 [41]。

💡 您是否想為既有的專案架構自動生成一份部署至 Azure ACA 的 GitHub Actions 工作流程（YAML 檔）呢？我可以為您詳細配置。

**引用來源：**
- [1] [[Agramont.net 部落格] 技術指引 - 使用 GitHub Actions 部署 .NET Aspire 至 Azure](https://agramont.net/blog/devops-intro-deploy-net-aspire-azure-github-actions/)
- [2] [[Microsoft 官方] 架構文件 - .NET Aspire 企業級 CI/CD 持續整合與部署策略概覽](https://aspire.dev/deployment/ci-cd/)
- [3] [[Microsoft 官方] 架構文件 - .NET Aspire 企業級 CI/CD 持續整合與部署策略概覽](https://aspire.dev/deployment/ci-cd/)
- [4] [[Microsoft 官方] 架構文件 - .NET Aspire 企業級 CI/CD 持續整合與部署策略概覽](https://aspire.dev/deployment/ci-cd/)
- [5] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [6] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [7] [[Microsoft 官方] 架構文件 - .NET Aspire 企業級 CI/CD 持續整合與部署策略概覽](https://aspire.dev/deployment/ci-cd/)
- [8] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [9] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [10] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [11] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 專案發布並部署至 Kubernetes 集群](https://aspire.dev/deployment/kubernetes/)
- [12] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [13] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [14] [[Microsoft 官方] 技術指引 - 使用 .NET Aspire 建置自訂發布與部署流水線](https://aspire.dev/deployment/custom-deployments/)
- [15] [[Microsoft 官方] 步驟指南 - 使用 azd 將 .NET Aspire 專案部署至 Azure 平台](https://aspire.dev/deployment/azure/)
- [16] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [17] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [18] [[Microsoft 官方] 架構文件 - .NET Aspire 企業級 CI/CD 持續整合與部署策略概覽](https://aspire.dev/deployment/ci-cd/)
- [19] [[Microsoft 官方] 步驟指南 - 使用 azd 將 .NET Aspire 專案部署至 Azure 平台](https://aspire.dev/deployment/azure/)
- [20] [[Microsoft 官方] 架構文件 - .NET Aspire 企業級 CI/CD 持續整合與部署策略概覽](https://aspire.dev/deployment/ci-cd/)
- [21] [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [22] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 專案發布並部署至 Kubernetes 集群](https://aspire.dev/deployment/kubernetes/)
- [23] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [24] [[Microsoft 官方] 技術指引 - 整合既有 Azure Developer CLI (azd) 流水線至 Aspire 專案](https://aspire.dev/deployment/azure/azure-developer-cli/)
- [25] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [26] [[Agramont.net 部落格] 技術指引 - 使用 GitHub Actions 部署 .NET Aspire 至 Azure](https://agramont.net/blog/devops-intro-deploy-net-aspire-azure-github-actions/)
- [27] [[Microsoft 官方] 案例研究 - Azure Developer CLI (azd) 於真實企業級部署之實踐](https://devblogs.microsoft.com/azure-sdk/azure-developer-cli-azd-in-a-real-life-scenario/)
- [28] [[Agramont.net 部落格] 技術指引 - 使用 GitHub Actions 部署 .NET Aspire 至 Azure](https://agramont.net/blog/devops-intro-deploy-net-aspire-azure-github-actions/)
- [29] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [30] [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [31] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [32] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [33] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [34] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [35] [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [36] [[C# Corner] 技術實作 - 基於 .NET Aspire 的端對端 (E2E) 整合測試架構與實踐](https://www.c-sharpcorner.com/article/end-to-end-integration-testing-with-net-aspire/)
- [37] [[Microsoft 官方] 架構文件 - 於 CI/CD 流水線中執行 .NET Aspire 整合測試之架構建議](https://aspire.dev/testing/testing-in-ci/)
- [38] [[Agramont.net 部落格] 技術指引 - 使用 GitHub Actions 部署 .NET Aspire 至 Azure](https://agramont.net/blog/devops-intro-deploy-net-aspire-azure-github-actions/)
- [39] [[Microsoft GitHub] 故障排除 - Azure DevOps 中 azd 部署安全參數未定義錯誤排查](https://github.com/microsoft/aspire/issues/7301)

### 7. 企業導入實務：授權模式、安全考量、觀測整合、與既有 Docker/Podman 環境的共存策略

這是一份針對企業導入 **.NET Aspire (現已更名為 Aspire [1])** 作為分散式應用程式編排平台時，在授權合規、安全防禦、觀測整合以及既有 container 環境共存等四大核心領域的技術 advisory 報告。

---

### 一、 授權模式與安全通訊架構

#### 1. 核心元件調度平台（DCP）的授權演進
在過去的評估中，企業常對於 Aspire 核心的 **DCP (Developer Control Plane)** 抱有授權疑慮，因為它最初是作為微軟內部的私有且專有授權組件運作 [2, 3]。
*   **MIT 授權化**：自 **2026 年 1 月 23 日起**，微軟已正式將 DCP 程式碼完全開源並採用 **MIT 授權** [2]。這意味著 Aspire 整個技術堆疊從編排、儀表板到核心調度引擎，均已達成 100% 的免費與開源（OSS）[4]，企業無須擔心任何核心技術被專有授權鎖定（Vendor Lock-in）或合規風險。

#### 2. 本機與獨立運行安全架構（Authentication & Transport Security）
*   **本機安全權杖（Browser Token）**：自 Aspire 13.x 起，本地開發時儀表板預設強制啟用安全驗證。每次啟動時都會在終端機輸出動態生成的唯一的安全性權杖（`?t=<token>`），未提供權杖的存取將直接被拒絕（401 Unauthorized） [5, 6]。
*   **獨立部署下的 OpenID Connect (OIDC) 整合**：若企業將 Aspire Dashboard 作為共享開發或測試環境的獨立監控面板時，絕不能關閉安全驗證 [7]。此時可透過配置 `Dashboard:Frontend:AuthMode` 為 `OpenIdConnect`，將其與企業內部的 **IDP（如 Entra ID、Keycloak 或其他 OIDC 認證中心）** 進行對接，實施 SSO 單一登入，並精細控制使用者 Claim 存取權限 [8]。
*   **OTLP 端點安全防護**：Telemetry 數據的收集端點（gRPC 與 HTTP）可以透過 `Dashboard:Otlp:AuthMode` 配置為 **API Key**（透過標頭 `x-otlp-api-key` 傳遞，建議大於 128 bit 的高熵密鑰 [9]）或 **Client Certificate（用戶端憑證雙向 TLS / mTLS）** 認證模式，以確保僅有授權的微服務或邊車（Sidecar）能向監控中心發送遙測數據 [10, 11]。

---

### 二、 企業級安全考量與機敏資訊防護

#### 1. 機敏資訊流動與遮蔽（Secret Management）
*   **本機機密防護**：**絕對不要在 `AppHost.cs` 的 C# 程式碼中寫入任何真實秘密（如密碼或 API 密鑰）** [12]。本地開發時，應使用 .NET 的 **User Secrets 機制（`dotnet user-secrets set`）**，並在 AppHost 中以 `secret: true` 宣告參數 [12, 13]：
    ```csharp
    var dbPassword = builder.AddParameter("database-password", secret: true);
    ```
    此宣告會自動確保該密碼在 Aspire 儀表板、環境變數監視器及系統日誌中被安全遮蔽 [13-15]。
*   **生產環境秘密注入（Key Vaults & External Providers）**：在生成發布資訊清單（Manifest）或打包部署時，Aspire 的參數與連線機制會自動轉換為 **安全參數預留位置（Secure Placeholders）**，並與目標環境（如 AKS Secrets, Azure Key Vault, HashiCorp Vault）進行無縫接合，在 runtime 時期動態注入，杜絕設定漂移與金鑰外洩風險 [7, 12, 16]。
*   **PII（個人識別資訊）遮蔽**：在將 Logs 導出至本地或生產環境 OTel Collector 時，應在微服務底層或收集器的 Transform Processor 階段針對常見機敏個資實施遮蔽 [7]。

#### 2. 容器與主機間的憑證信任問題（HTTPS Dev Certificates）
*   **信任鏈斷裂痛點**：在舊版 Aspire 中，當微服務被容器化，而 telemetry 收集端點（AppHost）運行在 Host 機器的 HTTPS 環境下，容器常會因為無法信任 Host 的「ASP.NET Core 開發自簽憑證」而導致 OTLP Telemetry 傳輸因 SSL 握手失敗而中斷 [17-19]。
*   **.NET 10 / Aspire 13.x 自動信任機制**：新版平台引入了 **自動信任（Automatic Certificate Trust）協定** [20]。現在當 Aspire 啟動容器資源時，會自動將 Host 機器的開發憑證掛載並匯入至運行中的容器作業系統中（不論是 Docker 還是 Podman），自動完成加密通道驗證，實現本地 mTLS 零配置體驗 [20]。

---

### 三、 生產環境觀測整合與 Telemetry 數據分發

#### 1. ServiceDefaults 的關鍵標準化定位
每個 Aspire 模板自帶的 `ServiceDefaults` 專案不應被刪除 [21, 22]。它透過 `AddServiceDefaults()` 封裝了 **可觀測性（OpenTelemetry 日誌、追蹤、指標標準規範）、健康檢查、服務發現與外呼 HttpClient Polly 彈性政策** [22-25]。

#### 2. 本地開發儀表板 vs 生產遙測架構
*   **開發儀表板的效能開銷**：Aspire Dashboard 的內建 OTLP 接收器是為「本地開發與單機調試」設計的 [26-28]。其內建 OTLP 模組是以單一進程、全記憶體（RAM）內存運作，約會佔用 50-100MB 記憶體，且不具備分發（Fan-out）與大吞吐量 OLAP 數據庫持久化儲存能力，**嚴禁將開發儀表板直接部署為生產遙測收集器** [26, 29-31]。
*   **企業級 Telemetry 分發架構（Grafana, InfluxDB, ElasticSearch）**：
    微軟官方的設計指導原則中，明確拒絕將 Aspire Dashboard 開發為生產遙測分發中心 [29, 30]。當企業需要導入 **Prometheus / Grafana、InfluxDB 或 ElasticSearch** 作為監控標準時，標準架構應為「**微服務 \\(\to\\) OpenTelemetry Collector \\(\to\\) 數據分析數據庫**」 [29, 31]。
*   **無痛切換 OTLP Exporter 端點（The OTLP Override Knob）**：
    企業無須更改微服務內的任何 SDK 程式碼 [27, 32, 33]。只需在 `AppHost.cs` 或 CI/CD 配置中，將服務的 `OTEL_EXPORTER_OTLP_ENDPOINT` 環境變數覆蓋，改為指向生產環境的 **OTel Collector 容器**，即可實現數據的集中管理 [27, 32, 34, 35]。OTel Collector 隨後會負責透過其 Exporters 將遙測數據分發（Fan-out）至對應的監控平台 [29, 31]。

---

### 四、 與既有 Docker/Podman 環境的共存與遷移策略

#### 1. Docker 與 Podman 雙運行時（Runtime）共存
*   **自動偵測與手動覆蓋**：Aspire 原生支援 **Docker Desktop** 與 **Podman** 兩大容器引擎 [36-38]。當偵測到兩者皆存在時，預設優先呼叫 Docker [39]。
*   **Podman 優先配置**：在對 Docker Desktop 商業授權敏感的大型企業環境中 [38, 40]，開發人員可透過系統/用戶環境變數設定 [39]：
    ```powershell
    [System.Environment]::SetEnvironmentVariable("DOTNET_ASPIRE_CONTAINER_RUNTIME", "podman", "User")
    ```
    DCP 將自動引導所有基礎設施容器（如 Postgres, Redis）透過 Podman 調度運行 [39, 41]。
*   **網路對應防護與 Host 解析**：
    *   **容器到容器通訊**：Aspire 內建虛擬 Bridge 網路，容器資源間預設可流暢互通 [42]。
    *   **容器到主機（Container-to-Host）**：當容器中的服務（如資料庫、Dapr 邊車）需要反向調用本機以原生進程運行的專案時，需要呼叫：
        *   **Docker 運行時**：對應為 `host.docker.internal` [43]。
        *   **Podman 運行時**：對應為 `host.containers.internal` [43, 44]。
    *   **本地自訂網域分享（Custom Local Domains）**：當 Host 機器的 `hosts` 檔案配置了 `api.myapp.localhost` 時，Aspire 支援在啟動容器時自動傳遞 `--add-host` 參數，使容器內得以直接解析 Host 自訂網域 [45]。

#### 2. 既有 Docker Compose 專案的漸進式共存與遷移
許多企業面臨大量既存 Docker Compose 設定檔，無法在短期內全面改寫為 C# AppHost 編排。Aspire 提供兩條極佳的過渡與共存道路：

##### 策略 A：漸進式共存（Custom Resources 引入外部容器）
若現存容器不希望被 Aspire 的生命週期生命控制（例如：不希望在停止偵錯時被強制銷毀，或者它本身就是一個共享的持久化 Docker Compose 實例）[46, 47]，可利用 **Custom Resources API (`AddExternalContainer`)** [46]：
```csharp
builder.AddExternalContainer("my-shared-db", "compose-mongodb-1");
```
此方法允許 Aspire 在不接管該容器生命週期的前提下，**將該外部容器的即時狀態、健康端點、以及標準 `docker logs` 輸出直接橋接（Pipe）並同步顯示在 Aspire 儀表板中** [46, 47]，完美解決既存系統的可觀測性零碎化問題。

##### 策略 B：Docker Compose 發布器（Docker Compose Publisher）
*   **本地 C# 編排 \\(\to\\) 生產 Compose 部署**：這解決了企業「**只想在本機享受 C# 強大編排，但生產環境仍堅持使用傳統 Docker Compose 在 Linux VPS 部署**」的經典矛盾 [48-50]。
*   **一鍵生成 Production 部署資產**：
    自 Aspire 9.2 起，微軟將著名的開源工具 `Aspir8` 的部分核心架構與作者 David Sekula 合作直接整合至 SDK [51]，提供 Compose Publisher 機制 [51, 52]：
    1. 在 `AppHost` 中宣告：`builder.AddDockerComposeEnvironment("dc");` [53, 54]。
    2. 本地透過 **Aspire CLI** 執行 [55, 56]：
       ```bash
       aspire publish -o docker-compose-artifacts
       ```
    3. **輸出內容**：Aspire 將會自動分析您的 C# 架構與依賴關係，在輸出目錄生成一份標準、參數化且高安全性的 **`docker-compose.yml`** 與對應的 **`.env`** 密鑰管理檔案 [55, 57, 58]。此 Compose 檔案已配置好：
       * 基礎設施鏡像版本與 depends_on 依賴啟動順序 [50, 57]。
       * 將敏感秘密抽離，使用動態環境變數佔位符 [16, 50, 55, 57]。
       * 自動封裝好內部服務發現與連接字串配置 [50, 57]。
    
    這使運維團隊可直接透過簡單的 `scp` 將打包資產複製到目標 Linux 主機，並執行最熟悉的 `docker compose up -d` 命令完成上線，達成無痛過渡 [50]。

---

💡 想要深入了解如何將上述生成的 Docker Compose 部署資產與企業內部的 GitLab CI/CD 或 GitHub Actions 流水線進行端到端自動化整合嗎？我們可以接著探討自動化建置與映像檔推送的實務配置。

**引用來源：**
- [1] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [2] [[Reddit 社群] 討論分析 - .NET Aspire Developer Control Plane (DCP) 機制剖析](https://www.reddit.com/r/dotnet/comments/1sfv650/aspire_developer_control_plane/)
- [3] [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [4] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [5] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [6] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [7] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [8] [[Microsoft GitHub] 架構文件 - .NET Aspire Dashboard 原始碼結構、架構與構建說明](https://github.com/dotnet/aspire/blob/main/src/Aspire.Dashboard/README.md)
- [9] [[Microsoft GitHub] 架構文件 - .NET Aspire Dashboard 原始碼結構、架構與構建說明](https://github.com/dotnet/aspire/blob/main/src/Aspire.Dashboard/README.md)
- [10] [[Microsoft GitHub] 架構文件 - .NET Aspire Dashboard 原始碼結構、架構與構建說明](https://github.com/dotnet/aspire/blob/main/src/Aspire.Dashboard/README.md)
- [11] [[Microsoft GitHub] 架構文件 - .NET Aspire Dashboard 原始碼結構、架構與構建說明](https://github.com/dotnet/aspire/blob/main/src/Aspire.Dashboard/README.md)
- [12] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [13] [[Microsoft 官方] 技術文件 - .NET Aspire 各個整合服務之間的端到端安全通訊架構](https://aspire.dev/integrations/custom-integrations/secure-communication/)
- [14] [[Microsoft 官方] 技術文件 - .NET Aspire 各個整合服務之間的端到端安全通訊架構](https://aspire.dev/integrations/custom-integrations/secure-communication/)
- [15] [[Microsoft 官方] 技術文件 - .NET Aspire 各個整合服務之間的端到端安全通訊架構](https://aspire.dev/integrations/custom-integrations/secure-communication/)
- [16] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [17] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [18] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [19] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [20] [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [21] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [22] [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [23] [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [24] [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [25] [[GitHub 範例] 程式碼實作 - NetAspire ServiceDefaults 擴充方法之實作原始碼](https://github.com/gncyyldz/NetAspire.Example/blob/master/NetAspire.Example.ServiceDefaults/Extensions.cs)
- [26] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [27] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [28] [[Microsoft 官方] 技術指引 - 如何獨立運行 (Standalone) .NET Aspire 觀測儀表板](https://aspire.dev/dashboard/standalone/)
- [29] [[Microsoft GitHub] 技術討論 - 將 .NET Aspire 觀測數據導出至 Grafana 與 InfluxDB 的技術方案](https://github.com/microsoft/aspire/issues/9861)
- [30] [[Microsoft GitHub] 技術討論 - 將 .NET Aspire 觀測數據導出至 Grafana 與 InfluxDB 的技術方案](https://github.com/microsoft/aspire/issues/9861)
- [31] [[Reddit 社群] 技術探討 - 提取 .NET Aspire 遙測數據並匯出為 CSV 之實作方法](https://www.reddit.com/r/dotnet/comments/1oqpm21/exporting_net_aspire_telemetry_traces_logs/)
- [32] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [33] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [34] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [35] [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [36] [[Kalle Marjokorpi 部落格] 案例研究 - .NET Aspire 對企業微服務技術選型與架構之深遠影響](https://www.kallemarjokorpi.fi/blog/how-net-aspire-changed-my-way-of-building-software/)
- [37] [[Microsoft 官方] 步驟指南 - .NET Aspire SDK 本機開發環境安裝與設定](https://dotnet-aspire-52.mintlify.app/installation)
- [38] [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)
- [39] [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)
- [40] [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [41] [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)
- [42] [[Microsoft GitHub] 故障排除 - 使用 Podman 時容器無法連線至 Aspire 主機服務之解決方案](https://github.com/microsoft/aspire/issues/4136)
- [43] [[Microsoft 官方] 技術指引 - 讓 Aspire 容器能夠解析主機之自訂本機網域設定](https://anthonysimmon.com/dotnet-aspire-containers-share-custom-hosts/)
- [44] [[Microsoft GitHub] 故障排除 - 本機容器連線至主機 Aspire 專案資源的網路對應](https://github.com/microsoft/aspire/discussions/1808)
- [45] [[Microsoft 官方] 技術指引 - 讓 Aspire 容器能夠解析主機之自訂本機網域設定](https://anthonysimmon.com/dotnet-aspire-containers-share-custom-hosts/)
- [46] [[Microsoft 官方] 技術指引 - 透過 Custom Resources API 於 Aspire 中引用既有 Docker 容器](https://anthonysimmon.com/referencing-external-docker-containers-dotnet-aspire-custom-resources/)
- [47] [[Microsoft 官方] 技術指引 - 透過 Custom Resources API 於 Aspire 中引用既有 Docker 容器](https://anthonysimmon.com/referencing-external-docker-containers-dotnet-aspire-custom-resources/)
- [48] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [49] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [50] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [51] [[InfoQ 媒體] 技術新聞 - .NET Aspire 9.2 部署功能擴展與儀表板優化](https://www.infoq.com/news/2025/04/dotnet-aspire-92-release/)
- [52] [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [53] [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [54] [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [55] [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [56] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [57] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [58] [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)

## 已移除的來源（品質過濾）

- Aspire - Awesome Software Architecture — 屬於缺乏實質技術深度的目錄頁。該來源僅是 GitHub Awesome-list 的 Markdown 連結彙整，整篇只列出外部文章、影片、工具和範例專案的標題與連結，並無任何具體的技術實作細節、架構分析或技術深度內容。
- Deploy .NET Aspire Apps to Azure in Minutes | Simple Deployment Tutorial - YouTube — 屬於缺乏實質技術深度的頁面。該來源在匯入時因爬蟲解析問題，並未擷取到影片的實際語音逐字稿或實質技術內容，整篇內容充斥著 YouTube 介面文字、播放控制選項以及無關的推薦影片清單（例如 Gamers Nexus 影片等），對技術研究毫無實質貢獻。
- Podman for .NET Developers: A Beginner-Friendly Docker Alternative - YouTube — 屬於缺乏實質技術深度的頁面。同為 YouTube 頁面爬取失敗的案例，其內容僅包含 YouTube 系統錯誤提示、分享控制資訊及其他不相關的推薦影片標題（例如 Dolly Parton 的趣味鏡頭），缺乏任何針對 Podman 或 .NET 容器化整合的具體實作或語音逐字稿細節。
- Developer Control Pane - Source/License Clarification · microsoft aspire · Discussion #1913 — 屬於過期資訊。此討論串主要針對 2023 年 .NET Aspire 早期預覽版時，微軟 Developer Control Plane (DCP) 仍為閉源/專有授權且合約禁止反組譯的爭議進行討論與抱怨。然而，微軟已於 2026 年 1 月將 DCP 專案正式以 MIT 授權開源，使此篇關於授權爭議與合約限制的質疑討論徹底過時，已被後續更新的開源現況所取代。

## 已移除的來源（蒸餾必要性）

- GitHub Actions CI/CD Pipeline for Deploying .NET Web API to Amazon ECS — 該來源詳細介紹如何將傳統的 .NET Web API 專案部署至 AWS ECS 平台，其容器建置、ECR 推送以及 ECS Task Definition 部署完全採用常規的 GitHub Actions 與 Dockerfile 流程。文中並無提及或採用任何與 .NET Aspire 相關的應用程式模型（AppHost）、編排、本機發布指令（aspire publish/deploy）或與既有 DevOps 的整合，對回答 .NET Aspire 的 7 個核心問題毫無技術貢獻。
- Agents/samples/dotnet/otel/README.md at main · microsoft/Agents - GitHub — 此來源為 Microsoft 365 Agents SDK 的範例說明檔，重點在於展示 Teams Agent 的訊息發送、JWT Token 驗證與 OpenTelemetry 基礎設定。雖然文檔提到可以在 Docker 中執行 standalone .NET Aspire Dashboard 容器來視覺化其觀測數據，但專案本身並未使用 .NET Aspire AppHost 進行分散式系統編排、服務發現或容器運行整合，亦不涉及 Aspire 9 或多語言整合邊界等核心議題，屬於離題範例。
- OpenTelemetry - Microsoft Learn — 此來源為 Microsoft Teams SDK 的 OTel 整合開發文檔（來源自 teams-ai-library GitHub），詳細說明 Bot / Agent 的 Tracer 與 Meter 儀器化程式碼實作。文中僅提及將 standalone Aspire Dashboard 視為本地端 OTLP 的多個收集器選項之一，完全偏離 .NET Aspire 作為 Distributed Application Platform 的架構、機制、多語言整合及 CI/CD 部署路徑研究，屬於無關的 SDK 操作說明。

## 參考來源清單

- [[Agramont.net 部落格] 技術指引 - 使用 GitHub Actions 部署 .NET Aspire 至 Azure](https://agramont.net/blog/devops-intro-deploy-net-aspire-azure-github-actions/)
- [[C# Corner] 技術實作 - 基於 .NET Aspire 的端對端 (E2E) 整合測試架構與實踐](https://www.c-sharpcorner.com/article/end-to-end-integration-testing-with-net-aspire/)
- [[Chris Ayers 部落格] 技術實作 - 使用 Aspire CLI 與 azd 建立部署流水線](https://chris-ayers.com/posts/aspire-cli-part-2/)
- [[Context7 部落格] 架構分析 - .NET Aspire 平台與 Orchestration 核心元件拆解](https://context7.com/websites/aspire_dev)
- [[DEV Community] 技術文章 - .NET Aspire 於 WSL2 與 Docker 環境下的整合設定](https://dev.to/glsolaria/net-aspire-and-wsl-docker-21k4)
- [[DEV Community] 技術文章 - ASP.NET Core 結合 .NET Aspire 啟用 OpenTelemetry 實作](https://dev.to/edcsu/opentelemetry-in-asp-net-with-aspire-4206)
- [[Dan Does Code 部落格] 技術實作 - 整合 AddEFMigrations 於 .NET Aspire 中管理資料庫移轉](https://www.dandoescode.com/blog/ef-core-migrations-in-aspire-with-addefmigrations)
- [[Dave Brock 部落格] 技術文章 - .NET Aspire 3 Service Defaults 設定機制](https://www.daveabrock.com/2025/08/13/net-aspire-3-service-defaults/)
- [[GitHub 範例] 程式碼實作 - NetAspire ServiceDefaults 擴充方法之實作原始碼](https://github.com/gncyyldz/NetAspire.Example/blob/master/NetAspire.Example.ServiceDefaults/Extensions.cs)
- [[GitHub 開源專案] 技術實作 - 使用 MSBuild 內建發布工具封裝 SQL Database 為容器鏡像](https://erikej.github.io/sqlclient/dotnet/dacfx/2026/04/09/dacfx-container-publish.html)
- [[GitHub 開源專案] 技術工具 - aspirational-manifests：解析與部署 AppHost 資訊清單](https://github.com/prom3theu5/aspirational-manifests)
- [[InfoQ 媒體] 技術新聞 - .NET Aspire 9.2 部署功能擴展與儀表板優化](https://www.infoq.com/news/2025/04/dotnet-aspire-92-release/)
- [[InfoQ 媒體] 技術新聞 - .NET Aspire 9.4 CLI 正式版與互動式儀表板新功能](https://www.infoq.com/news/2025/08/dotnet-aspire-9-4-release/)
- [[JetBrains 官方] 產品功能 - 在 JetBrains Rider 中使用 .NET Aspire 插件進行開發](https://blog.jetbrains.com/dotnet/2024/02/19/jetbrains-rider-and-the-net-aspire-plugin/)
- [[Kalle Marjokorpi 部落格] 案例研究 - .NET Aspire 對企業微服務技術選型與架構之深遠影響](https://www.kallemarjokorpi.fi/blog/how-net-aspire-changed-my-way-of-building-software/)
- [[Matt Kotsenas 部落格] 技術實務 - 在沙箱與受限權限環境中執行 .NET Aspire](https://matt.kotsenas.com/posts/aspire-in-a-sandbox/)
- [[Microsoft GitHub] 技術指引 - Azure SQL Database 本地模擬器容器之運作機制與限制](https://github.com/microsoft/azure-sql-database-container/blob/main/docs/what-is-the-container.md)
- [[Microsoft GitHub] 技術討論 - .NET Aspire 專案整合 Azure DevOps CI/CD 流水線設計](https://github.com/microsoft/aspire-samples/issues/1148)
- [[Microsoft GitHub] 技術討論 - 將 .NET Aspire 觀測數據導出至 Grafana 與 InfluxDB 的技術方案](https://github.com/microsoft/aspire/issues/9861)
- [[Microsoft GitHub] 故障排除 - Azure DevOps Agent 執行 Aspire 整合測試失敗排除](https://github.com/microsoft/aspire/discussions/5051)
- [[Microsoft GitHub] 故障排除 - Azure DevOps 中 azd 部署安全參數未定義錯誤排查](https://github.com/microsoft/aspire/issues/7301)
- [[Microsoft GitHub] 故障排除 - 使用 Podman 時 Container-to-Host 網路連線故障處理](https://github.com/microsoft/aspire/issues/6846)
- [[Microsoft GitHub] 故障排除 - 使用 Podman 時容器無法連線至 Aspire 主機服務之解決方案](https://github.com/microsoft/aspire/issues/4136)
- [[Microsoft GitHub] 故障排除 - 容器 telemetry 數據未正確顯示於 AppHost 儀表板排查](https://github.com/microsoft/aspire/issues/4131)
- [[Microsoft GitHub] 故障排除 - 本機容器連線至主機 Aspire 專案資源的網路對應](https://github.com/microsoft/aspire/discussions/1808)
- [[Microsoft GitHub] 故障排除 - 自建 WSL2 Docker 引擎在 Aspire 9.1 中的網絡迴歸問題](https://github.com/microsoft/aspire/issues/8692)
- [[Microsoft GitHub] 故障排除 - 自訂 Docker Host 設定在 .NET Aspire 中無效的問題排解](https://github.com/microsoft/aspire/issues/1650)
- [[Microsoft GitHub] 架構文件 - .NET Aspire Dashboard 原始碼結構、架構與構建說明](https://github.com/dotnet/aspire/blob/main/src/Aspire.Dashboard/README.md)
- [[Microsoft GitHub] 版本日誌 - .NET Aspire 13.3 變更說明與 API 調整](https://github.com/microsoft/aspire/wiki/13.3-Change-log)
- [[Microsoft GitHub] 議題討論 - .NET Aspire Java 託管整合之技術提案與限制](https://github.com/microsoft/aspire/issues/17993)
- [[Microsoft 官方] 套件規格 - Aspire.Hosting.Kubernetes 套件 API 說明文件](https://aspire.dev/reference/api/csharp/aspire.hosting.kubernetes/)
- [[Microsoft 官方] 套件規格 - KubernetesEnvironmentResource API 設計與資源對應規格](https://aspire.dev/reference/api/typescript/aspire.hosting.kubernetes/kubernetesenvironmentresource/)
- [[Microsoft 官方] 技術公告 - .NET Aspire 9.2 發布與多樣化部署路徑解密](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)
- [[Microsoft 官方] 技術公告 - .NET Aspire 9.3 發布與 GitHub Copilot 開發整合說明](https://devblogs.microsoft.com/dotnet/introducing-dotnet-aspire-93/)
- [[Microsoft 官方] 技術公告 - .NET Aspire 9.5 新特性與效能改進](https://devblogs.microsoft.com/dotnet/announcing-dotnet-aspire-95/)
- [[Microsoft 官方] 技術引導 - .NET Aspire 專案單元與整合測試起步指南](https://devblogs.microsoft.com/dotnet/getting-started-with-testing-and-dotnet-aspire/)
- [[Microsoft 官方] 技術指南 - 使用 .NET SDK 內建容器化功能打包應用程式](https://milanjovanovic.tech/blog/containerize-your-dotnet-applications-without-a-dockerfile)
- [[Microsoft 官方] 技術指引 - 使用 .NET Aspire 建置自訂發布與部署流水線](https://aspire.dev/deployment/custom-deployments/)
- [[Microsoft 官方] 技術指引 - 在 .NET Aspire AppHost 儀表板中實作自訂 HTTP 命令](https://aspire.dev/fundamentals/http-commands/)
- [[Microsoft 官方] 技術指引 - 如何獨立運行 (Standalone) .NET Aspire 觀測儀表板](https://aspire.dev/dashboard/standalone/)
- [[Microsoft 官方] 技術指引 - 整合既有 Azure Developer CLI (azd) 流水線至 Aspire 專案](https://aspire.dev/deployment/azure/azure-developer-cli/)
- [[Microsoft 官方] 技術指引 - 讓 Aspire 容器能夠解析主機之自訂本機網域設定](https://anthonysimmon.com/dotnet-aspire-containers-share-custom-hosts/)
- [[Microsoft 官方] 技術指引 - 透過 Custom Resources API 於 Aspire 中引用既有 Docker 容器](https://anthonysimmon.com/referencing-external-docker-containers-dotnet-aspire-custom-resources/)
- [[Microsoft 官方] 技術文件 - .NET Aspire 各個整合服務之間的端到端安全通訊架構](https://aspire.dev/integrations/custom-integrations/secure-communication/)
- [[Microsoft 官方] 技術文件 - .NET Aspire 的 Kubernetes 元件與原生集成規格](https://aspire.dev/integrations/compute/kubernetes/)
- [[Microsoft 官方] 技術文件 - .NET Aspire 開發環境下整合與執行 Python 服務](https://aspire.dev/integrations/frameworks/python/)
- [[Microsoft 官方] 技術文件 - C# 專案中 Service Defaults 的預設功能與擴充設定](https://aspire.dev/get-started/csharp-service-defaults/)
- [[Microsoft 官方] 技術文件 - 在 .NET Aspire 中自訂與定義資源的對外 URL](https://aspire.dev/fundamentals/custom-resource-urls/)
- [[Microsoft 官方] 技術文件 - 在 .NET Aspire 之中託管與執行外部原生執行檔](https://aspire.dev/app-host/executable-resources/)
- [[Microsoft 官方] 技術文件 - 用於建置分散式應用程式的 .NET Aspire SDK 規格](https://aspire.dev/get-started/aspire-sdk/)
- [[Microsoft 官方] 架構文件 - .NET Aspire 企業級 CI/CD 持續整合與部署策略概覽](https://aspire.dev/deployment/ci-cd/)
- [[Microsoft 官方] 架構文件 - .NET Aspire 內部部署機制與 Manifest 設計原理](https://aspire.dev/deployment/deploy-with-aspire/)
- [[Microsoft 官方] 架構文件 - .NET Aspire 發布資訊清單與多路徑部署模型概覽](https://dotnet-aspire-52.mintlify.app/deployment/overview)
- [[Microsoft 官方] 架構文件 - Azure Application Insights 與 OpenTelemetry 觀測整合](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview)
- [[Microsoft 官方] 架構文件 - 於 CI/CD 流水線中執行 .NET Aspire 整合測試之架構建議](https://aspire.dev/testing/testing-in-ci/)
- [[Microsoft 官方] 架構文件 - 深入分析 .NET Aspire AppHost 核心機制與專案生命週期](https://aspire.dev/get-started/app-host/)
- [[Microsoft 官方] 架構文件 - 深入理解作為 .NET Aspire 核心的 Developer Control Plane](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
- [[Microsoft 官方] 案例研究 - Azure Developer CLI (azd) 於真實企業級部署之實踐](https://devblogs.microsoft.com/azure-sdk/azure-developer-cli-azd-in-a-real-life-scenario/)
- [[Microsoft 官方] 步驟指南 - .NET Aspire SDK 本機開發環境安裝與設定](https://dotnet-aspire-52.mintlify.app/installation)
- [[Microsoft 官方] 步驟指南 - 使用 .NET Aspire 整合與管理 Go 語言微服務](https://aspire.dev/integrations/frameworks/go/go-get-started/)
- [[Microsoft 官方] 步驟指南 - 使用 .NET Aspire 開發與整合 Java 應用程式](https://aspire.dev/integrations/frameworks/java/java-get-started/)
- [[Microsoft 官方] 步驟指南 - 使用 azd 將 .NET Aspire 專案部署至 Azure 平台](https://aspire.dev/deployment/azure/)
- [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 專案發布並部署至 Kubernetes 集群](https://aspire.dev/deployment/kubernetes/)
- [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 應用發布並生成 Docker Compose YAML](https://aspire.dev/deployment/docker-compose/)
- [[Microsoft 官方] 步驟指南 - 將 .NET Aspire 整合至既有 ASP.NET Core 專案](https://aspire.dev/get-started/add-aspire-existing-app/)
- [[Microsoft 官方] 步驟指南 - 從零開始建立第一個 .NET Aspire 分散式應用程式](https://dotnet-aspire-52.mintlify.app/quickstart)
- [[Microsoft 官方] 產品介紹 - .NET Aspire 分散式系統編排平台之核心定位與願景](https://aspire.dev/get-started/what-is-aspire/)
- [[Microsoft 官方] 開發指引 - 使用 VS Code Docker 工具偵錯本機 .NET 容器化應用](https://devblogs.microsoft.com/dotnet/debugging-dotnet-containers-with-visual-studio-code-docker-tools/)
- [[Microsoft 官方] 開發教學 - Kubernetes 環境下 .NET Aspire 部署與資訊清單映射細節](https://learn.microsoft.com/en-us/shows/azure-developers-dotnet-aspire-day-2024/deploying-dotnet-aspire-projects-to-kubernetes)
- [[Microsoft 官方] 開發教學 - 免 Dockerfile 利用 dotnet publish 容器化 .NET 10 應用](https://codewithmukesh.com/blog/containerize-dotnet-without-dockerfile/)
- [[Microsoft 官方] 開發教學 - 深入解析 dotnet publish 容器化打包技術之參數與規格](https://learn.microsoft.com/en-us/dotnet/core/containers/sdk-publish)
- [[Milan Jovanović 部落格] 技術指引 - 透過 .NET Aspire 生成本地 Docker Compose 部署](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)
- [[NashTech 部落格] 技術指引 - .NET Aspire 整合 Next.js 與 Docker 開發實務](https://blog.nashtechglobal.com/aspire-from-zero-to-production-building-your-first-distributed-app-with-next-js-docker-and-net-10/)
- [[Packt 部落格] 開發教學 - 從零建置生產就緒的 .NET Aspire 應用程式](https://juliocasal.com/blog/net-aspire-tutorial-build-production-ready-apps-from-day-1)
- [[Particular Software] 架構分析 - 藉由 .NET Aspire 簡化企業級分散式系統之設計與開發](https://particular.net/webinars/effortless-distributed-systems-with-aspire)
- [[RaspeR87 部落格] 技術指引 - .NET Aspire 自訂發布與部署流水線實作](https://rasper87.blog/2025/11/05/net-aspire-custom-publish-deployment-pipelines/)
- [[Red Hat 部落格] 案例研究 - 整合 .NET Aspire、Dapr 與 Podman 簡化微服務本機開發](https://dev.to/rineshpk/simplifying-microservice-development-with-net-aspire-dapr-and-podman-3hp0)
- [[Reddit 社群] 技術探討 - 提取 .NET Aspire 遙測數據並匯出為 CSV 之實作方法](https://www.reddit.com/r/dotnet/comments/1oqpm21/exporting_net_aspire_telemetry_traces_logs/)
- [[Reddit 社群] 案例研究 - 從開發容器移轉至生產環境託管的實務架構討論](https://www.reddit.com/r/dotnet/comments/1kxau0s/aspire_is_amazing_how_to_go_from_dev_containers/)
- [[Reddit 社群] 比較分析 - 本機開發情境下 .NET Aspire 與 Docker Compose 功能對比](https://www.reddit.com/r/dotnet/comments/1i5ux36/docker_compose_vs_net_aspire/)
- [[Reddit 社群] 討論分析 - .NET Aspire Developer Control Plane (DCP) 機制剖析](https://www.reddit.com/r/dotnet/comments/1sfv650/aspire_developer_control_plane/)
- [[Reddit 社群] 討論分析 - 本地開發環境中 .NET Aspire 為何不將專案容器化運作的設計考量](https://www.reddit.com/r/dotnet/comments/1fmrrn4/why_does_aspire_not_run_my_projects_in_a/)
- [[Stack Overflow] 技術問答 - 非 Azure 生產環境下保護 ASP.NET Core 機敏資訊與憑證的實作方法](https://www.reddit.com/r/dotnet/comments/947h2k/aspnet_core_how_to_store_or_protect_app_secrets/)
- [[base14 Scout 部落格] 技術實作 - .NET Aspire OpenTelemetry 多服務追蹤實務](https://docs.base14.io/instrument/apps/auto-instrumentation/dotnet-aspire/)
- [[codewithmukesh 部落格] 開發教學 - 使用 .NET 10 與 Aspire 建置雲端原生應用](https://codewithmukesh.com/blog/aspire-for-dotnet-developers-deep-dive/)
- [[iT 邦幫忙 部落格] 技術文章 - 利用 .NET Aspire 簡化與統一團隊本機開發環境之實踐](https://dev.to/baoduy2412/net-aspire-simplifying-local-development-environment-and-testing-an8)
- [[mstack 部落格] 技術指引 - 自訂 .NET Aspire 元件與整合測試開發指南](https://mstack.nl/blogs/wiremock-net-aspire-component/)
- [[nikiforovall 部落格] 技術實作 - 結合 .NET Aspire 與 Python 開發 RAG 檢索增強生成應用](https://nikiforovall.blog/dotnet/ai/2026/02/22/building-rag-with-aspire-and-python.html)
- [https://stackoverflow.com/questions/79415638/add-a-value-from-an-azure-devops-pipeline-library-variable-to-an-aspire-net-con](https://stackoverflow.com/questions/79415638/add-a-value-from-an-azure-devops-pipeline-library-variable-to-an-aspire-net-con)
- [https://stackoverflow.com/questions/79811383/how-come-azure-developer-cli-says-my-aspire-bicep-is-invalid-when-running-in-pip](https://stackoverflow.com/questions/79811383/how-come-azure-developer-cli-says-my-aspire-bicep-is-invalid-when-running-in-pip)
