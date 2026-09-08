---
title: "Podman — Daemonless、Rootless-First 的容器引擎"
type: entity
created: 2026-09-05
updated: 2026-09-05
sources: 1
tags: [podman, container-runtime, daemonless, rootless, oci, pods, kubernetes]
topics: [container-infrastructure]
canonical: entities/podman
---

# Podman

> Podman 是 Red Hat 主導的容器引擎，以 daemonless 與 rootless-first 為核心設計，是 Docker 的主要替代方案。

## 概述

Podman 解決的問題跟 Docker 一樣——建立、管理與執行容器。但它的設計哲學不同：**沒有 daemon**，每個容器直接作為 CLI 的子程序運行 `[00:52]`。這帶來安全與架構上的差異。

## 架構特點

### Daemonless

```
使用者 CLI → 直接建立容器（無中央 daemon）
```

沒有單一的 `podmand` 背景程序需要持續運行。Podman CLI 直接管理容器，每個容器是獨立的子程序 `[00:52]`。好處：沒有單點故障、不需要常駐服務。

### Rootless-First

Podman 從設計之初就以 rootless 為核心，讓非 root 使用者能執行容器，天然支援最小權限模型 `[01:08]`。這在共享 Linux 伺服器上特別重要——不需要給每個開發者 root 權限就能跑容器。

> 「Podman is designed to let users run containers as a non-root user which helps support a least privilege model.」`[01:08]`

### OCI 相容

Podman 使用 OCI 開放標準的容器映像。同一個 Docker 建立的映像，Podman 可以直接使用，不需要轉換 `[02:17]`。

### 指令相容

指令幾乎一對一對應 Docker：

| Docker | Podman |
|--------|--------|
| `docker run` | `podman run` |
| `docker build` | `podman build` |
| `docker exec` | `podman exec` |
| `docker pull` | `podman pull` |
| `docker ps` | `podman ps` |

遷移門檻很低 `[02:17]`。

## Pod 支援

Podman 原生支援 **pod** 概念——將多個容器組合在一起，共享網路命名空間 `[03:16]`。這跟 Kubernetes 的 pod 概念一致，有助於在本地開發時理解 K8s 架構 `[03:38]`。

> 「Podman isn't Kubernetes, but its pod model can make the concept easier to understand when you're working locally.」`[03:38]`

## Docker Compose 整合

Podman 透過外部工具支援 compose 工作流：

- **podman-compose**：Python 實作的 compose 替代品
- **Podman + Docker Compose**：設定 `DOCKER_HOST` 指向 Podman socket

但 Docker Compose 的原生生態系更成熟，這是 Docker 的優勢 `[02:42]`。

## 何時選擇 Podman

- 主要在 Linux 環境工作 `[04:04]`
- 需要 daemonless 架構（不想有常駐背景程序）
- 需要 rootless-first（共享伺服器、最小權限）
- 想在本地體驗 Kubernetes pod 概念

## 在 Cheer 的場景

Docker Desktop 企業授權受限，Podman 是最被看好的替代方案之一。它相容 Docker API、CLI 指令幾乎一樣，遷移成本低。但需要評估：
1. Windows 上的 Podman 支援成熟度
2. 既有 Docker Compose 專案的相容性
3. 團隊學習曲線

## 來源

- [[wiki/sources/2026-09-05-docker-vs-podman|Docker vs Podman: Why Podman Exists]]
- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization with AI]]（提到 Podman 作為 Docker Desktop 替代方案）

## 相關頁面

- [[wiki/entities/docker|Docker]] — daemon 架構的容器引擎
- [[wiki/comparisons/docker-vs-podman|Docker vs Podman 比較]]
- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization]] — 替代方案脈絡
