---
title: AI Agent
topic: ai-agent
created: 2026-08-02
updated: 2026-08-13
---

# AI Agent

> AI Agent 核心概念與框架比較的導航頁。
> 
> ⚠️ 本 topic 已於 2026-08-08 拆分。AI Coding Tools 移至 [[wiki/topics/ai-development-tools|ai-development-tools]]，Agent Research 移至 [[wiki/topics/agent-research|agent-research]]，Agent Infrastructure 移至 [[wiki/topics/agent-infrastructure|agent-infrastructure]]，Pi 專門移至 [[wiki/topics/pi-agent|pi-agent]]。

## Entities

- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought]] — 讓模型「先想再答」的推理技術
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — 模型資源分配與限制
- [[wiki/entities/pi-mono|pi-mono]] — Pi Agent Toolkit monorepo（CLI entry point、extensions、read-only mode） 🛠️
- [[wiki/entities/tau|tau]] — Hugging Face 的 Pi Python port 🛠️
- [[wiki/entities/hermes-agent|hermes-agent]] — Nous Research 自我改進 AI agent 🛠️
- [[wiki/entities/waku-agent|waku-agent]] — Local-first personal AI agent，四大支柱 readable blueprint
- [[wiki/entities/qwenpaw|qwenpaw]] — AgentScope 本地 AI 助理，Agent OS 架構 + 安全沙箱
- [[wiki/entities/langgraph|LangGraph]] — Graph-based agent runtime，Pregel model + checkpointing 🛠️
- [[wiki/entities/omnigent|Omnigent]] — Databricks 開源 meta-harness
- [[wiki/entities/prime-agent|prime-agent]] — 建構於 Pi 之上，用 Python runtime 取代傳統 tool calling 🛠️
- [[wiki/entities/mario-zechner|Mario Zechner]] — Pi 作者
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Agent runtime（agentic loop、session tree、compaction、skills） 🛠️
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Agent Skills 集合（162k stars） 🛠️
- [[wiki/entities/uncle-bob|Uncle Bob (Robert C. Martin)]] — Clean Code 作者、軟體工程資深實踐者
- [[wiki/entities/theo-t3gg|Theo (t3gg)]] — 程式碼重要性光譜與驗證方法論倡導者
- [[wiki/entities/docling|Docling]] — IBM 文件解析框架，把 PDF 轉成結構化 tree document

## Concepts

- [[wiki/concepts/agentic-ai|Agentic AI]] — AI Agent 核心定義、四大支柱、設計模式 🛠️
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — AI Agent 兩種核心工作流模式
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/vibe-coding|Vibe Coding]] — 自然語言驅動的程式碼生成
- [[wiki/concepts/meta-harness|Meta-Harness]] — 對 harness 本身的再抽象/最佳化層
- [[wiki/concepts/late-conversion|Late Conversion]] — 延遲轉換模式
- [[wiki/concepts/defect-metrics|Defect Metrics]] — 缺陷密度與逃脫率指標
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]] — 模型寫 Python 腳本執行多操作取代逐次 tool call 🛠️
- [[wiki/concepts/recursive-language-model|Recursive Language Model]] — 可在 Python 程式邏輯中觸發的獨立 agent session 🛠️
- [[wiki/concepts/code-importance-spectrum|Code Importance Spectrum]] — 程式碼重要性光譜與驗證策略
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 狀態持久化的三種路徑（Pi vs LangGraph）
- [[wiki/concepts/chunkless-rag|Chunkless RAG]] — 保留文件結構的 RAG 方法，Agent 推導航而非 chunks 相似度搜尋

## Sources

- [[wiki/sources/2026-02-10-pi-agent-core-design|Pi Agent Core 設計]] — Mario Zechner 原始設計文件
- [[wiki/sources/2026-05-02-pi-mono-framework-tw|Pi Mono Framework 中文介紹]] — 中文入門文章
- [[wiki/sources/2026-08-05-pi-github-readme|Pi GitHub README]] — Pi repo 結構與安裝
- [[wiki/sources/2026-08-03-tau-python-port-of-pi|Tau — Pi Python Port]] — Hugging Face Python 版
- [[wiki/sources/2026-08-05-tau-github-readme|Tau GitHub README]] — Tau repo 結構
- [[wiki/sources/2026-08-05-hermes-agent-github-readme|Hermes Agent GitHub README]] — 自我改進 AI agent
- [[wiki/sources/2026-08-08-hermes-architecture-explained|Hermes Architecture Explained]] — 架構深度解析（YouTube）
- [[wiki/sources/2026-08-02-qwenpaw-github-readme|QwenPaw GitHub README]] — AgentScope 本地 AI 助理
- [[wiki/sources/2026-08-08-qwenpaw-youtube-intro|QwenPaw YouTube Intro]] — QwenPaw 介紹（YouTube）
- [[wiki/sources/2026-08-02-waku-agent-github-readme|Waku Agent GitHub README]] — Local-first personal AI agent
- [[wiki/sources/2026-08-02-waku-agent-code-walkthrough|Waku Agent Code Walkthrough]] — 原始碼解析（YouTube）
- [[wiki/sources/2026-08-03-loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種 AI Agent 工作流模式
- [[wiki/sources/2026-07-18-pi-resource-inventory|Pi Resource Inventory]] — Pi 資源盤點
- [[wiki/sources/2026-08-21-langgraph-in-10-minutes|LangGraph in 10 Minutes]] — LangGraph runtime 深度解析
- [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code|Uncle Bob Stopped Reading AI-Generated Code]] — AI 程式碼驗證方法論（YouTube）
- [[wiki/sources/2026-08-10-write-code-you-will-never-read-again|Write Code You Will Never Read Again]] — 程式碼重要性光譜（YouTube）
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent YouTube 介紹]] — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent
