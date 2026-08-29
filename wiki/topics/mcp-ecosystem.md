---
type: topic
title: MCP Ecosystem
topic: mcp-ecosystem
created: 2026-08-23
updated: 2026-08-23
---

# Mcp Ecosystem

> 與 Mcp Ecosystem 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/mcp-apps|MCP Apps — 互動式應用程式]] — MCP Apps 讓伺服器在聊天中直接渲染互動式 HTML 介面（資料視覺化、表單、儀表板），使用者無需離開對話即可與資料互動。 🛠️
- [[wiki/entities/mcp-authorization|MCP Authorization — OAuth 2.1 授權框架]] — MCP 使用標準化的 OAuth 2.1 授權流程保護伺服器上的敏感資源與操作，支援一般 OAuth、Client Credentials 與企業託管授權三種模式。 🛠️
- [[wiki/entities/mcp-extensions|MCP Extensions — 擴充機制]] — MCP 擴充套件是協定的可選附加功能，用於定義核心協定之外的功能，支援模組化、專業化或實驗性功能。 🛠️
- [[wiki/entities/mcp-model-context-protocol|MCP (Model Context Protocol)]] — Anthropic 發起的開源標準，讓 AI 應用程式透過統一協定連接外部系統——就像 AI 世界的 USB-C 介面。 🛠️
- [[wiki/entities/mcp-registry|MCP Registry — 官方伺服器註冊表]] — MCP Registry 是官方的 MCP Server 註冊表，支援多種套件格式（npm、PyPI、Docker 等），提供伺服器發現、審查政策與自動化發佈流程。 🛠️
- [[wiki/entities/mcp-tasks|MCP Tasks — 長任務擴充機制]] — MCP Tasks 讓伺服器為長時間運行的請求返回持久控制代碼，客戶端可輪詢進度、提供輸入並在重連後取得結果，而非阻塞連線。 🛠️

## Concepts

- [[wiki/concepts/mcp-agent-skills-integration|MCP + Agent Skills 整合]] — Agent Skills 與 MCP 是互補的擴充機制——Skills 提供知識與流程，MCP 提供工具連接。兩者可組合建構更強大的 AI Agent。 🛠️
- [[wiki/concepts/mcp-client-types|MCP Client Types — 類型與連線模式]] — MCP 支援多種終端用戶端類型（CLI、TUI、Web），每種連線模式（本地 STDIO、遠端 Streamable HTTP）各有適用場景。 🛠️
- [[wiki/concepts/mcp-clients|MCP Clients — 建置與操作]] — MCP Client 是維持與 MCP Server 連線的元件，負責為 MCP Host 取得上下文。每個 Client 維持與對應 Server 的專用連線。 🛠️
- [[wiki/concepts/mcp-protocol-versioning|MCP Protocol Versioning — 版本控制與演進]] — MCP 使用 `YYYY-MM-DD` 格式的字串版本識別碼，表示最後一次向後不相容變更的日期，並透過功能生命週期管理 deprecation。 🛠️
- [[wiki/concepts/mcp-servers|MCP Servers — 建置與操作]] — MCP Server 是向 MCP Client 提供上下文的程式，透過暴露 tools、resources、prompts 三種原語讓 AI 應用程式存取外部系統。 🛠️
- [[wiki/concepts/webmcp|WebMCP — 網頁原生的 Agent-Ready 工具暴露機制]] — WebMCP 讓網站在頁面內直接宣告 agent-ready 工具，透過 browser API 暴露給 AI agent，不需額外的 MCP server。工具生命週期與頁面生命週期綁定，不同頁面暴露不同工具。 🛠️

## Sources

- [[wiki/sources/2026-08-24-webmcp-youtube|Make your website agent ready with WebMCP]] — 來源：[YouTube — Make your website agent ready with WebMCP](https://www.youtube.com/watch?v=FARxSG_EY98) 🛠️
- [[wiki/sources/mcp-official-docs-42|MCP 官方文件彙整（42 篇）]] — 2026-08-22 從 modelcontextprotocol.io 剪藏的 42 篇官方文件，涵蓋 MCP 協定的完整規範。Protocol version `2026-07-28`。
- [[wiki/sources/mcp-sdks-and-tooling|MCP SDKs & Development Tooling]] — 彙整 MCP 官方多語言 SDK、MCP Inspector 開發工具、參考伺服器範例與常用食譜。 🛠️

## 相關 Topics
