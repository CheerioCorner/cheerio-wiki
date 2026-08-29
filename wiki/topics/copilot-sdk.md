---
type: topic
title: Copilot SDK
topic: copilot-sdk
created: 2026-08-23
updated: 2026-08-23
---

# Copilot Sdk

> 與 Copilot Sdk 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK — Custom Agents 平台]] — GitHub Copilot SDK 是 Copilot 的**程式化代理平台**，提供 Custom Agents、Sub-agent Orchestration、Custom Skills、Fleet Mode、Hooks、MCP 整合等完整 agent 能力。**⚠️ 此頁面基於 GitHub 官方文件（docs.github.com），修正了先前基於第三方文章的多處錯誤描述。** 🛠️

## Concepts

- [[wiki/concepts/copilot-agent-loop|Copilot Agent Loop — 多輪 Tool-Use 循環]] — Copilot CLI 的核心 orchestrator：從 prompt 到 session.idle 的完整 agentic tool-use loop。 🛠️
- [[wiki/concepts/copilot-custom-agents|Copilot Custom Agents — Sub-agent Orchestration]] — 定義具有 scoped tools/prompts 的輕量級 agent，runtime 自動委派為 sub-agent，在隔離 context 中執行。 🛠️
- [[wiki/concepts/copilot-fleet-mode|Copilot Fleet Mode — 並行 Sub-agent 編排]] — Fleet mode 是 Copilot 的**並行 orchestration pattern**，將工作拆分為獨立 units，分配給多個 sub-agent 並行執行，以 SQL todos 為共享協調狀態。 🛠️
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統 — Lifecycle Callback]] — Hooks 讓你在 Copilot session 的每個階段插入自訂邏輯：從 session 啟動、使用者 prompt、工具呼叫到 session 結束。 🛠️
- [[wiki/concepts/copilot-steering-queueing|Copilot Steering & Queueing — 訊息投遞模式]] — 當 session 正在處理時，兩種訊息投遞模式：steering（即時轉向）和 queueing（排隊等待）。 🛠️
- [[wiki/concepts/github-copilot-vs-claude-code-skills-ecosystem|Copilot 與 Claude Code Skills 生態系比較]] — GitHub Copilot Extensions 與 Claude Code 多層 Skills 系統在架構、規模、體驗與策略上的全面比較。 🛠️
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較 — Claude Code / GitHub Copilot / Pi Agent]] — 三大主流 Agent 框架在處理複雜、組合技 Skill 時的機制與設計取捨：如何組合呼叫、巢狀/委派機制、工具鏈整合。 🛠️

## Sources

- [[wiki/sources/2026-08-14-azure-managed-identity-with-byok|Azure Managed Identity with BYOK]] — Source: [Azure Managed Identity with BYOK](https://docs.github.com/en/copilot/how-tos/copilot-sdk/guides/azure-managed-identity)
- [[wiki/sources/2026-08-14-backend-services-setup|Backend Services Setup]] — Source: [Backend Services Setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started/backend-services)
- [[wiki/sources/2026-08-14-copilot-vs-claude-code-skills-ecosystem|GitHub Copilot vs Claude Code — Skills 生態系比較]] — Source: [GitHub Copilot vs Claude Code: Skills Ecosystem Comparison](https://aiskill.market/blog/github-copilot-vs-claude-code) 🛠️
- [[wiki/sources/2026-08-14-custom-agents-and-sub-agent-orchestration|Custom Agents and Sub-agent Orchestration — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-custom-skills|Custom Skills Guidelines — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-default-setup-bundled-cli|Default Setup (Bundled CLI)]] — Source: [Default Setup (Bundled CLI)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started/default-setup)
- [[wiki/sources/2026-08-14-error-handling-hook|Error Handling Hook]] — Source: [Error Handling Hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/error-handling)
- [[wiki/sources/2026-08-14-fleet-mode|Fleet Mode Parallel Orchestration — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-github-oauth-setup|GitHub OAuth Setup]] — Source: [GitHub OAuth Setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/guides/github-oauth)
- [[wiki/sources/2026-08-14-local-cli-setup|Local CLI Setup]] — Source: [Local CLI Setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started/local-cli)
- [[wiki/sources/2026-08-14-microsoft-agent-framework-integration|Microsoft Agent Framework Integration]] — Source: [Microsoft Agent Framework Integration](https://docs.github.com/en/copilot/how-tos/copilot-sdk/guides/microsoft-agent-framework)
- [[wiki/sources/2026-08-14-multi-tenancy-and-server-deployments|Multi-tenancy and Server Deployments]] — Source: [Multi-tenancy and Server Deployments](https://docs.github.com/en/copilot/how-tos/copilot-sdk/guides/multi-tenancy)
- [[wiki/sources/2026-08-14-opentelemetry-instrumentation-for-copilot-sdk|OpenTelemetry for Copilot SDK]] — Source: [OpenTelemetry for Copilot SDK](https://docs.github.com/en/copilot/how-tos/copilot-sdk/guides/opentelemetry)
- [[wiki/sources/2026-08-14-post-tool-use-hook|Post-tool Use Hook]] — Source: [Post-tool Use Hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/post-tool-use)
- [[wiki/sources/2026-08-14-pre-tool-use-hook|Pre-tool Use Hook]] — Source: [Pre-tool Use Hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/pre-tool-use)
- [[wiki/sources/2026-08-14-scaling-and-multi-tenancy|Scaling and Multi-tenancy]] — ⚠️ 此頁內容已併入 [[wiki/sources/2026-08-14-multi-tenancy-and-server-deployments|Multi-tenancy and Server Deployments]]，僅供歷史參照。
- [[wiki/sources/2026-08-14-session-hooks|Session Hooks]] — ⚠️ 此頁內容已併入 [[wiki/sources/2026-08-14-session-lifecycle-hooks|Session Lifecycle Hooks]]，僅供歷史參照。
- [[wiki/sources/2026-08-14-session-lifecycle-hooks|Session Lifecycle Hooks]] — Source: [Session Lifecycle Hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle)
- [[wiki/sources/2026-08-14-setup-guides|Setup Guides]] — Source: [Setup Guides](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started)
- [[wiki/sources/2026-08-14-steering-and-queueing|Steering & Queueing Message Patterns — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-the-agent-loop|The Agent Loop Execution — GitHub Copilot SDK]] — GitHub Copilot SDK 官方技術文件摘要，涵蓋自訂代理、技能、編排模式等。 🛠️
- [[wiki/sources/2026-08-14-user-prompt-submitted-hook|User Prompt Submitted Hook]] — Source: [User Prompt Submitted Hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/user-prompt-submitted)

## 相關 Topics

- [[wiki/topics/skill-system|Skill System]] — Skill 系統架構與方法論
- [[wiki/topics/skill-cases-and-comparisons|Skill Cases & Comparisons]] — 案例、比較文、生態系
