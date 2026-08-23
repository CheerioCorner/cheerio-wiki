---
title: MCP 官方文件彙整（42 篇）
type: source
created: 2026-08-23
updated: 2026-08-23
sources: 42
tags: [mcp, official-docs, modelcontextprotocol]
topics: [mcp-ecosystem]
canonical: sources/mcp-official-docs-42
provenance_raw:
  - "raw/web/2026-08-22-What is the Model Context Protocol (MCP).md"
  - "raw/web/2026-08-22-Architecture overview.md"
  - "raw/web/2026-08-22-Architecture overview 1.md"
  - "raw/web/2026-08-22-Understanding MCP servers.md"
  - "raw/web/2026-08-22-Understanding MCP clients.md"
  - "raw/web/2026-08-22-Authorization.md"
  - "raw/web/2026-08-22-Authorization Extensions.md"
  - "raw/web/2026-08-22-Understanding Authorization in MCP.md"
  - "raw/web/2026-08-22-OAuth Client Credentials.md"
  - "raw/web/2026-08-22-Enterprise-Managed Authorization.md"
  - "raw/web/2026-08-22-How to Authenticate When Publishing to the Official MCP Registry.md"
  - "raw/web/2026-08-22-The MCP Registry.md"
  - "raw/web/2026-08-22-The MCP Registry Moderation Policy.md"
  - "raw/web/2026-08-22-MCP Registry Aggregators.md"
  - "raw/web/2026-08-22-MCP Registry Supported Package Types.md"
  - "raw/web/2026-08-22-Quickstart Publish an MCP Server to the MCP Registry.md"
  - "raw/web/2026-08-22-Publishing Remote Servers.md"
  - "raw/web/2026-08-22-How to Automate Publishing with GitHub Actions.md"
  - "raw/web/2026-08-22-Versioning Published MCP Servers.md"
  - "raw/web/2026-08-22-Build an MCP server.md"
  - "raw/web/2026-08-22-Build an MCP client.md"
  - "raw/web/2026-08-22-Build an MCP App.md"
  - "raw/web/2026-08-22-Build with Agent Skills.md"
  - "raw/web/2026-08-22-CLI client.md"
  - "raw/web/2026-08-22-TUI client.md"
  - "raw/web/2026-08-22-Web client.md"
  - "raw/web/2026-08-22-Connect to local MCP servers.md"
  - "raw/web/2026-08-22-Connect to remote MCP Servers.md"
  - "raw/web/2026-08-22-Extensions Overview.md"
  - "raw/web/2026-08-22-Extension Support Matrix.md"
  - "raw/web/2026-08-22-Tasks.md"
  - "raw/web/2026-08-22-Protocol eras.md"
  - "raw/web/2026-08-22-MCP Apps.md"
  - "raw/web/2026-08-22-SDKs.md"
  - "raw/web/2026-08-22-MCP Inspector.md"
  - "raw/web/2026-08-22-Example Servers.md"
  - "raw/web/2026-08-22-Recipes.md"
  - "raw/web/2026-08-22-Debugging.md"
  - "raw/web/2026-08-22-Configuration and flags.md"
  - "raw/web/2026-08-22-Client Best Practices.md"
  - "raw/web/2026-08-22-Versioning.md"
---

# MCP 官方文件彙整（42 篇）

> 2026-08-22 從 modelcontextprotocol.io 剪藏的 42 篇官方文件，涵蓋 MCP 協定的完整規範。Protocol version `2026-07-28`。

## 文件分類

### 架構與核心概念（5 篇）
- `What is the Model Context Protocol (MCP).md` — MCP 介紹、USB-C 類比、生態系支援
- `Architecture overview.md` — 完整架構：參與者、資料/傳輸層、JSON-RPC 2.0、原語
- `Architecture overview 1.md` — 架構概述（可能為重複剪藏）
- `Understanding MCP servers.md` — 伺服器端概念
- `Understanding MCP clients.md` — 客戶端概念、elicitation

