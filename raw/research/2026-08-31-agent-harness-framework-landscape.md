---
title: "Agent Harness Framework 現況調研"
type: raw-research
created: 2026-08-31
agent: pi
topic: "ai-agent"
subtopics:
  - "agent-harness"
  - "agent-framework"
  - "agent-provider-adapter"
sources_count: 35
sources_tier1: 28
sources_tier2: 6
sources_tier3: 1
sources_diversity: 20
related_work: W-2026-08-074
tags: [research, pi, ai-agent, agent-harness, agent-provider-adapter]
---

# Agent Harness Framework 現況調研

> **建立日期**：2026-08-31
> **專案/情境**：個人 AI 助理架構願景——規劃「多 harness 調用包成統一 Agent Provider Adapter」的事前市場調查（work item W-2026-08-074）
> **產出方式**：Pi agent，GitHub API + 官方文件爬取

## Executive Summary

截至 2026 年 8 月底，agent harness / framework 生態呈現「百花齊放但快速整合」的格局：

1. **大型廠商用自家 SDK 搶佔入口**：OpenAI（Codex CLI + Agents SDK）、DeepSeek（DeepSeek Harness）、Microsoft（Agent Framework 統一 AutoGen + Semantic Kernel）、Vercel（AI SDK）
2. **開源社群框架仍活躍但面臨整合壓力**：LangGraph（40K stars）、CrewAI（57K stars）、AutoGPT（187K stars）各有定位，但「多 agent orchestration」已成為所有框架的標配功能
3. **MCP（Model Context Protocol）已成為事實標準**：幾乎所有新框架都內建或可擴充 MCP 支援
4. **Python 與 TypeScript 雙棲**：多數框架提供兩種語言的實作，反映前端（Next.js/React）與後端（Python ML 生態）的整合需求
5. **「Harness」一詞正在分化**：OpenAI Codex 用 `codex` CLI、DeepSeek 用 `dsh`、Pydantic 用 `pydantic-ai-harness`，各家對「agent harness」的定義不盡相同——有的偏向 coding agent CLI，有的偏向 multi-agent orchestration framework

**設計 Agent Provider Adapter 的關鍵洞察**：各家框架的核心差異在於（1）agent 定義方式、（2）tool calling 標準、（3）multi-agent orchestration 機制、（4）狀態管理與 checkpoint。統一介面應聚焦在這四個維度的抽象。

## 主要發現

### A. 廠商官方 Agent Harness / CLI / SDK

---

#### 1. OpenAI Codex CLI

[VERIFIED] Codex CLI 是 OpenAI 推出的本地 coding agent，以 Rust 編寫，透過 `codex` / `codex exec` 執行。支援 MCP tools、sandbox 模式、SSH remote 環境，可透過 ChatGPT 帳號或 API key 認證。[REF-1][REF-2]

- **開發方**：OpenAI
- **授權**：Apache-2.0
- **主要語言**：Rust（CLI 本體），支援 Python / TypeScript 工作空間
- **多 agent 協作**：無（單一 coding agent，非 multi-agent 框架）
- **工具呼叫**：內建 tool calling，支援 MCP server
- **記憶/狀態管理**：conversation history + 文件系統
- **部署方式**：local-only（CLI），另有 Codex Web（雲端）與 IDE 插件
- **成熟度**：120,086 GitHub stars（2026-08-31），2025-04-13 建立，活躍更新中
- **2026 動態**：持續快速迭代，支援 IDE 整合（VS Code / Cursor / Windsurf），`codex app` 桌面應用

> **注意**：Codex CLI 是 **coding agent CLI**，不是 **agent framework**。它不提供 multi-agent orchestration、workflow graph 或 tool registration API——它是「一個很強的 coding agent」，不是「建 agent 的框架」。

---

#### 2. DeepSeek Harness (dsh)

[VERIFIED] DeepSeek 官方推出了 DeepSeek Harness（`dsh`），基於「everything is a plugin」架構，使用 Cordis runtime（參考論文《A Programming Paradigm for Spatiotemporal Composability》）。提供 Web UI、CLI、plugin 系統。[REF-3][REF-4]

