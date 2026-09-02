---
title: "Pydantic AI — 型別安全的 Python Agent 框架"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [pydantic-ai, python, agent-framework, type-safety, durable-execution, dbos]
topics: [agent-runtime-implementations, coding-agent]
canonical: entities/pydantic-ai
---

# Pydantic AI — 型別安全的 Python Agent 框架

> 由 Pydantic 官方推出，堅持「AI should be plain Python」。端到端型別安全、依賴注入、耐用執行（DBOS/Temporal）。

## 核心架構

- **強型別 Agent**：`Agent[Deps, Output]` 泛型，`RunContext[Deps]` 依賴注入 `[§5]`
- **pydantic-graph**：有限狀態 FSM，靜態約束 LLM 請求與 Tool 調用節點流轉 `[§5]`
- **Capability（能力）**：工具、系統提示詞、生命週期 hooks 包裝成可複用模組，支援 On-Demand 動態加載 `[§5]`

## 耐用執行

與 DBOS（PostgreSQL 輕量持久化引擎）原生整合。`DBOSAgent` 自動建立 Transaction Checkpoints，crash 後從 checkpoint 重播 Resume，不重複執行 LLM 呼叫或副作用 `[§5]`。

## 可觀測性

- `Agent.iter` 迭代器逐節點驅動白盒調試 `[§5]`
- Pydantic Logfire（基於 OTel）原生綁定，實時呈現訊息交換、工具參數 Schema 驗證、Token 耗時與 DBOS Checkpoints `[§5]`

## 多 Agent

Hierarchical Supervisor-Worker 模式。主 Agent 透過 `FunctionTool` 委託子 Agent，子 Agent 的 system instruction、私有變量與 tools 完全隔離。DBOS 環境下子 Agent 自動實例化為耐用子工作流 `[§5]`。

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]

## 相關頁面

- [[wiki/entities/langgraph|LangGraph]] — Python 生態圈的狀態圖框架
- [[wiki/entities/openai-agents-sdk|OpenAI Agents SDK]] — 極低抽象的多 agent 協作框架
