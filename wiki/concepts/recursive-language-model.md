---
title: "Recursive Language Model (RLM)"
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 2
tags: [agent, subagent, multi-agent, orchestration, context-management]
topics: [agent-architecture]
canonical: concepts/recursive-language-model
---

> RLM（Recursive Language Model）——把 **context 當變數**、把 subagent delegation 當成 persistent REPL 內函式呼叫的架構，讓模型能寫「以自己的 context 為操作對象的程式」，用於長 session 的 context 管理與平行委派。

## 定義（2026-08-13 更新：核心定義修正）

⚠️ 舊版定義只寫「觸發 subagent」，遺漏了更根本的一層：RLM 的核心是**把 context 當成變數**。[[wiki/entities/prime-agent|Prime Agent]] 讓模型使用一個 persistent IPython kernel 作為主要工具，模型的歷史、subagent、工具都是 kernel 裡可程式化操作的物件，這讓模型可以寫「language model programs as actions over its own context」——例如把某段歷史存進變數、需要時再用程式搜尋/切分，而不是每輪都把全部歷史塞回輸入。**觸發 subagent 只是這個更廣定義下的一種應用**，不是 RLM 的全部。

## 技術實作

- **持久 IPython kernel**：session 全程存在，每輪都可呼叫；初始化時預先 import 每個 skill/tool 作為 module，包含 `rlm` 本身
- **`rlm()` 是 async function**：模型可自由呼叫並平行化 subagent 呼叫；`await rlm("sub-task", name="...")` 立即回傳一個 child handle（不是答案本身），後續溝通全部透過 `agent_message.send(...)` 進行
- **Fan-out 平行委派**：可同時派多個具名 subagent 各自執行獨立任務，主 agent 繼續做其他事，各 subagent 完成後以 `receiver_role="parent"` 回報
- **Persistent subagent**：subagent 呼叫結束後，其 session 目錄、context、IPython kernel、歷史紀錄仍然保留；可用 `rlm.list_subagents()` 找回既有 subagent，並用 `mode="follow_up"` 送出後續指令繼續同一個 session
- **通訊範圍限制**：多 agent 通訊限制在「核心家庭」（parent / sibling / child），避免任意跨 session 通訊

## 與傳統 Subagent 的差異

| 面向 | 傳統 Subagent | RLM |
|------|--------------|-----|
| 觸發方式 | Tool call | Python/IPython 函式呼叫（`await rlm(...)`） |
| Context | 共享或有限 | context 本身是變數，可程式化操作 |
| 生命週期 | 通常隨父代理，交回答案即結束 | 可 persistent，結束呼叫後仍保留 session 供後續接續 |
| 編排能力 | 固定 | 可在迴圈/條件中動態觸發、平行 fan-out |
| 通訊方式 | 回傳值 | 非同步 `agent_message.send(...)` |

## 使用場景

1. **平行委派**：主代理將子任務分配給多個 RLM subagent 同時執行
2. **條件觸發**：在 Python 邏輯中根據條件決定是否需要子代理
3. **Context 管理**：把長歷史存進變數而非反覆塞回輸入，需要時再程式化讀取，節省 context window 與 token
4. **長任務接續**：透過 persistent subagent，跨多輪甚至跨 session 接續同一項調查/工作

## 實作案例

- [[wiki/entities/prime-agent|Prime Agent]]：RLM 是其兩大核心抽象之一（另一個是 [[wiki/concepts/continual-harness|Continual Harness]]）
- [[wiki/entities/openai-codex|Codex]]：orchestration 功能有類似概念

## 來源
- [[wiki/sources/2026-08-13-prime-agent-official-blog|Prime Agent 官方部落格全文]]
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent YouTube 介紹]]

## 相關頁面
- [[wiki/entities/prime-agent|Prime Agent]]
- [[wiki/concepts/continual-harness|Continual Harness]]
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]]
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]]
