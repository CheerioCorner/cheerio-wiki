---
type: topic
title: Agent Runtime Implementations
topic: agent-runtime-implementations
created: 2026-08-23
updated: 2026-08-23
---

# Agent Runtime Implementations

> 與 Agent Runtime Implementations 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/claude-code|Claude Code — Anthropic AI Coding Agent]] — Claude Code 是 Anthropic 推出的 agentic coding tool，以終端機為起點，擴展到 Desktop App、Web、IDE、GitHub（`@claude` tag）。核心建立在 MCP（Model Context Protocol）之上，能自主觀察 codebase → 推理 → 執行工具 → 評估結果 → 重複循環。 🛠️
- [[wiki/entities/coinbase-ai-engineering|Coinbase AI Engineering — 多模型基礎設施與成本工程實踐]] — Coinbase 用 LLM gateway、task-based routing、aggressive caching 三大槓桿，在增加 AI 用量的同時降低 50% 花費。
- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK — Custom Agents 平台]] — GitHub Copilot SDK 是 Copilot 的**程式化代理平台**，提供 Custom Agents、Sub-agent Orchestration、Custom Skills、Fleet Mode、Hooks、MCP 整合等完整 agent 能力。**⚠️ 此頁面基於 GitHub 官方文件（docs.github.com），修正了先前基於第三方文章的多處錯誤描述。** 🛠️
- [[wiki/entities/github-copilot|GitHub Copilot — AI 程式碼輔助生態系]] — GitHub Copilot 是 GitHub（Microsoft 旗下）推出的 AI 程式碼輔助產品線，從 IDE 補全演化為完整 Agent 生態系：Extensions SDK、Agent Skills、Cloud Agent、Code Review、MCP 整合。 🛠️
- [[wiki/entities/hermes-agent|hermes-agent — 開源 AI Agent 框架]] — Nous Research 開源的 AI Agent，具備 40+ 工具鏈、跨會話記憶與動態 Skill 庫生成機制。
- [[wiki/entities/langchain-openwiki|OpenWiki（LangChain）— Agent 專用 Repo Wiki CLI]] — LangChain 團隊開發的開源 CLI 工具，自動產生和維護 repo wiki，專為 AI agent 消費設計，採用 Google OKF v0.2 格式。 🛠️
- [[wiki/entities/langgraph|LangGraph — Graph-based Agent Runtime with Durability]] — LangGraph 是 LangChain 團隊開發的低階 agent orchestration 框架，核心價值在 durability（checkpointing / human-in-the-loop / time travel），而非 graph API 本身。建立在 Google Pregel 論文（2010）模型上。
- [[wiki/entities/mattpocock-skills|mattpocock/skills — AI Coding Agent Skills 集合]] — Matt Pocock 維護的 AI coding agent skills 集合，162k+ stars，7.5M+ downloads。 🛠️
- [[wiki/entities/mcp-apps|MCP Apps — 互動式應用程式]] — MCP Apps 讓伺服器在聊天中直接渲染互動式 HTML 介面（資料視覺化、表單、儀表板），使用者無需離開對話即可與資料互動。 🛠️
- [[wiki/entities/mcp-model-context-protocol|MCP (Model Context Protocol)]] — Anthropic 發起的開源標準，讓 AI 應用程式透過統一協定連接外部系統——就像 AI 世界的 USB-C 介面。 🛠️
- [[wiki/entities/prime-agent|Prime Agent — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent]] — Prime Intellect 於 2026-08-05 發布的開源 self-improving coding harness，建構在 [[wiki/entities/pi-agent-core|pi-agent-core]] 之上，核心差異是給模型持久 IPython runtime 而非傳統 tool list，並用 [[wiki/concepts/continual-harness|Continual Harness]] 讓 agent 自己改寫自己的 harness 狀態。
- [[wiki/entities/qwenpaw|QwenPaw — Agent OS 架構的本地 AI 助理]] — AgentScope 團隊開發的開源個人 AI 助理，以 Agent OS 架構為核心，強調本地部署、安全沙箱與多通道整合。
- [[wiki/entities/skillopt|SkillOpt — 微軟技能優化器]] — 微軟提出的 SkillOpt 框架，將 SKILL.md 視為可訓練的外部參數，不修改閉源模型權重實現 Agent 自我進化。 🛠️
- [[wiki/entities/tau|tau — Pi 的 Python 移植版]] — Tau 是 Pi 的 **Python port**，由 [Hugging Face](https://huggingface.co) 開發並開源（MIT license）。架構與 Pi 完全相同，差異只在 TUI 層使用 Textual 框架。
- [[wiki/entities/waku-agent|waku-agent — Local-first personal AI agent with harness + loop + memory + eval]] — Waku agent 是一個 local-first 個人 AI 助理，以 readable code 展示 agent 系統的四大支柱：harness、loop、memory、eval/LLM-Ops。

## Concepts

- [[wiki/concepts/mcp-client-types|MCP Client Types — 類型與連線模式]] — MCP 支援多種終端用戶端類型（CLI、TUI、Web），每種連線模式（本地 STDIO、遠端 Streamable HTTP）各有適用場景。 🛠️
- [[wiki/concepts/multi-model-cost-routing|Multi-Model Cost Routing — 多模型基礎設施的成本工程]] — 從 Coinbase 和 Vercel 的實踐中抽象出的多模型路由設計原則：gateway 作為 control plane、模型可替換化、評測先行於信任。
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較 — Claude Code / GitHub Copilot / Pi Agent]] — 三大主流 Agent 框架在處理複雜、組合技 Skill 時的機制與設計取捨：如何組合呼叫、巢狀/委派機制、工具鏈整合。 🛠️

