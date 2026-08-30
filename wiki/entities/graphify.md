---
title: "Graphify — 開源代碼知識圖譜工具"
type: entity
created: 2026-08-06
updated: 2026-08-25
sources: 2
tags: [knowledge-graph, code-analysis, tree-sitter]
collection: entities
topics: [agent-research]
canonical: entities/graphify
---

> 接近 10 萬 Star 的 GitHub 開源項目，將代碼庫轉換為知識圖譜，大幅提升 AI Coding Agent 的理解能力。

## 基本資訊
| 屬性 | 值 |
|------|-----|
| GitHub Stars | ~100,000 |
| 核心技術 | tree-sitter AST 解析 |
| 算法 | Leiden 社群分群 |
| 輸出 | graph.json + GRAPH_REPORT.md |

## 核心功能
1. **本機確定性解析**：使用 tree-sitter 零 LLM 成本解析代碼 AST
2. **God Node 檢測**：透過 Leiden 演算法識別代碼庫核心樞紐節點
3. **Agent 技能對接**：透過 `/graphify` 指令載入結構圖
4. **Obsidian 匯出**：`--obsidian` 參數將知識圖譜自動轉換為 Obsidian vault（每個 node → 一個 .md + backlinks）

## 效能
- 節省高達 ~71.5 倍 Token 消耗
- 防止 Agent 進入死迴圈

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]
- [[wiki/sources/2026-08-25-graphify-obsidian-cheat-code|Graphify + Obsidian + Claude Code = CHEAT CODE]]

## 跨平台支援
支援 20+ 個 AI coding assistant 平台：Claude Code、Cursor、Codex、Gemini CLI、GitHub Copilot、Pi coding agent、Aider、Hermes、Kilo Code、Amp、Devin CLI 等。不限定 Claude Code。

## 相關頁面
- [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]]
- [[wiki/concepts/graphify-obsidian-export|Graphify → Obsidian Export]] — 匯出到 Obsidian 的工作流
- [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]] — 同品類工具，功能更完整（MCP 原生、SQLite 持久化、auto-index、視覺化），建議安裝後者
- [[wiki/entities/open-code-review|OpenCodeReview]] — Code review CLI，跟本工具零重疊
