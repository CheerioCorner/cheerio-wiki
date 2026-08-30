---
type: topic
title: Knowledge Management
topic: knowledge-mgmt
created: 2026-08-02
updated: 2026-08-25
---

# Knowledge Mgmt

> 與 Knowledge Mgmt 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/knowledge-garden-to-raw|knowledge-garden-to-raw — Notion → Raw 抓取 Skill]] — 從 Notion 知識花園抓取頁面內容，建立 raw 來源檔案，準備進行 wiki ingest。
- [[wiki/entities/knowledge-garden|knowledge-garden — Cheerio 知識花園 Manifest]] — **Notion 知識花園**的架構藍圖與離線快取。
- [[wiki/entities/learning-loop|learning-loop]] — 自動學習優化系統，建立完整的學習迴圈：做 → 記 → 學 → 優化 → 做。 🛠️
- [[wiki/entities/markitdown|MarkItDown]] — Microsoft 出品的「萬能格式 → Markdown」轉換器，專為 LLM 和文字分析流程設計。 🛠️
- [[wiki/entities/notionApi|notionApi — Notion MCP 伺服器]] — **MCP 伺服器**：`notionApi`，在 `~/.pi/agent/mcp.json` 設定，提供 Notion API 存取能力。
- [[wiki/entities/obsidian|Obsidian — AI Agent 時代的知識管理基礎設施]] — 從疫情催生的個人專案，成長為 150 萬活躍用戶的 AI Agent 最佳知識儲存庫。 🛠️
- [[wiki/entities/wiki-knowledge|wiki-knowledge — Obsidian 知識庫操作 Skill（已刪除）]] — ⚠️ **已刪除**（2026-08-08）。原始的三大流程已拆成三個獨立 skill，dispatcher 功能不再需要。 🛠️

## Concepts

- [[wiki/concepts/graphify-obsidian-export|Graphify → Obsidian Export]] — 將代碼知識圖譜匯出為 Obsidian vault 的工作流，讓 AI Coding Agent 可以在更廣泛的專案脈絡中查詢圖譜。
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]] — AI agent 記憶架構的設計框架：三支柱分析 × 五種存儲方式 × 四種檢索方法 × 六種維護策略。 🛠️
- [[wiki/concepts/content-redesign|Content 設計 Redesign]] — 知識花園 Content 設計重構：從「套模板」到「針對性報告」。經 Copilot + Gemini 三輪討論確立。 🛠️
- [[wiki/concepts/knowledge-garden-skill-architecture|知識花園 Skill 架構]] — 知識花園相關 skills 的架構全景圖，描述 11 個 skill 的職責、資料流和關聯。 🛠️
- [[wiki/concepts/knowledge-management|Knowledge Management]] — 系統化地收集、組織、檢索、維護知識的方法論與工具鏈。
- [[wiki/concepts/knowledge-system-architecture|知識系統架構]] — 經過 4 輪圓桌會議（Pi + Gemini + Copilot）討論確定，並於 2026-08-12 修正為雙模型共識機制（取代人類確認）的知識管理系統架構。詳見 [[wiki/decisions/knowledge-system-architecture-decision|決策文件]] 第十一節。
- [[wiki/concepts/lint-ruleset|Lint 規則集]] — 定義「什麼是好的知識」的標準，用來自動化檢查知識庫的品質。
- [[wiki/concepts/moc-map-of-content|MOC — Map of Content]] — 一種導航頁面，用來連結相關的筆記。它不是筆記本身，而是一張「地圖」。
- [[wiki/concepts/para|PARA — Projects/Areas/Resources/Archives]] — Tiago Forte 在《Building a Second Brain》中提出的信息組織系統，把所有信息分成四類。
- [[wiki/concepts/pi-project-workspace-model|pi-project-workspace-model — Pi 專案/工作區模型]] — Pi 的「專案」概念**不強制綁定單一實體路徑**。專案 = **設定作用域**（`.pi/` 目錄存在的位置），而非工作目錄本身。這使得「一個實體路徑對應多個專案人格」成為可能。 🛠️
- [[wiki/concepts/semantic-relations|語意關係 — Semantic Relations]] — 定義頁面之間「怎麼連結」的方式——不只是「這兩個頁面有關」，而是「A 支持 B」或「A 矛盾 B」。
- [[wiki/concepts/zettelkasten|Zettelkasten — 卡片盒筆記法]] — 由德國社會學家 Niklas Luhmann 實踐的知識管理方法，經 Sönke Ahrens《How to Take Smart Notes》系統化後廣泛流行。

