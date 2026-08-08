---
title: "LangGraph — Production-Grade Agent Runtime with Graph-Based State Management"
type: entity
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [langgraph, ai-agent, graph-engineering, state-management, durability, checkpointing, langchain]
collection: entities
topics: [ai-agent]
canonical: entities/langgraph
---

> LangGraph 是 LangChain 生態的 graph-based agent runtime，核心價值在 durability（checkpointing / human-in-the-loop / time travel），而非 graph API 本身。

## 基本資訊

| 項目 | 內容 |
|------|------|
| 全名 | LangGraph |
| 維護者 | LangChain Inc. |
| 授權 | MIT |
| 月下載量 | 65M+（2026 年 7 月） |
| 生產用戶 | Klarna、Replit、Elastic、Uber、LinkedIn、Cisco、Stripe |
| 語言 | Python（主）、JavaScript（落後） |
| 定位 | Low-level agent runtime，不抽象 prompts 或 architecture |

## 核心架構

### Graph API（20 行 Python）

```
State → Node → Edge → Compile → Invoke
```

1. **State** — typed dict / dataclass / Pydantic model，所有 node 共用
2. **Node** — `def node(state) -> partial_update`，sync/async 皆可
3. **Edge** — 固定（A→B）或 conditional（routing function 決定）
4. **Compile** — 接線 + 產生可呼叫物件

### Reducers

- 預設：node 回傳值 **替換** 舊值
- Reducer：改變合併規則（如 `add_messages` = append）
- 解決 parallel branch 同時寫同一 key 的 merge 問題

### Runtime：Pregel 模型

- 源自 Google 2010 論文（16 年分散式圖處理）
- Node 不互相呼叫，讀寫 named channels
- **Super-step**：每個 active node 執行一次 → reducers 合併 → 計算下一批
- Cycles 是一等公民（retry loop 不是 special case）

### Send API（Dynamic Fan-out）

- Conditional edge 回傳 `send` 物件列表
- Runtime 自動產生對應 workers
- MapReduce 寬度由資料決定

## Durability 系統

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

### Delta Channels（2026-05）

- 舊版：checkpoint N = steps 1~N 全量，O(n²) 增長
- 新版：只寫 diff + 定期 full snapshot
- 實測：200 turn agent 從 **5.3GB → 129MB**（40x 壓縮），仍標記 beta

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

## 何時用 / 何時不用

✅ **用**：agent 需要 survive（restart、human review、incident replay）
❌ **不用**：5 個 deterministic steps 或 30 秒 tool loop → while loop + JSON dump

## 來源
- [[wiki/sources/2026-08-21-langgraph-in-10-minutes|LangGraph in 10 Minutes — YouTube]]

## 相關頁面
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 三種持久化路徑比較（Pi vs LangGraph）
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — Loop vs Graph 兩種模式
- [[wiki/entities/waku-agent|waku-agent]] — Local-first agent，展現 loop + graph 混合
- [[wiki/entities/hermes-agent|hermes-agent]] — 自我改進 AI agent
- [[wiki/entities/tau|tau]] — Pi Python port
