---
title: pi-agent-core — 5 檔 / 1,500 行的 Agent Runtime
type: entity
created: 2026-07-11
updated: 2026-08-06
sources: 2
tags: [pi, agent, runtime, zechner]
collection: entities
topics: [ai-agent, extension-dev, skill]
canonical: entities/pi-agent-core
---

> pi-mono monorepo 內的 `packages/agent/`,全文為 agent runtime。**整個 runtime 由 5 個檔、約 1,500 行 TypeScript 構成**(以 [[[../../raw/web/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]]] 解析為準)。

> 本頁主要記錄檔案分工與關鍵類別；深度型別設計獨立到 [[wiki/concepts/late-conversion]]。迴圈結構與可觀測性目前僅作為 future concepts（`minimal-agent-loop`、`observability-layer`）追蹤，尚未建立獨立頁面。

## 來源
- [[wiki/sources/2026-02-10-pi-agent-core-design]] — 主要來源(語言物理位元 + 行號層次的解析)
- [[wiki/sources/2026-08-06-pi-architecture-walkthrough]] — 架構 walkthrough（session tree、compaction、skills、extensions 機制）
- 程式碼位置:`https://github.com/badlogic/pi-mono/blob/main/packages/agent/src/`
- 由 [[wiki/entities/pi-mono]] 主頁面統合;本頁是它的下層 runtime 焦點。

## 5 個檔(原始清單)
1. **`types.ts`**
   - `AgentMessage`(空介面 + Declaration Merging)
   - `AgentLoopConfig`(可插拔行為注入)
   - `AgentEvent`(11 種生命週期事件)
2. **`agent-loop.ts`** — 主迴圈(雙層 while)
3. **`agent.ts`** — Agent 類(steering/follow-up 兩個 queue)
4. **`proxy.ts`** — 帶寬優化的 client-side 重建
5. *(第 5 個檔案 A 篇未明確點名;推測為 `index.ts` 或 `convert.ts`,需從 repo 確認)*

> 注:A 文作者列了 5 個檔但只給出前 4 個名字。上游 repo 的 `packages/agent/src/` 內結構可能與原文略有差異。

## 三層事件生命週期(由外到內)
**Agent → Turn → (Message | Tool Execution)**,每層都有 start / end 事件:
```
agent_start / agent_end
  turn_start / turn_end
    message_start / message_update / message_end
    tool_execution_start / tool_execution_update / tool_execution_end
```
→ 這是 future concept `observability-layer` 的核心比喻對象。

## 兩個核心入口
| 函數 | 前置條件 | 用途 |
|---|---|---|
| `agentLoop(prompts, context, config)` | 可從空上下文開始 | 使用者發了新訊息 |
| `agentLoopContinue(context, config)` | 上下文最後一條非 assistant | 重試 / 恢復 |

→ 讓 continue() 變得優雅的關鍵設計:**構造錯誤訊息也會成為上下文**(`AssistantMessage, stopReason: "error"`)。詳見 [[wiki/concepts/late-conversion]]。

## 五項設計原則(由本類別衍生出)
- **極簡主義**(5 個檔 / 1,500 行 / 4 工具 / < 1000 token 系統提示)
- **可觀測性**(三層事件)
- **可干預性**(steering / follow-up 雙 queue)
- **最晚轉換**→ 獨立頁 [[wiki/concepts/late-conversion]]
- **自我進化**(透過 bash 自我呼叫)

## Agentic Loop 三步驟（Walkthrough 補充）

來源：[[wiki/sources/2026-08-06-pi-architecture-walkthrough]]

1. **Context Initialization**：system prompt + AGENTS.md + skills markup + tools descriptions + message history/current message
2. **Context Transformation**：check-compaction → 若需要則 LLM 摘要替換 history
3. **LLM Call + Tool Loop**：LLM → tool call → 工具執行 → 結果回傳 → LLM 決定下一步（可達數百次）

## Session Tree 結構

Session 以 **tree**（非 list）儲存於 `~/.pi/agent/sessions/<cwd>/`：
- 格式：JSONL（每行一個 JSON 物件）
- 每個 message 有 `id` + `parent` 欄位
- 支援 fork（分叉對話）：從同一 parent 建立分支
- `/tree` 指令導覽

## Compaction 機制

- **觸發**：`agent_end` 與 `before_prompt` 兩個時機
- **token 計算**：優先用 LLM 回傳 context tokens；否則累加 `usage.input + output + cache.read + cache.write`
- **不使用**字元數除以 4 的粗估
- **Summary 格式**：goal / constraints / progress / decisions / next steps / critical context

## Skills 機制

1. System prompt 中以 markup 列出所有 skill（name + description）
2. `/skill:<name>` 被 **interactive layer 攔截**，不送入 core
3. 替換為 `<skill>` markup（含 name、description、location）
4. LLM 依 prompt 指示用 `read` 工具讀取 location
5. Skill 內容**不自動注入**——LLM 主動讀取，保留 tool call 可觀測性

## 不做的事(立場,精準條列)
無 MCP / 無 sub-agents / 無 plan mode / 無權限檢查 / 無 maxSteps。
→ 對應 future concept `yolo-by-default` 與 [[wiki/entities/pi-mono]] 的「刻意不做」。

## 相關頁面
- Entities:[[wiki/entities/pi-mono]]、[[wiki/entities/mario-zechner]]、[[wiki/entities/langgraph|LangGraph]]（持久化對比）
- Concepts:[[wiki/concepts/late-conversion]]、[[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]]（Pi vs LangGraph 持久化路徑比較）
- Source:[[wiki/sources/2026-02-10-pi-agent-core-design]]
- Synthesis:[[wiki/concepts/minimal-agent-philosophy]]
