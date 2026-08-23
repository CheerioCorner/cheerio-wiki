---
title: MCP (Model Context Protocol)
type: entity
created: 2026-08-04
updated: 2026-08-23
sources: 42
tags: [mcp, model-context-protocol, anthropic, ai-agent, protocol, tools]
topics: [agent-infrastructure, agent-runtime-implementations, mcp-ecosystem]
canonical: entities/mcp-model-context-protocol
provenance_raw:
  - "raw/web/2026-08-22-What is the Model Context Protocol (MCP).md"
  - "raw/web/2026-08-22-Architecture overview.md"
---

# MCP (Model Context Protocol)

> Anthropic 發起的開源標準，讓 AI 應用程式透過統一協定連接外部系統——就像 AI 世界的 USB-C 介面。

## 什麼是 MCP？

MCP (Model Context Protocol) 是一個開源協定，用於連接 AI 應用程式與外部系統。透過 MCP，AI 應用程式（如 Claude、ChatGPT）可以連接資料來源（本地檔案、資料庫）、工具（搜尋引擎、計算機）和工作流程（專用 prompt），從而存取關鍵資訊並執行任務 `What is MCP`。

**核心價值**：
- **開發者**：減少建構或整合 AI 應用程式的開發時間與複雜度
- **AI 應用程式**：存取工具、資料來源與應用程式的生態系
- **終端使用者**：獲得更強大的 AI 應用程式，能存取資料並代為執行操作 `What is MCP`

## 架構概述

MCP 採用客戶端-伺服器架構，由三個核心參與者組成：

```
MCP Host（AI 應用程式，如 Claude Code / VS Code）
    ├── MCP Client 1 ←→ MCP Server A（本地，如 Filesystem）
    ├── MCP Client 2 ←→ MCP Server B（本地，如 Database）
    └── MCP Client 3 ←→ MCP Server C（遠端，如 Sentry）
```

### 三個核心參與者

| 參與者 | 說明 | 範例 |
|--------|------|------|
| **MCP Host** | 協調與管理多個 MCP Client 的 AI 應用程式 | Claude Desktop、VS Code、ChatGPT |
| **MCP Client** | 與 MCP Server 維持專用連線的元件 | Host 自動為每個 Server 建立 |
| **MCP Server** | 向 Client 提供上下文的程式 | Filesystem Server、Sentry Server |

`Architecture overview`

### 兩大層級

MCP 由兩個層級組成：

- **資料層 (Data Layer)**：基於 JSON-RPC 2.0 的通訊協定，定義訊息結構與語義，包含能力發現、伺服器功能（tools/resources/prompts）、客戶端功能（elicitation）與通知機制
- **傳輸層 (Transport Layer)**：管理通訊通道與認證，支援 STDIO（本地進程）與 Streamable HTTP（遠端伺服器）兩種傳輸機制 `Architecture overview`

### 無狀態設計

MCP 是無狀態協定。每個請求都在 `_meta` 欄位中攜帶協定版本與相關能力。伺服器透過強制的 `server/discover` 請求廣告其支援的版本與能力 `Architecture overview`。

## 核心原語 (Primitives)

### 伺服器端原語

MCP 定義了三種伺服器可暴露的核心原語：

| 原語 | 說明 | 發現方法 | 執行方法 |
|------|------|---------|---------|
| **Tools** | AI 應用程式可呼叫的可執行函數（檔案操作、API 呼叫、資料庫查詢） | `tools/list` | `tools/call` |
| **Resources** | 向 AI 應用程式提供上下文資訊的資料來源（檔案內容、資料庫記錄、API 回應） | `resources/list` | `resources/read` |
| **Prompts** | 幫助與語言模型結構化互動的可重用模板（系統 prompt、few-shot 範例） | `prompts/list` | `prompts/get` |

`Architecture overview`

### 客戶端原語

| 原語 | 說明 | 狀態 |
|------|------|------|
| **Elicitation** | 允許伺服器向使用者請求額外資訊（確認操作、取得更多輸入），透過 `elicitation/create` 方法 | ✅ 活躍 |
| **Sampling** | 允許伺服器向客戶端的 AI 應用程式請求語言模型補全 | ⚠️ 已棄用（2026-07-28） |
| **Logging** | 伺服器向客戶端發送除錯和監控日誌 | ⚠️ 已棄用（2026-07-28） |

`Architecture overview`

> ⚠️ **與舊版頁面差異**：舊版只提到 tools/resources/prompts 三大原語，遺漏了 Elicitation（客戶端原語）以及 Sampling/Logging 的棄用狀態。正確的分類是「三個伺服器端原語 + 一個活躍客戶端原語 + 兩個已棄用客戶端原語」。

## 擴充機制 (Extensions)

MCP 支援可選的擴充套件，用於定義核心協定之外的功能。擴充套件使用唯一識別碼 `{vendor-prefix}/{extension-name}` 格式（如 `io.modelcontextprotocol/oauth-client-credentials`）`Extensions Overview`。

