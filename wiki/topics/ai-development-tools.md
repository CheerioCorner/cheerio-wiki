---
type: topic
title: AI Development Tools
topic: ai-development-tools
created: 2026-08-08
updated: 2026-08-18
---

# Ai Development Tools

> 與 Ai Development Tools 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills — 官方 Skill 系統]] — Anthropic 官方定義的檔案系統驅動 Skill 系統，透過三層漸進式揭露讓 Claude 按需載入專業知識。**這是 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 在 Claude 生態系上的具體實作。** 🛠️
- [[wiki/entities/claude-code|Claude Code — Anthropic AI Coding Agent]] — Claude Code 是 Anthropic 推出的 agentic coding tool，以終端機為起點，擴展到 Desktop App、Web、IDE、GitHub（`@claude` tag）。核心建立在 MCP（Model Context Protocol）之上，能自主觀察 codebase → 推理 → 執行工具 → 評估結果 → 重複循環。 🛠️
- [[wiki/entities/claude-design|Claude Design]] — Anthropic 的 AI 設計工具，讓使用者透過簡單 prompt 建立 Wireframe、Prototype 與動畫，並可透過 MCP 匯出到 Claude Code 實作。
- [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp — 高效能代碼知識圖譜 MCP Server]] — DeusData 開源的高性能代碼知識圖譜 MCP server，基於 tree-sitter AST 分析將代碼庫索引為持久化知識圖譜，158 種語言、毫秒級查詢、99% Token 節省，零 API key。 🛠️
- [[wiki/entities/codegraph|CodeGraph — 代碼知識圖譜 MCP Server（colbymchenry/codegraph）]] — colbymchenry/codegraph——先驅級代碼知識圖譜 MCP server，TypeScript 實作（binary 發布），tree-sitter 解析 20+ 語言、SQLite+FTS5 存儲、框架路由識別、file watcher 即時同步，68k stars，2026-01 建立。 🛠️
- [[wiki/entities/deepseek-harness|DeepSeek Harness — Cordis 微核心編碼 Agent 平台]] — DeepSeek Harness 是 DeepSeek 推出的開源編碼 Agent 平台，基於 Cordis 微核心框架，採用「一切皆插件」的設計哲學。目前處於 Developer Preview 階段。 🛠️
- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK — Custom Agents 平台]] — GitHub Copilot SDK 是 Copilot 的**程式化代理平台**，提供 Custom Agents、Sub-agent Orchestration、Custom Skills、Fleet Mode、Hooks、MCP 整合等完整 agent 能力。**⚠️ 此頁面基於 GitHub 官方文件（docs.github.com），修正了先前基於第三方文章的多處錯誤描述。** 🛠️
- [[wiki/entities/github-copilot|GitHub Copilot — AI 程式碼輔助生態系]] — GitHub Copilot 是 GitHub（Microsoft 旗下）推出的 AI 程式碼輔助產品線，從 IDE 補全演化為完整 Agent 生態系：Extensions SDK、Agent Skills、Cloud Agent、Code Review、MCP 整合。 🛠️
- [[wiki/entities/gsap|gsap-skills — GSAP 官方 AI 技能包]] — GreenSock 官方推出的 AI 技能包，教 AI Coding Agent 如何正確使用 GSAP 動畫庫製作網頁動效。 🛠️
- [[wiki/entities/hyperframes|hyperframes — HTML 轉影片的 Agent 工具]] — 由 HeyGen 開發的開源工具，讓 AI Agent 透過撰寫 HTML 網頁來自動渲染成影片。
- [[wiki/entities/james-martin|James Martin]] — 資訊科技領域作家與先驅，1982 年出版《Application Development Without Programmers》，1991 年形式化提出 RAD（Rapid Application Development）方法論。
- [[wiki/entities/open-code-review|OpenCodeReview]] — 阿里巴巴開源的 AI 代碼審查 CLI 工具，19.3k stars，確定性工程 × Agent 混合架構。 🛠️
- [[wiki/entities/openai-codex|OpenAI Codex]] — OpenAI 的 AI Coding Agent，從 Code Completion 演化為命令列 Agent 工具。
- [[wiki/entities/pixel2motion|pixel2motion — AI Logo 動畫 Skill]] — AI Logo 動畫 Skill，將靜態 Logo 圖片自動轉為流暢的 SVG 動畫、HTML 動態展示、GIF/影片預覽。
- [[wiki/entities/remotion|Remotion — 程式碼驅動的影片製作框架]] — 用 React 程式碼製作影片的開源框架，資料驅動的程式化影片生成。
- [[wiki/entities/story-to-handdrawn-video|story-to-handdrawn-video — 中文故事轉手繪動畫 Skill]] — Agent Skill，將中文故事文字或圖片序列轉為手繪日記風格動畫（無聲 MP4）。
- [[wiki/entities/video-shotcraft|video-shotcraft — AI 影片電影運鏡 Skill]] — AI 影片 Skill，內建 152 種電影運鏡模板，把網站畫面轉為有電影感的產品廣告片。