## Sources

- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|mattpocock/skills — 完整 AI Coding 工作流程教學]] — 來源：[YouTube — mattpocock/skills: A complete AI Coding workflow, end-to-end](https://www.youtube.com/watch?v=M6mYodf0dJM)
- [[wiki/sources/2026-08-02-morphir-dotnet-agents|morphir-dotnet AGENTS.md — AI Agent 貢獻指南]] — FINOS morphir-dotnet 專案的 AGENTS.md，展示企業級 .NET 專案如何組織 AI agent 指南。 🛠️
- [[wiki/sources/2026-08-02-qwenpaw-github-readme|QwenPaw: GitHub README — 完整功能與安裝指南]] — 來源：[agentscope-ai/QwenPaw GitHub README](https://github.com/agentscope-ai/QwenPaw)
- [[wiki/sources/2026-08-02-waku-agent-code-walkthrough|Waku Waku! Waku agent — code walkthrough]] — 來源：[YouTube — Waku Waku! Waku agent is your personal AI agent](https://youtu.be/rvRyBhILrls)
- [[wiki/sources/2026-08-02-waku-agent-github-readme|Waku agent: GitHub README]] — 來源：[GitHub — ShenSeanChen/waku-agent](https://github.com/ShenSeanChen/waku-agent)
- [[wiki/sources/2026-08-03-tau-python-port-of-pi|Tau: A Python Port of Pi — Coding Harness Deep Dive]] — 來源：[YouTube — Tau: A Python Port of Pi](https://www.youtube.com/watch?v=qo1QNxWcm28)（25:03）
- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]] — Gemini Deep Research 完成的 LSP（Language Server Protocol）與 Code Graph 技術研究報告，涵蓋架構、實作案例、AI 整合應用。 🛠️
- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF + LSP + Code Graph 在 AI Coding Agent 生態系的整合應用]] — Gemini Deep Research 完成的三位一體程式碼智慧架構研究，涵蓋 OKF、LSP、Code Graph 在主流 AI Coding Agent 的整合應用。 🛠️
- [[wiki/sources/2026-08-05-hermes-agent-github-readme|Hermes Agent: GitHub README — 自我改進 AI Agent]] — 來源：[NousResearch/hermes-agent GitHub README](https://github.com/NousResearch/hermes-agent) 🛠️
- [[wiki/sources/2026-08-05-tau-github-readme|Tau: GitHub README]] — 來源：[huggingface/tau GitHub README](https://github.com/huggingface/tau)
- [[wiki/sources/2026-08-08-hermes-architecture-explained|Hermes Architecture EXPLAINED: Memory, Context & Gateways]] — 來源：[YouTube — Hermes Architecture EXPLAINED: Memory, Context & Gateways](https://youtu.be/n32qq7Kwzh0)
- [[wiki/sources/2026-08-08-qwenpaw-youtube-intro|QwenPaw: Your Personal AI Assistant — YouTube 介紹影片]] — 來源：[YouTube — QwenPaw: Your Personal AI Assistant](https://youtu.be/QE6FVnNcd3Y)
- [[wiki/sources/2026-08-10-antigravity-cli-integration|Antigravity CLI 整合 Session]] — 2026-08-10 的實作 session，整合 Antigravity CLI 取代已停用的 Gemini CLI 免費層。
- [[wiki/sources/2026-08-13-prime-agent-arc-agi-3-aiposthub|Prime Agent 是什麼？ARC-AGI-3「95.5%」背後（AI郵報分析）]] — AI 郵報（作者 Philo）針對 [[wiki/entities/prime-agent|Prime Agent]] 發布的獨立中文分析文，2026-08-06 刊出。核心貢獻是拆解 ARC-AGI-3「95.5%」宣稱背後的三種成績口徑，並提醒 `/refine` 自我改進機制的 reward hacking 風險。
- [[wiki/sources/2026-08-13-prime-agent-official-blog|Prime Agent: A self-improving RLM agent（官方部落格）]] — Prime Intellect 官方部落格原文，2026-08-05 發布，宣布開源 self-improving coding harness [[wiki/entities/prime-agent|Prime Agent]]，作者 Seth Karten、Alex L. Zhang、Kevin Thomas、Sebastian Müller 與 Prime Intellect Team。
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent]] — Martí Blanes 介紹 Prime Agent：建構於 Pi 之上，用 Python runtime 取代傳統 tool calling 的開源 coding agent。
- [[wiki/sources/2026-08-20-coinbase-ceo-5-strategies-ai-spend|Coinbase CEO outlined 5 strategies to keep AI spend low]] — Yahoo Finance 報導（2026-06-29）：Brian Armstrong 的 5 個降低成本策略，不限制 token 用量。
- [[wiki/sources/2026-08-20-docs-langchain-openwiki-overview|OpenWiki Official Docs — Overview]] — OpenWiki 官方文件 overview 頁。Deep Agents 驅動、LangSmith tracing、兩種模式。 🛠️
- [[wiki/sources/2026-08-20-how-coinbase-cut-its-ai-spend-by-50|How Coinbase cut its AI spend by 50% with 5 smart strategies]] — ⚠️ 付費牆文章，僅有摘要段落可讀。完整 5 招內容以 raw 8/9 為準。
- [[wiki/sources/2026-08-20-langchain-openwiki-cli|LangChain OpenWiki CLI — 給 AI Agent 讀的自動化 Repo Wiki]] — LangChain 團隊发布的 CLI 工具，自動產生和維護 repo wiki，專門給 AI agent 消費，採用 Google OKF v0.2 格式。 🛠️
- [[wiki/sources/2026-08-20-langchain-openwiki-github-repo|OpenWiki GitHub Repository — 15.4k Stars, MIT License]] — LangChain OpenWiki 的 GitHub repo：15.4k stars、1.1k forks、MIT license，Deep Agents 驅動的 CLI 工具。 🛠️
- [[wiki/sources/2026-08-20-thenewstack-multi-model-ai-infrastructure|Coinbase runs 1,200 agents and just slashed its AI bill in half]] — The New Stack 報導（2026-07-07）：Coinbase 1,200 AI agents，三大槓桿降半成本。Vercel 日路由破兆 token。
- [[wiki/sources/2026-08-21-langgraph-in-10-minutes|LangGraph in 10 Minutes (Explained Clearly)]] — 來源：[YouTube — LangGraph in 10 Minutes (Explained Clearly)](https://www.youtube.com/watch?v=BwZbdCzmZJc)

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — 協定、語言工具、企業治理
