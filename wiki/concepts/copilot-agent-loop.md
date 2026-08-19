---
title: "Copilot Agent Loop — 多輪 Tool-Use 循環"
type: concept
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [github-copilot, agent-loop, tool-use, turns]
topics: [copilot-sdk, ai-development-tools]
canonical: concepts/copilot-agent-loop
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-the-agent-loop.md"
---

# Copilot Agent Loop — 多輪 Tool-Use 循環

> Copilot CLI 的核心 orchestrator：從 prompt 到 session.idle 的完整 agentic tool-use loop。

## 架構

SDK 是傳輸層（JSON-RPC），CLI 是 orchestrator。當呼叫 `session.send({ prompt })` 時，CLI 進入 loop：

```
使用者 prompt → CLI → LLM API call → 工具執行 → 結果回傳 → LLM 決定下一步 → 重複
```

**關鍵設計**：每次迭代 = 一次 LLM API call = 一對 `assistant.turn_start` / `assistant.turn_end`。**無隱藏呼叫**。

## Turns 是什麼

一個 turn = 一次 LLM API call 及其後果：

1. CLI 將完整對話歷史（含所有先前 tool calls/results）送給 LLM
2. LLM 回應（可能包含 tool requests）
3. 若有 tool requests，CLI 執行工具
4. 發出 `assistant.turn_end`

使用者的一個訊息通常產生**多個 turns**。

## session.idle vs session.task_complete

| 信號 | 性質 | 說明 |
|------|------|------|
| `session.idle` | 機械性、非持久化 | loop 結束時**必定**發出，表示 agent 已停止處理 |
| `session.task_complete` | 語義性、持久化 | 模型**選擇性**發出，表示任務完成（含 summary） |

### Autopilot 模式

無人操作時，CLI 會主動追蹤 `task_complete`。若 loop 結束但模型未呼叫 `task_complete`，CLI 注入合成訊息催促模型繼續。

## 來源

- [[wiki/sources/2026-08-14-the-agent-loop|The Agent Loop]]

## 相關頁面

- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK]]
- [[wiki/concepts/copilot-custom-agents|Custom Agents]]
- [[wiki/concepts/copilot-fleet-mode|Fleet Mode]]
