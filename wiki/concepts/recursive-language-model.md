---
title: "Recursive Language Model (RLM)"
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [agent, subagent, multi-agent, orchestration]
topics: [ai-agent]
canonical: concepts/recursive-language-model
---

> RLM（Recursive Language Model）——可在 Python 程式邏輯中以函式呼叫觸發的獨立 agent session，用於平行委派與 context 管理。

## 定義

RLM 是 [[wiki/entities/prime-agent|Prime Agent]] 引入的概念：在 Python 腳本中呼叫一個函式，觸發一個**獨立的 agent session**（子代理），該子代理有自己的 context 和生命週期。

## 與傳統 Subagent 的差異

| 面向 | 傳統 Subagent | RLM |
|------|--------------|-----|
| 觸發方式 | Tool call | Python 函式呼叫 |
| Context | 共享或有限 | 獨立 session |
| 生命週期 | 隨父代理 | 獨立 |
| 編排能力 | 固定 | 可在迴圈/條件中動態觸發 |

## 使用場景

1. **平行委派**：主代理將子任務分配給多個 RLM subagent 同時執行
2. **條件觸發**：在 Python 邏輯中根據條件決定是否需要子代理
3. **Context 管理**：子代理的結果經過過濾後才回傳給主代理，節省 context window

## 實作案例

- [[wiki/entities/prime-agent|Prime Agent]]：RLM function 是其核心架構之一
- [[wiki/entities/openai-codex|Codex]]：orchestration 功能有類似概念

## 來源
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent YouTube 介紹]]

## 相關頁面
- [[wiki/entities/prime-agent|Prime Agent]]
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]]
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]]
