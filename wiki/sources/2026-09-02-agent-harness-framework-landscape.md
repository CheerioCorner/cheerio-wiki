---
title: "AI Agent Harness/Framework 全貌盤點 — 四大廠路線 + Python/TS 生態圈主流框架深度比較"
type: source
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [agent-harness, agent-framework, langgraph, pydantic-ai, openai-agents-sdk, crewai, smolagents, mastra, vercel-eve, autogen, semantic-kernel, deepseek-harness, codex, observability, multi-agent]
topics: [coding-agent, agent-architecture, agent-runtime-implementations]
canonical: sources/2026-09-02-agent-harness-framework-landscape
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260831-001/research-report.md
---

# AI Agent Harness/Framework 全貌盤點

> 來源：Gemini Deep Research（NotebookLM），177 筆來源，2026-08-31 產生
> 研究主題：現有 AI Agent Harness/Framework 全貌盤點，以四大廠牌路線為核心，涵蓋 Python 與 TypeScript 兩大語言生態圈的主流 Agent Framework

---

## 研究背景

本研究是 W-074（Cheerio 個人 AI 助理架構，傾向自建 harness）開工前的業界現況盤點，用於評估與對照。研究涵蓋四大廠牌路線與兩大語言生態圈的主流框架，重點比較三個技術維度：**架構設計**（狀態持久化）、**可觀測性能力**（tracing/logging）、**多 Agent 協作能力**（角色分工、工作流編排）。

---

## 四大廠牌路線

### 1. OpenAI Codex CLI/Agent

**架構：** 固定 Agent Loop（展開型 ReAct 迴圈），雲端 Docker 沙箱，GPT-5.2 Codex 驅動。CLI 暴露為 MCP 伺服器，使用 OpenAI Agents SDK 進行編排 `[§1]`。

**可觀測性：** 內建 OTel GenAI 語意追蹤，官方 Traces 儀表板，可導出至 OpenObserve/Braintrust/Datadog。支援 `ReasoningItem` 追蹤推理思維鏈 `[§1]`。

**多 Agent：** 兩種核心模式 — 去中心化 Handoff（`transfer_to_<agent_name>` 工具化，含 Hop Counter 防漂移）+ 經理委託（`agent.as_tool()`，子代理獨立 context）。`spawn_agent` 支援動態派生 `[§1]`。

**記憶：** 原生 Context 壓縮（GPT-5.2 訓練的遞歸摘要）、Session 接口（SQLite/SQLAlchemy）、長期記憶與對話分離（需顯式工具保存） `[§1]`。

### 2. DeepSeek Harness (dsh)

**架構：** 「一切皆插件」—— Cordis 響應式微內核，無特權核心。四種運行模式：Standard、Code（PTC）、Minimal、Creative `[§2]`。

**可觀測性：** Append-Only 會話日誌，Trajectory View（軌跡檢視圖），支援 Replay 與 Fork 分叉。安全管道：Hooks → Approvals → Permission Checks → Sandbox → Timeout `[§2]`。

**多 Agent：** Spawn & Fork 原語、Workflows（動態 DAG）、Ralph Mode（輪流執行）。本質是 Hierarchical Supervisor-Worker 模式。支援跨框架連接器（可將 Claude Code/Codex 封裝為子 Agent 插件） `[§2]`。

**當前侷限：** 尚未實現真正的 Swarm 協作，決策權仍高度集中於 Parent Agent `[§2]`。

### 3. Vercel 陣營（AI SDK + Eve）

**Vercel AI SDK：** 輕量無狀態，`ToolLoopAgent` 類別實作標準 ReAct 迴圈。AI Gateway 支援跨模型路由與 Failover `[§3]`。

**Vercel Eve（2026/06 開源 Beta）：** 「檔案系統即 Agent」—— 約定優於配置，`agent/` 目錄結構自動編譯為 Manifest。耐用執行基於 Vercel Workflow（確定性重放）。安全沙箱：Firecracker microVM `[§3]`。

