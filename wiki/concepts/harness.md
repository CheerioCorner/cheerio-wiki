---
title: "Harness — LLM 的驅動層"
type: concept
created: 2026-08-14
updated: 2026-09-05
sources: 3
tags: [harness, agent, agentic-loop, tool-calling, context-management, orchestration]
topics: [agent-architecture, meta-systems]
canonical: concepts/harness
notion: "https://app.notion.com/p/Harness-LLM-3bc5979e3a8c81f98e54eea5a2deeeea"
---

# Harness — LLM 的驅動層

> **成長階段：** 🌱 種子期
> **Tags：** 🔬 研究
> **最後更新：** 2026-08-14

---

## 一句話定義

**Harness 是包住單一 LLM、負責「驅動它做實事」的外殼/包裝層**——包括 agentic loop（讓模型能循環思考）、工具調度（讓模型能動手做）、權限控管（決定模型能做什麼）、context 管理（控制模型看到什麼）、session 記憶（讓模型跨步驟記住東西）。

模型本體（model）只負責推理和生成文字；harness 負責「讓這段推理落地成實際操作」。沒有 harness 的 LLM 只能聊天，有 harness 的 LLM 才能「做事」。

> 💡 多個 harness 的協同工作（multi-agent orchestration）是「上一層」的問題，見下方「分類層次」。

---

## Harness 負責的核心職責

| 職責 | 做什麼 | 沒有會怎樣 |
|------|--------|-----------|
| **Agentic Loop** | 決定「觀察→推理→行動→評估」的迴圈何時開始、何時停止 | 模型只能回一輪，多步驟任務做不成 |
| **Tool Calling / 工具調度** | 註冊可用工具（檔案讀寫、Shell、API），把模型的工具呼叫翻譯成實際操作 | 模型只能用嘴巴說，不能動手 |
| **Permission / 權限控管** | 決定哪些操作需要人類確認、哪些可以自動執行、sandbox 邊界在哪 | 危險操作直接跑、或什麼都要人批太慢 |
| **Context Management** | 控制模型看到哪些上下文（system prompt、歷史、文件片段）、壓縮、截斷、smart zone | 模型看太多會迷路、看太少會失憶 |
| **Memory / 記憶** | 工作記憶（當前 session）、長期記憶（跨 session）、程序性記憶（學到的 skill） | 每次都從零開始，跨步驟、跨任務無法累積 |

### 架構概覽

```
使用者輸入
    ↓
┌─────────────────────────────────────┐
│           Harness（驅動層）           │
│  ┌─────────────────────────────┐   │
│  │      Agentic Loop（迴圈）    │   │
│  │  ┌─────┐  ┌──────┐  ┌────┐│   │
│  │  │ LLM │→│ Tools │→│ Observe│  │   │
│  │  └─────┘  └──────┘  └────┘│   │
│  │         ↑           │       │   │
│  │         └───────────┘       │   │
│  └─────────────────────────────┘   │
│                                     │
│  Memory / Context / Permissions     │
└─────────────────────────────────────┘
    ↓
實際操作結果
```

---

## 主流產品的 Harness 取向比較

### 總覽表

| 面向 | Claude Code（Anthropic） | Codex CLI / ChatGPT（OpenAI） | GitHub Copilot（Microsoft） | Pi（earendil-works/pi，我們自建自用） |
|------|--------------------------|-------------------------------|---------------------------|-------------------------------------|
| **核心定位** | 終端機 agentic coding agent | 多平台 coding agent（CLI + IDE + 雲端） | IDE 嵌入 + 雲端 agent 平台 | 個人助理 + 多工具 orchestrator |
| **Agentic Loop** | 本地 while loop，自主決定迭代次數 | 本地 loop（CLI）+ 雲端 loop（Codex Web） | 雲端 loop 為主，IDE 輔助 | 本地 loop + skill-driven 工作流 |
| **工具調度** | MCP（Model Context Protocol）原生 | 內建工具集 + MCP 支援（Codex CLI） | Extensions SDK + Agent Skills + MCP | Extension System + Skills + Custom Tools |
| **權限控管** | Permission prompting（危險操作暫停詢問） | Codex CLI 三模式：suggest / auto-edit / full-auto（沙箱） | 雲端 sandbox 為主，本地較少限制 | AGENTS.md 規則 + 人類確認機制 |
| **Context 管理** | CLAUDE.md + Context Compaction（自動壓縮） | system prompt + 對話歷史 | Spaces + Custom Instructions + Memory（自動學習） | AGENTS.md + Wiki 知識庫 + Notion 花園 |
| **記憶** | 短期（session）為主，CLAUDE.md 為長期 | ChatGPT Memory（跨對話自動記憶） | Copilot Memory（從 repo 自動學習，Public Preview） | 多層記憶：Wiki + Notion + Session Files |
| **Sub-agent** | 支援 Subagent 動態派生與隔離執行（可用 prompt 工作流實現多 agent 並行審查） | Codex Web 支援 background task（雲端） | Cloud Agent + Custom Agents | Subagents + Round-table（多 AI 協作） |
| **設計哲學** | **開發者優先**——terminal-centric，MCP 開放生態 | **使用者優先**——多平台無縫體驗，降低門檻 | **平台優先**——深度整合 GitHub 生態（Issue → PR） | **生活優先**——跨域 orchestrator，不限 coding |

