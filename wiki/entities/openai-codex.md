---
title: OpenAI Codex
type: entity
created: 2026-08-04
updated: 2026-08-04
sources: 1
tags: [openai-codex, codex-cli, ai-coding-agent]
topics: [ai-agent]
canonical: entities/openai-codex
---

# OpenAI Codex

> OpenAI 的 AI Coding Agent，從 Code Completion 演化為命令列 Agent 工具。

## 架構演進

```
2021 — Codex (Code Completion, GPT-3 based)
2023 — Codex (Deprecation, GPT-4)
2024 — Codex CLI (Command-line Agent)
2025 — Codex Agent Architecture
```

## 核心組件

### 1. Codex CLI
- **用途**：命令列 AI Coding Agent
- **功能**：理解指令、生成程式碼、執行命令
- **特色**：終端機整合、腳本化

### 2. Codex Agent Architecture
- **Agentic Loop**：多步驟任務執行
- **Tool Use**：檔案操作、命令執行
- **Context Management**：智慧上下文管理

## LSP / Code Graph 整合

### 整合方式
- **CLI 工具鏈** — 執行階段整合
- **AST Parser** — Tree-sitter 增強
- **動態補全** — 根據上下文生成

### 限制
- 相比 Claude Code 的 MCP，整合較為封閉
- 主要依賴 OpenAI API

## 安裝與使用

```bash
# 安裝 Codex CLI
npm install -g @openai/codex

# 使用 Codex
codex "建立一個 REST API 使用 Express"
```

## 與其他 Agent 比較

| 特色 | Codex CLI | Claude Code | Copilot | Pi Agent |
|---|---|---|---|---|
| **介面** | CLI | CLI + MCP | IDE + CLI | Terminal |
| **LSP 整合** | 有限 | MCP Server | 內建 | Extension |
| **擴充性** | 低 | 極高 | 中等 | 高 |
| **生態系** | OpenAI | Anthropic | GitHub | 開源社群 |

## 來源

- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]
- [OpenAI Codex](https://openai.com/research/codex)
- [Codex CLI](https://github.com/openai/codex)

## 相關頁面

- [[wiki/entities/claude-code|Claude Code]] — 競品 AI Coding Agent
- [[wiki/entities/github-copilot|GitHub Copilot]] — 競品 AI Coding Agent
- [[wiki/entities/pi-mono|Pi Agent]] — 競品 AI Coding Agent
