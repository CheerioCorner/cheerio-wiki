---
title: "Make your website agent ready with WebMCP"
type: source
created: 2026-08-24
updated: 2026-08-24
sources: 1
tags: [mcp, webmcp, web, agent, ai-agent, browser]
collection: sources
topics: [mcp-ecosystem, agent-infrastructure]
canonical: sources/2026-08-24-webmcp-youtube
provenance:
  - kind: raw
    path: raw/youtube/make-your-website-agent-ready-with-webmcp.md
    url: https://www.youtube.com/watch?v=FARxSG_EY98
provenance_raw: "raw/youtube/make-your-website-agent-ready-with-webmcp.md"
provenance_url: https://www.youtube.com/watch?v=FARxSG_EY98
---

> 來源：[YouTube — Make your website agent ready with WebMCP](https://www.youtube.com/watch?v=FARxSG_EY98)
> 原始字幕：[[raw/youtube/make-your-website-agent-ready-with-webmcp|raw transcript]]
> 講者：Ugo（Google Developer Expert in AI）

## 一句話

WebMCP 讓網站在頁面內直接宣告 agent-ready 工具，透過 browser API 暴露給 AI agent，不需額外的 MCP server。

## 重點摘要

### WebMCP 是什麼

- 在網頁中直接聲明 agent-ready interfaces，工具隨頁面載入，透過簡單的 browser API 暴露給 AI agents `[00:36]`
- 行為像標準 MCP，但不跑在獨立伺服器上，而是住在頁面本身 `[00:46]`
- Agent 進到頁面後，不需要 scraping HTML 或猜測，直接看到一組預定義的工具 `[01:03]`

### Contextual Tools

- 工具跟頁面綁定，不同頁面暴露不同工具 `[01:15]`
- 首頁：search products / get categories / filter
- 產品頁：add to cart / get similar products
- Agent 呼叫工具就像呼叫任何 MCP tool，但工具是 contextual 的——跟它正在瀏覽的頁面對應 `[01:25]`

### Token 效率

- 比 screenshot-based agents 省非常多 tokens `[02:33]`
- 實際 demo 中，Gemini 用 WebMCP 工具完成一連串操作，只用了 screenshot-based agent 的一小部分 tokens `[02:41]`

### Imperative API

- 註冊工具 → 描述功能 → 宣告 inputs/outputs → 標記 required fields `[02:50]`
- Agent 拿到的是一個 precise typed contract `[03:00]`

### Local Agent Workflow

- WebMCP 不只服務遠端 agent，也支援本地 CLI agents 與 web UI 互動 `[03:15]`
- Demo 場景：開發者用 CLI agent 開發 data transformation → 透過 WebMCP 讓 agent 在瀏覽器上觸發 staging execution → 開發者即時看到 logs / lineage / sample data `[03:33]`
- 程式碼由 Gemini 在本地機器上開發，開發完成後自動觸發 staging execution 並導航到 pipeline page `[04:10]`

### Builder Control

- 網站建造者掌控 agent 如何體驗他們的產品 `[05:06]`
- 不是被動等待 agent 來 scraping，而是主動定義 agent 可以做什麼

### 開放問題（Cheer 提出）

- **資安風險**：在網站上加了 Web MCP 就等於多了一個入口，駭客有可能利用這個機會。WebMCP 的安全模型（如何防止惡意 agent 濫用暴露的工具？如何區分合法 agent 與攻擊者？）本影片未討論
- **規格歸屬**：WebMCP 是否為 W3C/WHATWG 標準化提案，或目前僅為 Google 生態系內的實驗性 API？本影片未明確說明
- **與 browser-use / computer-use 的關係**：WebMCP 與現有瀏覽器 extension-based agent 的關係與取捨未討論

## 來源

- [[raw/youtube/make-your-website-agent-ready-with-webmcp|Raw transcript — YouTube FARxSG_EY98]]

## 相關頁面

- [[wiki/concepts/webmcp|WebMCP]] — 概念頁
- [[wiki/entities/mcp-model-context-protocol|MCP]] — MCP 協議本體
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發
- [[wiki/concepts/mcp-clients|MCP Clients]] — 客戶端開發
