---
type: topic
title: Pi Agent Ecosystem
topic: pi-agent-ecosystem
created: 2026-08-23
updated: 2026-08-23
---

# Pi Agent Ecosystem

> 與 Pi Agent Ecosystem 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/mario-zechner|Mario Zechner]] — 維護 `badlogic/pi-mono` 整套 monorepo 的作者。他的立場:**「自主 agent 就是 LLM + tools + 一個迴圈」**。
- [[wiki/entities/pi-agent-core|pi-agent-core — 5 檔 / 1,500 行的 Agent Runtime]] — pi-mono monorepo 內的 `packages/agent/`,全文為 agent runtime。**整個 runtime 由 5 個檔、約 1,500 行 TypeScript 構成**(以 [[[../../raw/web/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]]] 解析為準)。 🛠️
- [[wiki/entities/pi-mono|pi-mono — Agent Toolkit Monorepo]] — [`earendil-works/pi`](https://github.com/earendil-works/pi)（前身 `badlogic/pi-mono`）是 Mario Zechner 維護的 TypeScript agent toolkit monorepo。核心定位：**「你的 agent 平台」**——不是單一既定體驗,而是一組可組合的 runtime、模型抽象層、UI。License：MIT。 🛠️
- [[wiki/entities/pi-agent/changelog/v0.84.0|Pi Agent v0.84.0 更新分析]] — 版本日期：2026-08-06 | 分析日期：2026-08-20 | 重要性：⭐⭐⭐⭐（4/5） 🛠️

## Sources

- [[wiki/sources/2026-02-10-pi-agent-core-design|下一代Agent架构——Pi Agent Core 设计逻辑深度解析]] — 這裡做為來源頁收存。目的是:反映從這篇文章推導出的主張、以及被這篇文章影響、我們接著在 wiki 內拆解的 entity / concept / comparison 頁。
- [[wiki/sources/2026-05-02-pi-mono-framework-tw|別再被複雜框架綁架:pi-mono 回歸直覺的 TypeScript AI Agent 開發框架]] — 與 [[wiki/sources/2026-02-10-pi-agent-core-design]] 為姊妹篇:**同一個專題的兩個視角**——A 篇解剖 `packages/agent/src/` 五支檔,B 篇俯瞰整個 monorepo。互不矛盾。
- [[wiki/sources/2026-07-18-pi-resource-inventory|2026-07-18 Pi 資源完整盤點]] — 來源：人類在 Pi 互動模式下要求「檢視我們現在擁有的所有資源」，Agent 掃描專案目錄、Pi 設定目錄、Obsidian 知識庫，彙整而成。 🛠️
- [[wiki/sources/2026-08-05-pi-github-readme|Pi GitHub README — Agent Harness 總覽]] — 來源：[earendil-works/pi GitHub README](https://github.com/earendil-works/pi)
- [[wiki/sources/2026-08-06-pi-architecture-walkthrough|Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained]] — 來源：[YouTube — Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained](https://www.youtube.com/watch?v=gTeujlv8qK0) 🛠️
- [[wiki/sources/2026-08-10-pi-cron-job-explained|Pi Cron Job 實作原理 — @pi-agents/loop 架構解析]] — 從 `@pi-agents/loop` extension 的原始碼與文件，解析 Pi 的 cron job 系統如何實作。 🛠️
- [[wiki/sources/2026-08-15-round-table-pi-agent-v084-learning|圓桌會議：Pi Agent v0.84.x 更新中值得學習的設計模式]] — 日期：2026-08-15 | 參與者：Claude（深度分析）、Copilot（實作面）| 主持人：Pi 🛠️
- [[wiki/sources/2026-08-19-pi-agent-github-copilot-provider-setup|Pi Agent GitHub Copilot Provider 設定指南]] — 整理自 Pi Agent 官方文件與 GitHub Issues，供 W-2026-08-028 參考使用。

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
- [[wiki/topics/pi-agent|Pi Agent]] — Pi Agent 專門（Extension / Dashboard / 排程）
