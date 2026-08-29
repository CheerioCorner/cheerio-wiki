---
type: topic
title: Skill System
topic: skill-system
created: 2026-08-23
updated: 2026-08-23
---

# Skill System

> 與 Skill System 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/agentskills-io-standard|agentskills.io — AI Agent Skill 開放標準]] — 一個包含**指令、腳本、資源**的可移植工作流模塊。 🛠️
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills — 官方 Skill 系統]] — Anthropic 官方定義的檔案系統驅動 Skill 系統，透過三層漸進式揭露讓 Claude 按需載入專業知識。**這是 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 在 Claude 生態系上的具體實作。** 🛠️

## Concepts

- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級 — 從 Skill、Hook 到 Extension 的能力邊界]] — 當 Skill 不足以控制流程，下一層是什麼？系統化比較 Skill / Hook / Extension 的能力光譜與適用場景。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的擴充層。** 🛠️
- [[wiki/concepts/agent-skills-api-usage|Agent Skills API 使用方法]] — 透過 Claude Messages API 呼叫與管理 Agent Skills 的技術機制：container 參數、多輪對話、版本管理與快取。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。**
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 企業大規模導入 Agent Skills 的安全審查、部署評估、生命週期管理與組織策略。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。** 🛠️
- [[wiki/concepts/agent-skills|Agent Skills — Skill 系統根概念]] — Skill 是**可重複使用、基於檔案系統的模組化能力單元**，為 AI Agent 提供特定領域的專業知識，將通用代理轉變為專家。 🛠️
- [[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計 — 從 Agent 產出到正式發布的多層把關]] — Agentic Pipeline 的 Checkpoint 與傳統 CI/CD 的根本差異：不只是「build pass/fail」，還必須驗證 Agent 的推理意圖、工具呼叫與語意品質。本頁彙整 Gemini + Codex 兩份調研的架構共識。 🛠️
- [[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents — Agent 框架之外的可靠性層]] — Agent framework（LangGraph、AutoGen 等）擅長推理與路由，但不擅長「等待數小時的人工核准」或「crash 後恢復到正確狀態」。Durable Execution 平台（Temporal、Azure Durable Functions）填補這段空白。 🛠️
- [[wiki/concepts/github-copilot-vs-claude-code-skills-ecosystem|Copilot 與 Claude Code Skills 生態系比較]] — GitHub Copilot Extensions 與 Claude Code 多層 Skills 系統在架構、規模、體驗與策略上的全面比較。 🛠️
- [[wiki/concepts/mcp-agent-skills-integration|MCP + Agent Skills 整合]] — Agent Skills 與 MCP 是互補的擴充機制——Skills 提供知識與流程，MCP 提供工具連接。兩者可組合建構更強大的 AI Agent。 🛠️
- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]] — 撰寫高品質 Agent Skill 的通用方法論：從結構規範到評估迭代，不限於特定平台。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。**
- [[wiki/concepts/skill-design-methodology|Skill 設計方法論 — 從人類意圖到 Skill 邊界]] — 在「怎麼寫好一支 skill」之前，先回答「這支 skill 的邊界在哪裡」。結合 Intention-First、Event Storming、DDD 與決策樹的頂層設計方法論。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的設計層，填補既有專題「如何決定 skill 邊界」的空白。** 🛠️
- [[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill — 技能優化元技能]] — 關於「如何編寫與重構技能規範」的高階指導法則，控制 LLM 以受控預算進行 SKILL.md 的文字級修復。 🛠️
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較 — Claude Code / GitHub Copilot / Pi Agent]] — 三大主流 Agent 框架在處理複雜、組合技 Skill 時的機制與設計取捨：如何組合呼叫、巢狀/委派機制、工具鏈整合。 🛠️

## Sources

- [[wiki/sources/2026-08-14-agent-skills-api-guide|透過 API 使用 Agent Skills — 完整指南]] — Source: [透過 API 使用 Agent Skills](https://platform.claude.com/docs/zh-TW/build-with-claude/skills-guide)
- [[wiki/sources/2026-08-14-agent-skills-api-quickstart|在 API 中開始使用 Agent Skills — Quickstart]] — Source: [在 API 中開始使用 Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/quickstart)
- [[wiki/sources/2026-08-14-agent-skills-enterprise-deployment|企業級 Agent Skills 部署指南]] — Source: [企業級 Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/enterprise)
- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills — Anthropic 官方概覽]] — Source: [Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/overview)
- [[wiki/sources/2026-08-14-anthropics-skills-github-repo|anthropics/skills — 官方 GitHub Repo]] — Source: [anthropics/skills](https://github.com/anthropics/skills)
- [[wiki/sources/2026-08-14-copilot-vs-claude-code-skills-ecosystem|GitHub Copilot vs Claude Code — Skills 生態系比較]] — Source: [GitHub Copilot vs Claude Code: Skills Ecosystem Comparison](https://aiskill.market/blog/github-copilot-vs-claude-code) 🛠️
- [[wiki/sources/2026-08-14-skill-writing-best-practices|Skill 撰寫最佳實踐]] — Source: [Skill 撰寫最佳實踐](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/best-practices)
- [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支 SKILL？· DevOps Board 系列（簡報來源筆記）]] — Cheer 自製 31 頁簡報，以 Azure DevOps Board 系列 13 支 skill 為案例，講解複雜系統的 Skill 設計方法論。2026-08 系統架構課分享會用。 🛠️
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|企業界 Agentic Pipeline / Orchestration 解決方案調研（Codex）]] — OpenAI Codex CLI（gpt-5.6-luna）針對長榮航空 IT 系統架構課「Skill 建置自動化」流程進行的企業級 Agentic Pipeline / Orchestration 解決方案調研。20 個 Tier 1 來源，12 個不同網站，引用以深連結為主。 🛠️
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|企業界 Agentic Pipeline / Orchestration 解決方案調研（Gemini）]] — Gemini 2.5-pro 針對長榮航空 IT 系統架構課「Skill 建置自動化」流程（分析→設計→開發→測試四階段）進行的企業級 Agentic Pipeline / Orchestration 解決方案調研。 🛠️
- [[wiki/sources/2026-09-01-ibm-youtube-skill-best-practices-supplement-draft|IBM YouTube 影片 Skill 五項最佳實踐 — 補充草稿]] — ⚠️ **草稿狀態**：本文件是待覆核的補充草稿，尚未併入任何既有文章或 Notion 花園。

## 相關 Topics

- [[wiki/topics/copilot-sdk|Copilot SDK]] — GitHub Copilot SDK 技術文件
- [[wiki/topics/skill-cases-and-comparisons|Skill Cases & Comparisons]] — 案例、比較文、生態系
- [[wiki/topics/skill-presentation-design|Skill Presentation & Design]] — 簡報/前端設計類 Skill