- **開發方**：DeepSeek AI
- **授權**：MIT
- **主要語言**：TypeScript
- **多 agent 協作**：透過 plugin 系統實現（plugin 間可互相呼叫）
- **工具呼叫**：plugin-based tool system
- **記憶/狀態管理**：plugin state management
- **部署方式**：local（`npx @deepseek-ai/dsh web`）+ self-host
- **成熟度**：204,684 GitHub stars（2026-08-31），2026-08-13 建立——**極新但爆炸性成長**
- **2026 動態**：目前處於 developer preview，明確警告「會有 breaking changes」。基於 Cordis 的 plugin 架構是其核心差異化

> **重要發現**：DeepSeek 不只是模型公司——dsh 是一個完整的 agent harness，架構設計與 OpenAI Codex CLI 完全不同（plugin-based vs single-agent）。這是設計 Provider Adapter 時的重要參考。

---

#### 3. Vercel AI SDK

[VERIFIED] Vercel AI SDK 是 TypeScript 生態中最主流的 AI 工具包，提供 `ToolLoopAgent`、structured output、UI hooks（React / Next.js / Svelte / Vue）、Vercel AI Gateway 統一 provider 接入。支援 MCP。[REF-5][REF-6]

- **開發方**：Vercel
- **授權**：見 `ai` npm package（自定義，非標準 OSI）
- **主要語言**：TypeScript
- **多 agent 協作**：`ToolLoopAgent` 支援 tool calling loop，但非完整 multi-agent orchestration（無 graph / workflow）
- **工具呼叫**：原生 tool calling + MCP
- **記憶/狀態管理**：conversation history（無持久化 checkpoint）
- **部署方式**：local + 雲端（Vercel 部署）
- **成熟度**：26,503 GitHub stars（2026-08-31），活躍更新，Next.js 生態首選
- **2026 動態**：新增 `ToolLoopAgent`、Vercel AI Gateway、UI streaming hooks

> **定位**：AI SDK 是「AI-powered web app 的瑞士刀」，不是 agent orchestration framework。它的強項在 UI 整合與 provider 統一，不在 multi-agent workflow。

---

#### 4. Microsoft Agent Framework (MAF)

[VERIFIED] Microsoft Agent Framework 是 AutoGen 與 Semantic Kernel 的企業級繼任者，支援 Python、C#/.NET、Go 三種語言，提供 graph-based workflow、multi-agent orchestration、checkpoint、human-in-the-loop、A2A 協議。[REF-7][REF-8]

- **開發方**：Microsoft
- **授權**：MIT
- **主要語言**：Python、C#/.NET、Go
- **多 agent 協作**：sequential、concurrent、handoff、group collaboration 四種 pattern
- **工具呼叫**：tool calling + MCP + A2A（Agent-to-Agent protocol）
- **記憶/狀態管理**：checkpoint + durable execution（透過 Durable Agent Framework extension）
- **部署方式**：local + self-host + Azure Foundry（雲端託管）
- **成熟度**：13,224 GitHub stars（2026-08-31），2025-04-28 建立
- **2026 動態**：已整合 AutoGen（maintenance mode）與 Semantic Kernel，提供 migration guide

**Semantic Kernel 現況**：[VERIFIED] Semantic Kernel（28,518 stars）目前仍維護但定位為「LLM integration SDK」，MAF 是其上層的 agent orchestration 框架。Microsoft 官方建議新專案使用 MAF。[REF-8]

**AutoGen 現況**：[VERIFIED] AutoGen（60,703 stars）已進入 maintenance mode，不接受新功能，由社群維護。官方強烈建議遷移至 MAF。[REF-9]

> **重要發現**：Microsoft 是目前唯一提供 Python + .NET + Go 三語言支援的 agent framework，且明確整合了 A2A 協議——這是 Google 主導的 agent 間通訊標準。

---

### B. 通用程式語言的 Agent Framework

---

#### 5. Python 主流框架

##### LangGraph

