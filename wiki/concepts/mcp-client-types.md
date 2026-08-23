---
title: MCP Client Types — 類型與連線模式
type: concept
created: 2026-08-23
updated: 2026-08-23
sources: 5
tags: [mcp, client, cli, tui, web, connection]
topics: [agent-runtime-implementations, mcp-ecosystem]
canonical: concepts/mcp-client-types
provenance_raw:
  - "raw/web/2026-08-22-CLI client.md"
  - "raw/web/2026-08-22-TUI client.md"
  - "raw/web/2026-08-22-Web client.md"
  - "raw/web/2026-08-22-Connect to local MCP servers.md"
  - "raw/web/2026-08-22-Connect to remote MCP Servers.md"
---

# MCP Client Types — 類型與連線模式

> MCP 支援多種終端用戶端類型（CLI、TUI、Web），每種連線模式（本地 STDIO、遠端 Streamable HTTP）各有適用場景。

## 客戶端類型

| 類型 | 說明 | 適用場景 |
|------|------|---------|
| **CLI Client** | 命令列介面，透過 STDIO 與本地 Server 通訊 | 開發測試、腳本自動化 |
| **TUI Client** | 終端 UI 介面，提供互動式操作 | 開發者日常使用 |
| **Web Client** | 瀏覽器介面，透過 Streamable HTTP 與遠端 Server 通訊 | 遠端存取、團隊共用 |

`CLI client`、`TUI client`、`Web client`

## 連線模式

### 本地連線 (STDIO)

本地 MCP Server 使用 STDIO 傳輸，透過標準輸入/輸出串流進行進程間通訊。優點是零網路開銷、低延遲 `Connect to local MCP servers`。

```
MCP Host
    ↓ stdin/stdout
MCP Server（本地進程）
```

### 遠端連線 (Streamable HTTP)

遠端 MCP Server 使用 Streamable HTTP 傳輸，Client 透過 HTTP POST 傳送訊息，Server 可選擇性使用 Server-Sent Events (SSE) 進行串流回應。支援標準 HTTP 認證方法（Bearer Token、API Key、自訂 Headers）`Connect to remote MCP Servers`。

```
MCP Host
    ↓ HTTP POST (+ optional SSE)
MCP Server（遠端平台）
```

## 選擇指南

- **需要存取本地檔案/資料庫** → STDIO 本地連線
- **需要多人共用同一 Server** → Streamable HTTP 遠端連線
- **開發測試** → CLI Client + STDIO
- **日常使用** → TUI 或 Web Client

## 相關頁面

- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發
- [[wiki/concepts/mcp-clients|MCP Clients]] — 客戶端開發
- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽

## 來源

- [[raw/web/2026-08-22-CLI client|CLI client]]
- [[raw/web/2026-08-22-TUI client|TUI client]]
- [[raw/web/2026-08-22-Web client|Web client]]
- [[raw/web/2026-08-22-Connect to local MCP servers|Connect to local MCP servers]]
- [[raw/web/2026-08-22-Connect to remote MCP Servers|Connect to remote MCP Servers]]
