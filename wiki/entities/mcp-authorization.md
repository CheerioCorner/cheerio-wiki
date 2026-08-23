---
title: MCP Authorization — OAuth 2.1 授權框架
type: entity
created: 2026-08-23
updated: 2026-08-23
sources: 6
tags: [mcp, authorization, oauth, security, enterprise]
topics: [agent-infrastructure, mcp-ecosystem]
canonical: entities/mcp-authorization
provenance_raw:
  - "raw/web/2026-08-22-Authorization.md"
  - "raw/web/2026-08-22-Authorization Extensions.md"
  - "raw/web/2026-08-22-Understanding Authorization in MCP.md"
  - "raw/web/2026-08-22-OAuth Client Credentials.md"
  - "raw/web/2026-08-22-Enterprise-Managed Authorization.md"
  - "raw/web/2026-08-22-How to Authenticate When Publishing to the Official MCP Registry.md"
---

# MCP Authorization — OAuth 2.1 授權框架

> MCP 使用標準化的 OAuth 2.1 授權流程保護伺服器上的敏感資源與操作，支援一般 OAuth、Client Credentials 與企業託管授權三種模式。

## 授權時機

MCP 伺服器的授權是**可選的**，但在以下情況強烈建議使用：
- 伺服器存取使用者特定資料（電子郵件、文件、資料庫）
- 需要審計誰執行了哪些操作
- 伺服器授予需要使用者同意的 API 存取
- 建構具有嚴格存取控制的企業環境 `Understanding Authorization in MCP`

## OAuth 2.1 授權流程

MCP 授權基於 OAuth 2.1 標準，遵循以下相關規範：
- **RFC 8414**：Authorization Server Metadata 發現
- **RFC 7591**：Dynamic Client Registration (DCR)
- **RFC 9728**：Protected Resource Metadata
- **RFC 8707**：Resource Indicators `Understanding Authorization in MCP`

### 授權流程步驟

1. Client 向 MCP Server 發送請求
2. Server 回應 401，附帶 `WWW-Authenticate` 標頭指向 Protected Resource Metadata
3. Client 從 Protected Resource Metadata 取得 Authorization Server 的 URL
4. Client 執行 Dynamic Client Registration 取得 client_id
5. Client 將使用者導向 Authorization Server 進行授權
6. 使用者同意後，Authorization Server 發放 access token
7. Client 使用 token 存取 MCP Server `Understanding Authorization in MCP`

### STDIO 傳輸的授權

對於使用 STDIO 傳輸的本地 MCP Server，可以使用環境變數憑證或第三方函式庫直接嵌入的憑證，而非 OAuth 流程 `Understanding Authorization in MCP`。

## 授權擴充套件

### OAuth Client Credentials

用於機器對機器 (M2M) 認證的擴充套件，不需要使用者互動 `OAuth Client Credentials`。

### Enterprise-Managed Authorization

用於企業環境的集中式存取控制框架 `Enterprise-Managed Authorization`。

## 安全最佳實踐

- **不要自行實作 Token 驗證**：使用經過驗證的函式庫
- **使用短期 Access Token**：減少被盜用的風險
- **Always validate tokens**：驗證 token 的 aud claim
- **強制 HTTPS**：生產環境不接受純 HTTP
- **最小權限 Scopes**：按 tool/capability 拆分存取權限
- **不要記錄憑證**：永遠不要 log Authorization 標頭或 token
- **DCR 控制**：注意 Trusted Hosts 和 unauthenticated DCR 的風險
- **Session ID 加固**：`Mcp-Session-Id` 視為不受信任輸入 `Understanding Authorization in MCP`

## 實作範例

官方提供 TypeScript、Python、C# 三種語言的完整實作範例，包含 Keycloak 整合、Token Introspection 驗證等 `Understanding Authorization in MCP`。

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/entities/mcp-registry|MCP Registry]] — Registry 發佈需要認證
- [[wiki/entities/mcp-extensions|MCP Extensions]] — Authorization 是 Extensions 的一部分

## 來源

- [[raw/web/2026-08-22-Authorization|Authorization]]
- [[raw/web/2026-08-22-Authorization Extensions|Authorization Extensions]]
- [[raw/web/2026-08-22-Understanding Authorization in MCP|Understanding Authorization in MCP]]
- [[raw/web/2026-08-22-OAuth Client Credentials|OAuth Client Credentials]]
- [[raw/web/2026-08-22-Enterprise-Managed Authorization|Enterprise-Managed Authorization]]
- [[raw/web/2026-08-22-How to Authenticate When Publishing to the Official MCP Registry|How to Authenticate When Publishing]]
