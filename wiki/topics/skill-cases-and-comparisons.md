---
type: topic
title: Skill Cases & Comparisons
topic: skill-cases-and-comparisons
created: 2026-08-23
updated: 2026-08-23
---

# Skill Cases And Comparisons

> 與 Skill Cases And Comparisons 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/anthropics-claude-code-frontend-design|anthropics/claude-code — frontend-design plugin]] — Anthropic 官方 frontend-design plugin——質性原則 + brainstorm→critique 工作流，不設固定數字規格。 🛠️
- [[wiki/entities/conardli-web-design-engineer-skill|ConardLi/garden-skills — web-design-engineer]] — 採協作式確認機制的 web-design-engineer skill——先宣告設計系統，等使用者確認才動工。 🛠️
- [[wiki/entities/dominikmartn-nothing-design-skill|dominikmartn/nothing-design-skill — Nothing 品牌風格皮膚]] — 窄範圍品牌皮膚型 skill——套用 Nothing（手機品牌）點陣/單色/工業風格，NEVER 自動觸發。
- [[wiki/entities/google-labs-code-stitch-skills|google-labs-code/stitch-skills — Stitch UI MCP Skill Library]] — Google Labs Stitch UI 設計工具 MCP server 的 skill library——三個 plugin 約 14 個子 skill。 🛠️
- [[wiki/entities/gsap|gsap-skills — GSAP 官方 AI 技能包]] — GreenSock 官方推出的 AI 技能包，教 AI Coding Agent 如何正確使用 GSAP 動畫庫製作網頁動效。 🛠️
- [[wiki/entities/leonxlnx-taste-skill|leonxlnx/taste-skill — Anti-Slop Frontend Framework]] — "The Anti-Slop Frontend Framework for AI Agents" — 13 個子 skill 的可攜式設計指令集，以三旋鈕系統與 LILA RULE 為核心。 🛠️
- [[wiki/entities/mattpocock-skills|mattpocock/skills — AI Coding Agent Skills 集合]] — Matt Pocock 維護的 AI coding agent skills 集合，162k+ stars，7.5M+ downloads。 🛠️
- [[wiki/entities/nextlevelbuilder-ui-ux-pro-max-skill|nextlevelbuilder/ui-ux-pro-max-skill — 跨平台 UI/UX 設計 Intelligence]] — 搜尋式推理引擎驅動的跨平台 UI/UX 設計系統，10 類優先排序規則 + 三層 token 架構。 🛠️
- [[wiki/entities/nutlope-hallmark|nutlope/hallmark — Anti-AI-Slop Design Skill]] — Together AI 的 Anti-AI-Slop design skill——21 種 macrostructure + 58 道 slop test + 六軸自評分數。 🛠️
- [[wiki/entities/pbakaus-impeccable|pbakaus/impeccable — 35 Playbooks + 59 Deterministic Detectors]] — 源自 Anthropic frontend-design skill 的進化版——35 個命令 playbook + 59 條不需 LLM 的 deterministic detector。 🛠️
- [[wiki/entities/pi-agent-core|pi-agent-core — 5 檔 / 1,500 行的 Agent Runtime]] — pi-mono monorepo 內的 `packages/agent/`,全文為 agent runtime。**整個 runtime 由 5 個檔、約 1,500 行 TypeScript 構成**(以 [[[../../raw/web/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]]] 解析為準)。 🛠️
- [[wiki/entities/round-table|Round Table — 多 AI 圓桌會議]] — Pi 主持的多 AI 討論機制。Pi 不參與討論，派出 subagent 搭配 Claude、Gemini、Copilot 進行序列討論。 🛠️
- [[wiki/entities/vercel-labs-web-interface-guidelines|vercel-labs/web-interface-guidelines — Web UI 合規稽核清單]] — Vercel 的扁平合規稽核清單——不是生成技能，是生成完之後的審查工具。
- [[wiki/entities/wiki-knowledge|wiki-knowledge — Obsidian 知識庫操作 Skill（已刪除）]] — ⚠️ **已刪除**（2026-08-08）。原始的三大流程已拆成三個獨立 skill，dispatcher 功能不再需要。 🛠️

## Concepts

- [[wiki/concepts/content-redesign|Content 設計 Redesign]] — 知識花園 Content 設計重構：從「套模板」到「針對性報告」。經 Copilot + Gemini 三輪討論確立。 🛠️
- [[wiki/concepts/knowledge-garden-skill-architecture|知識花園 Skill 架構]] — 知識花園相關 skills 的架構全景圖，描述 11 個 skill 的職責、資料流和關聯。 🛠️

## Sources

