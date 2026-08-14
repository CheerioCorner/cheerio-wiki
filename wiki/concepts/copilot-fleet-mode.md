---
title: "Copilot Fleet Mode — 並行 Sub-agent 編排"
type: concept
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [github-copilot, fleet-mode, parallel, orchestration, sql-todos]
topics: [skill, ai-development-tools]
canonical: concepts/copilot-fleet-mode
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-fleet-mode.md"
---

# Copilot Fleet Mode — 並行 Sub-agent 編排

> Fleet mode 是 Copilot 的**並行 orchestration pattern**，將工作拆分為獨立 units，分配給多個 sub-agent 並行執行，以 SQL todos 為共享協調狀態。

## 用途

**適合**：多檔案重構、批次 review、並行研究、文件刷新、migration tasks

**不適合**：步驟 2 依賴步驟 1 輸出的序列任務、緊密耦合編輯、小型任務

## 啟動方式

```typescript
// 從 session 啟動
await session.rpc.fleet.start({
    prompt: "Refactor each SDK package independently, then summarize.",
});

// 從 plan mode 啟動
type ExitPlanModeAction = "exit_only" | "interactive" | "autopilot" | "autopilot_fleet";
```

## 協調機制：SQL Todos

```sql
CREATE TABLE todos (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    status TEXT DEFAULT 'pending'  -- pending → in_progress → done/blocked
);
CREATE TABLE todo_deps (todo_id TEXT, depends_on TEXT);
```

Sub-agent 流程：
1. Claim 一個 ready todo（`status = 'in_progress'`）
2. 在 todo scope 內工作
3. 完成時設 `status = 'done'`，無法進行時設 `status = 'blocked'`

## 生命週期

Fleet mode 透過 runtime task 機制觸發 sub-agents。事件透過 session event stream 觀察：

```typescript
session.on((event) => {
    if (event.type === "subagent.started") { /* ... */ }
    if (event.type === "subagent.completed") { /* ... */ }
});
```

## 限制

- 實驗性功能（experimental in several SDKs）
- SQL todos 穩定性待確認
- 不免除 parent-agent review（並行 workers 可能產生不一致假設）

## 來源

- [[wiki/sources/2026-08-14-fleet-mode|Fleet Mode]]

## 相關頁面

- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK]]
- [[wiki/concepts/copilot-custom-agents|Custom Agents]]
- [[wiki/concepts/copilot-agent-loop|Agent Loop]]
