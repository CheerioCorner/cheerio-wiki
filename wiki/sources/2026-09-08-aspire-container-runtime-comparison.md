---
title: ".NET Aspire 容器執行環境替代方案比較（Windows 11 免 Docker Desktop）"
type: source
created: 2026-09-08
updated: 2026-09-08
sources: 1
tags: [dotnet-aspire, docker, podman, container-runtime, docker-desktop, licensing, windows]
topics: [dotnet-aspire, container-infrastructure]
provenance:
  - kind: raw
    path: raw/conversations/2026-09-08-aspire-container-runtime-comparison.md
  - kind: external
    url: https://aspire.dev/get-started/prerequisites/
  - kind: external
    url: https://github.com/dotnet/aspire/issues/3517
  - kind: external
    url: https://github.com/dotnet/aspire/issues/6846
  - kind: external
    url: https://github.com/dotnet/aspire/discussions/6813
  - kind: external
    url: https://github.com/dotnet/aspire/issues/8692
  - kind: external
    url: https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/
  - kind: external
    url: https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher
---

# .NET Aspire 容器執行環境替代方案比較

> Cheer 與 Claude 的對話研究，聚焦 Windows 11 上使用 .NET Aspire 時如何繞過 Docker Desktop 企業授權限制。延續 [[wiki/sources/2026-09-05-docker-vs-podman|Docker vs Podman]] 與 [[wiki/sources/2026-09-06-dotnet-aspire-deep-research|.NET Aspire 深度研究]] 的脈絡。

## 三條路徑總覽

| 路徑 | 定位 | 官方支援 | 授權 |
|------|------|---------|------|
| Podman | 本地開發 container runtime | ✅ 官方（preview 4 起） | Apache 2.0 |
| 裸 Docker Engine（WSL2） | 本地開發 container runtime | ❌ 非官方（社群摸索） | Engine Apache 2.0 / Desktop 付費 |
| Docker Compose Publisher | 部署產物生成（非本地開發） | ✅ 官方（9.2+） | Apache 2.0 |

## 路徑 1：Podman（官方支援，本地開發首選）

- 設定方式：`[System.Environment]::SetEnvironmentVariable("ASPIRE_CONTAINER_RUNTIME", "podman", "User")`
- 需求：Podman Desktop for Windows + `podman machine init` + `podman machine start`（WSL2-based VM）
- 來源：[Aspire Prerequisites](https://aspire.dev/get-started/prerequisites/)

### 已知問題

- **Issue #3517**（[連結](https://github.com/dotnet/aspire/issues/3517)）：Podman inspect 回傳的 `Entrypoint` 欄位是 JSON 陣列格式，但 Aspire 程式碼預期字串格式，導致 JSON deserialize 例外。實測案例是掛 RabbitMQ resource 時觸發。狀態：**Backlog，未修**。
- **Issue #6846**（[連結](https://github.com/dotnet/aspire/issues/6846)）：Podman 版本的 host DNS alias `host.containers.internal` 在某些情境下無法正確解析，實測案例是 Dapr sidecar 連線逾時。被標記為與 #6547 重複。

### 適用場景

專案不用 RabbitMQ 或 Dapr sidecar 時，兩個已知問題大機率不會踩到。

## 路徑 2：裸 Docker Engine（非官方，風險最高）

- Docker Engine（`dockerd`）本身是 Apache 2.0 授權，任何規模商業使用都免費。Docker Desktop 的訂閱限制（員工數 >250 或年營收 >$1000萬 需付費 $9–24/user/月）只綁定在 Docker Desktop 這個 GUI/VM 產品上，不涉及 Engine 本身。
- Aspire 官方只承認 Docker Desktop 和 Podman 兩種 runtime，裸 Docker Engine 走 `DOCKER_HOST=tcp://<wsl-ip>:2375` 是社群摸索出來的非官方做法
- 官方態度：維護者 David Fowl 在討論串中直接回應「These are all the issues you'll run into」，等於承認此路能走但不保證品質（[Discussion #6813](https://github.com/dotnet/aspire/discussions/6813)）

### 已知限制

- WSL 會因閒置自動關機，連線隨之斷掉
- WSL 的 IP 位址會變動，`DOCKER_HOST` 設定不是一次性有效
- **Issue #8692**（[連結](https://github.com/dotnet/aspire/issues/8692)）：Aspire 9.1 下，對同一個 resource 呼叫兩次 `WithEnvironment()` 會讓 DCP 無法建立容器。此問題**只在裸 WSL Docker Engine 設定下出現**，Docker Desktop 不會中，Aspire 9.0 也不會中。狀態：**Closed as not planned**（官方明確表示不會修）

## 路徑 3：Docker Compose Publisher（部署用，非本地開發）

- Aspire 本地開發（`aspire run`）用的是自有的 orchestrator DCP，跟 docker-compose 無關
- Aspire 9.2+ 正式功能：安裝 `Aspire.Hosting.Docker` NuGet package，用 `AddDockerComposeEnvironment` 啟用，執行 `aspire publish` 產出 `docker-compose.yaml` + `.env`
- 產出檔案跟 Docker Desktop 無關，只要有任何吃 compose 格式的 runtime（Docker Engine + `docker compose` plugin，或 Podman + `podman-compose`）就能跑
- 來源：[Aspire 9.2 公告](https://devblogs.microsoft.com/dotnet/dotnet-aspire-92-is-now-available-with-new-ways-to-deploy/)、[Milan Jovanović 教學](https://milanjovanovic.tech/blog/using-dotnet-aspire-with-the-docker-publisher)

## 結論

- **本地開發（F5 debug）**：建議用 Podman——官方正式支援管道，即使有已知 bug，至少在官方 backlog 追蹤範圍內
- **部署/發布**：建議用 Docker Compose Publisher + 免費 Docker Engine，完全不需要 Docker Desktop
- **裸 WSL Docker Engine**：風險最高，有一個已確認、官方明確表示不修的 regression，只作為最後備案

## 來源

- [[raw/conversations/2026-09-08-aspire-container-runtime-comparison|對話原始記錄]]
- [[wiki/sources/2026-09-06-dotnet-aspire-deep-research|.NET Aspire 深度研究]]（90 筆來源，更廣泛的 Aspire 技術全景）
- [[wiki/sources/2026-09-05-docker-vs-podman|Docker vs Podman: Why Podman Exists]]（Docker 與 Podman 架構差異的基礎知識）

## 相關頁面

- [[wiki/entities/dotnet-aspire|.NET Aspire]]
- [[wiki/entities/docker|Docker]]
- [[wiki/entities/podman|Podman]]
- [[wiki/comparisons/docker-vs-podman|Docker vs Podman]]
- [[wiki/comparisons/dotnet-aspire-windows-container-runtimes|Aspire 在 Windows 上的容器執行環境比較]]
- [[wiki/concepts/developer-control-plane-dcp|Developer Control Plane (DCP)]]
- [[wiki/concepts/dotnet-aspire-docker-compose-publisher|Aspire Docker Compose Publisher]]
