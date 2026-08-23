---
title: MCP Protocol Versioning — 版本控制與演進
type: concept
created: 2026-08-23
updated: 2026-08-23
sources: 2
tags: [mcp, versioning, protocol, lifecycle, deprecation]
topics: [agent-infrastructure, mcp-ecosystem]
canonical: concepts/mcp-protocol-versioning
provenance_raw:
  - "raw/web/2026-08-22-Versioning.md"
  - "raw/web/2026-08-22-Protocol eras.md"
---

# MCP Protocol Versioning — 版本控制與演進

> MCP 使用 `YYYY-MM-DD` 格式的字串版本識別碼，表示最後一次向後不相容變更的日期，並透過功能生命週期管理 deprecation。

## 版本格式

MCP 使用基於日期的版本格式 `YYYY-MM-DD`，不使用數字遞增。只要變更維持向後相容，版本就不會遞增 `Versioning`。

## 修訂狀態

| 狀態 | 說明 |
|------|------|
| **Draft** | 進行中的規格，尚未準備好使用 |
| **Current** | 當前協定版本（`2026-07-28`），可繼續接收向後相容變更 |
| **Final** | 已完成的規格，不會再變更 |

`Versioning`

## 功能生命週期

個別功能可被標記為 **Deprecated**，表示功能仍屬於規格的一部分，但已排定移除。Deprecated 功能必須：
- 記錄遷移路徑
- 在規格中保留至少 12 個月（或快速移除例外的 90 天）
- 之後才可在未來修訂中標記為 **Removed** `Versioning`

## 版本協商

每個請求都透過 `_meta` 欄位中的 `io.modelcontextprotocol/protocolVersion` 標示所使用的協定版本。在 Streamable HTTP 上，同一值也出現在 `MCP-Protocol-Version` 標頭中 `Versioning`。

如果伺服器不支援請求的版本，會回應 `UnsupportedProtocolVersionError`，列出其支援的版本。客戶端可使用共同支援的版本重試請求。

## server/discover

客戶端可透過 `server/discover` 強制請求取得伺服器支援的協定版本、能力與身份。這是選擇性的——客戶端可自由直接發送任何請求 `Versioning`。

## 已棄用功能

截至 `2026-07-28`，以下功能已棄用：
- **Sampling**：伺服器向客戶端的 LLM 請求補全 → 新實作應直接整合 LLM provider API
- **Logging**：伺服器向客戶端發送日誌 → 新實作應使用 stderr (STDIO) 或 OpenTelemetry

`Architecture overview`

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/entities/mcp-registry|MCP Registry]] — Registry 的版本管理
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發

## 來源

- [[raw/web/2026-08-22-Versioning|Versioning]]
- [[raw/web/2026-08-22-Protocol eras|Protocol eras]]
