---
type: topic
title: Coding Agent
topic: coding-agent
created: 2026-08-19
updated: 2026-08-19
---

# Coding Agent

> 與 Coding Agent 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp — 高效能代碼知識圖譜 MCP Server]] — DeusData 開源的高性能代碼知識圖譜 MCP server，基於 tree-sitter AST 分析將代碼庫索引為持久化知識圖譜，158 種語言、毫秒級查詢、99% Token 節省，零 API key。 🛠️
- [[wiki/entities/codegraph|CodeGraph — 代碼知識圖譜 MCP Server（colbymchenry/codegraph）]] — colbymchenry/codegraph——先驅級代碼知識圖譜 MCP server，TypeScript 實作（binary 發布），tree-sitter 解析 20+ 語言、SQLite+FTS5 存儲、框架路由識別、file watcher 即時同步，68k stars，2026-01 建立。 🛠️
- [[wiki/entities/deepseek-harness|DeepSeek Harness — Cordis 微核心編碼 Agent 平台]] — DeepSeek Harness 是 DeepSeek 推出的開源編碼 Agent 平台，基於 Cordis 微核心框架，採用「一切皆插件」的設計哲學。目前處於 Developer Preview 階段。 🛠️

## Concepts

- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization with AI — 用 AI 現代化遺留系統的方法論]] — 用 AI coding agent 現代化 legacy codebase 的三步方法論——Plan（sandbox 隔離 + recon 出 blast radius）→ Execute（context 管理 + TDD/BDD 交叉分工）→ Verify（人工 / CI AI / staging smoke test 三層過濾）。是 [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] 針對「無文件、無測試、無 CI/CD 的既有系統」這個高風險場景的特化版本。 🛠️

## Sources

- [[wiki/sources/2026-07-23-plannotator-copilot-setup|Plannotator IT 安裝手冊：GitHub Copilot CLI 整合]] — 來源：PDF 安裝手冊（2026-07-23），描述 Plannotator 與 GitHub Copilot CLI 在 Windows 上的整合安裝流程。 🛠️
- [[wiki/sources/2026-08-25-codebase-memory-mcp-vs-codegraph|深入拆解 Codebase-Memory-MCP vs CodeGraph：兩種代碼知識圖譜的路線之爭]] — YouTube 影片：深入拆解 codebase-memory-mcp 與 CodeGraph 的技術差異、設計哲學與定位分析。 🛠️
- [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]] — 2026 年 AI 編碼 Agent／IDE 開發工具 Harness 的生命週期擴充機制（Hook）盤點與比較，涵蓋 GitHub Copilot、Claude Code、OpenAI Codex CLI、Pi Coding Agent、DeepSeek Harness 五大工具。 🛠️
- [[wiki/sources/2026-08-25-google-cloud-legacy-code-ai|How to modernize legacy codebases using AI coding agents]] — 來源：[Google Cloud Tech YouTube](https://www.youtube.com/watch?v=RKvckuC6Bsk) | 講者：Martin & JK | 時長：8:13 | 語言：English | 字幕：Manual captions 🛠️

## Others

- [[wiki/comparisons/coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較]] — 2026 年 AI 編碼 Agent／IDE 開發工具 Harness 的生命週期擴充機制（Hook）深度比較，涵蓋五大工具的優劣勢、生命週期事件、配置方式與企業跨工具通用標準分析。 🛠️

## 相關 Topics
