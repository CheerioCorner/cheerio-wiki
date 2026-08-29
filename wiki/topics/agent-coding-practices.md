---
type: topic
title: Agent Coding Practices
topic: agent-coding-practices
created: 2026-08-23
updated: 2026-08-23
---

# Agent Coding Practices

> 與 Agent Coding Practices 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/theo-t3gg|Theo (t3gg) — 程式碼重要性光譜與驗證方法論倡導者]] — Theo（t3gg）是科技 YouTube 頻道主，以務實的軟體工程觀點聞名，近期提出「程式碼重要性光譜」和「用 cheap code 驗證 important code」的方法論。
- [[wiki/entities/uncle-bob|Uncle Bob (Robert C. Martin) — Clean Code 作者、軟體工程資深實踐者]] — Uncle Bob（Robert C. Martin）是《Clean Code》和《Clean Architecture》作者，軟體工程界的標誌性人物，近期因「不再閱讀 AI 生成的程式碼」言論引發熱議。

## Concepts

- [[wiki/concepts/code-importance-spectrum|Code Importance Spectrum — 程式碼重要性光譜]] — 一個用來判斷應該閱讀多少程式碼的框架：從不重要的 slop 到人命關天的關鍵系統，不同層級需要不同的驗證策略。
- [[wiki/concepts/contract-driven-development|Contract-Driven Development（契約驅動開發）]] — 用結構化契約作為 AI 模型間交接的唯一真相來源，取代自然語言聊天的不確定性。
- [[wiki/concepts/defect-metrics|Defect Metrics — 軟體品質測量]] — 兩個關鍵的軟體品質指標：Defect Density（缺陷密度）和 Escape Rate（逃脫率）。
- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization with AI — 用 AI 現代化遺留系統的方法論]] — 用 AI coding agent 現代化 legacy codebase 的三步方法論——Plan（sandbox 隔離 + recon 出 blast radius）→ Execute（context 管理 + TDD/BDD 交叉分工）→ Verify（人工 / CI AI / staging smoke test 三層過濾）。是 [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] 針對「無文件、無測試、無 CI/CD 的既有系統」這個高風險場景的特化版本。 🛠️
- [[wiki/concepts/rapid-application-development|Rapid Application Development (RAD)]] — 1991 年 James Martin 提出的軟體開發方法論，強調速度、迭代開發與使用者反饋，核心假設是「使用者在看到東西之前不知道自己要什麼」。 🛠️
- [[wiki/concepts/spec-driven-development|Spec-Driven Development (SDD)]] — 以精確規格（Spec）作為單一事實來源，引導程式碼生成並驗證結果的方法論。在 AI 時代，是補足 vibe coding 安全缺陷的關鍵機制。 🛠️
- [[wiki/concepts/vibe-coding|Vibe Coding — 自然語言驅動的程式碼生成]] — 用自然語言和 AI 工具生成和優化程式碼的開發方式，降低門檻但引入新的安全挑戰。 🛠️

## Sources

- [[wiki/sources/2026-08-02-vibe-coding-implications|Vibe Coding 的未來與影響]] — Blackduck 部落格文章，探討 vibe coding（用自然語言和 AI 工具生成程式碼）的風險與最佳實踐。
- [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code|Uncle Bob Stopped Reading AI-Generated Code]] — 來源：[YouTube — Uncle Bob Stopped Reading AI-Generated Code](https://www.youtube.com/watch?v=sClTAvkQDOU)
- [[wiki/sources/2026-08-10-write-code-you-will-never-read-again|Write Code You Will Never Read Again]] — 來源：[YouTube — Write Code You Will Never Read Again](https://youtu.be/434cG4g5KLE)
- [[wiki/sources/2026-08-17-frontend-ai-roundtable|圓桌會議紀要：Claude vs Gemini vs ChatGPT — 前端全鏈路能力比較]] — 2026-08-17 圓桌會議，參與者：Claude、Gemini、ChatGPT (Codex)，3 輪討論。 🛠️
- [[wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding|RAD Methodology for AI Vibe Coding]] — YouTube 影片（10:40），探討 1991 年 James Martin 提出的 RAD 方法論如何對應現代 AI 代理與 vibe coding 開發流程。 🛠️
- [[wiki/sources/2026-08-25-google-cloud-legacy-code-ai|How to modernize legacy codebases using AI coding agents]] — 來源：[Google Cloud Tech YouTube](https://www.youtube.com/watch?v=RKvckuC6Bsk) | 講者：Martin & JK | 時長：8:13 | 語言：English | 字幕：Manual captions 🛠️

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
- [[wiki/topics/ai-development-tools|AI Development Tools]] — AI 程式碼輔助工具導航