## Sources

- [[wiki/sources/2026-07-18-pi-resource-inventory|2026-07-18 Pi 資源完整盤點]] — 來源：人類在 Pi 互動模式下要求「檢視我們現在擁有的所有資源」，Agent 掃描專案目錄、Pi 設定目錄、Obsidian 知識庫，彙整而成。 🛠️
- [[wiki/sources/2026-08-01-okf-knowledge-catalog|OKF Knowledge Catalog — Google Cloud 參考實作]] — Google Cloud 開發的 OKF 參考實作，包含 reference agent 和視覺化工具。 🛠️
- [[wiki/sources/2026-08-02-wiki-okf-migration-complete|2026-08-02 Wiki × OKF 架構優化完整工作紀錄]] — 本頁是本輪 Wiki × OKF 架構優化的完整 canonical work record。它記錄決策、實作、驗證、Git commits、保留邊界與未來工作；不是 package source code、PLAN 或 raw conversation 的替代品。 🛠️
- [[wiki/sources/2026-08-07-ai-related-seeds|Notion AI 相關種子 — agentskills.io + Axway AI Gateway]] — 來源：Notion「任何當下 → AI相關」頁面 🛠️
- [[wiki/sources/2026-08-08-plannotator-obsidian-presentation|Plannotator × Obsidian — 知識迴流系統報告簡報企劃]] — 來源：Pi 整理的簡報企劃文件，經過 Gemini + Copilot 雙重審閱 🛠️
- [[wiki/sources/2026-08-13-garden-guard-incident|2026-08-13 花園巡檢事故與 garden-guard extension 建立全記錄]] — 類型：內部事故報告 + 修復全過程記錄 🛠️
- [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|AI Agent Memory Systems — 5 種記憶架構設計方式]] — 來源：[YouTube](https://www.youtube.com/watch?v=072eNztI06k) | 作者：Sean Chen ([@ShenSeanChen](https://github.com/ShenSeanChen)) | 頻道：Sean's AI Stories | 時長：30:26 🛠️
- [[wiki/sources/2026-08-25-graphify-obsidian-cheat-code|Graphify + Obsidian + Claude Code = CHEAT CODE]] — 將 Graphify 知識圖譜匯出為 Obsidian vault 的實作教學，含四種整合策略。 🛠️

## Others

- [[wiki/decisions/graphify-vs-codebase-memory-mcp|Graphify vs codebase-memory-mcp 整合決策]] — 大腦 wiki 本身不搭配任一工具；程式碼專案預設只裝 codebase-memory-mcp 🛠️
- [[wiki/decisions/knowledge-system-architecture-decision|知識系統架構改進決策]] — 版本：v2.0
- [[wiki/decisions/package-knowledge-boundary|Package Knowledge Boundary]] 🛠️
- [[wiki/decisions/project-okf-bundle-boundary|Project OKF Bundle Boundary]] 🛠️
- [[wiki/decisions/raw-conversation-promotion-workflow|Raw Conversation Promotion Workflow]] 🛠️
- [[wiki/decisions/shared-wiki-collection-model|Shared Wiki Collection Model]] 🛠️
- [[wiki/discussions/canonicalization-and-semantic-linking|Canonicalization and Semantic Linking]] 🛠️
- [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]] 🛠️
- [[wiki/discussions/topic-stub-cleanup|Existing Topic Stub Cleanup]] 🛠️
- [[wiki/discussions/wiki-youtube-pipeline-timing|YouTube-to-Wiki Pipeline Timing]] 🛠️
- [[wiki/visualizations/obsidian-seed-map|Obsidian 視覺地圖 — 功能與延伸應用]] — 展示 Obsidian 的核心功能、插件生態、延伸應用、AI Agent 整合，以及與我們研究專題的關聯。

## 相關 Topics
