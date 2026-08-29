---
type: topic
title: Extension Development
topic: extension-dev
created: 2026-08-02
updated: 2026-08-06
---

# Extension Dev

> 與 Extension Dev 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/markitdown|MarkItDown]] — Microsoft 出品的「萬能格式 → Markdown」轉換器，專為 LLM 和文字分析流程設計。 🛠️
- [[wiki/entities/mcp-apps|MCP Apps — 互動式應用程式]] — MCP Apps 讓伺服器在聊天中直接渲染互動式 HTML 介面（資料視覺化、表單、儀表板），使用者無需離開對話即可與資料互動。 🛠️
- [[wiki/entities/mcp-extensions|MCP Extensions — 擴充機制]] — MCP 擴充套件是協定的可選附加功能，用於定義核心協定之外的功能，支援模組化、專業化或實驗性功能。 🛠️
- [[wiki/entities/mcp-tasks|MCP Tasks — 長任務擴充機制]] — MCP Tasks 讓伺服器為長時間運行的請求返回持久控制代碼，客戶端可輪詢進度、提供輸入並在重連後取得結果，而非阻塞連線。 🛠️
- [[wiki/entities/open-code-review|OpenCodeReview]] — 阿里巴巴開源的 AI 代碼審查 CLI 工具，19.3k stars，確定性工程 × Agent 混合架構。 🛠️
- [[wiki/entities/pi-agent-core|pi-agent-core — 5 檔 / 1,500 行的 Agent Runtime]] — pi-mono monorepo 內的 `packages/agent/`,全文為 agent runtime。**整個 runtime 由 5 個檔、約 1,500 行 TypeScript 構成**(以 [[[../../raw/web/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]]] 解析為準)。 🛠️
- [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard — Real-time Web Dashboard for Pi Sessions]] — BlackBeltTechnology 開發的 Pi coding-agent 即時 Web Dashboard。一個瀏覽器 tab 管理多個 pi session——**與 TUI 並存，不取代它**。 🛠️
- [[wiki/entities/pi-loop-scheduler|pi-loop-scheduler — Pi 的 Cron Job 與排程系統]] — `@pi-agents/loop` — Pi 的排程 extension，提供 cron job、動態自節奏、idle gating 等功能。非 core runtime 內建，而是透過 extension 機制載入。 🛠️
- [[wiki/entities/pi-mono|pi-mono — Agent Toolkit Monorepo]] — [`earendil-works/pi`](https://github.com/earendil-works/pi)（前身 `badlogic/pi-mono`）是 Mario Zechner 維護的 TypeScript agent toolkit monorepo。核心定位：**「你的 agent 平台」**——不是單一既定體驗,而是一組可組合的 runtime、模型抽象層、UI。License：MIT。 🛠️
- [[wiki/entities/pi-web-access-zh-tw|pi-web-access-zh-tw — Web 存取擴充的繁體中文本地化版]] — **你維護的 `pi-web-access` 繁體中文本地化套件**，已安裝到 Pi 全域（`pi install file:../../../Cheerio/pi-web-access-zh-tw`）。 🛠️
- [[wiki/entities/plannotator|Plannotator]] — AI coding agent 的視覺化審閱工具，在瀏覽器中標注 plan、code review、HTML artifact。

## Concepts

- [[wiki/concepts/agent-extension-installation|Agent Extension 安裝位置與方式]] — 不同的 Agent 框架有各自安裝 Extension 的方式和儲存位置。記錄這些差異有助於理解 Agent 的運作機制。
- [[wiki/concepts/npm-publishing-workflow|Package Publishing Workflow — 統一的發佈流程觀點]] — **核心觀點：** 不管叫什麼名字（Package、Extension、Marketplace），發佈到市場的流程本質上是一樣的。差異只在平台、市場和來源不同。
- [[wiki/concepts/okf-open-knowledge-format|OKF (Open Knowledge Format) — 統一的知識表示格式]] — 用 **Markdown + YAML frontmatter** 來表示知識，讓 **人類和 AI 都能讀**。

## Sources

- [[wiki/sources/2026-07-11-plannotator-research|2026-07-11 Plannotator 研究]]
- [[wiki/sources/2026-07-23-plannotator-copilot-setup|Plannotator IT 安裝手冊：GitHub Copilot CLI 整合]] — 來源：PDF 安裝手冊（2026-07-23），描述 Plannotator 與 GitHub Copilot CLI 在 Windows 上的整合安裝流程。 🛠️
- [[wiki/sources/2026-07-31-npm-publishing-setup|2026-07-31 Package Publishing 首次實作]] — 今天完成了第一個 Package 的發佈流程設定。雖然這次是用 NPM，但學到的流程適用於所有市場。
- [[wiki/sources/2026-08-01-okf-extension-development|2026-08-01 OKF Extension 開發實作]]
- [[wiki/sources/2026-08-01-okf-knowledge-catalog|OKF Knowledge Catalog — Google Cloud 參考實作]] — Google Cloud 開發的 OKF 參考實作，包含 reference agent 和視覺化工具。 🛠️
- [[wiki/sources/2026-08-06-pi-architecture-walkthrough|Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained]] — 來源：[YouTube — Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained](https://www.youtube.com/watch?v=gTeujlv8qK0) 🛠️
- [[wiki/sources/2026-08-10-pi-cron-job-explained|Pi Cron Job 實作原理 — @pi-agents/loop 架構解析]] — 從 `@pi-agents/loop` extension 的原始碼與文件，解析 Pi 的 cron job 系統如何實作。 🛠️
- [[wiki/sources/2026-08-13-garden-guard-incident|2026-08-13 花園巡檢事故與 garden-guard extension 建立全記錄]] — 類型：內部事故報告 + 修復全過程記錄 🛠️
- [[wiki/sources/2026-08-15-round-table-pi-agent-v084-learning|圓桌會議：Pi Agent v0.84.x 更新中值得學習的設計模式]] — 日期：2026-08-15 | 參與者：Claude（深度分析）、Copilot（實作面）| 主持人：Pi 🛠️

## 相關 Topics
