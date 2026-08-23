---
title: MCP SDKs & Development Tooling
type: source
created: 2026-08-23
updated: 2026-08-23
sources: 4
tags: [mcp, sdks, typescript, python, csharp, inspector, tools]
topics: [ai-development-tools, mcp-ecosystem]
canonical: sources/mcp-sdks-and-tooling
provenance_raw:
  - "raw/web/2026-08-22-SDKs.md"
  - "raw/web/2026-08-22-MCP Inspector.md"
  - "raw/web/2026-08-22-Example Servers.md"
  - "raw/web/2026-08-22-Recipes.md"
---

# MCP SDKs & Development Tooling

> 彙整 MCP 官方多語言 SDK、MCP Inspector 開發工具、參考伺服器範例與常用食譜。

## 官方 SDKs

MCP 提供多種語言的官方 SDK `SDKs`：

| 語言 | 套件 | 說明 |
|------|------|------|
| **TypeScript** | `@modelcontextprotocol/sdk` | Node.js / Deno / Bun |
| **Python** | `mcp` | Python 3.10+ |
| **C#** | `ModelContextProtocol` | .NET 9+ |
| **Java** | `io.modelcontextprotocol:sdk` | Java 17+ |
| **Kotlin** | `io.modelcontextprotocol:kotlin-sdk` | JVM / Native |
| **Swift** | `ModelContextProtocol` | macOS / iOS |
| **Go** | `github.com/modelcontextprotocol/go-sdk` | Go 1.21+ |

## MCP Inspector

MCP Inspector 是官方的開發除錯工具，用於：
- 連接 MCP Server 並列出可用功能
- 手動呼叫 tools 並檢視回應
- 檢視 JSON-RPC 訊息流
- 測試授權流程 `MCP Inspector`

GitHub: `https://github.com/modelcontextprotocol/inspector`

## 參考伺服器範例

官方提供多種參考伺服器實作，展示不同使用場景 `Example Servers`。

## 常用食譜 (Recipes)

常見的 MCP 實作模式與食譜 `Recipes`。

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發
- [[wiki/concepts/mcp-clients|MCP Clients]] — 客戶端開發
- [[wiki/entities/mcp-extensions|MCP Extensions]] — 擴充機制

## 來源

- [[raw/web/2026-08-22-SDKs|SDKs]]
- [[raw/web/2026-08-22-MCP Inspector|MCP Inspector]]
- [[raw/web/2026-08-22-Example Servers|Example Servers]]
- [[raw/web/2026-08-22-Recipes|Recipes]]
