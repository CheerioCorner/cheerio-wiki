---
title: Late Conversion(最晚轉換)
type: concept
created: 2026-07-11
updated: 2026-07-11
sources: 1
tags: [concept, agent, pi, typescript]
collection: concepts
topics: [ai-agent]
canonical: concepts/late-conversion
---

> 一種型別設計策略:**整個內部邏輯都以「應用層 AgentMessage」運作,只在邊界(打 LLM 前一刻)才濾成 LLM 能懂的 `Message[]`**。換句話說:保留訊息的「應用程式語義」直到最後一刻再翻譯。
>
> 原出處:[[wiki/sources/2026-02-10-pi-agent-core-design]] 3.1;發明人:Zechner 在 pi-agent-core 用 TypeScript `Declaration Merging` 實現。

## 為什麼存在

LLM 的 `Message` 類型只涵蓋「user / assistant / tool result」三種角色;但真實應用存在大量 **「不需要給 LLM 看的訊息」**:

| 場景 | 訊息類型(role) | 交 LLM? |
|---|---|---|
| 使用者提問 | `user` | ✅ |
| Agent 回覆 | `assistant` | ✅ |
| 工具結果 | `tool_result` | ✅ |
| UI 通知 | `notification` | ❌ |
| 文件變更事件 | `artifact` | ❌ |
| 會話分支標記 | `branch` | ❌ |

如果一開始就用 LLM 的 `Message`,這些「内部俗務」就會被迫塞進 LLM 的上下文,污染並增大 token。Late conversion 反過來:**讓應用程式保有全部訊息,只在邊界過濾。**

## 實作策略:TS Declaration Merging

```typescript
// 空介面——應用層透過 declaration merging 擴充
export interface CustomAgentMessages {
    // Empty by default - apps extend via declaration merging
}

// AgentMessage = LLM 訊息 ∪ 自訂訊息
export type AgentMessage = Message | CustomAgentMessages[keyof CustomAgentMessages];
```

關鍵點:
- 應用層透過 declaration merging 擴充 `CustomAgentMessages` 後,**TypeScript 編譯器會自動更新聯合類型**。
- 內部所有邏輯(壓縮、分支、UI 渲染)都基於 `AgentMessage` 運轉。
- **只在呼叫 LLM 的瞬間**透過 `convertToLlm(messages)` 過濾為可發送的 `Message[]`。

## 好處(三大)
1. **應用語義不洩漏給 LLM**:UI 通知、artifact、branch 標記不在上下文內。
2. **跨 provider 會話遷移成本低**:換 LLM 時只換 `convertToLlm()` 即可,應用層不動。
3. **可觀測性保留**:內部所有事件都看得到,不因為轉成 LLM 訊息而消失。

## 何時該用 / 不該用
- ✅ 需要跨 provider 切換 / 不同模型供應商
- ✅ 應用層有自己的訊息種類(UI 事件、文件變更、會話元資料)
- ✅ 想要可觀測但不汙染 LLM 上下文
- ❌ 純單純 LLM 對話、不需要應用層元資料
- ❌ 不在意上下文 token 預算

## 相關頁面
- Entities:[[wiki/entities/pi-agent-core]]
- Concepts: `minimal-agent-loop`、`observability-layer`、`steering-followup`（尚未建立的 future concepts）
- Source:[[wiki/sources/2026-02-10-pi-agent-core-design]]
- Synthesis:[[wiki/concepts/minimal-agent-philosophy]]
