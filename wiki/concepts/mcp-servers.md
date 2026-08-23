---
title: MCP Servers — 建置與操作
type: concept
created: 2026-08-23
updated: 2026-08-23
sources: 4
tags: [mcp, server, development, debugging]
topics: [agent-infrastructure, ai-development-tools, mcp-ecosystem]
canonical: concepts/mcp-servers
provenance_raw:
  - "raw/web/2026-08-22-Understanding MCP servers.md"
  - "raw/web/2026-08-22-Build an MCP server.md"
  - "raw/web/2026-08-22-Debugging.md"
  - "raw/web/2026-08-22-Configuration and flags.md"
---

# MCP Servers — 建置與操作

> MCP Server 是向 MCP Client 提供上下文的程式，透過暴露 tools、resources、prompts 三種原語讓 AI 應用程式存取外部系統。

## 伺服器核心概念

MCP Server 是響應 MCP Client 請求的程式。本地 MCP Server（使用 STDIO 傳輸）通常服務單一 Client；遠端 MCP Server（使用 Streamable HTTP）通常服務多個 Client `Architecture overview`。

Server 的主要職責：
- 暴露可用的 tools、resources、prompts（透過 `*/list` 方法）
- 回應 `tools/call`、`resources/read`、`prompts/get` 等請求
- 支援 `server/discover` 進行能力廣告 `Architecture overview`

## 建置 MCP Server

建置 MCP Server 的核心步驟：
1. 選擇 SDK（TypeScript、Python、C# 等）
2. 定義 tools（可執行函數）
3. 定義 resources（資料來源）
4. 定義 prompts（互動模板）
5. 選擇傳輸方式（STDIO 或 Streamable HTTP）
6. 設定連線參數 `Build an MCP server`

### 範例：簡單的加法 Tool

```typescript
server.registerTool(
  "add",
  {
    title: "Addition Tool",
    description: "Add two numbers together",
    inputSchema: {
      a: z.number().describe("First number to add"),
      b: z.number().describe("Second number to add"),
    },
  },
  async ({ a, b }) => ({
    content: [{ type: "text", text: `${a} + ${b} = ${a + b}` }],
  }),
);
```

`Understanding Authorization in MCP`

## 除錯

MCP Inspector 是官方的開發除錯工具，可用於：
- 連接 MCP Server 並列出可用的 tools/resources/prompts
- 手動呼叫 tools 並查看回應
- 檢視 JSON-RPC 訊息流 `Debugging`

## 設定與旗標

MCP Server 支援多種設定選項，包含連線參數、安全性設定、以及傳輸層配置 `Configuration and flags`。

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/concepts/mcp-clients|MCP Clients]] — 客戶端開發
- [[wiki/sources/mcp-sdks-and-tooling|MCP SDKs & Tooling]] — SDK 與工具

## 來源

- [[raw/web/2026-08-22-Understanding MCP servers|Understanding MCP servers]]
- [[raw/web/2026-08-22-Build an MCP server|Build an MCP server]]
- [[raw/web/2026-08-22-Debugging|Debugging]]
- [[raw/web/2026-08-22-Configuration and flags|Configuration and flags]]
