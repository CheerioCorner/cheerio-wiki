---
title: MCP Extensions — 擴充機制
type: entity
created: 2026-08-23
updated: 2026-08-23
sources: 2
tags: [mcp, extensions, vendor-prefix, support-matrix]
topics: [extension-dev, mcp-ecosystem]
canonical: entities/mcp-extensions
provenance_raw:
  - "raw/web/2026-08-22-Extensions Overview.md"
  - "raw/web/2026-08-22-Extension Support Matrix.md"
---

# MCP Extensions — 擴充機制

> MCP 擴充套件是協定的可選附加功能，用於定義核心協定之外的功能，支援模組化、專業化或實驗性功能。

## 擴充機制

MCP 擴充套件使用唯一識別碼格式：`{vendor-prefix}/{extension-name}`。官方擴充使用 `io.modelcontextprotocol` 前綴，第三方擴充使用反向域名（如 `com.example/my-extension`）`Extensions Overview`。

## 官方擴充儲存庫

官方擴充位於 Model Context Protocol GitHub 組織中，以 `ext-` 為前綴的儲存庫：

| 擴充類別 | 儲存庫 | 說明 |
|---------|--------|------|
| **Authorization** | `ext-auth` | OAuth Client Credentials、Enterprise-Managed Authorization |
| **MCP Apps** | `ext-apps` | 聊天中渲染互動式 HTML 介面 |
| **MCP Tasks** | `ext-tasks` | 長時間運行操作的非同步任務執行 |

`Extensions Overview`

## 支援矩陣

各大 MCP Host（Claude Desktop、VS Code、Cursor 等）對各擴充的支援程度不同，詳見 Extension Support Matrix `Extension Support Matrix`。

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/entities/mcp-authorization|MCP Authorization]] — Authorization 擴充
- [[wiki/entities/mcp-tasks|MCP Tasks]] — Tasks 擴充
- [[wiki/entities/mcp-apps|MCP Apps]] — Apps 擴充

## 來源

- [[raw/web/2026-08-22-Extensions Overview|Extensions Overview]]
- [[raw/web/2026-08-22-Extension Support Matrix|Extension Support Matrix]]
