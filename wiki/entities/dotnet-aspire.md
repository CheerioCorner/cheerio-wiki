---
title: ".NET Aspire"
type: entity
created: 2026-09-06
updated: 2026-09-06
sources: 90
tags: [dotnet-aspire, microsoft, cloud-native, microservices, orchestration]
topics: [dotnet-aspire, container-platform, enterprise-architecture]
canonical: entities/dotnet-aspire
---

# .NET Aspire

微軟開發的 **Code-first Distributed Application Platform**，專為雲端原生與微服務架構設計。它不是容器引擎，而是應用模型 + 開發期編排 + 觀測 + 部署描述的統一層。

## 核心定位

> Aspire 管理「應用系統如何組成」，Runtime 負責「Container 如何真正執行」。

- **不是** Docker Desktop 的替代品
- **不是** 生產環境的運行時（AppHost 專案在生產環境不運行）
- **是** 從開發到部署的應用模型統一層

## 架構分層

```
AI Harness / Copilot / Agent（規劃、產生、驗證、操作、治理）
        ↓
Microsoft Aspire（Application Model / Orchestration / Service Discovery / Observability）
        ↓
Deployment Description（Docker Compose / Helm / K8s Manifest）
        ↓
Container Runtime（Docker Engine / Podman / containerd）
        ↓
Infrastructure（Notebook / VM / On-prem / Cloud）
```

## 四大核心能力

1. **Application Model** — Code-first 拓撲（AppHost 專案定義服務圖）
2. **Local Orchestration** — DCP（Developer Control Plane，本地微型 K8s API）
3. **Service Discovery** — 環境變數自動注入 + 用戶端名稱解析
4. **Observability** — 無縫 OpenTelemetry 整合 + 即時 Dashboard

## 三種部署路徑

| 路徑 | 產物 | 適合場景 |
|---|---|---|
| Docker Compose | `docker-compose.yaml` + `.env` | 少量主機、簡單服務組合 |
| Podman Compose | 同上（透過 Compose Provider） | 降低 Docker Desktop 授權依賴 |
| Kubernetes | Helm Chart / Manifests | 正式企業平台、多系統共享叢集 |

## 關鍵限制

- 本機開發採「專案跑裸機行程、依賴走容器」的**混部模式**
- Container-to-Host 網路在 WSL2/Podman 環境容易出問題
- Dashboard 僅限本機開發（內存儲存，不適合做 APM）
- Python 的 Telemetry 有 gRPC ALPN 相容性問題

## 與 Cheer 工作的關聯

- **W-074（個人 AI 助理架構）**：Aspire 的 Application Model + DCP 編排模式可作為「核心 + Plugin 分離」架構的參考
- **W-080（ADO Agent）**：Aspire 的 CI/CD 整合（Azure DevOps Pipeline）是 W-080 後續部署的潛在路徑
- **企業提案簡報**：本頁面是簡報中「Aspire 是什麼」的技術基礎

## 來源

- [[wiki/sources/2026-09-06-dotnet-aspire-deep-research|深度研究報告]]（90 筆來源，rc-20260906-001）
- [[wiki/sources/2026-09-08-aspire-container-runtime-comparison|Aspire 容器執行環境替代方案比較]]（免 Docker Desktop 方案）
- [Microsoft 官方文件](https://aspire.dev/)
- [Developer Control Plane 深入分析](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)

## 相關頁面

- [[wiki/concepts/developer-control-plane-dcp|Developer Control Plane (DCP)]] — 本地編排核心
- [[wiki/comparisons/docker-vs-podman|Docker vs Podman]] — 容器引擎選型
- [[wiki/comparisons/dotnet-aspire-windows-container-runtimes|Aspire 在 Windows 上的容器執行環境比較]] — 免 Docker Desktop 方案比較
- [[wiki/concepts/dotnet-aspire-docker-compose-publisher|Aspire Docker Compose Publisher]] — 部署產物生成
- [[wiki/topics/container-infrastructure|Container Infrastructure]] — 容器基礎設施主題
