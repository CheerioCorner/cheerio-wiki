---
title: MCP (Model Context Protocol)
type: entity
created: 2026-08-04
updated: 2026-08-14
sources: 1
tags: [mcp, model-context-protocol, anthropic, ai-agent, lsp-integration]
topics: [agent-infrastructure, ai-agent]
canonical: entities/mcp-model-context-protocol
---

# MCP (Model Context Protocol)

> Anthropic 推出的開放標準，將 LSP、Code Graph 等服務封裝為 AI Agent 可呼叫的 Tools。

## 核心概念

**問題**：AI Agent 需要存取外部工具（LSP、資料庫、API），但各工具介面不統一

**解決方案**：標準化 Protocol，將工具封裝為 MCP Server，Agent 透過 MCP Client 呼叫

## 架構

```
AI Agent (MCP Client)
    ↓ MCP Protocol
MCP Server (LSP / DB / API)
    ↓
外部服務
```

## 核心組件

### 1. MCP Server
- 封裝外部工具為標準化介面
- 暴露 `tools`、`resources`、`prompts`
- 支援 stdio / HTTP 傳輸

### 2. MCP Client
- AI Agent 內建的 MCP 客戶端
- 發現可用的 MCP Servers
- 呼叫 Tools、讀取 Resources

### 3. Protocol Messages
- `tools/list` — 列出可用 Tools
- `tools/call` — 呼叫特定 Tool
- `resources/read` — 讀取資源
- `prompts/list` — 列出可用 Prompts

## LSP 整合範例

### mcp-server-lsp
將 LSP 伺服器封裝為 MCP Server：

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
    }
  }
}
```

### 暴露的 Tools
| Tool | 說明 |
|---|---|
| `lsp_get_definition` | 取得符號定義位置 |
| `lsp_get_references` | 取得符號所有參考 |
| `lsp_get_hover` | 取得型別資訊 |
| `lsp_get_diagnostics` | 取得語法診斷 |

## 主要使用場景

1. **Claude Code** — 透過 MCP 整合 LSP
2. **Cursor** — MCP Server 支援
3. **Windsurf** — MCP 工具鏈整合
4. **Pi Agent** — Extension 整合 MCP

## 社群生態

| MCP Server | 說明 |
|---|---|
| `@modelcontextprotocol/server-lsp` | LSP → MCP 轉接器 |
| `@modelcontextprotocol/server-filesystem` | 檔案系統存取 |
| `@modelcontextprotocol/server-fetch` | HTTP 請求 |
| `@modelcontextprotocol/server-sqlite` | SQLite 資料庫 |

## 安全性

1. **Tool 白名單** — 只允許特定 Tools 被呼叫
2. **沙箱隔離** — MCP Server 運行於隔離環境
3. **權限控制** — 限制檔案存取範圍

## 來源

- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]
- [[raw/web/2026-08-04-What is the Model Context Protocol (MCP)|MCP 說明]]
- [MCP 官方文件](https://modelcontextprotocol.io/)

## 相關頁面

- [[wiki/entities/lsp|LSP]] — MCP 封裝的語言伺服器協定
- [[wiki/concepts/code-graph|Code Graph]] — MCP 可暴露的 Code Graph 服務
- [[wiki/concepts/okf-open-knowledge-format|OKF]] — MCP 可整合的知識庫格式