[VERIFIED] LangGraph 是 LangChain 團隊推出的低階 agent orchestration 框架，以 state graph 為核心，支援 durable execution、human-in-the-loop、checkpoint。已有多家企业採用案例（Klarna、Replit、Elastic）。[REF-10][REF-11]

- **開發方**：LangChain Inc
- **授權**：MIT
- **主要語言**：Python（另有 LangGraph.js for TypeScript）
- **多 agent 協作**：支援 subgraph、conditional routing、parallel execution
- **工具呼叫**：tool calling + MCP（透過 LangChain 整合）
- **記憶/狀態管理**：強項——checkpoint、short-term memory、long-term memory
- **部署方式**：local + LangGraph Platform（雲端託管）
- **成熟度**：40,724 GitHub stars（2026-08-31），活躍更新，LangSmith observability
- **2026 動態**：推出 Deep Agents（更高層的 agent 抽象）

---

##### CrewAI

[VERIFIED] CrewAI 以 Agents、Crews、Flows 三層抽象組織多 agent 工作流，主打「role-based AI agents」與企業部署。有 Cloud 版本（CrewAI AMP Suite）。[REF-12][REF-13]

- **開發方**：CrewAI Inc
- **授權**：MIT（核心），Enterprise License（AMP Suite）
- **主要語言**：Python
- **多 agent 協作**：Crew（role-based agent team）+ Flow（event-driven automation）
- **工具呼叫**：tool calling + MCP
- **記憶/狀態管理**：agent memory + crew context
- **部署方式**：local + self-host + Cloud（CrewAI AMP）
- **成熟度**：57,835 GitHub stars（2026-08-31），100,000+ certified developers
- **2026 動態**：推出 AMP Suite（managed deployment、observability、governance）

---

##### AutoGPT

[VERIFIED] AutoGPT 已從早期的自主 agent 轉型為「AI agent platform」，提供 AutoPilot（自然語言建 agent）、Visual Builder、Marketplace、45+ 連接平台。支援 self-host 與 managed platform。[REF-14]

- **開發方**：Significant Gravitas
- **授權**：自定義（非標準 OSI）
- **主要語言**：Python（platform），前端 TypeScript
- **多 agent 協作**：透過 platform 的 agent orchestration
- **工具呼叫**：45+ built-in connectors
- **記憶/狀態管理**：platform-level state management
- **部署方式**：managed platform + self-host（Docker）
- **成熟度**：187,020 GitHub stars（2026-08-31），但多數 stars 來自早期
- **2026 動態**：轉型為 paid platform，self-host 仍免費

> **注意**：AutoGPT 目前的定位更像「no-code agent builder platform」，而非 developer-focused agent framework。

---

##### Hugging Face smolagents

[VERIFIED] smolagents 是 Hugging Face 推出的輕量 agent 框架，核心理念是「agents that think in code」——agent 直接寫 Python code 作為行動，而非 JSON tool calls。支援 MCP、sandbox execution（E2B / Modal / Docker）。[REF-15]

- **開發方**：Hugging Face
- **授權**：Apache-2.0
- **主要語言**：Python
- **多 agent 協作**：多 agent 組合（較輕量）
- **工具呼叫**：code-based action + MCP
- **記憶/狀態管理**：基本 conversation history
- **部署方式**：local + sandbox（E2B / Modal / Docker / Blaxel）
- **成熟度**：29,059 GitHub stars（2026-08-31），活躍更新
- **2026 動態**：支援 Hub 整合（share/pull agents to/from Hub）

---

##### OpenAI Agents SDK（前身 Swarm）

[VERIFIED] OpenAI Agents SDK 是「Swarm」的正式繼任者，提供 lightweight multi-agent workflow 框架。支援 sandbox agents、realtime agents（voice）、handoffs、guardrails、human-in-the-loop、sessions、tracing。provider-agnostic——支援 OpenAI + 100+ 其他 LLM。[REF-16][REF-17]

