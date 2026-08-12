---
title: "Code Execution as Tool Calling"
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [agent, python, runtime, execution]
topics: [ai-agent]
canonical: concepts/code-execution-as-tool-calling
---

> 「模型寫 Python 腳本執行多操作」取代「一次一個 tool call」的模式——單一腳本可等同 50 次 tool call，加速執行並透過程式內過濾節省 context window。

## 定義

傳統 AI Agent 使用 tool calling：模型拿到工具列表，逐次呼叫工具，每次結果回傳 context。Code Execution 模式則是給模型一個 Python runtime，讓模型撰寫腳本一次性執行多個操作。

## 核心優勢

1. **速度**：一個 Python 腳本可等同 50 次 tool call，省去來回溝通的延遲
2. **Context 節省**：Python 程式可過濾不相關資訊，只把最重要的結果回傳給模型
3. **Persistent 環境**：Python 環境中的變數可跨對話保存，幫助 agent 記憶
4. **靈活性**：可寫條件判斷、迴圈、錯誤處理，比固定工具列表更靈活

## 實作案例

- [[wiki/entities/prime-agent|Prime Agent]]：建構在 Pi 之上，用 Python runtime 取代 tool calling
- CodeAct 模式：學術研究中的類似概念

## 與傳統 Tool Calling 的比較

| 面向 | Tool Calling | Code Execution |
|------|-------------|----------------|
| 執行單位 | 一次一個工具 | 一個腳本多個操作 |
| Context 消耗 | 每次結果都回傳 | 可過濾後回傳 |
| 速度 | 來回多次 | 一次執行 |
| 靈活性 | 固定工具列表 | 程式邏輯 |
| Persistent | 無 | 變數可保存 |

## 來源
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent YouTube 介紹]]

## 相關頁面
- [[wiki/entities/prime-agent|Prime Agent]]
- [[wiki/concepts/recursive-language-model|Recursive Language Model]]
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]]
