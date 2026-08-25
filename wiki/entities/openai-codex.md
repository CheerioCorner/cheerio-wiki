---
title: OpenAI Codex
type: entity
created: 2026-08-04
updated: 2026-08-04
sources: 1
tags: [openai-codex, codex-cli, ai-coding-agent]
topics: [ai-development-tools]
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

## Hook 機制（2026-08-25 研究更新）

根據 [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]]：

- **正式名稱**：Codex Hook System
- **引進版本**：v0.116.0 企業級功能更新
- **配置路徑**：`~/.codex/hooks.json` 或 `~/.codex/config.toml` 的 `[hooks]` 區塊
- **實作方式**：無狀態外部二進位攔截器（`codex-interceptor`），透過 Unix socket 與 Falco 的 `prempti` 安全經紀人通訊

### 實務限制

- **生命週期極窄**：雖然定義了 10 個事件，但外部安全經紀人實務上**僅支援 `PreToolUse` 與 `PermissionRequest`**。若將其註冊到其他 8 個事件（如 PostToolUse），會直接組態報錯並強制結束進程
- **流失 Ask 能力**：Codex 缺乏 per-call 的 Hook 使用者確認介面，原本在策略中的 Ask 指令會被無差別硬性轉為 Deny
- **信任授權未自動化**：即使自動寫入設定檔，Codex 仍強制要求使用者必須手動在終端執行過一次 `/hooks` 信任確認
- **高度實驗性**：目前架構仍在早期開發，且並未內置於主流安裝程式中

### Fail-Safe 設計

- **Fail-Closed**：當通訊中斷或攔截器異常時，系統預設硬性拒絕（Deny）該工具執行
- **設計理念**：為高安全局域網設計，將安全漏洞風險降到最低

## 來源

- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]
- [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]]
- [OpenAI Codex](https://openai.com/research/codex)
- [Codex CLI](https://github.com/openai/codex)

## 相關頁面

- [[wiki/entities/claude-code|Claude Code]] — 競品 AI Coding Agent
- [[wiki/entities/github-copilot|GitHub Copilot]] — 競品 AI Coding Agent
- [[wiki/entities/pi-mono|Pi Agent]] — 競品 AI Coding Agent
