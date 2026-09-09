---
title: "Container Infrastructure"
type: topic
created: 2026-09-05
updated: 2026-09-05
tags: [container-infrastructure, docker, podman, container-runtime, devops]
canonical: topics/container-infrastructure
---

# Container Infrastructure

> 容器引擎、容器運行時、容器編排等基礎設施相關頁面導航。

## 容器引擎

| 頁面 | 說明 |
|------|------|
| [[wiki/entities/docker|Docker]] | Daemon 架構的主流容器引擎 |
| [[wiki/entities/podman|Podman]] | Daemonless、Rootless-First 的容器引擎 |

## 比較

| 頁面 | 說明 |
|------|------|
| [[wiki/comparisons/docker-vs-podman|Docker vs Podman]] | 架構差異與選型建議 |
| [[wiki/comparisons/dotnet-aspire-windows-container-runtimes|Aspire 在 Windows 上的容器執行環境比較]] | 免 Docker Desktop 方案比較 🛠️ |

## 來源

| 頁面 | 說明 |
|------|------|
| [[wiki/sources/2026-09-09-usestrix-strix-github-research|usestrix/strix — 開源 AI 自主滲透測試工具研究]] | Strix Docker 依賴分析：強制 Docker、Podman 未提及、推測替代路徑 🛠️ |
| [[wiki/sources/2026-09-05-docker-vs-podman|Docker vs Podman: Why Podman Exists]] | 5 分鐘影片：Docker vs Podman 核心差異 |
| [[wiki/sources/2026-09-08-aspire-container-runtime-comparison|Aspire 容器執行環境替代方案比較]] | Aspire 免 Docker Desktop 方案 🛠️ |

## 相關 Topics

- [[wiki/topics/backend-systems|Backend Systems]] — 後端基礎設施（vLLM、GPU 等 AI 推理 infra）
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — AI Agent 運行基礎設施

## 背景脈絡

Cheer 在長榮航空 IT 部門面臨 Docker Desktop 企業授權限制，持續研究容器替代方案。此 topic 收錄容器基礎設施相關知識，包括：
- Docker Desktop 替代方案評估（Podman、Rancher Desktop、Colima）
- 容器引擎架構差異（daemon vs daemonless）
- 容器安全模型（rootless）
