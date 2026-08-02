---
title: pi-agent-core — 5 檔 / 1,500 行的 Agent Runtime
type: entity
created: 2026-07-11
updated: 2026-07-11
sources: 1
tags: [pi, agent, runtime, zechner]
collection: entities
topics: [ai-agent]
canonical: entities/pi-agent-core
---

> pi-mono monorepo 內的 `packages/agent/`,全文為 agent runtime。**整個 runtime 由 5 個檔、約 1,500 行 TypeScript 構成**(以 [2026-02-10-pi-agent-core-design] 解析為準)。

> 本頁主要記錄檔案分工與關鍵類別；深度型別設計獨立到 [[concepts/late-conversion]]。迴圈結構與可觀測性目前僅作為 future concepts（`minimal-agent-loop`、`observability-layer`）追蹤，尚未建立獨立頁面。

## 來源
- [[sources/2026-02-10-pi-agent-core-design]] — 主要來源(語言物理位元 + 行號層次的解析)
- 程式碼位置:`https://github.com/badlogic/pi-mono/blob/main/packages/agent/src/`
- 由 [[entities/pi-mono]] 主頁面統合;本頁是它的下層 runtime 焦點。

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

→ 讓 continue() 變得優雅的關鍵設計:**構造錯誤訊息也會成為上下文**(`AssistantMessage, stopReason: "error"`)。詳見 [[concepts/late-conversion]]。

## 五項設計原則(由本類別衍生出)
- **極簡主義**(5 個檔 / 1,500 行 / 4 工具 / < 1000 token 系統提示)
- **可觀測性**(三層事件)
- **可干預性**(steering / follow-up 雙 queue)
- **最晚轉換**→ 獨立頁 [[concepts/late-conversion]]
- **自我進化**(透過 bash 自我呼叫)

## 不做的事(立場,精準條列)
無 MCP / 無 sub-agents / 無 plan mode / 無權限檢查 / 無 maxSteps。
→ 對應 future concept `yolo-by-default` 與 [[entities/pi-mono]] 的「刻意不做」。

## 相關頁面
- Entities:[[entities/pi-mono]]、[[entities/mario-zechner]]
- Concepts:[[concepts/late-conversion]]；`minimal-agent-loop`、`observability-layer`、`steering-followup` 尚未建立
- Source:[[sources/2026-02-10-pi-agent-core-design]]
- Synthesis:[[concepts/minimal-agent-philosophy]]
