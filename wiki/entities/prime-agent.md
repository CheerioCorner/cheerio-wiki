---
title: "Prime Agent — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent"
type: entity
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [agent, python, runtime, open-source]
topics: [ai-agent]
canonical: entities/prime-agent
---

> Prime Intelligence 開發的開源 coding agent，建構在 Pi 之上，核心差異是給模型 Python runtime 而非傳統 tool list。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| 開發者 | Prime Intelligence |
| 基礎 | 建構在 [[wiki/entities/pi-agent-core|pi-agent-core]] 之上 |
| 授權 | 完全開源 |
| 核心差異 | Python runtime 取代 tool calling |

## 核心架構

### 1. Python Runtime
- 模型拿到的不是工具列表，而是一個 Python runtime
- 模型寫 Python 腳本執行多個操作，一次執行可等同 50 次 tool call
- 環境是 persistent 的，變數可跨對話保存
- 詳見 [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]]

### 2. RLM（Recursive Language Model）
- 在 Python 程式邏輯中觸發 [[wiki/concepts/recursive-language-model|subagent]]
- 獨立的 agent session，不是傳統 tool-call 式 subagent
- 可在 for 迴圈、條件判斷中動態觸發
- 用於平行委派與 context 管理

### 3. Continual Harness
- Agent 可自動改進自身的 harness
- 比 [[wiki/entities/hermes-agent|Hermes]] 的自動學習更進一步：不只修改行為，還修改 harness 本身
- **⚠️ 風險**：可能讓 agent 學到錯誤行為，需定期審查

## 與其他 Agent 的比較

| Agent | 基礎 | 執行方式 | Subagent |
|-------|------|----------|----------|
| Pi | 自建 runtime | Tool calling | 不支援 |
| Prime Agent | Pi 之上 | Python runtime | RLM function |
| Hermes | 自建 | Tool calling + 自動學習 | 傳統 |
| Tau | Pi Python port | Tool calling | 不支援 |
| Codex | 自建 | Tool calling | Orchestration |

## 來源
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent YouTube 介紹]]

## 相關頁面
- [[wiki/entities/pi-agent-core|pi-agent-core]] — 基礎 runtime
- [[wiki/entities/hermes-agent|hermes-agent]] — 自動學習比較
- [[wiki/entities/tau|tau]] — Pi Python port
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]]
- [[wiki/concepts/recursive-language-model|Recursive Language Model]]
