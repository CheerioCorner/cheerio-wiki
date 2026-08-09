---
title: "Obsidian 視覺地圖 — 功能與延伸應用"
type: visualization
created: 2026-08-09
updated: 2026-08-09
tags: [obsidian, visual-map, knowledge-management, ai-agent]
topics: [knowledge-mgmt]
canonical: visualizations/obsidian-seed-map
---

# 🗺️ Obsidian 視覺地圖 — 功能與延伸應用

> 展示 Obsidian 的核心功能、插件生態、延伸應用、AI Agent 整合，以及與我們研究專題的關聯。

## 架構說明

本視覺地圖分為六個層級：

### 1. 🏗️ 核心功能
- **Markdown 檔案**：純文字格式，100 年後仍可讀
- **雙向連結**：`[[wikilinks]]` 建立語意關聯
- **知識圖譜**：Graph View 視覺化筆記關聯
- **Properties**：YAML Frontmatter 原生支援
- **Bases**：類 Notion 的資料庫視圖（2025-05 新增）
- **Canvas**：無限畫布，空間化思考

### 2. 🧩 插件生態系（4,000+）
- **AI 插件**：Smart Connections（語意搜尋）、Copilot for Obsidian
- **資料查詢**：Dataview（動態查詢）、Templater（動態模板）
- **自動化**：Local REST API、QuickAdd
- **同步**：Git 插件、Obsidian Sync

### 3. 🚀 延伸應用
- **Zettelkasten**：卡片盒筆記法，原子化筆記 + 雙向連結
- **PARA 系統**：Projects / Areas / Resources / Archives
- **Second Brain**：第二大腦，外部記憶系統
- **寫作環境**：書籍、論文、部落格
- **每日筆記**：日誌、習慣追蹤、反思
- **個人 Wiki**：文件、知識庫

### 4. 🤖 AI Agent 整合
- **LLM Wiki 範式**：Karpathy 2026，Obsidian = IDE，LLM = 程式員
- **檔案系統直接存取**：零延遲讀寫（我們目前的做法）
- **Local REST API**：MCP 協議，精確區塊更動
- **URI Scheme**：obsidian:// 觸發 GUI 操作

### 5. 🏢 我們的實作
- **raw/**：唯讀原始資料（LLM 幻覺防護層）
- **wiki/**：LLM 維護的筆記（持久層）
- **Notion 花園**：人類閱讀展示層
- **OKF 整合**：Open Knowledge Format，90%+ 相容
- **Plannotator**：審查 → 沉澱 → 再餵養的閉環

### 6. 🔬 關聯研究專題
- **AI Agent 架構研究**：Obsidian 作為 AI Agent 的知識基礎設施
- **知識管理系統**：LLM Wiki 新範式 vs 傳統 RAG
- **Extension 開發**：插件生態系的接口標準化

## 資料來源

- Gemini Deep Research（2026-08-09）
- 本機 wiki 知識庫
- 官方文檔 + 社群資料
