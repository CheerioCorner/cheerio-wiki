---
type: topic
title: Agent Runtime Implementations
topic: agent-runtime-implementations
created: 2026-08-23
updated: 2026-08-23
---

# Agent Runtime Implementations

> 具體命名的 agent 框架、工具與平台實作案例。

## Entities

- [[wiki/entities/claude-code|Claude Code — Anthropic AI Coding Agent]] — 基於 MCP 的 agentic coding tool 🛠️
- [[wiki/entities/github-copilot|GitHub Copilot — AI 程式碼輔助生態系]] — 從 IDE 補全演化為完整 Agent 生態系 🛠️
- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK — Custom Agents 平台]] — Custom Agents SDK，hooks / fleet-mode / MCP 🛠️
- [[wiki/entities/langgraph|LangGraph — Graph-based Agent Runtime]] — LangChain 的低階 agent orchestration 框架 🛠️
- [[wiki/entities/mcp-model-context-protocol|MCP (Model Context Protocol)]] — Anthropic 開放標準，封裝 LSP / Code Graph 為 Tools 🛠️
- [[wiki/entities/hermes-agent|hermes-agent — 開源 AI Agent 框架]] — Nous Research 的 40+ 工具鏈、跨會話記憶 agent 🛠️
- [[wiki/entities/waku-agent|waku-agent — Local-first personal AI agent]] — 展示 agent 四大支柱：harness / loop / memory / eval
- [[wiki/entities/qwenpaw|QwenPaw — Agent OS 架構的本地 AI 助理]] — AgentScope 的本地部署 + 安全沙箱
- [[wiki/entities/prime-agent|Prime Agent — 用 Python Runtime 取代 Tool Calling]] — Prime Intellect 的 self-improving coding harness 🛠️
- [[wiki/entities/tau|tau — Pi 的 Python 移植版]] — Hugging Face 的 Pi Python port 🛠️
- [[wiki/entities/mattpocock-skills|mattpocock/skills — AI Coding Agent Skills 集合]] — 162k+ stars，7.5M+ downloads 🛠️
- [[wiki/entities/skillopt|SkillOpt — 微軟技能優化器]] — 將 SKILL.md 視為可訓練的外部參數 🛠️
- [[wiki/entities/langchain-openwiki|OpenWiki（LangChain）]] — Agent 專用 Repo Wiki CLI，OKF v0.2 🛠️
- [[wiki/entities/coinbase-ai-engineering|Coinbase AI Engineering]] — 多模型基礎設施與成本工程實踐 🛠️

## Concepts

- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]] — Claude Code / Copilot / Pi 三框架比較
- [[wiki/concepts/multi-model-cost-routing|Multi-Model Cost Routing]] — 多模型基礎設施的成本工程 🛠️

## Sources

- [[wiki/sources/2026-08-02-qwenpaw-github-readme|QwenPaw GitHub README]] — AgentScope 本地 AI 助理
- [[wiki/sources/2026-08-08-qwenpaw-youtube-intro|QwenPaw YouTube Intro]] — QwenPaw 介紹影片
- [[wiki/sources/2026-08-02-waku-agent-github-readme|Waku Agent GitHub README]] — Local-first personal AI agent
- [[wiki/sources/2026-08-02-waku-agent-code-walkthrough|Waku Agent Code Walkthrough]] — 原始碼解析（YouTube）
- [[wiki/sources/2026-08-05-hermes-agent-github-readme|Hermes Agent GitHub README]] — 自我改進 AI agent
- [[wiki/sources/2026-08-08-hermes-architecture-explained|Hermes Architecture Explained]] — 架構深度解析（YouTube）
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent YouTube 介紹]] — 用 Python Runtime 取代 Tool Calling
- [[wiki/sources/2026-08-13-prime-agent-official-blog|Prime Agent 官方部落格全文]] — RLM / Continual Harness 完整定義
- [[wiki/sources/2026-08-13-prime-agent-arc-agi-3-aiposthub|Prime Agent ARC-AGI-3 分析（AI郵報）]] — 拆解三種成績口徑
- [[wiki/sources/2026-08-03-tau-python-port-of-pi|Tau — Pi Python Port]] — Hugging Face Python 版
- [[wiki/sources/2026-08-05-tau-github-readme|Tau GitHub README]] — Tau repo 結構
- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|mattpocock/skills 教學]] — 完整 AI Coding 工作流程
- [[wiki/sources/2026-08-21-langgraph-in-10-minutes|LangGraph in 10 Minutes]] — LangGraph runtime 深度解析
- [[wiki/sources/2026-08-10-antigravity-cli-integration|Antigravity CLI 整合]] — Gemini CLI 整合方案
- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF + LSP + CodeGraph AI Agent 研究]] — 三者整合分析
- [[wiki/sources/2026-08-20-langchain-openwiki-cli|LangChain OpenWiki CLI — YouTube 影片]] 🛠️
- [[wiki/sources/2026-08-20-langchain-openwiki-github-repo|OpenWiki GitHub README]] 🛠️
- [[wiki/sources/2026-08-20-openwiki-0-2-brings-okf-to-codebase-docs|OpenWiki 0.2 官方部落格]] 🛠️
- [[wiki/sources/2026-08-20-docs-langchain-openwiki-overview|OpenWiki 官方文件 Overview]] 🛠️
- [[wiki/sources/2026-08-20-antaoalmada-openwiki-and-okf-agent-wikis|OpenWiki & OKF — Antão Almada]] 🛠️
- [[wiki/sources/2026-08-20-thenewstack-multi-model-ai-infrastructure|Coinbase 1,200 agents — The New Stack]] 🛠️
- [[wiki/sources/2026-08-20-coinbase-ceo-5-strategies-ai-spend|Coinbase CEO 5 策略 — Yahoo Finance]] 🛠️
- [[wiki/sources/2026-08-20-how-coinbase-cut-its-ai-spend-by-50|Coinbase cut AI spend 50% — 付費牆版]] 🛠️
- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]] — AI Code Assistant 核心基礎
- [[wiki/sources/2026-08-02-morphir-dotnet-agents|Morphir .NET Agents]] — FINOS .NET binding

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — 協定、語言工具、企業治理