## Concepts

- [[wiki/concepts/agent-skills|Agent Skills — Skill 系統根概念]] — Skill 是**可重複使用、基於檔案系統的模組化能力單元**，為 AI Agent 提供特定領域的專業知識，將通用代理轉變為專家。 🛠️
- [[wiki/concepts/agents-md-format|AGENTS.md — Agent 專案配置規範]] — 放在專案根目錄的 markdown 文字檔，每次 agent 開始工作時自動讀取，用來定義專案的行為規則。類似 README，但寫給 agent 看。 🛠️
- [[wiki/concepts/ai-animation-generation-approaches|AI 動畫生成手法分類]]
- [[wiki/concepts/ai-code-review|AI Code Review]] — 用 AI 自動化代碼審查，從「人工逐行檢查」進化到「AI 初審 + 人類終審」。 🛠️
- [[wiki/concepts/ai-coding-workflow|AI Coding Workflow — 結構化 AI 編碼工作流程]] — 一套結構化的 AI 編碼工作流程，把模糊想法變成可追踪、可審查的程式碼變更。
- [[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構 — Event / Matcher / Handler]] — Claude Code Hooks 是一套 **deterministic 的事件驅動自動化機制**——在 AI 忘記或跳過規則時，由軟體層強制執行檢查、防呆與品質把關。三層架構：Event 決定時機、Matcher 篩選操作、Handler 決定動作。 🛠️
- [[wiki/concepts/copilot-agent-loop|Copilot Agent Loop — 多輪 Tool-Use 循環]] — Copilot CLI 的核心 orchestrator：從 prompt 到 session.idle 的完整 agentic tool-use loop。 🛠️
- [[wiki/concepts/copilot-custom-agents|Copilot Custom Agents — Sub-agent Orchestration]] — 定義具有 scoped tools/prompts 的輕量級 agent，runtime 自動委派為 sub-agent，在隔離 context 中執行。 🛠️
- [[wiki/concepts/copilot-fleet-mode|Copilot Fleet Mode — 並行 Sub-agent 編排]] — Fleet mode 是 Copilot 的**並行 orchestration pattern**，將工作拆分為獨立 units，分配給多個 sub-agent 並行執行，以 SQL todos 為共享協調狀態。 🛠️
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統 — Lifecycle Callback]] — Hooks 讓你在 Copilot session 的每個階段插入自訂邏輯：從 session 啟動、使用者 prompt、工具呼叫到 session 結束。 🛠️
- [[wiki/concepts/copilot-steering-queueing|Copilot Steering & Queueing — 訊息投遞模式]] — 當 session 正在處理時，兩種訊息投遞模式：steering（即時轉向）和 queueing（排隊等待）。 🛠️
- [[wiki/concepts/design-md-format|design.md Format]] — Google 定義的品牌設計文件格式，讓 AI coding agent 在產生設計前先讀懂品牌規範，確保所有輸出 on-brand。
- [[wiki/concepts/mcp-servers|MCP Servers — 建置與操作]] — MCP Server 是向 MCP Client 提供上下文的程式，透過暴露 tools、resources、prompts 三種原語讓 AI 應用程式存取外部系統。 🛠️
- [[wiki/concepts/rapid-application-development|Rapid Application Development (RAD)]] — 1991 年 James Martin 提出的軟體開發方法論，強調速度、迭代開發與使用者反饋，核心假設是「使用者在看到東西之前不知道自己要什麼」。 🛠️
- [[wiki/concepts/skill-design-methodology|Skill 設計方法論 — 從人類意圖到 Skill 邊界]] — 在「怎麼寫好一支 skill」之前，先回答「這支 skill 的邊界在哪裡」。結合 Intention-First、Event Storming、DDD 與決策樹的頂層設計方法論。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的設計層，填補既有專題「如何決定 skill 邊界」的空白。** 🛠️
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較 — Claude Code / GitHub Copilot / Pi Agent]] — 三大主流 Agent 框架在處理複雜、組合技 Skill 時的機制與設計取捨：如何組合呼叫、巢狀/委派機制、工具鏈整合。 🛠️
- [[wiki/concepts/spec-driven-development|Spec-Driven Development (SDD)]] — 以精確規格（Spec）作為單一事實來源，引導程式碼生成並驗證結果的方法論。在 AI 時代，是補足 vibe coding 安全缺陷的關鍵機制。 🛠️
- [[wiki/concepts/vibe-coding|Vibe Coding — 自然語言驅動的程式碼生成]] — 用自然語言和 AI 工具生成和優化程式碼的開發方式，降低門檻但引入新的安全挑戰。 🛠️

## Sources

