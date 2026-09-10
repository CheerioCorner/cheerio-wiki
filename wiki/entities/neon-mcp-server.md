---
title: "Neon MCP Server — 現代 MCP Server 設計參考架構"
type: entity
created: 2026-09-10
updated: 2026-09-10
sources: 1
tags: [mcp, server, neon, postgres, sdk]
topics: [mcp-ecosystem, agent-infrastructure]
canonical: entities/neon-mcp-server
---

> Neon Postgres 官方開源的 MCP 伺服器，展示結合自動生成 SDK、人體工學複合操作（Ergonomic Methods）與獨立工具包（Neon Tools）的現代 MCP 架構設計範例。

## 架構

Neon MCP server 採用三層架構 `[04:10]-[04:44]`：

```
┌─────────────────────────────────┐
│  Neon MCP Server / Framework    │  ← 匯入 neon tools
│  Adapters (Mastra, Eve)         │
├─────────────────────────────────┤
│  neon tools 套件                 │  ← 獨立 tool call 包裝
│  (raw endpoints + ergonomic)    │
├─────────────────────────────────┤
│  手寫 ergonomic 方法             │  ← create_with_compute 等
├─────────────────────────────────┤
│  OpenAPI 自動產生的 SDK          │  ← 涵蓋所有 API endpoint
└─────────────────────────────────┘
```

### 1. OpenAPI 自動產生的 SDK

Neon 的 SDK 由 OpenAPI spec 自動產生，確保涵蓋所有 API endpoint `[04:10]`。

### 2. 手寫 Ergonomic 方法包裝層

在自動產生的 SDK 之上，Neon 另外加了一層手寫的人體工學方法 `[04:23]`。例如 `create_with_compute` 把建立 branch 的三個 API 呼叫（建立 branch、附加運算資源、取得連線字串）封裝為單一方法 `[03:11]-[03:40]`。

### 3. Neon Tools 獨立套件

把原始 endpoint 與 ergonomic 方法都轉換成 tool call，包裝為獨立套件 `[04:44]`。優勢 `[04:55]-[05:11]`：

- Neon MCP server 匯入此套件對外暴露 tool
- 可用於建構 Mastra、Eve 等 agent framework 的 adapter
- tool 依類別分組，方便限縮 agent 可存取的 tool 範圍

## 設計哲學

> 「the server is controlling what tools are available, but then the client handles discovering and executing those tools in whatever way it sees fit」`[05:27]`

核心分工：server 端定義工具邊界，client 端負責發現與執行。

## 開源

影片提到 Neon MCP server 與 neon tools 套件均為開源 `[05:27]`。

## 來源

- [[wiki/sources/2026-09-08-mcp-just-got-a-whole-lot-better|MCP Just Got a Whole Lot Better]]

## 相關頁面

- [[wiki/concepts/mcp-servers|MCP Servers]]
- [[wiki/concepts/progressive-tool-discovery|Progressive Tool Discovery]]
- [[wiki/concepts/mcp-agent-skills-integration|MCP + Agent Skills 整合]]
