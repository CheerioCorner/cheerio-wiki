---
title: "LangGraph in 10 Minutes (Explained Clearly)"
type: source
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [langgraph, ai-agent, graph-engineering, state-management, durability, checkpointing]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-21-langgraph-in-10-minutes
---

> 來源：[YouTube — LangGraph in 10 Minutes (Explained Clearly)](https://www.youtube.com/watch?v=BwZbdCzmZJc)
> 原始字幕：[[raw/youtube/langgraph-in-10-minutes|raw transcript]]

## 一句話

LangGraph 的真正價值不在 graph API（20 行 Python 就能寫完），而在 runtime：checkpointing、human-in-the-loop、time travel 與 delta channels，這些才是 production agent 需要的基礎設施。

## 重點摘要

### 四大組件（Graph API）

1. **State** — typed dictionary / dataclass / Pydantic model，所有 node 共讀共寫的唯一 schema
2. **Node** — Python function，接收 state、回傳 partial update，無 base class，支援 sync/async
3. **Edge** — 固定 edge（A → B）或 conditional edge（routing function 決定下一個 node）
4. **Compile** — 接線 start → nodes → end，產生可 invoke 的物件

### 第五個隱藏概念：Reducers

- 預設行為：node 回傳值 **替換** 該 key 的舊值
- Reducer：改變合併規則（如 `add_messages` 是 append 而非覆蓋）
- 存在原因：parallel branch 同時寫同一 key 時，需要決定如何 merge

### Runtime 核心：Pregel 模型

- 源自 Google 2010 年論文，16 年分散式圖處理經驗
- Node 不互相呼叫，而是讀寫 named channels
- Runtime 以 **super-step** 為單位推進：每個 active node 執行一次 → 透過 reducers 合併 → 計算下一批
- 循環（cycles）是一等公民，retry loop 不是 special case

### Send API（動態 Fan-out）

- Conditional edge 回傳 `send` 物件列表，每個攜帶自己的 state slice
- Runtime 自動產生對應數量的 workers
- MapReduce 寬度由資料決定，而非預先畫死

### Checkpointer（Durability）

- `compile(checkpointer=...)` + `invoke(thread_id=...)` 兩行啟用
- 每個 step 完整 state 寫入 storage
- 三種 durability mode：`sync`（下一步前寫入）、`async`（同步寫）、`exit`（完成時寫）
- 存储選項：InMemorySaver（測試）、SQLite（筆電）、Postgres（production）

### 三大衍生功能

1. **Human-in-the-loop**：`interrupt()` 暫停 → 人工審核 → `resume()` 繼續。注意：resume 時 node 從頭重跑，side effect 必須 idempotent
2. **Time Travel**：查詢 thread 的 checkpoint 歷史，回放或 fork（修改 state 後分支）
3. **Streaming**：7 種模式（full state / state updates / model tokens / custom events / checkpoints / task lifecycle / debug）

### Delta Channels（ storage 優化）

- 舊版：checkpoint N 包含 steps 1~N 全部資料，storage 隨 run length 平方增長
- 2026 年 5 月：delta channels，只寫 diff + 定期 full snapshot
- 實測：200 turn coding agent 從 5.3GB → 129MB（40x 壁縮）

### 何時不該用 LangGraph

- 5 個 deterministic retrieval steps 或 30 秒內完成的 tool loop → while loop + JSON dump 就夠
- LangChain 自己的 `create agent`（v1.0）提供更高層抽象
- **Deep Agents**：LangChain 的 opinionated harness，含 planning、virtual FS、sub-agents、skills
- **Functional API**：`@entry_point` + `@task` 裝飾器，同 runtime 無 graph

### Stripe 案例

- 用 Deep Agents 建立公司級 agent
- 1 工程師 × 1 週 → 296 users → 4 週後 5,000+ users / 60,000 sessions / 1,000 internal skills

## 來源
- [[raw/youtube/langgraph-in-10-minutes|Raw transcript — YouTube BwZbdCzmZJc]]

## 相關頁面
- [[wiki/entities/langgraph|LangGraph]] — 框架 entity
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種工作流模式比較
- [[wiki/entities/waku-agent|waku-agent]] — 展現 loop + graph 的實際框架
