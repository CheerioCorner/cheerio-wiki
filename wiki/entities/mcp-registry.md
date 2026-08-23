---
title: MCP Registry — 官方伺服器註冊表
type: entity
created: 2026-08-23
updated: 2026-08-23
sources: 9
tags: [mcp, registry, publishing, packages, governance]
topics: [agent-infrastructure, mcp-ecosystem]
canonical: entities/mcp-registry
provenance_raw:
  - "raw/web/2026-08-22-The MCP Registry.md"
  - "raw/web/2026-08-22-The MCP Registry Moderation Policy.md"
  - "raw/web/2026-08-22-MCP Registry Aggregators.md"
  - "raw/web/2026-08-22-MCP Registry Supported Package Types.md"
  - "raw/web/2026-08-22-Quickstart Publish an MCP Server to the MCP Registry.md"
  - "raw/web/2026-08-22-Publishing Remote Servers.md"
  - "raw/web/2026-08-22-How to Automate Publishing with GitHub Actions.md"
  - "raw/web/2026-08-22-Versioning Published MCP Servers.md"
  - "raw/web/2026-08-22-How to Authenticate When Publishing to the Official MCP Registry.md"
---

# MCP Registry — 官方伺服器註冊表

> MCP Registry 是官方的 MCP Server 註冊表，支援多種套件格式（npm、PyPI、Docker 等），提供伺服器發現、審查政策與自動化發佈流程。

## 什麼是 MCP Registry？

MCP Registry 是官方的 MCP Server 發現與註冊中心，讓使用者能找到可用的 MCP Server `The MCP Registry`。

## 支援的套件類型

MCP Registry 支援多種套件格式，包括 npm、PyPI、Docker 等 `MCP Registry Supported Package Types`。

## 審查政策

所有提交到 MCP Registry 的伺服器都需經過審查，確保品質與安全性 `The MCP Registry Moderation Policy`。

## 發佈流程

### Quickstart

快速將 MCP Server 發佈到 Registry 的步驟：
1. 確認 Server 符合 MCP 規範
2. 設定認證（OAuth 2.1）
3. 提交審查 `Quickstart Publish an MCP Server`

### 遠端伺服器發佈

遠端 MCP Server（使用 Streamable HTTP 傳輸）的發佈方式 `Publishing Remote Servers`。

### GitHub Actions 自動化

使用 GitHub Actions 自動化發佈流程，確保每次版本更新都自動同步到 Registry `How to Automate Publishing with GitHub Actions`。

### 版本管理

已發佈的 MCP Server 的版本控制策略與最佳實踐 `Versioning Published MCP Servers`。

### Registry 認證

發佈到 Registry 需要通過 OAuth 2.1 認證 `How to Authenticate When Publishing`。

## 第三方 Aggregators

除了官方 Registry，也有第三方聚合器提供 MCP Server 發現服務 `MCP Registry Aggregators`。

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/entities/mcp-authorization|MCP Authorization]] — Registry 發佈需要認證
- [[wiki/concepts/mcp-protocol-versioning|MCP Protocol Versioning]] — 版本控制

## 來源

- [[raw/web/2026-08-22-The MCP Registry|The MCP Registry]]
- [[raw/web/2026-08-22-The MCP Registry Moderation Policy|The MCP Registry Moderation Policy]]
- [[raw/web/2026-08-22-MCP Registry Aggregators|MCP Registry Aggregators]]
- [[raw/web/2026-08-22-MCP Registry Supported Package Types|MCP Registry Supported Package Types]]
- [[raw/web/2026-08-22-Quickstart Publish an MCP Server to the MCP Registry|Quickstart Publish]]
- [[raw/web/2026-08-22-Publishing Remote Servers|Publishing Remote Servers]]
- [[raw/web/2026-08-22-How to Automate Publishing with GitHub Actions|Automate Publishing]]
- [[raw/web/2026-08-22-Versioning Published MCP Servers|Versioning Published MCP Servers]]
- [[raw/web/2026-08-22-How to Authenticate When Publishing to the Official MCP Registry|Registry Authentication]]
