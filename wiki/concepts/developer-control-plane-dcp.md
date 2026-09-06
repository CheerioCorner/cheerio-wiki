---
title: "Developer Control Plane (DCP)"
type: concept
created: 2026-09-06
updated: 2026-09-06
sources: 90
tags: [dotnet-aspire, dcp, orchestration, kubernetes, local-development]
topics: [dotnet-aspire, container-platform]
---

# Developer Control Plane (DCP)

.NET Aspire 的本地編排核心引擎，本質上是一個**微型的本地 Kubernetes API 伺服器**。

## 運作機制

- 由 `dcp.exe`、`dcpctrl.exe`、`dcpd.exe` 等二進位程式組成（Go 語言編譯）
- AppHost 啟動時，Aspire 為 DCP 產生臨時 `kubeconfig`，建立自訂 API Server 端點
- Aspire 透過 .NET Kubernetes Client 將資源圖發送給 DCP
- DCP 控制器管理 `Container` 與 `Executable` 的生命週期、連接埠指派、多副本、程序監控

## 與 Docker Compose 的差異

| 維度 | DCP | Docker Compose |
|---|---|---|
| 定位 | 開發期微型 K8s API | 容器組態工具 |
| 通訊 | Kubernetes Client API | Docker Engine API |
| 資源模型 | 類 K8s 資源（Executable/Container） | Compose YAML 服務定義 |
| 網路管理 | DCP 自行管理虛擬網橋 | Docker 網路驅動 |
| 多語言 | 同時管理容器 + 裸機行程 | 僅管理容器 |

## 已知限制

- 僅支援 Linux 容器（不支援 Windows 容器）
- Container-to-Host 網路在 WSL2/Podman 環境容易故障
- 憑證不信任問題（容器內部不信任主機開發憑證）
- 參數處理回傳（Regression）：非 Docker Desktop 環境下 multi-line 環境變數可能被截斷

## 來源

- [[wiki/sources/2026-09-06-dotnet-aspire-deep-research|深度研究報告]]
- [DCP 深入分析](https://anthonysimmon.com/exploring-microsoft-developer-control-plane-core-dotnet-aspire-dotnet-8/)
