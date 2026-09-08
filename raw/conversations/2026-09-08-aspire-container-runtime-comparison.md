---
title: ".NET Aspire 在 Windows 11 上不用 Docker Desktop 的可行方案比較"
source_kind: conversation
created: 2026-09-08
related_raw:
  - "raw/youtube/docker-vs-podman-why-podman-exists.md"
---

# .NET Aspire 在 Windows 11 上不用 Docker Desktop 的可行方案比較

> Cheer 與 Claude 的對話研究，延續 Docker Desktop 企業授權受限的脈絡。所有 URL 均經實際 WebFetch/WebSearch 驗證。

## 研究主題 1：.NET Aspire + Podman 在 Windows 11 上的可行性

- Aspire 官方支援 Podman 作為 container runtime（自 Aspire preview 4 起），設定方式：PowerShell `[System.Environment]::SetEnvironmentVariable("ASPIRE_CONTAINER_RUNTIME", "podman", "User")`
- 需求：安裝 Podman Desktop for Windows（Apache 2.0 授權，無企業授權限制）、`podman machine init` + `podman machine start`（WSL2-based VM，概念類似 Docker Desktop 的 WSL2 backend）
- 已知問題（官方 GitHub issue，狀態為 Backlog/未修）：
  - Issue #3517（https://github.com/dotnet/aspire/issues/3517）：Podman inspect 回傳的 `Entrypoint` 欄位是 JSON 陣列格式，但 Aspire 程式碼預期字串格式，導致 JSON deserialize 例外。實測案例是掛 RabbitMQ resource 時觸發。狀態：Backlog，未修。
  - Issue #6846（https://github.com/dotnet/aspire/issues/6846）：Podman 版本的 host DNS alias `host.containers.internal` 在某些情境下無法正確解析，實測案例是 Dapr sidecar 連線逾時（`host.containers.internal/169.254.1.2:38047`）。被標記為與 #6547 重複。
- 來源：https://aspire.dev/get-started/prerequisites/（官方前置需求頁，PowerShell 設定範例的原始出處）

## 研究主題 2：.NET Aspire + 裸 Docker Engine（不透過 Docker Desktop）在 WSL2 上

- 授權面完全沒問題：Docker Engine（`dockerd`）本身是 Apache 2.0 授權，任何規模商業使用都免費。Docker Desktop 的訂閱限制（員工數 >250 或年營收 >$1000萬 需付費 $9–24/user/月）只綁定在 Docker Desktop 這個 GUI/VM 產品上，不涉及 Docker Engine 本身。
- 但 Aspire 官方**只承認 Docker Desktop 和 Podman 兩種 runtime**，裸 Docker Engine 走 `DOCKER_HOST=tcp://<wsl-ip>:2375` 是社群摸索出來的**非官方支援**做法
- 官方態度：維護者 David Fowl 在討論串中直接回應「These are all the issues you'll run into」，並附上已知問題清單連結，等於承認此路能走但不保證品質（來源：https://github.com/dotnet/aspire/discussions/6813）
- 已知限制：
  - WSL 會因閒置自動關機，連線隨之斷掉
  - WSL 的 IP 位址會變動，`DOCKER_HOST` 設定不是一次性有效
  - 已確認的 regression（https://github.com/dotnet/aspire/issues/8692）：Aspire 9.1 下，對同一個 resource 呼叫兩次 `WithEnvironment()` 會讓 DCP（Distributed Control Plane）無法建立容器。此問題**只在裸 WSL Docker Engine 設定下出現**，Docker Desktop 不會中，Aspire 9.0 也不會中。狀態：**Closed as not planned**（官方明確表示不會修）

## 研究主題 3：.NET Aspire Docker Compose Publisher（官方正式支援，用於部署而非本地開發）

- Aspire 本地開發（`aspire run`）用的是自有的 orchestrator DCP，跟 docker-compose 無關
- 但 Aspire 9.2 起有正式支援的 Docker Compose Publisher：安裝 `Aspire.Hosting.Docker` NuGet package，用 `AddDockerComposeEnvironment` 啟用，執行 `aspire publish` 會產出 `docker-compose.yaml` + `.env`
- 這份產出檔案本身跟 Docker Desktop 無關，只要有任何吃 compose 格式的 runtime（標準 Docker Engine + `docker compose` plugin，或 Podman + `podman-compose`）就能跑
- 來源：https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/ 、 https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher

## Claude 結論/建議

- 本地開發（F5 debug）：建議用 **Podman**，因為它是官方正式支援管道（即使有已知 bug，至少在官方 backlog 追蹤範圍內），裸 WSL Docker Engine 是「非官方支援」且有一個已確認、官方明確表示不修的 regression，風險更高、且無修復承諾
- 若專案本身不用 RabbitMQ 或 Dapr sidecar，Podman 的兩個已知問題大機率不會踩到，可視實際用到的 resource 種類決定
- 部署/發布階段：建議用 Aspire 的 Docker Compose Publisher（9.2+ 正式功能），產出的 compose 檔案丟給合規、免費的 Docker Engine 執行，完全不需要 Docker Desktop，風險最低
- 使用者的核心動機：公司環境因 Docker Desktop 授權限制無法自由使用，持續在找替代方案（跟先前 ingest 過的 Docker vs Podman 影片、`wiki/concepts/legacy-code-modernization.md` 是同一條研究脈絡）
