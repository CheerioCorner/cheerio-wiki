---
title: Copilot SDK
topic: copilot-sdk
created: 2026-08-23
updated: 2026-08-23
---

# Copilot SDK

> GitHub Copilot SDK 技術文件：Custom Agents、Sub-agent Orchestration、Hooks、Fleet Mode、Setup 與部署指南。

## Entities

- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK — Custom Agents 平台]] — 程式化代理平台，Custom Agents + Hooks + Fleet Mode + MCP 🛠️

## Concepts

- [[wiki/concepts/copilot-agent-loop|Copilot Agent Loop — 多輪 Tool-Use 循環]] — 從 prompt 到 session.idle 的 agentic loop 🛠️
- [[wiki/concepts/copilot-custom-agents|Copilot Custom Agents — Sub-agent Orchestration]] — scoped tools/prompts 的輕量級 agent 🛠️
- [[wiki/concepts/copilot-fleet-mode|Copilot Fleet Mode — 並行 Sub-agent 編排]] — SQL todos 共享狀態的並行 pattern 🛠️
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統 — Lifecycle Callback]] — session 全階段的自訂邏輯插入 🛠️
- [[wiki/concepts/copilot-steering-queueing|Copilot Steering & Queueing — 訊息投遞模式]] — 即時轉向 vs 排隊等待 🛠️
- [[wiki/concepts/github-copilot-vs-claude-code-skills-ecosystem|Copilot 與 Claude Code Skills 生態系比較]] — 架構、規模、體驗全面比較
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]] — Claude Code / Copilot / Pi 三框架比較

## Sources

- [[wiki/sources/2026-08-14-copilot-vs-claude-code-skills-ecosystem|GitHub Copilot vs Claude Code — Skills 生態系比較]] — 第三方分析
- [[wiki/sources/2026-08-14-setup-guides|Setup Guides]] — 設定指南總覽
- [[wiki/sources/2026-08-14-default-setup-bundled-cli|Default Setup (Bundled CLI)]] — Node.js/.NET SDK 內含 CLI
- [[wiki/sources/2026-08-14-local-cli-setup|Local CLI Setup]] — 進階 CLI 設定
- [[wiki/sources/2026-08-14-github-oauth-setup|GitHub OAuth Setup]] — OAuth 認證設定
- [[wiki/sources/2026-08-14-azure-managed-identity-with-byok|Azure Managed Identity with BYOK]] — Azure 認證設定
- [[wiki/sources/2026-08-14-backend-services-setup|Backend Services Setup]] — server-side 部署
- [[wiki/sources/2026-08-14-multi-tenancy-and-server-deployments|Multi-tenancy and Server Deployments]] — 多租戶部署 🛠️
- [[wiki/sources/2026-08-14-scaling-and-multi-tenancy|Scaling and Multi-tenancy]] — ⚠️ 已併入 Multi-tenancy
- [[wiki/sources/2026-08-14-microsoft-agent-framework-integration|Microsoft Agent Framework Integration]] — MAF 整合
- [[wiki/sources/2026-08-14-opentelemetry-instrumentation-for-copilot-sdk|OpenTelemetry for Copilot SDK]] — 追蹤設定
- [[wiki/sources/2026-08-14-session-lifecycle-hooks|Session Lifecycle Hooks]] — session 生命週期 hook
- [[wiki/sources/2026-08-14-session-hooks|Session Hooks]] — ⚠️ 已併入 Session Lifecycle Hooks
- [[wiki/sources/2026-08-14-pre-tool-use-hook|Pre-tool Use Hook]] — 工具執行前 hook
- [[wiki/sources/2026-08-14-post-tool-use-hook|Post-tool Use Hook]] — 工具執行後 hook
- [[wiki/sources/2026-08-14-error-handling-hook|Error Handling Hook]] — 錯誤處理 hook
- [[wiki/sources/2026-08-14-user-prompt-submitted-hook|User Prompt Submitted Hook]] — 使用者訊息前處理 hook

## 相關 Topics

- [[wiki/topics/skill-system|Skill System]] — Skill 系統架構與方法論
- [[wiki/topics/skill-cases-and-comparisons|Skill Cases & Comparisons]] — 案例、比較文、生態系
