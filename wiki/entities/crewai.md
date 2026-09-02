---
title: "CrewAI — 角色扮演與任務管道的多 Agent 框架"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [crewai, multi-agent, role-play, task-pipeline, python]
topics: [agent-runtime-implementations]
canonical: entities/crewai
---

# CrewAI — 角色扮演與任務管道的多 Agent 框架

> 以 Role-Play（角色扮演）為設計中樞的高階 Python Agent 框架。核心原語：Agents（Persona）、Tasks（output 格式約束）、Crews（團隊）。

## 核心架構

- **應用層高階抽象**：Agents（具備 Persona 設定）+ Tasks（嚴格約束 output 格式）+ Crews（串聯團隊） `[§5]`
- **自動化 Embedding 記憶系統**：對話期間自動將重要事實與用戶偏好進行語意切片，自動產生 Embedding 寫入向量資料庫，實現 LTM/STM 的 RAG 召回 `[§5]`

## 多 Agent 協作

- **Process**：Sequential（順序）或 Hierarchical（層級）串聯 Task 管道 `[§5]`
- 基於業務化的 Task 依賴鏈：Task 1 產生的 `expected_output` 強型別結果自動傳遞給 Task 2 對應的 Specialist Agent `[§5]`
- 適合處理固定且可預測的業務流程 `[§5]`

## 可觀測性

預設較黑盒，主要關注任務執行進度與角色分發。微觀 LLM trace 需外接 OTel 平台（Logfire/Langfuse）或使用官方 Cloud 平台 `[§5]`。

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]