### 各家強項分析

**Claude Code 的強項：MCP 原生 + 本地自主性**
- MCP 是目前最開放的 tool protocol 標準，第三方工具容易接入
- 本地執行意味著速度快、隱私好、不依賴雲端
- Hooks 系統（PreToolUse / PostToolUse）讓 harness 本身可被擴展
- Subagent 動態派生支援多視角並行審查

**Codex CLI / ChatGPT 的強項：多平台統一 + 沙箱安全**
- 同一個 agent 可以在 CLI、IDE、Desktop App、Web 上跑，context 共享
- Codex CLI 的沙箱模式（sandbox）提供較強的安全隔離——full-auto 模式下 agent 在受限環境中自主執行
- ChatGPT 的 Memory 功能讓跨對話記憶最自然（自動學習，不用手動維護）

**GitHub Copilot 的強項：GitHub 生態深度整合 + 雲端 scale**
- 從 Issue 到 PR 的完整流程（Copilot Workspace）是獨家優勢
- Extensions SDK 讓第三方 agent 可以接入 Copilot 平台，成為「agent 的 agent」
- Cloud Agent 在 GitHub 基礎設施上跑，天然整合 repo 權限、CI/CD

**Pi（earendil-works/pi）的強項：跨域 orchestration + 開放組合**
- 不只 coding——工作追蹤、知識管理、多 AI 協作都包
- Extension 系統讓任何人可以擴展 Pi 的能力
- Wiki 知識庫 + Notion 花園提供最結構化的長期記憶
- Round-table（多 AI 圓桌會議）是獨家的 multi-model 協作機制

### 待查證

- OpenAI Codex CLI 的完整沙箱機制細節（README 資訊有限，需要測試實證）
- GitHub Copilot Memory 的實際學習範圍與限制（Public Preview，文件較少）
- 各家 context window 的實際壓縮策略（Claude Code 有 Compaction，其他家文件不明確）

---

## 與 Model Runtime 的邊界

Harness 是「驅動 LLM 做實事的外殼」，但它**不是模型伺服器本身**。兩者有明確的職責切分：

| 面向 | Harness | Model Runtime |
|------|---------|---------------|
| **代表實作** | Claude Code、Pi、Copilot | llama-server、vLLM、Ollama |
| **職責** | Agentic loop、工具調度、權限、context | 模型載入、推論執行、KV cache 管理 |
| **生命週期** | 綁定 session / 專案 | 可跨 session 常駐（singleton）|
| **通訊方式** | HTTP API / subprocess | GPU / VRAM 直接操作 |

**關鍵原則**：Harness 不應綁定或打包 GGUF 權重檔，模型生命週期交由獨立的 Runtime 服務管理，Harness 僅透過標準 API 介面溝通。Runtime 應以 singleton + reference counting + health check 方式管理，生命週期綁在 harness process 而不是單次 invoke。

> 詳見 [[wiki/discussions/local-model-runtime-in-w074-architecture|Local Model Runtime 在 W-074/W-080 中的定位]] 與 [[wiki/entities/llama-cpp|llama.cpp]]

---

## 分類層次：三種 Harness

花園中提到的 harness 可以分成三個層次：

### 1. Coding Agent Harness（基礎層）

**定義：** 包住單一 LLM、讓它能執行 coding 任務的驅動層。

**核心組件：** Agentic Loop + Tool Calling + 權限控管 + Context Management + Session 記憶

**代表實作：**
- Claude Code（Anthropic）— MCP 原生，terminal-centric
- Codex CLI（OpenAI）— 多平台，沙箱安全
- Pi（earendil-works/pi）— 跨域 orchestrator
- Hermes Agent — 自我改進 agent

> 這是「概念根」——本頁面主要定義的層次。

### 2. Meta-Harness（中間層）

**定義：** 對 coding agent harness 本身的再抽象/最佳化。有兩種**同名異義**的主流取向：

#### 2a. 治理/組合型 Meta-Harness（harness of harnesses）

在多個現成 harness 之上加一層**統一治理**——路由、成本控制、沙箱隔離、跨 agent 協作。核心問題是「如何管理多個 agent」。

| 面向 | 說明 |
|------|------|
| **操作對象** | 完整的 AI coding agents |
| **目標** | 讓多個 agents 協同工作 |
| **「meta」的意義** | meta-orchestration（元编排） |
| **代表實作** | [[wiki/entities/omnigent]]（Databricks 開源）、MindStudio OmniAgent |

