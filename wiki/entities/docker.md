---
title: "Docker — Daemon 架構的主流容器引擎"
type: entity
created: 2026-09-05
updated: 2026-09-05
sources: 1
tags: [docker, container-runtime, daemon-architecture, docker-desktop, docker-compose]
topics: [container-infrastructure]
canonical: entities/docker
---

# Docker

> Docker 是最廣泛使用的容器引擎，以 daemon 架構為核心，搭配成熟的 Docker Compose 生態系。

## 概述

Docker 讓開發者打包、分發與執行容器化應用。其核心架構依賴一個長期運行的背景程序——**Docker daemon**（`dockerd`），所有容器操作都透過 CLI 發送請求給 daemon 處理 `[00:15]`。

## 架構特點

### Daemon 架構

```
使用者 CLI → Docker daemon (dockerd) → 容器
```

CLI 把請求送給 daemon，由 daemon 統一管理容器的建立、啟動、停止、映像、網路與儲存 `[00:41]`。優點是集中管理；風險是 daemon 掛了，所有容器管理都受影響。

### Rootless 支援

Docker **支援** rootless mode（daemon 和容器都可以非 root 運行），但這是後來加入的選項，不是原始設計核心 `[01:35]`。在共享 Linux 伺服器上，Docker 傳統上可能需要 root 權限才能啟動容器。

### Docker Compose

Docker Compose 是多容器應用的事實標準工具。透過 YAML 描述多個服務（backend、database、cache 等），一條指令啟動整個開發環境 `[02:42]`。生態系非常成熟，有大量既有專案、腳本、整合工具 `[03:09]`。

## Docker Desktop 授權問題

Docker Desktop 在企業環境（超過 250 人或年營收超過 1000 萬美元）需要付費授權。這是 Cheer 在長榮航空 IT 部門面臨的實際問題——正在研究替代方案。

### 替代方案

- **Podman**：daemonless、rootless-first、相容 Docker API（→ [[wiki/entities/podman|Podman]]）
- **Rancher Desktop**：GUI 友善、內建 K3s/Containerd
- **Colima**：macOS 輕量級方案

## 何時選擇 Docker

- 團隊已廣泛使用 Docker，有大量既有 Compose 專案 `[03:45]`
- 需要成熟的第三方工具整合與社群支援 `[03:59]`
- 容器初學者，Docker 文件與教程最豐富

## 來源

- [[wiki/sources/2026-09-05-docker-vs-podman|Docker vs Podman: Why Podman Exists]]
- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization with AI]]（提到 Docker Desktop 授權問題）

## 相關頁面

- [[wiki/entities/podman|Podman]] — daemonless 替代方案
- [[wiki/comparisons/docker-vs-podman|Docker vs Podman 比較]]
- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization]] — Docker Desktop 替代方案脈絡