- [[wiki/sources/2026-08-02-defect-escape-rate-guide|Defect Escape Rate（DER）測量指南]] — 來源：[Step-by-step guide on how to measure Defect Escape Rate](https://instatus.com/blog/der) 🛠️
- [[wiki/sources/2026-08-04-code-review-graph|code-review-graph：用知識圖譜幫 AI Code Review 省下 8 倍 Token]] — 來源：[別再讓 AI 讀整個專案了！code-review-graph 用知識圖譜幫你省下 8 倍 Token](https://repoinside.com/tirth8205/code-review-graph) 🛠️
- [[wiki/sources/2026-08-13-claude-design-youtube|How To Use Claude Design To Build Beautiful Sites]] — AI LABS 教學影片，介紹 Claude Design 的五階段工作流程，從 design.md 到實際部署。
- [[wiki/sources/2026-08-14-custom-agents-and-sub-agent-orchestration|Custom Agents and Sub-agent Orchestration — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-custom-skills|Custom Skills Guidelines — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-fleet-mode|Fleet Mode Parallel Orchestration — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-steering-and-queueing|Steering & Queueing Message Patterns — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-the-agent-loop|The Agent Loop Execution — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-17-7-ai-animation-skills|7 個用 AI 做動畫的 Skills（Debug Tuboshu）]] — 來源：[YouTube Shorts — 7 個用 AI 做動畫的 Skills](https://www.youtube.com/shorts/8k4WhKuA3OA)
- [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支 SKILL？· DevOps Board 系列（簡報來源筆記）]] — Cheer 自製 31 頁簡報，以 Azure DevOps Board 系列 13 支 skill 為案例，講解複雜系統的 Skill 設計方法論。2026-08 系統架構課分享會用。 🛠️
- [[wiki/sources/2026-08-17-frontend-ai-roundtable|圓桌會議紀要：Claude vs Gemini vs ChatGPT — 前端全鏈路能力比較]] — 2026-08-17 圓桌會議，參與者：Claude、Gemini、ChatGPT (Codex)，3 輪討論。 🛠️
- [[wiki/sources/2026-08-18-claude-code-hooks-tutorial|Claude Code 必學設定 Hooks，完整教學一次搞懂 (Gary Chen)]] — 影片講者：Gary Chen | 時長：19:56 | 語言：zh-TW | 來源：YouTube 🛠️
- [[wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding|RAD Methodology for AI Vibe Coding]] — YouTube 影片（10:40），探討 1991 年 James Martin 提出的 RAD 方法論如何對應現代 AI 代理與 vibe coding 開發流程。 🛠️
- [[wiki/sources/2026-08-25-codebase-memory-mcp-vs-codegraph|深入拆解 Codebase-Memory-MCP vs CodeGraph：兩種代碼知識圖譜的路線之爭]] — YouTube 影片：深入拆解 codebase-memory-mcp 與 CodeGraph 的技術差異、設計哲學與定位分析。 🛠️
- [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]] — 2026 年 AI 編碼 Agent／IDE 開發工具 Harness 的生命週期擴充機制（Hook）盤點與比較，涵蓋 GitHub Copilot、Claude Code、OpenAI Codex CLI、Pi Coding Agent、DeepSeek Harness 五大工具。 🛠️
- [[wiki/sources/mcp-sdks-and-tooling|MCP SDKs & Development Tooling]] — 彙整 MCP 官方多語言 SDK、MCP Inspector 開發工具、參考伺服器範例與常用食譜。 🛠️

## Others

- [[wiki/comparisons/coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較]] — 2026 年 AI 編碼 Agent／IDE 開發工具 Harness 的生命週期擴充機制（Hook）深度比較，涵蓋五大工具的優劣勢、生命週期事件、配置方式與企業跨工具通用標準分析。 🛠️
- [[wiki/discussions/ai-frontend-handoff-methodology|把前端交給 AI 的有效方法論（研究種子）]] — 研究問題：**在開發前端時，怎麼把工作交給 AI 才「有效」？** 尤其著眼於企業推動——一套可複製、可驗證、能守住品牌與品質的前端 AI 協作方法論。這是一顆研究種子（尚未定案），第一份素材是 [[wiki/sources/2026-08-13-claude-design-youtube|Claude Design 五階段工作流影片]]，但目標不綁單一工具。

## 相關 Topics

- [[wiki/topics/code-review|Code Review]] — Code Review 專題導航

## Sources

- [[wiki/sources/2026-08-20-opencode-review-deep-research|OpenCodeReview Deep Research]] — 阿里內部故事、社群討論、互補分析
- [[wiki/sources/2026-08-02-vibe-coding-implications|Vibe Coding Implications]] — Vibe Coding 對開發工具的影響分析
- [[wiki/sources/2026-08-04-code-review-graph|code-review-graph]] — Tree-sitter 知識圖譜做 Code Review，省 8.2 倍 Token 🛠️
- [[wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding|RAD Methodology for AI Vibe Coding]] — YouTube 影片：RAD 四階段與 AI 開發流程對應 🛠️