#### 2b. 自動演化/優化型 Meta-Harness（evolve the harness）

用一個**演化搜尋迴圈**自動尋找最優的 harness 程式碼。核心問題是「如何讓 harness 自己變好」。

| 面向 | 說明 |
|------|------|
| **操作對象** | LLM 的程式碼 harness |
| **目標** | 自動尋找最優的 harness 程式 |
| **「meta」的意義** | meta-optimization（元最佳化） |
| **代表實作** | Stanford Meta-Harness 論文（[[https://arxiv.org/abs/2603.28052|Lee et al., 2026]]）、harness-forge |

> 兩者並不互斥：可以用定義 2b 的方法去最佳化定義 2a 中的某個 agent harness，或用定義 2a 的方法協調多個定義 2b 的演化迴圈。

### 3. Multi-Agent Orchestration（上層）

**定義：** 多個 harness（或 meta-harness）協同工作的系統——涉及 agent 間的通訊、任務分配、結果整合。

**代表實作：**
- Omnigent Server — 跨 agent session 協作
- Pi（earendil-works/pi）的 round-table — 多 AI 圓桌討論
- Claude Code 的 Subagent — 並行審查

> 這是 [[wiki/topics/ai-agent]] 研究專題的延伸。

### 三層關係圖

```
Multi-Agent Orchestration（上層）
    ↑ 多個 harness 協同
Meta-Harness（中間層）
    ↑ 治理/組合 或 自動演化/優化
Coding Agent Harness（基礎層）  ← 本頁面定義
    ↑ 包住 LLM
LLM / Model 本體
```

---

## 與既有花園內容的關係

本頁面是**概念根**——定義「harness 是什麼」。以下既有內容是「具體實例」或「延伸研究」：

| 既有內容 | 類型 | 與本頁面的關係 |
|----------|------|---------------|
| [[wiki/entities/omnigent]] | 種子（🌱） | Meta-harness 的具體實例（Databricks 開源，治理/組合型） |
| [[wiki/concepts/meta-harness]] | 概念 | 對 harness 的再抽象，本頁面的「上一層」 |
| [[wiki/concepts/agentic-ai]] | 概念 | 與本頁面互補——agentic AI 是整體概念，harness 是其中的驅動層組件 |
| [[wiki/entities/claude-code]] | 種子（🌱） | Coding agent harness 的具體實例 |
| [[wiki/entities/github-copilot]] | 種子（🌱） | Coding agent harness 的具體實例 |
| [[wiki/concepts/continual-harness]] | 概念 | Harness 的自我改進變體（Prime Agent） |

**內容邊界：**
- **比較與定義** → 放在本頁面（概念根）
- **個別產品的深入研究** → 留在各自的種子頁面
- **Meta-harness 的最佳化/orchestration** → 留在 [[wiki/topics/meta-systems]] 研究專題
- **避免重複**：本頁面不重複 Omnigent 的三大支柱、不重複 Claude Code 的 Hooks 細節——用連結指向對應種子

---

## 下一步

- [ ] 補充安全/沙箱隔離面向（prompt injection 防護、容器沙箱、rm -rf 防護機制）
- [ ] 收集 harness 評測基準（SWE-bench、TerminalBench）的對應數據
- [ ] 調研通訊協定（MCP、A2A）在 harness 間的角色
- [ ] 調研 context 隔離/防污染（subagent 獨立 context window）的實作方式
- [ ] 考慮建立視覺地圖（三層 harness 關係圖）

## 連結

- **花園內：**
  - [[wiki/entities/omnigent]] — Meta-harness 實例（治理/組合型）
  - [[wiki/concepts/meta-harness]] — Meta-harness 研究專題
  - [[wiki/concepts/agentic-ai]] — Agentic AI 概念根
  - [[wiki/entities/claude-code]] — Claude Code 種子
  - [[wiki/entities/github-copilot]] — GitHub Copilot 種子
  - [[wiki/concepts/continual-harness]] — Continual Harness（自我改進）
  - [[wiki/topics/ai-agent]] — AI Agent 研究專題
- **外部來源：**
  - [Stanford Meta-Harness 論文](https://arxiv.org/abs/2603.28052)
  - [Claude Code 文件](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code)
  - [OpenAI Codex CLI](https://github.com/openai/codex)
  - [GitHub Copilot 文件](https://docs.github.com/en/copilot)
  - [MCP 官方](https://modelcontextprotocol.io/)

## 來源

- [[wiki/sources/2026-07-13-omnigent-meta-harness-databricks|Omnigent Meta-Harness]] — Databricks 開源治理/組合型 meta-harness
- Stanford Meta-Harness 論文 — [[https://arxiv.org/abs/2603.28052|Lee et al., 2026]]，自動演化/優化型 meta-harness
