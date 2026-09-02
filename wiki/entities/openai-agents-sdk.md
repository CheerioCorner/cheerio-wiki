---
title: "OpenAI Agents SDK — 極低抽象的多 Agent 協作框架"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [openai-agents-sdk, swarm, handoff, multi-agent, python, typescript]
topics: [agent-runtime-implementations, coding-agent]
canonical: entities/openai-agents-sdk
---

# OpenAI Agents SDK — 極低抽象的多 Agent 協作框架

> 前身為 Swarm 教育框架的生產級升級版。僅保留五大核心原語：Agent、Handoff、Guardrail、Session、Tracing。

## 核心原語

1. **Agent** — 模型 + System Instructions + Tools `[§5]`
2. **Handoff** — 去中心化對等交接，封裝為 `transfer_to_<agent_name>` 工具 `[§5]`
3. **Guardrail** — 輸入/輸出防護欄 `[§5]`
4. **Session** — 對話持久化（`SQLiteSession`、`SQLAlchemySession`） `[§5]`
5. **Tracing** — OTel GenAI 語意追蹤 `[§5]`

## 多 Agent 協作

### 去中心化 Handoff
- LLM 視角下就是一個普通工具，命名為 `transfer_to_<agent_name>` `[§5]`
- Runner 暫停當前 Agent、加載目標 Agent 的 System Instructions，Session History 無縫移轉 `[§5]`
- **Hop Counter 防漂移**：交接跳數超標即安全熔斷 `[§5]`

### 經理委託（Agent-as-a-Tool）
- `agent.as_tool()` 將子 Agent 封裝為 Tool `[§5]`
- 子代理運作於獨立 message history 邊界，執行完畢後返回 Zod/Pydantic 驗證的強型別結果 `[§5]`

## 記憶與壓縮

- `OpenAIResponsesCompactionSession` 裝飾器：歷史累積超過閾值時，背景自動調用雲端 `responses.compact` API，將多輪工具對話精簡為合成摘要對 `[§5]`
- 保留最近 N 輪 verbatim 對話，兼顧 prompt cache 與記憶深度 `[§5]`

## 可觀測性

OTel GenAI 語意標準，可視化推理模型 Thinking/Reasoning Spans。原生與 OpenAI 官方 Dashboard 整合 `[§5]`。

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]

## 相關頁面

- [[wiki/entities/openai-codex|OpenAI Codex]] — Codex CLI/Agent 使用 Agents SDK 進行編排
- [[wiki/entities/pydantic-ai|Pydantic AI]] — 另一種型別安全的 Python Agent 框架