**可觀測性：** AI SDK 原生 OTel 整合；Eve 部署於 Vercel 後自動啟用「Agent Runs」面板（Trace 樹 + Token 成本追蹤） `[§3]`。

**多 Agent：** AI SDK 不內建多代理編排（需外部協調器）；Eve 原生支援 Hierarchical Subagents（`agent/subagents/` 目錄）+ Slack 審批門禁（`approval: always()`，暫停時零計算成本） `[§3]`。

### 4. Microsoft 陣營（AutoGen + Semantic Kernel + MAF）

**Semantic Kernel：** 企業級 Plugin SDK，LLM 作為 Orchestrator 動態調用企業代碼/API。強型別、YAML 宣告式定義，原生支援 .NET/Python/Java `[§4]`。

**AutoGen (v0.4/AG2)：** 非同步事件驅動 Actor Model。三層架構：AgentChat（高階 API）→ Core（事件驅動 Actor）→ Extensions（LLM 客戶端）。主要針對 Python `[§4]`。

**Microsoft Agent Framework (MAF)：** 2026/04 發布 1.0，統一 SK 的企業安全基礎與 AutoGen 的多代理編排理念 `[§4]`。

**SK 可觀測性：** `IFunctionInvocationFilter` 攔截器（Plugin 函數執行前後強制攔截） `[§4]`。

**SK 多 Agent：** `AgentGroupChat` + 顯式策略（SelectionStrategy + TerminationStrategy） `[§4]`。

**AutoGen 多 Agent：** GroupChat、Swarm 模式（去中心化 LLM 驅動）、GraphFlow（DAG）、Debate 模式 `[§4]`。

---

## Python 生態圈五大框架

### 5. LangChain/LangGraph

**架構：** 顯式狀態圖（Nodes + Edges + 全域 State），Checkpointer 在每個 Super-step 端到端持久化。支援 Postgres/Redis/MongoDB/DynamoDB（350KB 以上自動分流至 S3）。原生支援 Time Travel、Interrupts、Resume `[§5]`。

**可觀測性：** LangSmith 深度整合，可圖形化剖析節點跳轉與 State 變遷 `[§5]`。

**多 Agent：** 完全建立在 StateGraph 拓撲上，Reducer 函數合併 State Channels。嵌套子圖實現上下文隔離。邏輯嚴謹但開發者需手寫大量狀態轉移 `[§5]`。

### 6. Pydantic AI

**架構：** 端到端型別安全（`Agent[Deps, Output]`），依賴注入（`RunContext[Deps]`），pydantic-graph 有限狀態機。與 DBOS 原生整合——自動建立 Transaction Checkpoints，crash 後從 checkpoint 重播 Resume `[§5]`。

**可觀測性：** `Agent.iter` 迭代器逐節點驅動，Pydantic Logfire（基於 OTel）原生綁定 `[§5]`。

**多 Agent：** Hierarchical Supervisor-Worker，主 Agent 透過 `FunctionTool` 委託子 Agent。DBOS 環境下子 Agent 自動實例化為耐用子工作流 `[§5]`。

### 7. OpenAI Agents SDK（Python/TS）

**架構：** 極低抽象，五大核心原語：Agent、Handoff、Guardrail、Session、Tracing。`OpenAIResponsesCompactionSession` 裝飾器在背景自動調用雲端 `responses.compact` API 壓縮歷史 `[§5]`。

**可觀測性：** OTel GenAI 語意標準，可視化推理模型 Thinking/Reasoning Spans `[§5]`。

**多 Agent：** 雙軌並行 — 去中心化 Handoff（`transfer_to_<agent_name>`，含 Hop Counter）+ 經理委託（`agent.as_tool()`）。Session History 無縫移轉 `[§5]`。

### 8. smolagents（Hugging Face）

**架構：** Code-first——LLM 直接輸出 Python 代碼（AST 解析執行），將 Multi-step loops 壓縮在單次 LLM 往返中完成。本地 `LocalPythonExecutor` 僅 best-effort，生產環境強制要求 Docker/E2B/Modal 沙箱 `[§5]`。

**可觀測性：** 整合 Arize Phoenix/AX（OTel 衍生），支援 `agent.replay()` 本機重放 `[§5]`。

**多 Agent：** Orchestrator-Worker 模式，子 Agent 內部 step-by-step 日誌被物理隔絕（Private History），Orchestrator 僅接收最終 JSON `[§5]`。

### 9. CrewAI

**架構：** 高階角色扮演（Agents + Tasks + Crews），自動化 Embedding 記憶系統（語意切片 + 向量資料庫 RAG） `[§5]`。

**可觀測性：** 預設較黑盒，主要關注任務執行進度。微觀 LLM trace 需外接 OTel 平台（Logfire/Langfuse） `[§5]`。

**多 Agent：** Sequential 或 Hierarchical Process 串聯 Task 管道。基於業務化的 Task 依賴鏈，適合固定可預測的流程 `[§5]`。

---

## TypeScript 生態圈

### 10. Mastra

**架構：** 全包式平台（Batteries-Included），確定性工作流引擎（`.then()` 串行 / `.parallel()` 併行 / `.branch()` 條件 / `.doWhile()` 循環）。Platform-agnostic（Node/Cloudflare Workers/自建） `[§6]`。

**觀察式記憶（Observational Memory, OM）：** 獨特技術亮點——Observer（30k 觸發，5x-40x 壓縮）+ Reflector（40k 重構，去除失效事實）。在 LongMemEval 達 94.87% SOTA。支援 Thread 與 Resource 雙重 Scope `[§6]`。

**可觀測性：** Mastra Studio 本地 Web UI（`localhost:4111`）+ Evals 測試框架（LLM-as-a-judge + CI/CD 回歸測試） `[§6]`。

**多 Agent：** 順序 + Handoff + Hierarchical 混合。最獨特：將整套 Workflow 包裝成普通 Tool 交給 Supervisory Agent 呼叫 `[§6]`。

### 11. LangChain.js / LangGraph.js

與 Python 版功能對齊：顯式狀態圖、Checkpointer（Postgres/Redis/DynamoDB with S3 分流）、Store 長期記憶、LangSmith 整合 `[§6]`。

### 12. OpenAI Agents SDK (TS)

與 Python 版功能對齊：極簡原語、Session 接口、`OpenAIResponsesCompactionSession` 歷史自動壓縮、OTel 追蹤 `[§6]`。

---

## 可觀測性分級（綜合比較）

### 第一級：完整過程可稽核

> 完整記錄整棵執行樹（Trace Spans），包含推理思維鏈、狀態變遷、歷史更迭。支援回溯/分叉/重放。

| 框架 | 稽核深度 | 觀測平台 |
|------|---------|---------|
| LangGraph/LangGraph.js | Checkpointer 端到端快照 + State 變遷 | LangSmith |
| DeepSeek Harness | Append-Only 日誌 + Trajectory View | 本機 Web UI（支援 Replay/Fork） |
| OpenAI Agents SDK | OTel GenAI + ReasoningItem 追蹤 | OpenAI 官方 Dashboard |
| Pydantic AI | `Agent.iter` 逐節點驅動 + pydantic-graph FSM | Pydantic Logfire |
| Vercel Eve | Event Log + 確定性重放 | Agent Runs 面板（自動啟用） |
| smolagents | AST 執行稽核 + `agent.replay()` | Arize Phoenix/AX |

### 第二級：工具呼叫與生命週期攔截

> 提供 Hooks/Filters/攔截器，但需額外配置 OTel 導出才能完整稽核。

