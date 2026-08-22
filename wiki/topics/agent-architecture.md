---
type: topic
title: Agent Architecture
topic: agent-architecture
created: 2026-08-23
updated: 2026-08-23
---

# Agent Architecture

> Agent 核心設計概念與架構模式：harness、迴圈、持久化、擴充層級、型別策略。

## Concepts

- [[wiki/concepts/harness|Harness — LLM 的驅動層]] — 定義 harness 是什麼、三層分類、主流產品比較 🌱
- [[wiki/concepts/continual-harness|Continual Harness]] — harness 狀態 CRUD 化、`/refine` 自我改進 pipeline 🛠️
- [[wiki/concepts/agentic-ai|Agentic AI]] — AI Agent 核心定義、四大支柱、設計模式 🛠️
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — AI Agent 兩種核心工作流模式
- [[wiki/concepts/portable-graph-agent-pattern|Portable Graph Agent Pattern]] — Graph pattern 下放到輕量專家 Agent 的可攜式設計
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/recursive-language-model|Recursive Language Model]] — 把 context 當變數、以函式呼叫觸發的獨立 agent session 🛠️
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]] — 模型寫 Python 腳本執行多操作取代逐次 tool call 🛠️
- [[wiki/concepts/late-conversion|Late Conversion]] — 延遲轉換模式
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級 — 從 Skill、Hook 到 Extension 的能力邊界]] — 三層能力光譜
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 狀態持久化的三種路徑（Pi vs LangGraph）
- [[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]] — Agent 框架之外的可靠性層（Temporal / Azure Durable Functions）🛠️
- [[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]] — 從 Agent 產出到正式發布的多層把關 🛠️

## Entities

- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought (CoT)]] — 讓模型「先想再答」的推理技術

## Sources

- [[wiki/sources/2026-08-03-loop-vs-graph-engineering|Loop vs Graph Engineering — AI Agent Concepts Demystified]] — 兩種 AI Agent 工作流模式
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Agentic Pipeline 調研（Codex）]] — 20 個 Tier 1 來源，Durable Execution + 五類 Gate
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|Agentic Pipeline 調研（Gemini）]] — 7 個 Tier 1 來源，雙層驗證機制

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
