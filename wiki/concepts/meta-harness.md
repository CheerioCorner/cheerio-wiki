---
title: Meta-Harness
type: concept
created: 2026-07-13
updated: 2026-07-30
sources: 3
tags: [harness, optimization, orchestration, multi-agent, llm, research-project]
collection: concepts
topics: [meta-systems]
canonical: concepts/meta-harness
---

# Meta-Harness

> Meta-Harness 是「對 harness 本身的再抽象/最佳化層」——有兩個獨立但共享核心隱喻的主流定義。

---

## 核心概念鍊

```
被測試的元件 → Harness（包裝層） → Meta-Harness（對 harness 的 meta 層）
```

傳統 **harness（鞍具/測試封裝）** 是圍繞一個元件寫的包裝程式碼，負責設定環境、餵入輸入、執行、收集結果、清理環境。Meta-Harness 則是圍繞 harness 本身的層。

---

## 兩種定義

### 定義 A：學術研究 — Harness 自動演化（Stanford IRIS Lab）

出自 [*Meta-Harness: End-to-End Optimization of Model Harnesses*](https://arxiv.org/abs/2603.28052)（Lee et al., 2026）

**問題：** LLM 系統的表現不只取決於模型權重，也取決於 **harness**——圍繞固定基礎模型的程式碼，決定「存什麼、取什麼、秀什麼」。但這些 harness 長期是手寫的。

**解法：** 用一個**演化搜尋迴圈**自動尋找最優的 harness：

```
(1) Proposer（coding agent）讀取完整檔案系統 —
     所有候選 harness 原始碼 + 執行記錄 + 分數
(2) Proposer 提出新的 harness 候選
(3) 在 held-out tasks 上評估
(4) logs 存回檔案系統，loop 重複
```

**關鍵創新（vs 傳統方法）：**

| 面向 | 傳統方法（Self-Refine, OPRO, TextGrad…） | Meta-Harness |
|---|---|---|
| 歷史壓縮方式 | 摘要 / scalar score / sliding window | **完整檔案系統**（原始碼 + traces + scores） |
| 可用 context 量 | ≤ **26K tokens** | ≤ **10M tokens**（proposer 用 grep/cat 自行探索） |
| 診斷能力 | 從分數猜原因 | 可 trace 到特定 harness 決策 |

**成果：**
- Text Classification：比 ACE 方法 **+7.7 分**，4× 更少 context tokens
- Math Reasoning（IMO 級）：5 個 unseen 模型平均 **+4.7 分**
- TerminalBench-2：Claude Opus 4.6 **#2**（76.4%），Claude Haiku 4.5 **#1**（37.6%）

**延伸專案：**
- `harness-forge` — 將 Meta-Harness 實作為 Claude Code skill（尚未建立 Wiki entity 頁）
- `SuperagenticAI-metaharness` — Python library + CLI（尚未建立 Wiki entity 頁）
- 「Don't Train the Model, Evolve the Harness」 — 應用於 Harvey Legal Agent Benchmark

---

### 定義 B：業界工具 — Multi-Agent Orchestration Layer

> 代表性實作：OmniAgent、MindStudio

**問題：** 沒有單一 AI agent（Claude Code、Codex CLI、Gemini…）在所有任務上最強。手動切換會中斷 context、遺失狀態。

**解法：** 在所有 agent 之上加一層 **orchestration layer**，稱為 meta-harness：

```
使用者輸入
    │
    ▼
┌─────────────────────┐
│    Meta-Harness     │ ← 路由、context 管理、錯誤處理
│   (Orchestrator)    │
└──────┬──────┬──────┘
       │      │
       ▼      ▼
  Claude Code  Codex CLI  ...
```

**核心功能：**
- **Agent selection** — 判斷任務適合哪個 agent
- **Context management** — 跨 agent 的共享/轉換 context
- **Session continuity** — 不同 agent 輪流上場，使用者感覺同一場對話
- **Parallel execution** — 同時跑多個 agents
- **Failure handling** — 主要 agent 失敗時 fallback
- **Output normalization** — 統一不同 agent 的回應格式

**與單純 model switching 的區別：**
- Model switching = dropdown（手動選模型）
- Meta-harness = 有邏輯的 orchestration（自動 routing + context 管理 + 錯誤處理）

---

## 兩種定義的關係

它們共享同一個核心隱喻，只是操作對象不同：

| | 定義 A（Stanford） | 定義 B（OmniAgent） |
|---|---|---|
| 操作的對象 | LLM 的程式碼 harness | 完整的 AI coding agents |
| 目標 | 自動尋找最優的 harness 程式 | 讓多個 agents 協同工作 |
| 「meta」的意義 | meta-optimization | meta-orchestration |

兩者並不互斥：你可以用定義 A 的方法去最佳化定義 B 中的某個 agent harness，或者用定義 B 的方法協調多個定義 A 的演化迴圈。

---

### 定義 B 的具體實作

| 實作 | 開發者 | 特色 |
|---|---|---|
| `OmniAgent` | MindStudio | 最早提出 meta-harness 作為多 agent orchestration 的概念；尚未建立 Wiki entity 頁 |
| [[wiki/entities/omnigent]] | Databricks | 開源（Apache 2.0），強調 security policies + 即時協作，roadmap 包含 GEPA 整合 |

---

### 業界實作案例盤點（2026-09 補充）

> 來源：[[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]（177 筆來源）

以下框架直接展示了定義 B 中 orchestration layer 的六項核心功能（agent selection / context management / session continuity / parallel execution / failure handling / output normalization）的業界實作方式：

| 框架 | Agent Selection | Context Management | Parallel Execution | Failure Handling | 觀測性分級 |
|---|---|---|---|---|---|
| [[wiki/entities/openai-agents-sdk\|OpenAI Agents SDK]] | Handoff 工具化（`transfer_to_<agent_name>`） | Session History 無縫移轉 + ResponsesCompaction | 去中心化 Handoff + 經理委託雙軌 | Hop Counter 安全熔斷 | 第一級 |
| [[wiki/entities/deepseek-harness\|DeepSeek Harness]] | Hierarchical Supervisor-Worker | Append-Only 日誌 + Event Stream | Spawn/Fork + Ralph Mode + 並行 DAG | Replay/Fork 重放 | 第一級 |
| [[wiki/entities/langgraph\|LangGraph]] | StateGraph 拓撲硬性約束 | Reducer 函數合併 State Channels + Checkpointer | 嵌套子圖 | Time Travel + Resume | 第一級 |
| [[wiki/entities/vercel-eve\|Vercel Eve]] | Subagent 目錄結構定義 | Event Log + 確定性重放 | Hierarchical Subagents | Suspend/Resume（零計算成本） | 第一級 |
| [[wiki/entities/mastra\|Mastra]] | Workflow-as-Tool 包裝 | 觀察式記憶（OM）5x-40x 壓縮 | 併行 DAG + Handoff + Hierarchical | Workflow suspend/resume | 第一級 |
| [[wiki/entities/pydantic-ai\|Pydantic AI]] | FunctionTool 委託 | DBOS Transaction Checkpoints | 耐用子工作流 | DBOS 重播 Resume | 第一級 |
| [[wiki/entities/autogen\|AutoGen]] | Swarm（LLM 驅動動態分派） | 非同步 Actor Mailbox | Swarm + GraphFlow + Debate | UserProxyAgent 人機協同 | 第二級 |
| [[wiki/entities/semantic-kernel\|Semantic Kernel]] | SelectionStrategy（LLM 判斷下一位發言者） | ChatHistory 強型別 | AgentGroupChat | TerminationStrategy + MaximumIterations | 第二級 |
| [[wiki/entities/crewai\|CrewAI]] | Role-Play Persona 分派 | 自動化 Embedding + 向量 RAG | Sequential/Hierarchical Process | 有限（依賴第三方 OTel） | 第三級 |
| [[wiki/entities/smolagents\|smolagents]] | Orchestrator-Worker 中樞調度 | Private History 隔離 | 多 Specialist 子代理並行 | 沙箱隔離 | 第一級 |

**觀察：** 業界已從「無狀態單 agent」演進到「多 agent orchestration」的完整光譜。第一級觀測性框架（LangGraph、OpenAI SDK、DeepSeek Harness）已將 OTel 追蹤 + 耐用執行視為生產級必備，而非可選功能。這印證了 Meta-Harness 定義 B 的核心論點：orchestration layer 不只是「加一層路由」，而是需要完整的可觀測性、狀態持久化與失敗恢復基礎設施。

## 相關頁面

- `harness-concept` — 傳統測試 harness 與 model harness 的基本概念（尚未建立 Wiki concept 頁）
- [[wiki/concepts/minimal-agent-philosophy]] — Pi 的 minimal agent 哲學，與 meta-harness 的「加法」取徑形成對比
- [[wiki/entities/plannotator]] — 另一種 agent 視覺化審閱工具

## 我們的研究定位

> **Meta-Harness 是我們的研究專題。** 這是我們正在探索的一個重要方向，未來會持續收集相關的概念、工具和實作案例。

### 已收集的樹苗

| 樹苗 | 來源 | 成長階段 | 備註 |
|------|------|---------|------|
| [[wiki/entities/omnigent]] | Databricks | 🌱 種子期 | 開源 meta-harness，但目前不支援 Windows |
| [[wiki/entities/openai-agents-sdk]] | OpenAI | 🌳 成熟 | 極低抽象，Handoff 工具化是最輕量的多 agent 協作 |
| [[wiki/entities/deepseek-harness]] | DeepSeek | 🌱 開發者預覽 | 一切皆插件，支援跨框架連接器 |
| [[wiki/entities/langgraph]] | LangChain | 🌳 成熟 | 顯式狀態圖，Checkpointer 端到端耐用 |
| [[wiki/entities/vercel-eve]] | Vercel | 🌱 開源 Beta | 檔案系統即 Agent，Slack 審批門禁 |
| [[wiki/entities/mastra]] | Mastra (Gatsby) | 🌳 成熟 | 觀察式記憶（OM）SOTA，Workflow-as-Tool |
| [[wiki/entities/pydantic-ai]] | Pydantic | 🌳 成熟 | 型別安全 + DBOS 耐用執行 |
| [[wiki/entities/autogen]] | Microsoft | 🌳 成熟（→MAF） | Swarm/GraphFlow/Debate 多模式 |
| [[wiki/entities/semantic-kernel]] | Microsoft | 🌳 成熟 | 企業級 Plugin SDK，.NET/Python/Java |
| [[wiki/entities/crewai]] | CrewAI | 🌳 成熟 | 角色扮演高階抽象，可觀測性較弱 |
| [[wiki/entities/smolagents]] | Hugging Face | 🌳 成熟 | Code-first，AST 執行壓縮多步驟 |
| [[wiki/entities/herdr]] | herdr | 🌳 成熟 | 可觀測性工具（非 orchestration），DHH 使用 |

### 未來可能的樹苗

- Stanford IRIS Lab 的 Meta-Harness 論文實作（定義 A）
- MindStudio OmniAgent（定義 B）

### 研究方向

1. **理解兩種定義的差異與互補性** — 學術 vs 業界
2. **評估哪些工具適合我們的工作流** — 特別是 Windows 環境
3. **探索 meta-harness 與 Pi 的關係** — 加法取徑 vs 最小化哲學
4. **收集更多實作案例** — 建立比較分析

---

## 來源

- arXiv 論文：*Meta-Harness: End-to-End Optimization of Model Harnesses*（https://arxiv.org/abs/2603.28052）
- GitHub repo：stanford-iris-lab/meta-harness（https://github.com/stanford-iris-lab/meta-harness）
- Yoonho Lee 的說明頁：https://yoonholee.com/meta-harness/
- MindStudio 部落格：What is Meta-Harness for AI Agents（https://www.mindstudio.ai/blog/what-is-meta-harness-ai-agents-omniagent）
- Databricks Blog：Introducing Omnigent（https://www.databricks.com/blog/introducing-omnigent-meta-harness-combine-control-and-share-your-agents）
- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]（177 筆來源）— 定義 B 業界實作案例盤點