| 框架 | 攔截機制 |
|------|---------|
| Semantic Kernel | `IFunctionInvocationFilter`（Plugin 執行前後強制攔截） |
| Vercel AI SDK | 需配置 `instrumentation.ts` 外接 OTel |
| AutoGen（v0.4 前） | 高階 Message 對話流日誌（v0.4 後引入事件驅動 + OTel） |

### 第三級：完全黑盒

> 框架本身不內建 OTel 追蹤，需依賴第三方平台。

| 框架 | 說明 |
|------|------|
| CrewAI | 預設僅任務進度日誌，微觀 trace 需外接 OTel |

> TS 生態圈主流框架（Eve、Mastra、LangGraph.js、OpenAI SDK）皆已跨越黑盒階段。

---

## 對 Meta-Harness 研究專題的意義

本研究直接對應 [[wiki/concepts/meta-harness|Meta-Harness]] 的**定義 B（Multi-Agent Orchestration Layer）**，提供了目前業界最完整的 harness/framework 實作案例盤點：

1. **Agent selection** — 各框架如何判斷任務適合哪個 agent（Handoff 工具化 vs 選擇策略 vs LLM 路由）
2. **Context management** — 跨 agent 的共享/轉換 context（Session History 移轉 vs StateGraph vs 獨立 context 隔離）
3. **Session continuity** — 不同 agent 輪流上場的狀態持久化（Checkpointer vs Event Log vs DBOS Checkpoint）
4. **Parallel execution** — 同時跑多個 agents（Swarm vs Ralph Mode vs 併行 DAG）
5. **Failure handling** — 主要 agent 失敗時 fallback（Hop Counter 安全熔斷 vs TerminationStrategy vs 確定性重放）
6. **Output normalization** — 統一不同 agent 的回應格式（Zod/Pydantic 驗證 vs JSON Schema）

這些框架展示了從「無狀態單 agent」到「多 agent orchestration」的完整光譜，是理解 Meta-Harness 定義 B 業界實作的基礎材料。

---

## 對 Cheerio 助理的啟發

1. **可觀測性是第一級需求**：所有生產級框架都將 OTel 追蹤視為一等公民
2. **耐用執行是關鍵差異化**：LangGraph Checkpointer、DeepSeek Replay/Fork、Vercel Eve 確定性重放、DBOS Transaction Checkpoint
3. **Handoff 工具化是最輕量的多 agent 協作模式**：OpenAI Agents SDK 的 `transfer_to_<agent_name>` 以最低程式碼行數實現動態分工
4. **觀察式記憶（Mastra OM）** 解決長對話 Context 膨脹問題，值得借鑑
5. **自建 harness 的定位**：不與任何框架綁定，而是借鑑各框架的最佳實踐組合

---

## 來源

- [[raw/deep-research/rc-20260831-001/research-report|Gemini Deep Research: AI Agent Harness/Framework 全貌盤點（177 筆來源）]]

## 相關頁面

- [[wiki/concepts/meta-harness|Meta-Harness]] — 定義 B 的業界實作案例盤點
- [[wiki/entities/langgraph|LangGraph]] — 既有 entity 頁
- [[wiki/entities/openai-codex|OpenAI Codex]] — 既有 entity 頁
- [[wiki/entities/deepseek-harness|DeepSeek Harness]] — 既有 entity 頁
- [[wiki/entities/pydantic-ai|Pydantic AI]] — 新建
- [[wiki/entities/openai-agents-sdk|OpenAI Agents SDK]] — 新建
- [[wiki/entities/crewai|CrewAI]] — 新建
- [[wiki/entities/mastra|Mastra]] — 新建
- [[wiki/entities/vercel-eve|Vercel Eve]] — 新建
- [[wiki/entities/semantic-kernel|Semantic Kernel]] — 新建
- [[wiki/entities/autogen|AutoGen]] — 新建
- [[wiki/entities/smolagents|smolagents]] — 新建