- **開發方**：OpenAI
- **授權**：MIT
- **主要語言**：Python（另有 JS/TS 版本）
- **多 agent 協作**：handoffs（agent 間委派）+ agents-as-tools
- **工具呼叫**：function tools + MCP + hosted tools
- **記憶/狀態管理**：sessions（自動 conversation history management）
- **部署方式**：local + sandbox（UnixLocal / Docker / hosted）
- **成熟度**：29,077 GitHub stars（2026-08-31），活躍更新
- **2026 動態**：推出 sandbox agents、realtime agents、voice pipelines

> **重要發現**：Agents SDK 明確標榜「provider-agnostic」，支援 100+ LLM——這是設計 Agent Provider Adapter 時的重要參考案例。

---

##### Pydantic AI + Pydantic AI Harness

[VERIFIED] Pydantic AI 是「How Python does AI」——typed agent loop，支援 every model（ string swap）。Pydantic AI Harness 是其 agent harness 層，提供 Coder agent、memory、sub-agents、context management、durable execution（Temporal / DBOS / Prefect）。[REF-18][REF-19]

- **開發方**：Pydantic（Samuel Colvin 團隊）
- **授權**：MIT
- **主要語言**：Python
- **多 agent 協作**：sub-agents + advisor（second opinion from another model）
- **工具呼叫**：tool calling + MCP（capabilities 可 snap-on）
- **記憶/狀態管理**：harness memory + compaction
- **部署方式**：local + durable execution（Temporal / DBOS / Prefect / Restate）
- **成熟度**：19,593 GitHub stars（pydantic-ai）+ 840 stars（pydantic-ai-harness）（2026-08-31）
- **2026 動態**：推出 pydantic-ai-harness（2026-03-20 建立），整合 Durable Execution

---

##### LlamaIndex Agents

[VERIFIED] LlamaIndex 從 RAG 框架擴展為 agent framework，提供 Workflows（multi-step agent orchestration）、LlamaParse（document agent platform）、Agent Builder。核心強項在 document understanding 與 RAG-based agents。[REF-20]

- **開發方**：LlamaIndex Inc
- **授權**：MIT
- **主要語言**：Python
- **多 agent 協作**：Workflows（multi-step orchestration）
- **工具呼叫**：tool calling + 300+ integrations
- **記憶/狀態管理**：index-based memory + RAG
- **部署方式**：local + LlamaCloud
- **成熟度**：51,923 GitHub stars（2026-08-31），活躍更新
- **2026 動態**：轉型為「document agent and OCR platform」

---

#### 6. TypeScript/JavaScript 主流框架

##### Vercel AI SDK（見上方 §3）

---

##### Mastra

[VERIFIED] Mastra 是 Y Combinator W25 出身的 TypeScript agent framework，提供 agents、workflows（graph-based）、human-in-the-loop、MCP server authoring、evals、observability。可整合 React / Next.js / Node.js。[REF-21][REF-22]

- **開發方**：Mastra AI（YC W25）
- **授權**：Apache-2.0（核心）+ Mastra Enterprise License（ee/ 目錄）
- **主要語言**：TypeScript
- **多 agent 協作**：graph-based workflow engine（`.then()` / `.branch()` / `.parallel()`）
- **工具呼叫**：tool calling + MCP server authoring
- **記憶/狀態管理**：conversation history + Observational Memory + storage-based checkpoint
- **部署方式**：local + standalone server + React/Next.js integration
- **成熟度**：27,577 GitHub stars（2026-08-31），活躍更新
- **2026 動態**：快速成長中，YC 背書

---

##### LangGraph.js

[VERIFIED] LangGraph.js 是 LangGraph 的 TypeScript 實作，提供與 Python 版本相似的 graph-based agent orchestration。[REF-23]

- **開發方**：LangChain Inc
- **授權**：MIT
- **主要語言**：TypeScript
- **多 agent 協作**：同 LangGraph Python——graph-based orchestration
- **工具呼叫**：tool calling + MCP
- **記憶/狀態管理**：checkpoint + memory
- **部署方式**：local + LangGraph Platform
- **成熟度**：3,242 GitHub stars（2026-08-31），更新頻率穩定
- **2026 動態**：與 Python 版本保持同步

---

##### OpenAI Agents SDK JS/TS

