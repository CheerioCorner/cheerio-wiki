---
title: Agentic AI
type: concept
created: 2026-08-10
updated: 2026-08-10
tags: [ai-agent, agentic, autonomy, tool-use, loop, memory]
topics: [ai-agent]
canonical: concepts/agentic-ai
---

# Agentic AI

> 具備自主性、能使用工具、跨步驟執行任務的 AI 系統。核心公式：**LLM + Tools + Loop**。

## 核心定義

```
Agentic AI = LLM + Tools + a Loop
```

Agent 不是「更大的模型」，而是「讓模型能做事的包裝層」。模型負責推理，agent 負責：
1. 決定做什麼（reasoning）
2. 執行操作（tool use）
3. 觀察結果（observation）
4. 決定下一步（loop）

---

## 四大支柱

| 支柱 | 職責 | 關鍵問題 |
|------|------|---------|
| **Harness** | 執行環境、工具註冊、事件處理 | 「agent 在什麼環境中運作？」 |
| **Loop** | agentic 迴圈、停止條件、錯誤處理 | 「agent 何時開始、何時結束？」 |
| **Memory** | 狀態持久化、對話歷史、跨會話記憶 | 「agent 如何記住東西？」 |
| **Tools** | 外部能力擴展（檔案、API、shell） | 「agent 能做什麼？」 |

### 架構圖

```
使用者輸入
    ↓
┌─────────────────────────────────────┐
│           Harness（執行環境）         │
│  ┌─────────────────────────────┐   │
│  │         Loop（迴圈）         │   │
│  │  ┌─────┐  ┌─────┐  ┌─────┐ │   │
│  │  │ LLM │→│Tools│→│ Observe│ │   │
│  │  └─────┘  └─────┘  └─────┘ │   │
│  │         ↑           │       │   │
│  │         └───────────┘       │   │
│  └─────────────────────────────┘   │
│                                     │
│  Memory: 對話歷史 / Session / State  │
└─────────────────────────────────────┘
    ↓
輸出結果
```

---

## 設計模式

### 1. Minimal Agent Philosophy（減法哲學）

> 「自主 agent = LLM + tools + a loop，其餘都是鍋。」— Mario Zechner

| 不做 | 替代方案 |
|------|---------|
| 無 MCP | CLI + README 透過 `bash` 按需載入 |
| 無 sub-agents | 用 `bash` 自我呼叫 |
| 無 plan mode | 用 `PLAN.md` 文件替代 |
| 無 permission popups | 「安全劇場」（security theater） |
| 無 maxSteps | 迴圈自然結束 |

**核心洞察**：前沿模型已被 RL 訓練得足夠理解「編碼 Agent」是什麼，不需要 10,000 token 的系統提示詞。

### 2. Agent Durability Patterns（狀態持久化）

| 路徑 | 複雜度 | 恢復能力 | 適合場景 |
|------|--------|---------|---------|
| **無持久化**（While loop + JSON） | ⭐ | ❌ | 30 秒內完成的 tool loop |
| **Checkpoint**（定期存檔） | ⭐⭐ | ⭐⭐ | 長時間任務、可容忍部分重來 |
| **Full Persistence**（event sourcing） | ⭐⭐⭐ | ⭐⭐⭐ | 需要完整審計軌跡 |

### 3. Loop vs Graph Engineering

| 模式 | 架構 | 適用場景 |
|------|------|---------|
| **Loop** | while loop + LLM call | 簡單順序任務、tool use |
| **Graph** | DAG + 狀態機 | 複雜工作流、多分支、需要 human-in-the-loop |

### 4. Context Management

| 策略 | 說明 |
|------|------|
| **Smart Zone** | ~140k tokens 的最佳工作區 |
| **Compaction** | 壓縮對話歷史保留關鍵資訊 |
| **Session Tree** | 跨 session 的狀態傳遞 |

---

## Agent 框架比較

| 框架 | 核心特色 | 語言 | 適用場景 |
|------|---------|------|---------|
| **Pi Agent** | Minimal philosophy、extension 系統 | TypeScript | 個人開發、CLI 工具 |
| **Hermes Agent** | 40+ 工具、跨會話記憶、動態 Skill | Python | 自我改進 agent |
| **Waku Agent** | Local-first、readable blueprint | Python | 教學、理解架構 |
| **LangGraph** | Graph-based、Pregel model | Python | 複雜多步驟工作流 |
| **Tau** | Pi 的 Python port | Python | 輕量級 agent |
| **Omnigent** | Databricks meta-harness | Python | Multi-agent orchestration |

---

## 關鍵概念

### Tool Use

Agent 的能力上限取決於它能使用的工具：

| 工具類型 | 範例 | 用途 |
|---------|------|------|
| **檔案操作** | read, write, edit | 讀寫本地檔案 |
| **Shell 執行** | bash | 執行任何命令 |
| **API 呼叫** | HTTP client | 存取外部服務 |
| **資料庫** | SQL client | 資料查詢與修改 |
| **瀏覽器** | Puppeteer | 網頁互動 |

### Memory 類型

| 類型 | 儲存位置 | 生命週期 |
|------|---------|---------|
| **Working Memory** | Context window | 單次 session |
| **Session Memory** | 檔案/DB | 跨 session |
| **Long-term Memory** | Vector DB | 永久 |
| **Procedural Memory** | Skill 文件 | 學到的能力 |

### Safety 與 Control

| 機制 | 說明 |
|------|------|
| **Human-in-the-loop** | 關鍵操作需人類確認 |
| **Sandbox** | 限制 agent 可存取的資源 |
| **Rate limiting** | 限制 API 呼叫頻率 |
| **Audit trail** | 記錄所有操作 |

---

## 相關頁面

### 核心概念
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 狀態持久化的三種路徑
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種核心工作流模式
- [[wiki/concepts/meta-harness|Meta-Harness]] — 對 harness 本身的再抽象/最佳化層
- [[wiki/concepts/agent-extension-installation|Agent Extension Installation]] — 安裝位置與方式

### Agent 框架
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi agent 核心 runtime 🛠️
- [[wiki/entities/hermes-agent|hermes-agent]] — Nous Research 自我改進 AI agent 🛠️
- [[wiki/entities/waku-agent|waku-agent]] — Local-first personal AI agent
- [[wiki/entities/tau|tau]] — Hugging Face 的 Pi Python port
- [[wiki/entities/langgraph|LangGraph]] — Graph-based agent runtime
- [[wiki/entities/omnigent|Omnigent]] — Databricks meta-harness

### Topics
- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 核心概念與框架比較
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — 協定、語言工具、企業治理
