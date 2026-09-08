---
title: ".NET Aspire 在 Windows 11 上的容器執行環境比較（免 Docker Desktop）"
type: comparison
created: 2026-09-08
updated: 2026-09-08
sources: 1
tags: [dotnet-aspire, docker, podman, container-runtime, docker-desktop, licensing, windows, comparison]
topics: [dotnet-aspire, container-infrastructure]
canonical: comparisons/dotnet-aspire-windows-container-runtimes
---

# .NET Aspire 在 Windows 11 上的容器執行環境比較

> 當 Docker Desktop 企業授權受限時，Aspire 專案在 Windows 11 上有三條替代路徑。本頁比較其定位、已知問題與適用場景。

## 橫向對照

| 面向 | Podman | 裸 Docker Engine（WSL2） | Docker Compose Publisher |
|------|--------|------------------------|------------------------|
| **定位** | 本地開發 runtime | 本地開發 runtime | 部署產物生成 |
| **Aspire 官方支援** | ✅ 正式（preview 4+） | ❌ 非官方（社群摸索） | ✅ 正式（9.2+） |
| **授權** | Apache 2.0 | Engine Apache 2.0 / Desktop 付費 | Apache 2.0 |
| **設定複雜度** | 中（需 podman machine） | 高（DOCKER_HOST 手動設定） | 低（NuGet + API 啟用） |
| **穩定性** | 有已知 bug 但可追蹤 | 有 regression 且官方不修 | 穩定（正式功能） |
| **WSL 依賴** | 是（WSL2-based VM） | 是（直接跑在 WSL2） | 否（產出檔案可跨平台） |
| **IP 漂移問題** | 否（Podman machine 管理） | 是（WSL IP 會變） | 否 |
| **官方修復承諾** | 有（Backlog 追蹤） | 無（Closed as not planned） | 有 |

## 決策樹

```
你要做什麼？
├── 本地開發（F5 debug）
│   ├── 專案用 RabbitMQ 或 Dapr？
│   │   ├── 是 → 評估影響後決定（Podman 有已知 issue）
│   │   └── 否 → Podman（官方支援，風險最低）
│   └── 一定要用 Docker Engine？
│       └── 裸 WSL Docker Engine（非官方，風險最高，無修復承諾）
└── 部署/發布
    └── Docker Compose Publisher（9.2+）→ 產出 compose 檔 → 免費 Docker Engine 執行
```

## 關鍵差異深入

### Podman 的已知問題

Podman 是官方支援管道，但有兩個 Backlog 級 bug：
- **Issue #3517**：Podman inspect 回傳的 `Entrypoint` 是 JSON 陣列，Aspire 預期字串 → JSON deserialize 例外（[連結](https://github.com/dotnet/aspire/issues/3517)）
- **Issue #6846**：`host.containers.internal` DNS 解析失敗 → Dapr sidecar 逾時（[連結](https://github.com/dotnet/aspire/issues/6846)）

若專案不用 RabbitMQ 或 Dapr sidecar，這兩個問題大機率不會踩到。

### 裸 Docker Engine 的結構性風險

不只是 bug 的問題，而是**官方沒有義務修**：
- WSL 閒置自動關機 → 連線斷掉
- WSL IP 漂移 → `DOCKER_HOST` 需要重新設定
- **Issue #8692**：`WithEnvironment()` 雙重呼叫 regression，**只在裸 WSL Docker Engine 下出現**，官方標記 Closed as not planned（[連結](https://github.com/dotnet/aspire/issues/8692)）

David Fowl 的態度：「These are all the issues you'll run into」（[Discussion #6813](https://github.com/dotnet/aspire/discussions/6813)）——承認能走，但不保證品質。

### Docker Compose Publisher 的定位

這不是本地開發的替代品，而是**部署階段**的工具：
- `aspire run` 用的是 DCP（[[wiki/concepts/developer-control-plane-dcp|Developer Control Plane]]），跟 docker-compose 無關
- `aspire publish` 產出 `docker-compose.yaml` + `.env`，需要另外用 `docker compose` 或 `podman-compose` 執行
- 完全繞過 Docker Desktop 授權問題，因為產出的 compose 檔案可以用任何 OCI 相容 runtime 執行

## 結論

| 情境 | 建議 | 理由 |
|------|------|------|
| 本地開發，不用 RabbitMQ/Dapr | Podman | 官方支援，已知問題不會踩到 |
| 本地開發，用 RabbitMQ 或 Dapr | 評估 Podman 影響 或 接受裸 WSL 風險 | 取決於 resource 種類 |
| 部署/發布 | Docker Compose Publisher + 免費 Docker Engine | 零授權風險，官方正式功能 |
| 任何情境 | 裸 WSL Docker Engine | 最後備案，無修復承諾 |

## 來源

- [[wiki/sources/2026-09-08-aspire-container-runtime-comparison|Aspire 容器執行環境替代方案比較]]
- [[wiki/sources/2026-09-06-dotnet-aspire-deep-research|.NET Aspire 深度研究]]

## 相關頁面

- [[wiki/entities/dotnet-aspire|.NET Aspire]]
- [[wiki/entities/docker|Docker]]
- [[wiki/entities/podman|Podman]]
- [[wiki/comparisons/docker-vs-podman|Docker vs Podman — 架構差異與選型建議]]
- [[wiki/concepts/developer-control-plane-dcp|Developer Control Plane (DCP)]]
- [[wiki/concepts/dotnet-aspire-docker-compose-publisher|Aspire Docker Compose Publisher]]
- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization]] — Docker Desktop 替代方案脈絡