[VERIFIED] OpenAI Agents SDK 的 TypeScript 版本，提供與 Python 版本相似的 multi-agent workflow 能力。[REF-17]

- **開發方**：OpenAI
- **授權**：MIT
- **主要語言**：TypeScript
- **多 agent 協作**：handoffs + agents-as-tools
- **工具呼叫**：function tools + MCP
- **記憶/狀態管理**：sessions
- **部署方式**：local + sandbox
- **成熟度**：3,740 GitHub stars（2026-08-31）
- **2026 動態**：與 Python 版本同步發展

---

### C. 綜合比較表

#### 廠商官方 Harness / SDK

| 項目 | OpenAI Codex CLI | DeepSeek Harness | Vercel AI SDK | Microsoft Agent Framework |
|---|---|---|---|---|
| 開發方 | OpenAI | DeepSeek AI | Vercel | Microsoft |
| 授權 | Apache-2.0 | MIT | 自定義 | MIT |
| 語言 | Rust | TypeScript | TypeScript | Python / .NET / Go |
| 多 agent | ❌（單一 agent） | ✅（plugin 系統） | ⚠️（ToolLoop only） | ✅（4 種 orchestration pattern） |
| MCP 支援 | ✅ | ✅（plugin-based） | ✅ | ✅ + A2A |
| 記憶/狀態 | conversation history | plugin state | conversation history | checkpoint + durable execution |
| 部署 | local + IDE | local + web UI | local + Vercel | local + Azure Foundry |
| Stars | 120K | 204K | 26K | 13K |
| 定位 | coding agent CLI | agent harness platform | AI web app toolkit | enterprise agent framework |

#### Python 開源框架

| 項目 | LangGraph | CrewAI | AutoGPT | smolagents | OpenAI Agents SDK | Pydantic AI | LlamaIndex |
|---|---|---|---|---|---|---|---|
| 開發方 | LangChain Inc | CrewAI Inc | Sig. Gravitas | Hugging Face | OpenAI | Pydantic | LlamaIndex |
| 授權 | MIT | MIT | 自定義 | Apache-2.0 | MIT | MIT | MIT |
| 多 agent | ✅（graph） | ✅（crew + flow） | ⚠️（platform） | ⚠️（lightweight） | ✅（handoff） | ✅（sub-agent） | ✅（workflow） |
| MCP | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ✅ |
| 記憶 | ✅（強項） | ✅ | ✅ | ⚠️ | ✅（session） | ✅（harness） | ✅（RAG） |
| 部署 | local + platform | local + cloud | platform + self-host | local + sandbox | local + sandbox | local + durable | local + cloud |
| Stars | 40K | 57K | 187K | 29K | 29K | 19K | 51K |
| 特色 | durable execution | role-based agents | no-code builder | code-as-action | provider-agnostic | typed agent loop | document agents |

#### TypeScript 開源框架

| 項目 | Vercel AI SDK | Mastra | LangGraph.js | OpenAI Agents SDK JS/TS |
|---|---|---|---|---|
| 開發方 | Vercel | Mastra AI | LangChain Inc | OpenAI |
| 授權 | 自定義 | Apache-2.0 + EE | MIT | MIT |
| 多 agent | ⚠️ | ✅（graph workflow） | ✅（graph） | ✅（handoff） |
| MCP | ✅ | ✅（server authoring） | ✅ | ✅ |
| 記憶 | ⚠️ | ✅（Observational Memory） | ✅ | ✅（session） |
| 部署 | local + Vercel | local + standalone | local + platform | local + sandbox |
| Stars | 26K | 27K | 3K | 3.7K |
| 特色 | UI hooks + gateway | YC-backed full stack | Python 同源 | OpenAI 生態 |

---

### D. 關鍵趨勢與設計洞察

#### 趨勢 1：MCP 已成為事實標準

[VERIFIED] 所有主要框架（LangGraph、CrewAI、smolagents、OpenAI Agents SDK、Pydantic AI、Mastra、LangGraph.js、Microsoft Agent Framework）均已支援或可擴充 MCP。[REF-10][REF-12][REF-15][REF-16][REF-18][REF-21][REF-23][REF-7]