- [[wiki/sources/2026-07-11-mattpocock-skills|mattpocock/skills — 工程實踐 Skills 集合]] — 視覺化：[[wiki/visualizations/mattpocock-skills.canvas|mattpocock/skills Canvas]]
- [[wiki/sources/2026-08-02-morphir-dotnet-agents|morphir-dotnet AGENTS.md — AI Agent 貢獻指南]] — FINOS morphir-dotnet 專案的 AGENTS.md，展示企業級 .NET 專案如何組織 AI agent 指南。 🛠️
- [[wiki/sources/2026-08-05-hermes-agent-github-readme|Hermes Agent: GitHub README — 自我改進 AI Agent]] — 來源：[NousResearch/hermes-agent GitHub README](https://github.com/NousResearch/hermes-agent) 🛠️
- [[wiki/sources/2026-08-06-pi-architecture-walkthrough|Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained]] — 來源：[YouTube — Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained](https://www.youtube.com/watch?v=gTeujlv8qK0) 🛠️
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 頻道 5 支 AI Agent 前沿研究影片]] — 來源：[Wow YouTube 頻道](https://www.youtube.com/@wow.insight) 5 支前沿 AI Agent 研究影片 🛠️
- [[wiki/sources/2026-08-07-ai-related-seeds|Notion AI 相關種子 — agentskills.io + Axway AI Gateway]] — 來源：Notion「任何當下 → AI相關」頁面 🛠️
- [[wiki/sources/2026-08-26-anthropics-claude-code-frontend-design|anthropics/claude-code — frontend-design plugin]] — Anthropic 官方隨 Claude Code 附帶的 frontend-design plugin，走 brainstorm→critique 的原則式流程而非硬性數字規格。 🛠️
- [[wiki/sources/2026-08-26-conardli-garden-skills|ConardLi/garden-skills — 精選 Skill 集合（含 web-design-engineer）]] — 5 個子 skill 集合，只有 web-design-engineer 屬前端視覺設計範疇——協作式：宣告設計系統後等待使用者確認才動工。
- [[wiki/sources/2026-08-26-dominikmartn-nothing-design-skill|dominikmartn/nothing-design-skill — Nothing 品牌風格皮膚]] — 單一窄範圍 skill，套用 Nothing（手機品牌）的點陣/單色/工業風格——品牌皮膚型，非通用品味把關。
- [[wiki/sources/2026-08-26-google-labs-code-stitch-skills|google-labs-code/stitch-skills — Stitch UI 設計工具 Skill Library]] — Google Labs 為 Stitch UI 設計工具 MCP server 開發的 skill library，taste-design skill 的規則與 taste-skill LILA RULE 高度相似。 🛠️
- [[wiki/sources/2026-08-26-greensock-gsap-skills|greensock/gsap-skills — GSAP 官方 AI 技能集合]] — GSAP 官方 org 維護的 8 個子 skill 集合，教 agent 正確使用 GSAP：core/timeline/scrolltrigger/plugins/utils/react/performance/frameworks。 🛠️
- [[wiki/sources/2026-08-26-leonxlnx-taste-skill|leonxlnx/taste-skill — Anti-Slop Frontend Framework]] — "The Anti-Slop Frontend Framework for AI Agents" — 一套阻止 AI coding agent 產出「通用模板感」UI 的可攜式設計指令集。 🛠️
- [[wiki/sources/2026-08-26-nextlevelbuilder-ui-ux-pro-max-skill|nextlevelbuilder/ui-ux-pro-max-skill — 跨平台 UI/UX 設計 Intelligence]] — 提供「design intelligence for building professional UI/UX across multiple platforms and frameworks」，透過可搜尋的推理引擎產生客製化設計系統。 🛠️
- [[wiki/sources/2026-08-26-nutlope-hallmark|nutlope/hallmark — Anti-AI-Slop Design Skill]] — Anti-AI-slop design skill for Claude Code, Cursor, and Codex — 21 種 macrostructure + 58 道 slop test + 六軸自評分數。 🛠️
- [[wiki/sources/2026-08-26-pbakaus-impeccable|pbakaus/impeccable — 基於 Anthropic frontend-design 的設計語言]] — "The design language that makes your AI harness better at design" — 源自 Anthropic 官方 frontend-design skill 的進化版本。 🛠️
- [[wiki/sources/2026-08-26-vercel-labs-web-interface-guidelines|vercel-labs/web-interface-guidelines — Web UI 合規稽核清單]] — Vercel 維護的單一扁平準則文件，透過 install.sh fan-out 成多工具格式，用途是程式碼審查/合規檢查。

## Others

- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 AI Agent Skills 比較]] — 10 個前端/UI 視覺設計相關的 AI agent skill repo 全景比較——誰在解什麼問題、誰跟誰互補、什麼情境選哪個。 🛠️

## 相關 Topics

- [[wiki/topics/skill-system|Skill System]] — Skill 系統架構與方法論
- [[wiki/topics/copilot-sdk|Copilot SDK]] — GitHub Copilot SDK 技術文件
- [[wiki/topics/skill-presentation-design|Skill Presentation & Design]] — 簡報/前端設計類 Skill
