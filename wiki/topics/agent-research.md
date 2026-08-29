---
type: topic
title: Agent Research
topic: agent-research
created: 2026-08-08
updated: 2026-08-13
---

# Agent Research

> 與 Agent Research 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/area-l|AReaL — 開源 Agent 強化學習基礎設施]] — Ant Group 等機構發表的 AReaL2.0 系統，實現 Agent 自我進化的線上強化學習框架。
- [[wiki/entities/graphify|Graphify — 開源代碼知識圖譜工具]] — 接近 10 萬 Star 的 GitHub 開源項目，將代碼庫轉換為知識圖譜，大幅提升 AI Coding Agent 的理解能力。
- [[wiki/entities/memgraph-rag|MemGraph-RAG — 記憶圖譜多智能體檢索增強生成]] — KDD 2026 頂會論文，透過三層全局記憶架構與三大 AI 神探多智能體協作，實現 0.061 秒極速檢索。
- [[wiki/entities/morphir-dotnet|morphir-dotnet]] — FINOS 開發的 .NET binding for Morphir ecosystem，提供 IR（intermediate representation）和 tooling。
- [[wiki/entities/skillopt|SkillOpt — 微軟技能優化器]] — 微軟提出的 SkillOpt 框架，將 SKILL.md 視為可訓練的外部參數，不修改閉源模型權重實現 Agent 自我進化。 🛠️

## Concepts

- [[wiki/concepts/arc-agi-3-benchmark|ARC-AGI-3 Benchmark]] — ARC-AGI-3——ARC Prize Foundation 於 2026 年推出的互動式推理 benchmark，測試 agent 在完全未知規則的模擬世界中探索、建立假設、修正錯誤與長期規劃的能力，用 **RHAE**（Relative Human Action Efficiency）評分。[[wiki/entities/prime-agent|Prime Agent]] 用它作為主要評測案例，也是「harness 差異可以讓同一個模型表現差 3 倍以上」的核心例證。 🛠️
- [[wiki/concepts/atdp|ATDP — Agent Trajectory Data Protocol]] — Agent 軌跡數據協定，將 Agent 與環境互動的每一步結構化標準化，為自我進化奠定數據基礎。
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph — 代碼知識圖譜]] — 結合 tree-sitter AST 解析與圖論算法，將代碼庫轉換為結構化知識圖譜，讓 AI Agent 精確理解代碼架構。
- [[wiki/concepts/context-cot|Context-CoT — 上下文思維鏈]] — 透過「三階煉獄」推理合成 pipeline，強制 LLM 嚴格錨定上下文進行推理，解決「作弊」問題。
- [[wiki/concepts/continual-harness|Continual Harness]] — Continual Harness——把 agent harness 自身的狀態（prompt、skills、memory、sub-agents）抽象成 agent 可以 create/read/update/delete（CRUD）的物件，讓 harness 能根據自己的執行軌跡持續調整，而不是設計時就寫死。[[wiki/entities/prime-agent|Prime Agent]] 的兩大核心抽象之一（另一個是 [[wiki/concepts/recursive-language-model|RLM]]）。 🛠️
- [[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill — 技能優化元技能]] — 關於「如何編寫與重構技能規範」的高階指導法則，控制 LLM 以受控預算進行 SKILL.md 的文字級修復。 🛠️

## 相關 Topics