官方擴充套件包括：
- **Authorization Extensions** — OAuth Client Credentials、Enterprise-Managed Authorization
- **MCP Apps** — 在聊天中渲染互動式 HTML 介面
- **MCP Tasks** — 長時間運行操作的非同步任務執行

詳見 [[wiki/entities/mcp-extensions|MCP Extensions]]、[[wiki/entities/mcp-tasks|MCP Tasks]]、[[wiki/entities/mcp-apps|MCP Apps]]。

## 版本控制

MCP 使用基於字串的版本識別碼，格式為 `YYYY-MM-DD`，表示最後一次向後不相容變更的日期 `Versioning`。

- **Draft**：進行中的規格
- **Current**：當前協定版本（`2026-07-28`）
- **Final**：已完成的規格

詳見 [[wiki/concepts/mcp-protocol-versioning|MCP Protocol Versioning]]。

## 生態系

MCP 受到廣泛支援：Claude、ChatGPT、VS Code、Cursor、MCPJam 等都支援 MCP `What is MCP`。

- SDKs 支援 TypeScript、Python、C#、Java、Kotlin、Swift、Go 等語言 [[wiki/sources/mcp-sdks-and-tooling|MCP SDKs]]
- MCP Registry 提供官方伺服器註冊表 [[wiki/entities/mcp-registry|MCP Registry]]
- MCP Inspector 提供開發除錯工具 [[wiki/sources/mcp-sdks-and-tooling|MCP SDKs]]

## ⚠️ 舊版頁面規範演進比較

> 本頁面於 2026-08-04 建立時，唯一來源已移入 `raw/.trash/`。以下根據 2026-08-22 剪藏的 42 篇官方文件（protocol version `2026-07-28`），逐項比對既有論述。

| 舊版論述 | 狀態 | 2026-07-28 官方規範 |
|----------|------|---------------------|
| MCP 是「將 LSP、Code Graph 等服務封裝為 AI Agent 可呼叫的 Tools」 | ⚠️ **已過期** | MCP 不再只定位為 LSP 封裝。它是通用的 AI 應用程式與外部系統連接標準，支援 Claude、ChatGPT、VS Code 等多種 Host，工具不限於 LSP |
| 核心原語為 tools/resources/prompts 三大 primitive | ⚠️ **已過期** | 正確分類為「三個伺服器端原語（tools/resources/prompts）+ 一個活躍客戶端原語（elicitation）+ 兩個已棄用客戶端原語（sampling/logging）」 |
| 架構圖為「AI Agent → MCP Protocol → MCP Server → 外部服務」 | ⚠️ **已過期** | 正確架構為 Host/Client/Server 三角色，資料層（JSON-RPC 2.0）+ 傳輸層（STDIO/Streamable HTTP）兩層級 |
| LSP 整合範例（`@modelcontextprotocol/server-lsp`）作為主要展示 | ⚠️ **已過期** | LSP 只是眾多 MCP Server 之一。官方範例包括 Filesystem、Sentry、Database 等多種場景 |
| 安全性僅提「Tool 白名單、沙箱隔離、權限控制」 | ⚠️ **已過期** | 完整的 OAuth 2.1 授權框架（RFC 8414/7591/9728/8707），含 Token 驗證、DCR、HTTPS 強制等 |
| 無提及 Registry、Extensions、Tasks、Apps、Versioning | 🔴 **完全缺失** | MCP Registry（官方伺服器註冊表）、Extensions（擴充機制）、Tasks（長任務）、Apps（互動式 UI）、Versioning（YYYY-MM-DD 版本控制）均已正式規範化 |

## 相關頁面

- [[wiki/entities/mcp-authorization|MCP Authorization]] — OAuth 2.1 授權框架
- [[wiki/entities/mcp-registry|MCP Registry]] — 官方伺服器註冊表
- [[wiki/entities/mcp-extensions|MCP Extensions]] — 擴充機制
- [[wiki/entities/mcp-tasks|MCP Tasks]] — 長任務擴充
- [[wiki/entities/mcp-apps|MCP Apps]] — 互動式應用程式
- [[wiki/concepts/mcp-protocol-versioning|MCP Protocol Versioning]] — 版本控制
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發
- [[wiki/concepts/mcp-clients|MCP Clients]] — 客戶端開發
- [[wiki/concepts/mcp-client-types|MCP Client Types]] — 客戶端類型與連線
- [[wiki/sources/mcp-sdks-and-tooling|MCP SDKs & Tooling]] — SDK 與開發工具
- [[wiki/concepts/mcp-agent-skills-integration|MCP + Agent Skills]] — Agent Skills 整合
- [[wiki/entities/lsp|LSP]] — Language Server Protocol
- [[wiki/concepts/agent-skills|Agent Skills]] — Skill 系統根概念

## 來源

所有 42 篇原始剪藏來自 modelcontextprotocol.io 官方文件（剪藏日期 2026-08-22），完整列表見 [[wiki/sources/mcp-official-docs-42|MCP 官方文件彙整（42 篇）]]。
