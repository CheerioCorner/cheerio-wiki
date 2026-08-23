---
title: "WebMCP — 網頁原生的 Agent-Ready 工具暴露機制"
type: concept
created: 2026-08-24
updated: 2026-08-24
sources: 1
tags: [mcp, webmcp, web, agent, browser, ai-agent]
collection: concepts
topics: [mcp-ecosystem, agent-infrastructure]
canonical: concepts/webmcp
---

> WebMCP 讓網站在頁面內直接宣告 agent-ready 工具，透過 browser API 暴露給 AI agent，不需額外的 MCP server。工具生命週期與頁面生命週期綁定，不同頁面暴露不同工具。

## 什麼是 WebMCP

WebMCP 是一種把 MCP 的 tool-exposure 模式從「獨立 server」下放到「網頁本身」的技術。網站建造者在頁面中註冊工具、描述功能、宣告 inputs/outputs、標記 required fields，agent 進到頁面後就能直接看到並呼叫這些工具，不需要額外連接任何外部服務 `Make your website agent ready with WebMCP [00:36]`。

## 與傳統 MCP Server 的差異

| 面向 | 傳統 MCP Server | WebMCP |
|------|----------------|--------|
| 部署位置 | 獨立伺服器行程 | 隨網頁載入，瀏覽器內宣告 |
| 連線對象 | MCP Client ↔ MCP Server | Agent（瀏覽器內）↔ 頁面暴露的 tool |
| Context 綁定 | 通常是全站或全服務範圍 | Contextual — 依頁面/路由不同暴露不同工具 |
| 生命週期 | 獨立於網頁 | 與頁面同步載入/卸載 |
| 設定方式 | 獨立配置檔或環境變數 | Imperative API（register → describe → declare → mark） |

> ⚠️ WebMCP 仍是 MCP 心智模型的一個部署形態——「暴露 tool 給 agent 消費」的核心概念不變，只是傳輸/宣告方式不同。不是取代既有 MCP Server/Client 架構。

## Contextual Tools

WebMCP 的核心特性是工具與頁面綁定 `Make your website agent ready with WebMCP [01:15]`：

```
首頁 → search products / get categories / filter
產品頁 → add to cart / get similar products
結帳頁 → process payment / apply coupon
```

Agent 在不同頁面上看到的工具集不同，不需要連接頁面以外的任何東西。這比全站暴露同一套工具更精準，也更安全。

## Imperative API 使用流程

1. **註冊工具（register）** — 告訴瀏覽器「這個頁面有一個工具」`[02:50]`
2. **描述功能（describe）** — agent 看得懂這個工具做什麼
3. **宣告 inputs/outputs** — typed contract，agent 有精確的型別資訊 `[03:00]`
4. **標記 required fields** — 哪些參數是必填

完成後，agent 拿到的是一個 precise typed contract，不需要 scraping 或猜測。

## Token 效率

這是 WebMCP 最直接的商業價值 `Make your website agent ready with WebMCP [02:33]`：

| 方式 | Token 消耗 | 準確度 |
|------|-----------|--------|
| Screenshot-based agent | 高（每步都要截圖+OCR） | 低（容易誤判 DOM） |
| DOM scraping | 中（傳遞大量 HTML） | 中（結構不保證） |
| **WebMCP** | **低（typed contract，只傳必要參數）** | **高（預定義工具，零猜測）** |

## Local Agent Workflow

WebMCP 不只服務遠端 agent `Make your website agent ready with WebMCP [03:15]`：

```
開發者 CLI agent（本地）
    ↓ 開發 code
    ↓ 觸發 staging execution
    ↓ 透過 WebMCP 導航到 pipeline page
    ↓ 開發者在 Web UI 上看到 logs / lineage / sample data
```

這打通了「本地 agent 開發 → Web UI 即時監控」的工作流，開發者可以即時看到 data pipeline 的執行狀況，而不需要切換工具或手動查詢。

## 開放問題與限制

### 資安風險（Cheer 提出）

在網站上加了 Web MCP 就等於多了一個入口。駭客有可能利用這個機會：
- **惡意 agent 濫用**：如何區分合法 agent 與攻擊者？本影片未討論 WebMCP 的安全模型
- **Tool 暴露範圍**：暴露的工具如果有寫入操作（如 add to cart、process payment），安全風險更高
- **認證機制**：WebMCP 是否支援 OAuth/Token-based 認證？還是任何進到頁面的 agent 都能呼叫？

### 規格歸屬

- WebMCP 是否為 W3C/WHATWG 標準化提案，或目前僅為 Google 生態系內的實驗性 API？本影片未明確說明規格歸屬
- 與現有瀏覽器 extension-based agent（如 browser-use、computer-use）的關係與取捨未討論

## 來源

- [[wiki/sources/2026-08-24-webmcp-youtube|Make your website agent ready with WebMCP — YouTube]]

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — MCP 協議本體
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發
- [[wiki/concepts/mcp-clients|MCP Clients]] — 客戶端開發
- [[wiki/concepts/mcp-protocol-versioning|MCP Protocol Versioning]] — 版本控制
