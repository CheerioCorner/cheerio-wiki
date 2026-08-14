---
title: "LangGraph — Graph-based Agent Runtime with Durability"
type: entity
created: 2026-08-21
updated: 2026-08-14
sources: 2
tags: [langgraph, ai-agent, graph-engineering, state-management, durability, checkpointing, langchain]
collection: entities
topics: [ai-agent]
canonical: entities/langgraph
---

> LangGraph 是 LangChain 團隊開發的低階 agent orchestration 框架，核心價值在 durability（checkpointing / human-in-the-loop / time travel），而非 graph API 本身。建立在 Google Pregel 論文（2010）模型上。

## 基本資訊

| 項目 | 內容 |
|------|------|
| 全名 | LangGraph |
| 維護者 | LangChain Inc. |
| 授權 | MIT |
| 版本 | v1.2.11（2026-08-14） |
| GitHub Stars | ~39,600 |
| 月下載量 | 65M+（2026 年 7 月） |
| 生產用戶 | Klarna、Replit、Elastic、Uber、LinkedIn、Cisco、Stripe |
| 語言 | Python（主，3.10~3.13）+ JavaScript（LangGraph.js） |
| 定位 | Low-level agent runtime，不抽象 prompts 或 architecture |

## 核心架構

### Graph / Node / Edge / State

LangGraph 的架構用四個核心概念理解：

**Graph（圖）**：整個 agent 工作流的容器。用 `StateGraph` 定義 State 類型，把 Node 接上去，compile 成 `Pregel` 物件才能執行。

**Node（節點）**：Graph 中的處理單元。每個 Node 是一個 Python function，接收當前 State、回傳 State 的更新。Node 可以是 LLM 呼叫、工具執行、條件判斷，甚至是另一個子 Graph（subgraph）。

**Edge（邊）**：定義 Node 之間的執行順序和條件路由。三種 Edge：
- **Normal Edge**：無條件從 A 到 B
- **Conditional Edge**：根據 State 決定下一步去哪個 Node
- **START / END**：特殊的起始和結束標記

**State（狀態）**：LangGraph 的靈魂。三種定義方式：
- `TypedDict` — 輕量、無 schema 驗證
- `Pydantic BaseModel` — 完整 type validation
- `Annotated[list[T], operator.add]` — 搭配 reducer function，定義多個 Node 寫入同一欄位時如何合併

State 的每個欄位對應一個 **Channel**，Channel 決定如何合併多個 Node 的寫入（LastValue、BinaryOperatorAggregate、DeltaChannel 等）。

### Runtime：Pregel 模型

- 源自 Google 2010 論文（16 年分散式圖處理）
- Node 不互相呼叫，讀寫 named channels
- **Super-step**：每個 active node 執行一次 → reducers 合併 → 計算下一批
- Cycles 是一等公民（retry loop 不是 special case）

### Send API（Dynamic Fan-out）

- Conditional edge 回傳 `send` 物件列表
- Runtime 自動產生對應 workers
- MapReduce 寬度由資料決定

### Subgraph 與 Multi-Agent

LangGraph 支援 subgraph（子圖），可以把一個 Graph 嵌入另一個 Graph，實現 hierarchical agent 架構。搭配 `Send` API，可以實現 parallel node execution——一個 Node 動態產生多個下游任務。

## Durability 系統（核心價值）

### Checkpointer

```python
graph.compile(checkpointer=MemorySaver())
graph.invoke(input, config={"thread_id": "abc"})
```

每 step 完整 state 寫入 storage。三種 mode：

| Mode | 行為 | 適用 |
|------|------|------|
| `sync` | 下一步前寫入 | 最安全 |
| `async` | 同步寫 | 平衡 |
| `exit` | 完成時寫 | 最快 |

存儲：InMemorySaver（測試）→ SQLite（筆電）→ Postgres（production）

### 三大衍生功能

1. **Human-in-the-loop**：`interrupt()` 暫停 + `resume()` 繼續
   - ⚠️ resume 時 node 從頭重跑，side effect 必須 idempotent
   - ⚠️ 不要用 try-except 包 interrupt（會吞掉暫停 exception）
2. **Time Travel**：查詢 checkpoint 歷史、回放或 fork
3. **Streaming**：7 種模式（full state / tokens / events / checkpoints / debug）

### Delta Channels（實驗性）

- 舊版：checkpoint N = steps 1~N 全量，O(n²) 增長
- 新版：只寫 diff + 定期 full snapshot
- 實測：200 turn agent 從 **5.3GB → 129MB**（40x 壓縮），仍標記 beta
- ⚠️ 社群討論中提到的「40x 壓縮比」在官方源碼中未找到佐證數字，DeltaChannel 機制確實存在但壓縮比未公開

