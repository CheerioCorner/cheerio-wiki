---
title: MCP Apps — 互動式應用程式
type: entity
created: 2026-08-23
updated: 2026-08-23
sources: 2
tags: [mcp, apps, interactive-ui, iframe, sandbox]
topics: [extension-dev, agent-runtime-implementations, mcp-ecosystem]
canonical: entities/mcp-apps
provenance_raw:
  - "raw/web/2026-08-22-MCP Apps.md"
  - "raw/web/2026-08-22-Build an MCP App.md"
---

# MCP Apps — 互動式應用程式

> MCP Apps 讓伺服器在聊天中直接渲染互動式 HTML 介面（資料視覺化、表單、儀表板），使用者無需離開對話即可與資料互動。

## 為什麼不用獨立 Web App？

MCP Apps 相比獨立 Web App 的關鍵優勢：
- **上下文保留**：App 生活在對話中，使用者不需切換分頁
- **雙向資料流**：App 可呼叫 MCP Server 的任何工具，Host 可推送新結果
- **整合 Host 能力**：App 可委託 Host 執行已連接的功能（如排程會議）
- **安全保障**：App 在沙箱 iframe 中運行，無法存取父頁面或 Cookie `MCP Apps`

## 工作原理

1. **UI 預載入**：Tool 描述包含 `_meta.ui.resourceUri` 指向 `ui://` 資源
2. **資源取得**：Host 從 Server 取得 UI 資源（HTML + JS + CSS）
3. **沙箱渲染**：在受限的 iframe 中渲染
4. **雙向通訊**：透過 JSON-RPC 協定的 `ui/` 方法名稱前綴進行通訊 `MCP Apps`

## 適用場景

- 資料探索（互動式地圖、圖表）
- 多選項配置（表單、設定面板）
- 即時儀表板（監控、分析） `MCP Apps`

## 相關頁面

- [[wiki/entities/mcp-extensions|MCP Extensions]] — 擴充機制總覽
- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發

## 來源

- [[raw/web/2026-08-22-MCP Apps|MCP Apps]]
- [[raw/web/2026-08-22-Build an MCP App|Build an MCP App]]
