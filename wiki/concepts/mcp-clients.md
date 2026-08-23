---
title: MCP Clients — 建置與操作
type: concept
created: 2026-08-23
updated: 2026-08-23
sources: 3
tags: [mcp, client, elicitation, development]
topics: [agent-infrastructure, mcp-ecosystem]
canonical: concepts/mcp-clients
provenance_raw:
  - "raw/web/2026-08-22-Understanding MCP clients.md"
  - "raw/web/2026-08-22-Build an MCP client.md"
  - "raw/web/2026-08-22-Client Best Practices.md"
---

# MCP Clients — 建置與操作

> MCP Client 是維持與 MCP Server 連線的元件，負責為 MCP Host 取得上下文。每個 Client 維持與對應 Server 的專用連線。

## 客戶端核心概念

MCP Client 由 MCP Host（AI 應用程式）建立，每個 Server 對應一個 Client 實例 `Architecture overview`。

Client 的核心職責：
- 透過 `server/discover` 發現伺服器能力
- 使用 `*/list` 方法列出可用的 tools/resources/prompts
- 呼叫 `tools/call`、`resources/read`、`prompts/get` 等方法
- 支援 version negotiation（版本協商）`Versioning`

## Elicitation：客戶端原語

Elicitation 是 MCP 定義的客戶端原語，允許伺服器向使用者請求額外資訊。這是唯一活躍的客戶端原語（Sampling 和 Logging 已棄用）`Architecture overview`。

運作方式：
1. 伺服器透過 `elicitation/create` 請求使用者輸入
2. 請求透過 Multi Round-Trip Requests (MRTR) 模式傳遞
3. Client 將請求轉發給使用者
4. 使用者的回應透過 `tasks/update` 回傳給伺服器 `Architecture overview`

> ⚠️ **舊版遺漏**：舊版 wiki 頁面完全未提及 Elicitation 作為客戶端原語。

## 建置 MCP Client

建置 Client 需要：
1. 選擇 SDK 並實作 MCP 協定
2. 處理連線建立與版本協商
3. 實作 tools/resources/prompts 的消費邏輯
4. 處理通知與訂閱 `Build an MCP client`

## 最佳實踐

Client 開發的最佳實踐包含：
- 正確處理版本不相容錯誤
- 實作重試與容錯機制
- 管理多 Server 連線的生命週期
- 正確處理通知訂閱 `Client Best Practices`

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發
- [[wiki/concepts/mcp-client-types|MCP Client Types]] — 客戶端類型與連線

## 來源

- [[raw/web/2026-08-22-Understanding MCP clients|Understanding MCP clients]]
- [[raw/web/2026-08-22-Build an MCP client|Build an MCP client]]
- [[raw/web/2026-08-22-Client Best Practices|Client Best Practices]]
