---
title: Claude Code
type: entity
created: 2026-08-04
updated: 2026-08-04
sources: 1
tags: [claude-code, anthropic, mcp, ai-coding-agent]
topics: [ai-agent]
canonical: entities/claude-code
---

# Claude Code

> Anthropic 的 AI Coding Agent，基於 MCP Protocol 整合 LSP 與外部工具。

## 核心特色

### 1. MCP 整合
- 原生支援 Model Context Protocol
- 透過 MCP Server 整合 LSP、資料庫、API
- 開放標準，高度可擴充

### 2. Agentic Loop
```
Observe → Reason → Action → Evaluate → Loop
```

### 3. Tool Calling
- `read` / `write` / `edit` — 檔案操作
- `bash` — 命令執行
- MCP Tools — LSP、Code Graph 等

## 安裝與使用

```bash
# 安裝 Claude Code CLI
npm install -g @anthropic-ai/claude-code

# 啟動 Claude Code
claude

# 設定 MCP Server
claude mcp add lsp-python npx -y @modelcontextprotocol/server-lsp --command pyright-langserver --args --stdio
```

## MCP 配置

### mcp_config.json
```json
{
  "mcpServers": {
    "lsp-python": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-lsp", "--command", "pyright-langserver", "--args", "--stdio"]
    },
    "lsp-typescript": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-lsp", "--command", "typescript-language-server", "--args", "--stdio"]
    },
    "okf-wiki": {
      "command": "node",
      "args": ["./scripts/okf-mcp-server.js", "--wiki-dir", "./.knowledge-wiki"]
    }
  }
}
```

## OKF 整合

Claude Code 完全相容 OKF 格式：

```
.knowledge-wiki/
├── index.md              # 入口
├── architecture.md       # 架構文件
├── modules/
│   ├── auth.md
│   └── database.md
└── decisions/
    └── 2026-08-04-orm-choice.md
```

Agent 可直接讀取 OKF Markdown 理解專案知識。

## 與其他 Agent 比較

| 特色 | Claude Code | Copilot | Codex CLI | Pi Agent |
|---|---|---|---|---|
| **Protocol** | MCP (開放) | Extensions SDK | API | Extension |
| **LSP 整合** | MCP Server | 內建探針 | CLI 工具 | Extension |
| **OKF 相容** | ✅ 原生 | ⚠️ 有限 | ⚠️ 有限 | ✅ Skill |
| **擴充性** | 極高 | 中等 | 低 | 高 |

## 來源

- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]
- [Claude Code 文件](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code)
- [MCP 官方](https://modelcontextprotocol.io/)

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — Claude Code 的核心 Protocol
- [[wiki/entities/github-copilot|GitHub Copilot]] — 競品 AI Coding Agent
- [[wiki/entities/pi-mono|Pi Agent]] — 競品 AI Coding Agent