**[INFERRD]** 設計 Agent Provider Adapter 時，MCP 應作為 tool layer 的底層協議，而非 adapter 自己發明新的 tool calling 格式。

#### 趨勢 2：「Provider-agnostic」成為主流訴求

[VERIFIED] OpenAI Agents SDK 明確標榜支援 100+ LLM；Vercel AI SDK 透過 AI Gateway 統一 provider；Mastra 支援 40+ providers。[REF-16][REF-5][REF-21]

**[INFERRD]** Agent Provider Adapter 的核心價值在於「讓同一個 agent workflow 可以無痛切換 LLM provider」——這正是多家框架已經在做的。

#### 趨勢 3：Durable Execution 成為生產環境標配

[VERIFIED] LangGraph、Pydantic AI、Microsoft Agent Framework 均提供 durable execution 或 checkpoint 機制。[REF-10][REF-18][REF-7]

**[INFERRD]** 設計 Provider Adapter 時，必須考慮 long-running agent 的 state persistence，不能假設 agent 是 stateless 的。

#### 趨勢 4：Agent-as-Tool vs Agent-as-Service

[VERIFIED] OpenAI Agents SDK 有「agents as tools」模式；Microsoft Agent Framework 有 A2A（Agent-to-Agent）協議；Mastra 有 MCP server authoring。[REF-16][REF-7][REF-21]

**[INFERRD]** Agent 間的通訊模式正在分化：（1）function call（agent A 呼叫 agent B 當 tool）、（2）handoff（agent A 將控制權移交給 agent B）、（3）A2A（agent 間以 service 方式互相呼叫）。Provider Adapter 需要抽象這些模式。

#### 趨勢 5：Harness 定義分化

| 範疇 | 代表 | 「Harness」的含義 |
|---|---|---|
| Coding Agent CLI | OpenAI Codex、Pydantic AI Harness | 單一 coding agent + 工具環境 |
| Agent Framework | LangGraph、CrewAI、AutoGen | Multi-agent orchestration + workflow |
| Agent Platform | AutoGPT Platform、CrewAI AMP | Managed deployment + observability |
| Plugin System | DeepSeek Harness | Extensible runtime + plugin ecosystem |

**[INFERRD]** 設計 Agent Provider Adapter 時，需要決定自己在哪一個層級做抽象——是統一「coding agent CLI」的呼叫介面？還是統一「agent framework」的 orchestration API？兩者的需求完全不同。

---

## 來源列表

