---
title: "Copilot Steering & Queueing — 訊息投遞模式"
type: concept
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [github-copilot, steering, queueing, interaction-pattern]
topics: [skill, ai-development-tools]
canonical: concepts/copilot-steering-queueing
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-steering-and-queueing.md"
---

# Copilot Steering & Queueing — 訊息投遞模式

> 當 session 正在處理時，兩種訊息投遞模式：steering（即時轉向）和 queueing（排隊等待）。

## 兩種模式

| 模式 | 行為 | 適用場景 |
|------|------|---------|
| **`"immediate"`** (steering) | 注入**當前** LLM turn | Agent 走錯路時即時轉向 |
| **`"enqueue"`** (queueing) | 排隊等當前 turn 結束後 FIFO 處理 | 追加後續任務 |

## Steering（即時轉向）

```typescript
// 開始長任務
await session.send({ prompt: "Refactor the authentication module" });

// Agent 工作中，即時轉向
await session.send({ prompt: "Use JWT instead of sessions", mode: "immediate" });
```

訊息注入 `ImmediatePromptProcessor` 佇列，在下一次 LLM 請求前注入對話。若 turn 在處理前完成，自動移至 queue。

## Queueing（排隊）

```typescript
await session.send({ prompt: "Set up project structure" });
await session.send({ prompt: "Add unit tests", mode: "enqueue" });  // 等第一個 turn 結束
await session.send({ prompt: "Update README", mode: "enqueue" });   // FIFO 順序
```

## 選擇指引

| 場景 | 模式 |
|------|------|
| Agent 走錯路 | Steering |
| 追加後續任務 | Queueing |
| Agent 即將犯錯 | Steering |
| 批次不相關請求 | Queueing |
| 加入額外 context | Steering |

## 來源

- [[wiki/sources/2026-08-14-steering-and-queueing|Steering and Queueing]]

## 相關頁面

- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK]]
- [[wiki/concepts/copilot-agent-loop|Agent Loop]]
