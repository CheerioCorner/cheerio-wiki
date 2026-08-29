---
type: topic
title: Pi Agent
topic: pi-agent
created: 2026-08-08
updated: 2026-08-08
---

# Pi Agent

> 與 Pi Agent 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard — Real-time Web Dashboard for Pi Sessions]] — BlackBeltTechnology 開發的 Pi coding-agent 即時 Web Dashboard。一個瀏覽器 tab 管理多個 pi session——**與 TUI 並存，不取代它**。 🛠️
- [[wiki/entities/pi-loop-scheduler|pi-loop-scheduler — Pi 的 Cron Job 與排程系統]] — `@pi-agents/loop` — Pi 的排程 extension，提供 cron job、動態自節奏、idle gating 等功能。非 core runtime 內建，而是透過 extension 機制載入。 🛠️
- [[wiki/entities/pi-web-access-zh-tw|pi-web-access-zh-tw — Web 存取擴充的繁體中文本地化版]] — **你維護的 `pi-web-access` 繁體中文本地化套件**，已安裝到 Pi 全域（`pi install file:../../../Cheerio/pi-web-access-zh-tw`）。 🛠️
- [[wiki/entities/pi-agent/changelog/v0.84.0|Pi Agent v0.84.0 更新分析]] — 版本日期：2026-08-06 | 分析日期：2026-08-20 | 重要性：⭐⭐⭐⭐（4/5） 🛠️

## Concepts

- [[wiki/concepts/pi-project-workspace-model|pi-project-workspace-model — Pi 專案/工作區模型]] — Pi 的「專案」概念**不強制綁定單一實體路徑**。專案 = **設定作用域**（`.pi/` 目錄存在的位置），而非工作目錄本身。這使得「一個實體路徑對應多個專案人格」成為可能。 🛠️

## Sources

- [[wiki/sources/2026-08-15-round-table-pi-agent-v084-learning|圓桌會議：Pi Agent v0.84.x 更新中值得學習的設計模式]] — 日期：2026-08-15 | 參與者：Claude（深度分析）、Copilot（實作面）| 主持人：Pi 🛠️

## 相關 Topics