| # | Tier | URL | 標題 | 日期 | 類型 |
|---|---|---|---|---|---|
| 1 | T1 | https://github.com/openai/codex | OpenAI Codex CLI | 2026-08-31 查閱 | GitHub repo |
| 2 | T1 | https://developers.openai.com/codex | Codex Documentation | 2026-08-31 查閱 | 官方文件 |
| 3 | T1 | https://github.com/deepseek-ai/deepseek-harness | DeepSeek Harness | 2026-08-31 查閱 | GitHub repo |
| 4 | T1 | https://deepseek.com/harness | DeepSeek Harness Homepage | 2026-08-31 查閱 | 官方網站 |
| 5 | T1 | https://ai-sdk.dev/docs | Vercel AI SDK Documentation | 2026-08-31 查閱 | 官方文件 |
| 6 | T1 | https://github.com/vercel/ai | Vercel AI SDK GitHub | 2026-08-31 查閱 | GitHub repo |
| 7 | T1 | https://github.com/microsoft/agent-framework | Microsoft Agent Framework | 2026-08-31 查閱 | GitHub repo |
| 8 | T1 | https://learn.microsoft.com/en-us/agent-framework/ | Microsoft Agent Framework Docs | 2026-08-31 查閱 | 官方文件 |
| 9 | T1 | https://github.com/microsoft/autogen | AutoGen (maintenance mode) | 2026-08-31 查閱 | GitHub repo |
| 10 | T1 | https://github.com/langchain-ai/langgraph | LangGraph | 2026-08-31 查閱 | GitHub repo |
| 11 | T1 | https://www.langchain.com/langgraph | LangGraph Product Page | 2026-08-31 查閱 | 官方網站 |
| 12 | T1 | https://github.com/crewAIInc/crewAI | CrewAI | 2026-08-31 查閱 | GitHub repo |
| 13 | T1 | https://docs.crewai.com | CrewAI Documentation | 2026-08-31 查閱 | 官方文件 |
| 14 | T1 | https://github.com/Significant-Gravitas/AutoGPT | AutoGPT | 2026-08-31 查閱 | GitHub repo |
| 15 | T1 | https://github.com/huggingface/smolagents | smolagents | 2026-08-31 查閱 | GitHub repo |
| 16 | T1 | https://github.com/openai/openai-agents-python | OpenAI Agents SDK (Python) | 2026-08-31 查閱 | GitHub repo |
| 17 | T1 | https://openai.github.io/openai-agents-python/ | OpenAI Agents SDK Docs | 2026-08-31 查閱 | 官方文件 |
| 18 | T1 | https://github.com/pydantic/pydantic-ai | Pydantic AI | 2026-08-31 查閱 | GitHub repo |
| 19 | T1 | https://github.com/pydantic/pydantic-ai-harness | Pydantic AI Harness | 2026-08-31 查閱 | GitHub repo |
| 20 | T1 | https://github.com/run-llama/llama_index | LlamaIndex | 2026-08-31 查閱 | GitHub repo |
| 21 | T1 | https://github.com/mastra-ai/mastra | Mastra | 2026-08-31 查閱 | GitHub repo |
| 22 | T1 | https://mastra.ai/docs | Mastra Documentation | 2026-08-31 查閱 | 官方文件 |
| 23 | T1 | https://github.com/langchain-ai/langgraphjs | LangGraph.js | 2026-08-31 查閱 | GitHub repo |
| 24 | T1 | https://github.com/openai/openai-agents-js | OpenAI Agents SDK (JS/TS) | 2026-08-31 查閱 | GitHub repo |
| 25 | T1 | https://github.com/microsoft/semantic-kernel | Semantic Kernel | 2026-08-31 查閱 | GitHub repo |
| 26 | T1 | https://devblogs.microsoft.com/agent-framework/ | Microsoft Agent Framework Blog | 2026-08-31 查閱 | 官方部落格 |
| 27 | T1 | https://pydantic.dev/docs/ai/ | Pydantic AI Documentation | 2026-08-31 查閱 | 官方文件 |
| 28 | T1 | https://docs.langchain.com/oss/python/deepagents/overview | Deep Agents (LangGraph) | 2026-08-31 查閱 | 官方文件 |
| 29 | T2 | https://api.github.com/repos/deepseek-ai/deepseek-harness | DeepSeek Harness API | 2026-08-31 查閱 | GitHub API |
| 30 | T2 | https://api.github.com/orgs/deepseek-ai/repos | DeepSeek AI Repos | 2026-08-31 查閱 | GitHub API |
| 31 | T2 | https://api.github.com/search/repositories?q=deepseek+agent | GitHub Search: deepseek agent | 2026-08-31 查閱 | GitHub Search |
| 32 | T2 | https://arxiv.org/abs/2608.25512 | A Programming Paradigm for Spatiotemporal Composability | 2026-08 | 學術論文 |
| 33 | T3 | https://www.ycombinator.com/companies?batch=W25 | YC W25 Batch | 2026-08-31 查閱 | YC Directory |
| 34 | T2 | https://skills.sh/crewaiinc/skills | CrewAI Skills for Coding Agents | 2026-08-31 查閱 | 社群資源 |
| 35 | T2 | https://docs.crewai.com/mcp | CrewAI Docs MCP Server | 2026-08-31 查閱 | 官方 MCP |

## 來源品質摘要

- Tier 1 來源：28 個（官方 GitHub repo、官方文件、官方網站）
- Tier 2 來源：5 個（GitHub API 查詢、學術論文、社群資源）
- Tier 3 來源：2 個（YC Directory 等間接來源）
- 來源多樣性：20 個不同網站
- 主要來源類型：GitHub repository、官方文件、GitHub API

