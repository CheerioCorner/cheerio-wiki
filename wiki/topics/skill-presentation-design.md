---
type: topic
title: Skill Presentation & Design
topic: skill-presentation-design
created: 2026-08-23
updated: 2026-08-23
---

# Skill Presentation Design

> 與 Skill Presentation Design 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/anthropics-claude-code-frontend-design|anthropics/claude-code — frontend-design plugin]] — Anthropic 官方 frontend-design plugin——質性原則 + brainstorm→critique 工作流，不設固定數字規格。 🛠️
- [[wiki/entities/conardli-web-design-engineer-skill|ConardLi/garden-skills — web-design-engineer]] — 採協作式確認機制的 web-design-engineer skill——先宣告設計系統，等使用者確認才動工。 🛠️
- [[wiki/entities/google-labs-code-stitch-skills|google-labs-code/stitch-skills — Stitch UI MCP Skill Library]] — Google Labs Stitch UI 設計工具 MCP server 的 skill library——三個 plugin 約 14 個子 skill。 🛠️
- [[wiki/entities/gsap|gsap-skills — GSAP 官方 AI 技能包]] — GreenSock 官方推出的 AI 技能包，教 AI Coding Agent 如何正確使用 GSAP 動畫庫製作網頁動效。 🛠️
- [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill — AI Agent HTML PPT 技能]] — 歸藏（@op7418）開發的 AI agent 簡報技能，生成單文件 HTML 橫向翻頁 PPT、配圖和多平台封面，內建演講者模式。
- [[wiki/entities/huashu-design|huashu-design — HTML 原生設計技能]] — 花叔（@alchaincyf）開發的 HTML 原生設計技能，一句話 prompt 生成高保真原型、幻燈片、動畫，含 20 設計哲學 + 5 維評審 + MP4 導出。MIT 開源。
- [[wiki/entities/leonxlnx-taste-skill|leonxlnx/taste-skill — Anti-Slop Frontend Framework]] — "The Anti-Slop Frontend Framework for AI Agents" — 13 個子 skill 的可攜式設計指令集，以三旋鈕系統與 LILA RULE 為核心。 🛠️
- [[wiki/entities/nextlevelbuilder-ui-ux-pro-max-skill|nextlevelbuilder/ui-ux-pro-max-skill — 跨平台 UI/UX 設計 Intelligence]] — 搜尋式推理引擎驅動的跨平台 UI/UX 設計系統，10 類優先排序規則 + 三層 token 架構。 🛠️
- [[wiki/entities/nutlope-hallmark|nutlope/hallmark — Anti-AI-Slop Design Skill]] — Together AI 的 Anti-AI-Slop design skill——21 種 macrostructure + 58 道 slop test + 六軸自評分數。 🛠️
- [[wiki/entities/pbakaus-impeccable|pbakaus/impeccable — 35 Playbooks + 59 Deterministic Detectors]] — 源自 Anthropic frontend-design skill 的進化版——35 個命令 playbook + 59 條不需 LLM 的 deterministic detector。 🛠️

## Concepts

- [[wiki/concepts/html-slide-deck-workflow|HTML Slide Deck 製作工作流]] — 用 AI agent 製作高品質 HTML 簡報的完整工作流，整合 guizang-ppt-skill 和 huashu-design 兩套設計系統。

## Sources

- [[wiki/sources/2026-08-08-guizang-ppt-skill|guizang-ppt-skill — 歸藏 HTML PPT 技能 GitHub README]] — 來源：[op7418/guizang-ppt-skill GitHub README](https://github.com/op7418/guizang-ppt-skill)
- [[wiki/sources/2026-08-08-huashu-design|huashu-design — 花叔 HTML 原生設計技能 GitHub README]] — 來源：[alchaincyf/huashu-design GitHub README](https://github.com/alchaincyf/huashu-design)
- [[wiki/sources/2026-08-08-plannotator-obsidian-presentation|Plannotator × Obsidian — 知識迴流系統報告簡報企劃]] — 來源：Pi 整理的簡報企劃文件，經過 Gemini + Copilot 雙重審閱 🛠️
- [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支 SKILL？· DevOps Board 系列（簡報來源筆記）]] — Cheer 自製 31 頁簡報，以 Azure DevOps Board 系列 13 支 skill 為案例，講解複雜系統的 Skill 設計方法論。2026-08 系統架構課分享會用。 🛠️
- [[wiki/sources/2026-08-26-anthropics-claude-code-frontend-design|anthropics/claude-code — frontend-design plugin]] — Anthropic 官方隨 Claude Code 附帶的 frontend-design plugin，走 brainstorm→critique 的原則式流程而非硬性數字規格。 🛠️
- [[wiki/sources/2026-08-26-google-labs-code-stitch-skills|google-labs-code/stitch-skills — Stitch UI 設計工具 Skill Library]] — Google Labs 為 Stitch UI 設計工具 MCP server 開發的 skill library，taste-design skill 的規則與 taste-skill LILA RULE 高度相似。 🛠️
- [[wiki/sources/2026-08-26-greensock-gsap-skills|greensock/gsap-skills — GSAP 官方 AI 技能集合]] — GSAP 官方 org 維護的 8 個子 skill 集合，教 agent 正確使用 GSAP：core/timeline/scrolltrigger/plugins/utils/react/performance/frameworks。 🛠️
- [[wiki/sources/2026-08-26-leonxlnx-taste-skill|leonxlnx/taste-skill — Anti-Slop Frontend Framework]] — "The Anti-Slop Frontend Framework for AI Agents" — 一套阻止 AI coding agent 產出「通用模板感」UI 的可攜式設計指令集。 🛠️
- [[wiki/sources/2026-08-26-nextlevelbuilder-ui-ux-pro-max-skill|nextlevelbuilder/ui-ux-pro-max-skill — 跨平台 UI/UX 設計 Intelligence]] — 提供「design intelligence for building professional UI/UX across multiple platforms and frameworks」，透過可搜尋的推理引擎產生客製化設計系統。 🛠️
- [[wiki/sources/2026-08-26-nutlope-hallmark|nutlope/hallmark — Anti-AI-Slop Design Skill]] — Anti-AI-slop design skill for Claude Code, Cursor, and Codex — 21 種 macrostructure + 58 道 slop test + 六軸自評分數。 🛠️
- [[wiki/sources/2026-08-26-pbakaus-impeccable|pbakaus/impeccable — 基於 Anthropic frontend-design 的設計語言]] — "The design language that makes your AI harness better at design" — 源自 Anthropic 官方 frontend-design skill 的進化版本。 🛠️

## Others

- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 AI Agent Skills 比較]] — 10 個前端/UI 視覺設計相關的 AI agent skill repo 全景比較——誰在解什麼問題、誰跟誰互補、什麼情境選哪個。 🛠️

## 相關 Topics

- [[wiki/topics/skill-system|Skill System]] — Skill 系統架構與方法論
- [[wiki/topics/skill-cases-and-comparisons|Skill Cases & Comparisons]] — 案例、比較文、生態系
