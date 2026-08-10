# AI Agent 學習路線圖

> 從「使用 Pi Agent」到「建立自己的 Agent」的學習路徑。

## 🎯 最終目標

**建立自己的 AI Agent** — 理解核心原理，能從零開始建造。

## 📍 目前位置

```
Phase 1: 理解使用 ████████████ 100% ✅
Phase 2: 理解原理 ██████░░░░░░  50% 🔄
Phase 3: 理解架構 ██░░░░░░░░░░  15%
Phase 4: 動手建造 ░░░░░░░░░░░░   0%
```

## Phase 1：理解使用 ✅

**目標**：能熟練使用 Pi Agent 的各項功能。

- [x] 日常操作（對話、工具呼叫、檔案操作）
- [x] Skill 系統（使用現有 skill）
- [x] Knowledge Base 管理（wiki-knowledge）
- [x] Work Tracker（任務追蹤）
- [x] Cron Jobs（自動化任務）

**里程碑**：能用 Pi Agent 完成日常工作。

## Phase 2：理解原理 🔄

**目標**：理解 Pi Agent 背後的技術原理。

### 已完成
- [x] Provider 系統概覽（模型如何接入）
- [x] Session 模型（對話如何管理）
- [x] Token 預算概念

### 進行中
- [ ] Chain-of-thought 原理
- [ ] Provider 系統深入（Baseten 接入方式）
- [ ] Extension 系統原理

### 待做
- [ ] Tool 系統原理（工具如何定義和呼叫）
- [ ] Context 管理（對話歷史如何壓縮）
- [ ] Streaming 原理（即時回應如何實作）

**里程碑**：能解釋 Pi Agent 的核心機制。

## Phase 3：理解架構

**目標**：理解 Pi Agent 的整體架構設計。

- [ ] Harness 架構（Agent 的執行環境）
- [ ] Agent 與 Model 的分離（關注點分離）
- [ ] Event-driven 設計（事件如何流動）
- [ ] Plugin/Extension 架構（擴充機制）
- [ ] Session 持久化（狀態如何保存）

**里程碑**：能畫出 Pi Agent 的架構圖。

## Phase 4：動手建造

**目標**：建立自己的 AI Agent。

### 選擇 Framework
研究並選擇一個 Agent Framework：
- [ ] LangChain / LangGraph
- [ ] AutoGen
- [ ] CrewAI
- [ ] 自己從零建造

### 建造步驟
1. [ ] 最小可行 Agent（能對話）
2. [ ] 加入 Tool 系統（能執行操作）
3. [ ] 加入 Session 管理（能記住對話）
4. [ ] 加入 Context 管理（能處理長對話）
5. [ ] 加入 Extension 機制（能擴充功能）
6. [ ] 加入 Provider 系統（能接多種模型）

**里程碑**：能展示自己的 Agent 並解釋每個組件。

## 📚 學習資源

### Pi Agent 內部
- CHANGELOG.md — 每次更新的設計決策
- docs/ — 官方文件
- examples/ — 程式碼範例
- wiki/entities/pi-agent/ — 我們的分析

### 外部資源
- LLM 基礎：Andrej Karpathy 的 YouTube 系列
- Agent 概念：Lilian Weng 的部落格
- Framework 文件：LangChain / AutoGen / CrewAI 官方文件

## 🔄 學習迴路

每個學習週期：
1. **研究**：讀文件、看更新、找資料
2. **分析**：理解為什麼這樣設計
3. **實作**：動手玩、寫 demo
4. **記錄**：更新 wiki、寫心得
5. **連結**：跟已知概念串連

## 🔗 相關頁面

### Pi Agent 核心
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Agent runtime（agentic loop、session tree、compaction）
- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought]] — 讓模型「先想再答」的推理技術
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — 模型資源分配與限制
- [[wiki/concepts/pi-project-workspace-model|Pi Project Workspace Model]] — 專案工作區模型

### Extension 與排程
- [[wiki/concepts/agent-extension-installation|Agent Extension 安裝]] — 安裝位置與方式
- [[wiki/entities/pi-loop-scheduler|pi-loop-scheduler]] — Cron Job 與排程系統
- [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]] — 即時 Web Dashboard

### 架構與 Framework
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種核心工作流模式
- [[wiki/entities/langgraph|LangGraph]] — Graph-based agent runtime
- [[wiki/entities/tau|tau]] — Hugging Face 的 Pi Python port

### 學習追蹤
- [[wiki/topics/pi-agent-learning/progress|📊 學習進度]] — 追蹤每次學習成果

---

## 標籤

#ai-agent #learning #roadmap