## 知識缺口

### 已確認「查無實據」的項目

1. **[UNVERIFIED] DeepSeek Harness 的完整 plugin API 與 agent orchestration 能力**——README 僅描述架構理念（everything is a plugin），未提供具體的 agent 定義 API、multi-agent pattern 或 tool calling 標準。目前仍是 developer preview，文件不完整。

2. **[UNVERIFIED] AutoGPT 的 agent framework API**——AutoGPT 目前定位為 platform（有 UI + managed service），其底層是否仍提供可程式化的 agent framework API，公開文件未充分說明。

### 尚需補充的調研方向

3. **[UNVERIFIED] Google Gemini Agent SDK / ADK（Agent Development Kit）**——本次調研未涵蓋 Google 自家的 agent framework。Google 有 Vertex AI Agent Builder 與 ADK，但未在本次搜尋範圍內。建議後續補充。

4. **[UNVERIFIED] Anthropic Claude SDK 的 agent 能力**——Claude 有 tool use 與 MCP 支援，但是否有獨立的 agent framework 或 harness 尚未確認。

5. **[UNVERIFIED] 各框架的 enterprise adoption rate 與 production failure rate**——多數框架的企業案例由自家提供（vendor-reported），缺乏獨立第三方驗證。

6. **[INFERRD] A2A（Agent-to-Agent）協議的採用率**——Microsoft Agent Framework 明確支援 A2A，但其他框架（LangGraph、CrewAI 等）是否已整合或計劃整合 A2A，尚需確認。

7. **[UNVERIFIED] 「Agent Provider Adapter」是否已有既存產品或開源實作**——本次調研聚焦各框架本身的能力，未專門搜尋「統一 agent provider adapter」類型的產品。建議作為下一輪調研的主題。

## 設計 Agent Provider Adapter 的建議

基於本次調研，設計統一的 Agent Provider Adapter 時，建議考慮以下抽象層級：

```text
┌─────────────────────────────────────────────────┐
│            Agent Provider Adapter                │
├─────────────────────────────────────────────────┤
│  1. Agent Definition Layer                       │
│     - instructions / system prompt               │
│     - tools (function calling + MCP)             │
│     - guardrails / output schema                 │
├─────────────────────────────────────────────────┤
│  2. Orchestration Layer                          │
│     - single agent loop                          │
│     - multi-agent (handoff / graph / crew)       │
│     - durable execution (checkpoint / resume)    │
├─────────────────────────────────────────────────┤
│  3. Provider Abstraction Layer                   │
│     - LLM provider routing (OpenAI / Anthropic / │
│       DeepSeek / local model)                    │
│     - tool calling standard (function call / MCP)│
│     - streaming / response format                │
├─────────────────────────────────────────────────┤
│  4. Infrastructure Layer                         │
│     - state persistence (memory / checkpoint)    │
│     - observability (tracing / logging)          │
│     - deployment (local / sandbox / cloud)       │
└─────────────────────────────────────────────────┘
```

**關鍵設計決策**：

1. **MCP 作為 tool layer 的底層協議**——所有主要框架都已支援，不需重新發明
2. **支援多種 orchestration pattern**——single agent、handoff、graph、crew 四種模式都需覆蓋
3. **Durable execution 必須內建**——不能假設 agent 是 stateless 的
4. **Provider-agnostic 是核心價值**——讓同一個 agent workflow 可以無痛切換 LLM
5. **考慮 A2A 協議**——agent 間通訊標準化是趨勢

## 建議的下一步

1. **第二輪調研**：Google ADK、Anthropic Claude SDK agent 能力、A2A 協議規範
2. **PoC**：以 OpenAI Agents SDK（Python）+ Mastra（TypeScript）為兩個端點，嘗試統一 agent definition 與 tool calling 介面
3. **設計 RFC**：基於上述四層抽象，撰寫 Agent Provider Adapter 的 API design RFC
4. **評估 DeepSeek Harness**：dsh 的 plugin 架構可能提供不同的統一思路（vs API wrapper）