## 生態系工具

### LangGraph Studio / LangSmith Studio

視覺化除錯桌面工具，整合進 LangSmith Studio：
- 即時觀察 State 在每個 superstep 的變化
- 互動式修改 State、測試 conditional edge 的路由
- 視覺化 Graph 結構與執行路徑

### LangGraph Platform / Cloud

LangChain 提供的生產部署平台：
- 自動化 checkpoint 管理與持久化
- Horizontal scaling（多 worker 並行）
- Cron scheduling、Webhook 觸發
- 與 LangSmith 整合的 Observability

### LangGraph.js（多語言支援）

TypeScript/JavaScript 版本，提供同等級功能：
- GitHub：`langchain-ai/langgraphjs`
- Stars：~3,200（截至 2026-08）
- 核心概念完全對應 Python 版

## 高層替代方案

| 方案 | 說明 |
|------|------|
| `create agent` (LangChain v1.0) | 幾行 code 產生 working agent，middleware 客製化 |
| Deep Agents | Opinionated harness：planning + virtual FS + sub-agents + skills |
| Functional API | `@entry_point` + `@task`，同 runtime 無 graph |

### Deep Agents 案例：Stripe

- 1 工程師 × 1 週建完
- 296 users → 4 週後 5,000+ users / 60,000 sessions / 1,000 internal skills
- 「解決所有 non-stripy 問題，讓我們專注 stripy agent 問題」

## 與其他 Agent Runtime 比較

| 維度 | LangGraph | Pi Agent | AutoGen | CrewAI |
|------|-----------|----------|---------|--------|
| **核心抽象** | Graph（Node + Edge + State） | Agentic Loop（while(true) + tools） | Agent-to-Agent 通訊 | Role-based Task |
| **狀態管理** | Channel-based State + Checkpoint | Context 物件（session 級） | 對話歷史 | Task context |
| **持久化** | 內建（SQLite/PG/InMemory） | 無（依賴外部） | 無內建 | 無內建 |
| **Human-in-the-loop** | 內建 interrupt + resume | 無（依賴 TUI） | 有限支援 | 無 |
| **Multi-Agent** | Subgraph + Send API | 無原生支援 | 核心設計 | 核心設計 |
| **語言** | Python + JS/TS | Node.js | Python + .NET | Python |
| **定位** | 低階 orchestration 框架 | 開發者 coding agent | Multi-agent 對話 | 團隊角色模擬 |
| **授權** | MIT | MIT | CC-BY-4.0 | MIT |
| **GitHub Stars** | ~39,600 | — | ~38,000 | ~26,000 |

> AutoGen 授權備註：`microsoft/autogen` LICENSE 為 Creative Commons Attribution 4.0 International，非 MIT。

### 與 Pi Agent 的架構差異（深度比較）

**根本哲學差異：**
- **LangGraph**：「把 agent 當成有狀態的計算圖」——framework-first 方法
- **Pi Agent**：「agent 就是 while(true) 迴圈」——developer-first 方法，程式碼本身就是架構

**狀態模型：**
- LangGraph State 是**聲明式**的（TypedDict / Pydantic），透過 Channel Reducer 合併更新
- Pi Context 是**命令式的**（mutable 物件），直接在迴圈中 mutate

**擴展方式：**
- LangGraph 透過 subgraph 和 conditional edge 擴展
- Pi 透過 extension + skill 擴展

**適用場景：**
- LangGraph：**長時間運行、跨 failure 持久、人類介入**的 production agent
- Pi：**快速迭代、輕量部署、開發者主導**的 coding agent

## 何時用 / 何時不用

✅ **用**：agent 需要 survive（restart、human review、incident replay）
❌ **不用**：5 個 deterministic steps 或 30 秒 tool loop → while loop + JSON dump

## 來源
- [[wiki/sources/2026-08-21-langgraph-in-10-minutes|LangGraph in 10 Minutes — YouTube]]
- Notion 種子：[[https://app.notion.com/p/3bb5979e3a8c81d187eee8b3c55cdd4b|LangGraph — Graph-based Agent Runtime]]

## 相關頁面
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 三種持久化路徑比較（Pi vs LangGraph）
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — Loop vs Graph 兩種模式
- [[wiki/entities/waku-agent|waku-agent]] — Local-first agent，展現 loop + graph 混合
- [[wiki/entities/hermes-agent|hermes-agent]] — 自我改進 AI agent
- [[wiki/entities/tau|tau]] — Pi Python port