### 授權與安全（6 篇）
- `Authorization.md` — 授權概述
- `Authorization Extensions.md` — OAuth Client Credentials + Enterprise-Managed Auth
- `Understanding Authorization in MCP.md` — 完整 OAuth 2.1 教學（Keycloak + TS/Python/C#）
- `OAuth Client Credentials.md` — M2M 認證擴充
- `Enterprise-Managed Authorization.md` — 企業授權框架
- `How to Authenticate When Publishing to the Official MCP Registry.md` — Registry 認證

### MCP Registry（8 篇）
- `The MCP Registry.md` — Registry 概述
- `The MCP Registry Moderation Policy.md` — 審查政策
- `MCP Registry Aggregators.md` — 第三方聚合器
- `MCP Registry Supported Package Types.md` — 支援套件類型
- `Quickstart Publish an MCP Server to the MCP Registry.md` — 發佈教學
- `Publishing Remote Servers.md` — 遠端伺服器發佈
- `How to Automate Publishing with GitHub Actions.md` — CI/CD 自動化
- `Versioning Published MCP Servers.md` — 版本管理

### 建置 MCP 組件（4 篇）
- `Build an MCP server.md` — 伺服器建置指南
- `Build an MCP client.md` — 客戶端建置指南
- `Build an MCP App.md` — App 建置指南
- `Build with Agent Skills.md` — Agent Skills 整合

### 客戶端類型（5 篇）
- `CLI client.md` — CLI 客戶端
- `TUI client.md` — TUI 客戶端
- `Web client.md` — Web 客戶端
- `Connect to local MCP servers.md` — 本地連線
- `Connect to remote MCP Servers.md` — 遠端連線

### 擴充與任務（4 篇）
- `Extensions Overview.md` — 擴充機制概述
- `Extension Support Matrix.md` — 支援矩陣
- `Tasks.md` — 長任務擴充
- `Protocol eras.md` — 協定演進

### MCP Apps（1 篇）
- `MCP Apps.md` — 互動式應用程式

### 開發工具（7 篇）
- `SDKs.md` — 官方 SDKs
- `MCP Inspector.md` — 開發除錯工具
- `Example Servers.md` — 參考伺服器範例
- `Recipes.md` — 常用食譜
- `Debugging.md` — 除錯指南
- `Configuration and flags.md` — 設定與旗標
- `Client Best Practices.md` — 客戶端最佳實踐

### 版本控制（1 篇）
- `Versioning.md` — 協定版本控制

## 對應的 Wiki 頁面

| Wiki 頁面 | 類型 | 涵蓋文件數 |
|-----------|------|-----------|
| [[wiki/entities/mcp-model-context-protocol\|MCP]] | entity | 3（架構核心） |
| [[wiki/concepts/mcp-servers\|MCP Servers]] | concept | 4 |
| [[wiki/concepts/mcp-clients\|MCP Clients]] | concept | 3 |
| [[wiki/concepts/mcp-client-types\|MCP Client Types]] | concept | 5 |
| [[wiki/entities/mcp-authorization\|MCP Authorization]] | entity | 6 |
| [[wiki/entities/mcp-registry\|MCP Registry]] | entity | 9 |
| [[wiki/entities/mcp-extensions\|MCP Extensions]] | entity | 2 |
| [[wiki/entities/mcp-tasks\|MCP Tasks]] | entity | 1 |
| [[wiki/entities/mcp-apps\|MCP Apps]] | entity | 2 |
| [[wiki/concepts/mcp-protocol-versioning\|MCP Versioning]] | concept | 2 |
| [[wiki/sources/mcp-sdks-and-tooling\|MCP SDKs & Tooling]] | source | 4 |
| [[wiki/concepts/mcp-agent-skills-integration\|MCP + Agent Skills]] | concept | 1 |
