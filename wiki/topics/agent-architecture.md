---
type: topic
title: Agent Architecture
topic: agent-architecture
created: 2026-08-23
updated: 2026-08-23
---

# Agent Architecture

> 與 Agent Architecture 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought (CoT)]] — 讓模型「先想再答」的推理技術。

## Concepts

- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns — 狀態持久化的三種路徑]] — Agent 需要「記住自己做到哪裡」的程度不同，對應三種持久化路徑。選錯路徑的代價：要么 crash 後重來，要么 complexity tax 太重。
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級 — 從 Skill、Hook 到 Extension 的能力邊界]] — 當 Skill 不足以控制流程，下一層是什麼？系統化比較 Skill / Hook / Extension 的能力光譜與適用場景。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的擴充層。** 🛠️
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 企業大規模導入 Agent Skills 的安全審查、部署評估、生命週期管理與組織策略。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。** 🛠️
- [[wiki/concepts/agentic-ai|Agentic AI]] — 具備自主性、能使用工具、跨步驟執行任務的 AI 系統。核心公式：**LLM + Tools + Loop**。
- [[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計 — 從 Agent 產出到正式發布的多層把關]] — Agentic Pipeline 的 Checkpoint 與傳統 CI/CD 的根本差異：不只是「build pass/fail」，還必須驗證 Agent 的推理意圖、工具呼叫與語意品質。本頁彙整 Gemini + Codex 兩份調研的架構共識。 🛠️
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]] — 「模型寫 Python 腳本執行多操作」取代「一次一個 tool call」的模式——單一腳本可等同 50 次 tool call，加速執行並透過程式內過濾節省 context window。
- [[wiki/concepts/continual-harness|Continual Harness]] — Continual Harness——把 agent harness 自身的狀態（prompt、skills、memory、sub-agents）抽象成 agent 可以 create/read/update/delete（CRUD）的物件，讓 harness 能根據自己的執行軌跡持續調整，而不是設計時就寫死。[[wiki/entities/prime-agent|Prime Agent]] 的兩大核心抽象之一（另一個是 [[wiki/concepts/recursive-language-model|RLM]]）。 🛠️
- [[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents — Agent 框架之外的可靠性層]] — Agent framework（LangGraph、AutoGen 等）擅長推理與路由，但不擅長「等待數小時的人工核准」或「crash 後恢復到正確狀態」。Durable Execution 平台（Temporal、Azure Durable Functions）填補這段空白。 🛠️
- [[wiki/concepts/harness|Harness — LLM 的驅動層]] — **成長階段：** 🌱 種子期 🛠️
- [[wiki/concepts/late-conversion|Late Conversion(最晚轉換)]] — 一種型別設計策略:**整個內部邏輯都以「應用層 AgentMessage」運作,只在邊界(打 LLM 前一刻)才濾成 LLM 能懂的 `Message[]`**。換句話說:保留訊息的「應用程式語義」直到最後一刻再翻譯。
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering — AI Agent 兩種核心工作流模式]] — Loop 與 Graph 是 AI Agent 系統中兩種互補的工作流模式：Loop 讓 agent 自主迭代探索，Graph 用預定義流程平行執行。兩者共存而非替代。
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy — 「減法大於加法」]] — 兩篇來源、兩個視角,**落到同一個結論**:agent 框架的價值不取決於堆了多少功能,而取決於它**拒絕排除了多少東西**。
- [[wiki/concepts/portable-graph-agent-pattern|Portable Graph Agent Pattern — 可攜式 Graph 專家 Agent 模式]] — 將 Graph Engineering 的「並行 scan → 小 loop → 彙整」架構從 harness 層級下放到「輕量領域專家小 agent」，打造不綁定特定 harness、可被任意執行環境調用的可攜式專家單元。
- [[wiki/concepts/recursive-language-model|Recursive Language Model (RLM)]] — RLM（Recursive Language Model）——把 **context 當變數**、把 subagent delegation 當成 persistent REPL 內函式呼叫的架構，讓模型能寫「以自己的 context 為操作對象的程式」，用於長 session 的 context 管理與平行委派。
- [[wiki/concepts/sub-agents-pattern|Sub-agents — 子 agent 委派模式]] — 主 agent 將複雜任務拆解，分派給多個子 agent 在獨立 context window 中執行，完成後回傳結果。 🛠️

## Sources

- [[wiki/sources/2026-08-03-loop-vs-graph-engineering|Loop vs Graph Engineering — AI Agent Concepts Demystified]] — 來源：[YouTube — Loop vs Graph Engineering — AI Agent Concepts Demystified](https://youtu.be/IMLwvK08JVc)
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|企業界 Agentic Pipeline / Orchestration 解決方案調研（Codex）]] — OpenAI Codex CLI（gpt-5.6-luna）針對長榮航空 IT 系統架構課「Skill 建置自動化」流程進行的企業級 Agentic Pipeline / Orchestration 解決方案調研。20 個 Tier 1 來源，12 個不同網站，引用以深連結為主。 🛠️
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|企業界 Agentic Pipeline / Orchestration 解決方案調研（Gemini）]] — Gemini 2.5-pro 針對長榮航空 IT 系統架構課「Skill 建置自動化」流程（分析→設計→開發→測試四階段）進行的企業級 Agentic Pipeline / Orchestration 解決方案調研。 🛠️
- [[wiki/sources/2026-09-04-ibm-agentic-ai-five-terms|5 Terms You Need to Know About Agentic AI — IBM Technology]] — IBM Technology 出品，主講 Martin Keen。10 分鐘內拆解 front-end AI agent 底下的五個核心組件。 🛠️

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
