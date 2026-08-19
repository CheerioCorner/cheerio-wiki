---
title: "Agent Durability Patterns — 狀態持久化的三種路徑"
type: concept
created: 2026-08-21
updated: 2026-08-21
sources: 2
tags: [ai-agent, durability, checkpointing, state-persistence, design-tradeoff]
collection: concepts
topics: [agent-architecture]
canonical: concepts/agent-durability-patterns
---

> Agent 需要「記住自己做到哪裡」的程度不同，對應三種持久化路徑。選錯路徑的代價：要么 crash 後重來，要么 complexity tax 太重。

## 核心問題

Agent 跑到一半 crash 了，怎麼辦？

這個問題的解法取決於：**你願意付出多少複雜度，換多少「恢復能力」？**

## 三種路徑

### 路徑 1：無持久化（While Loop + JSON）

```
while not done:
    result = llm_call(state)
    state = update(state, result)
    save_json(state)  # 可選
```

| 項目 | 內容 |
|------|------|
| 複雜度 | ⭐ 最低 |
| 恢復能力 | ❌ 無（crash = 重來） |
| 適合場景 | 30 秒內完成的 tool loop、deterministic pipeline |
| 代表 | 簡單 script、CLI 工具 |
| 成本 | 零（幾行 code） |

**影片原話**：「If your agent is five deterministic retrieval steps or one tool loop that either finishes in 30 seconds or does not, a while loop and a JSON dump is the correct engineering.」

### 路徑 2：訊息層級持久化（Pi Agent Session Tree）

```
Session Tree
├── message 1 (user)
├── message 2 (assistant)
│   ├── message 3 (user)  ← fork 從這裡
│   └── message 4 (assistant)
└── message 5 (assistant)
```

| 項目 | 內容 |
|------|------|
| 複雜度 | ⭐⭐ 低 |
| 恢復能力 | ⚠️ 部分（可 fork 對話、可 continue） |
| 適合場景 | 互動式 coding agent、需要 fork 的對話 |
| 代表 | Pi Agent Core |
| 成本 | 5 檔 / 1,500 行 |

**能做到**：
- ✅ Fork 對話（從某個 message 分支）
- ✅ Continue（`agentLoopContinue()` 恢復執行）
- ✅ Compaction（context 太長時 LLM 摘要壓縮）

**做不到**：
- ❌ 每步存完整 state（只存 message）
- ❌ Step 回放（無法問「step 38 時 state 是什麼」）
- ❌ Human-in-the-loop（只有 steering queue，不是 interrupt/resume）
- ❌ Parallel branch merge（無 reducers）

### 路徑 3：狀態層級持久化（LangGraph Checkpointer）

```
Checkpoint
├── Step 0: {topic: "AI", articles: [], summaries: []}
├── Step 1: {topic: "AI", articles: [a,b,c], summaries: []}
├── Step 2: {topic: "AI", articles: [a,b,c], summaries: [s1,s2,s3]}
└── Step 3: {topic: "AI", articles: [...], report: "..."}
```

| 項目 | 內容 |
|------|------|
| 複雜度 | ⭐⭐⭐⭐ 高 |
| 恢復能力 | ✅ 完整（每步可回放、可 fork） |
| 適合場景 | Production agent、需要 incident replay、human review |
| 代表 | LangGraph |
| 成本 | 1-2 週學習曲線 + 複雜度 tax |

**能做到**：
- ✅ 每步存完整 state
- ✅ Step 回放（time travel）
- ✅ Human-in-the-loop（`interrupt()` + `resume()`）
- ✅ Parallel branch merge（reducers）
- ✅ Dynamic fan-out（Send API）
- ✅ Delta channels（5.3GB → 129MB 壓縮）

**代價**：
- ❌ 學習曲線 1-2 週
- ❌ 需要自己寫 schema、nodes、wiring
- ❌ Complexity tax（你可能用不到 80% 的功能）

## 對比總表

| 維度 | 路徑 1：While Loop | 路徑 2：Session Tree | 路徑 3：Checkpointer |
|------|-------------------|---------------------|---------------------|
| 複雜度 | ⭐ | ⭐⭐ | ⭐⭐⭐⭐ |
| Crash 恢復 | ❌ | ⚠️ 部分 | ✅ 完整 |
| Fork/分支 | ❌ | ✅ | ✅ |
| Step 回放 | ❌ | ❌ | ✅ |
| Human 介入 | ❌ | ⚠️ steering | ✅ interrupt/resume |
| Parallel merge | ❌ | ❌ | ✅ reducers |
| 學習成本 | 0 | 低 | 1-2 週 |
| 適用規模 | 個人 script | 個人 agent | 團隊 production |

## 設計決策框架

**問自己三個問題**：

1. **Agent crash 後，重來的代價有多大？**
   - 小（30 秒 / 免費 API）→ 路徑 1
   - 中（幾分鐘 / 有成本）→ 路徑 2
   - 大（幾小時 / 重要工作）→ 路徑 3

2. **需要人類介入審查嗎？**
   - 不需要 → 路徑 1 或 2
   - 需要 → 路徑 3

3. **需要事後 debug agent 的每一步嗎？**
   - 不需要 → 路徑 1 或 2
   - 需要（incident review）→ 路徑 3

## 與現有頁面的關係

- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — Pi 的極簡哲學，偏向路徑 2
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — Loop 通常是路徑 1/2，Graph 通常是路徑 3
- [[wiki/entities/langgraph|LangGraph]] — 路徑 3 的代表實作
- [[wiki/entities/pi-agent-core|pi-agent-core]] — 路徑 2 的代表實作

## 來源
- [[wiki/sources/2026-08-21-langgraph-in-10-minutes|LangGraph in 10 Minutes — YouTube]]
- [[wiki/sources/2026-02-10-pi-agent-core-design|Pi Agent Core Design]]

## 相關頁面
- [[wiki/entities/langgraph|LangGraph]] — 路徑 3 完整介紹
- [[wiki/entities/pi-agent-core|pi-agent-core]] — 路徑 2 完整介紹
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — 極簡哲學
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 工作流模式
