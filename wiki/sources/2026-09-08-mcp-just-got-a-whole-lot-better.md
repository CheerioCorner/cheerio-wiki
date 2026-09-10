---
title: "MCP Just Got a Whole Lot Better：漸進式工具發現與現代 MCP Server 架構演進"
type: source
created: 2026-09-10
updated: 2026-09-10
sources: 1
tags: [mcp, tool-calling, progressive-tool-discovery, code-mode, sdk-design, neon]
topics: [mcp-ecosystem, agent-architecture, agent-infrastructure, ai-development-tools]
provenance_raw:
  - kind: raw
    path: raw/youtube/mcp-server-design-evolution.md
---

# MCP Just Got a Whole Lot Better

> 剖析 MCP 職責劃分的架構轉向：Client 端接管漸進式工具發現（Progressive Tool Discovery）與程式化工具調用（Code Mode），Server 端則轉向以 SDK 為基礎封裝人體工學複合工作流（Ergonomic Methods）。

## 影片資訊

- **頻道**：Neon Postgres
- **發布日期**：2026-09-08
- **時長**：00:05:56
- **連結**：[YouTube](https://www.youtube.com/watch?v=BqRhBq-_kgE)

## 核心論點

### 一對一映射的問題

早期 MCP server 常見做法是把每個 API endpoint 對應一個 tool `[00:25]`。endpoint 一多，tool 數量爆炸會在 session 開始前就塞爆 context window，且相似 tool 會讓 agent 選錯 `[00:57]`。

### 分層工具調用模式的出現

為解決上述問題，許多 MCP server 改採分層 tool call 模式：用「搜尋 endpoint / 檢視 endpoint 定義 / 執行請求」三個 tool 取代數百個 tool `[01:03]`。這個模式原本實作在 MCP server 端。

### 職責轉移：Client 端接管

這個分層模式現在被許多 MCP client（尤其是熱門 coding agent）改在 client 端實作，正式定名為**「漸進式工具發現（Progressive Tool Discovery）」**，已成為 MCP client 的推薦最佳實踐 `[01:26]-[01:49]`。

### 程式化工具調用（Code Mode）

Codex、Claude Code 等 agent 也實作了**「programmatic tool calling」**，又稱 code mode：agent 撰寫腳本在沙箱環境中串連多個 tool call，只把最終結果回傳給模型 `[01:49]`。

> 「between progressive discovery handling tool call selection, and then code mode handling tool composition, the clients are now starting to take more ownership of the problems that servers originally were trying to solve」`[02:19]`

### Server 端的轉向：Ergonomic Workflow Tools

即使 client 能力增強，也不代表應該退回一對一 endpoint 對應 tool 的做法 `[02:39]`。常見工作流程需要串接多個 API call，若每次都靠 agent 現場拼湊會浪費大量 token `[03:00]`。

> 「this is the same reason that SDKs exist. They give you the raw endpoints, but also bundle common workflows into single operations」`[03:00]`

### Neon 實例：三層架構

以 Neon 為例，建立一個 branch 實際上是三個獨立 API 呼叫（建立 branch、附加運算資源、取得連線字串）`[03:11]-[03:32]`。Neon SDK 把它們包成單一方法 `create_with_compute` `[03:40]`。

Neon 的架構 `[04:10]-[04:44]`：
1. **OpenAPI 自動產生的 SDK** — 涵蓋所有 endpoint
2. **手寫 ergonomic 方法包裝層** — 如 `create_with_compute`
3. **獨立 neon tools 套件** — 把原始 endpoint 與 ergonomic 方法都轉成 tool call

neon tools 供 Neon MCP server 匯入對外暴露 tool，同時也用它建構 Mastra、Eve 等 agent framework 的 adapter `[04:55]-[05:11]`。tool 依類別分組，方便限縮 agent 可存取的 tool 範圍 `[05:11]`。

### 核心結論

> 「the big idea here is that the server is controlling what tools are available, but then the client handles discovering and executing those tools in whatever way it sees fit」`[05:27]`

## 提及的公司/專案/文件

| 名稱 | 類型 | 說明 |
|------|------|------|
| Neon Postgres | 公司/產品 | 影片來源，展示 MCP server 設計模式 |
| Codex（OpenAI） | 專案 | 實作 progressive tool discovery 與 code mode 的 coding agent |
| Claude Code（Anthropic） | 專案 | 同上 |
| Mastra | 框架 | Neon tools 的 adapter 目標之一 |
| Eve（Vercel） | 框架 | Neon tools 的 adapter 目標之一 |

## 新頁面建議

| 類型 | 頁面 | 說明 |
|------|------|------|
| concept | [[wiki/concepts/progressive-tool-discovery\|Progressive Tool Discovery]] | 影片核心概念，MCP client 端的三階段工具發現模式 |
| entity | [[wiki/entities/neon-mcp-server\|Neon MCP Server]] | Neon 的 MCP server 參考架構 |

## 既有頁面更新

| 頁面 | 變更 |
|------|------|
| [[wiki/concepts/mcp-servers\|MCP Servers]] | 新增 tool 暴露模式演化段落 |
| [[wiki/concepts/code-execution-as-tool-calling\|Code Execution as Tool Calling]] | 補充 MCP 生態中的 code mode 落地案例 |
| [[wiki/concepts/mcp-clients\|MCP Clients]] | 提及 progressive tool discovery 作為推薦模式 |
| [[wiki/topics/mcp-ecosystem\|MCP Ecosystem]] | 加入新頁面導航連結 |

## 來源

- [[raw/youtube/mcp-server-design-evolution|MCP Just Got a Whole Lot Better — YouTube Transcript]]

## 相關頁面

- [[wiki/concepts/mcp-servers|MCP Servers]]
- [[wiki/concepts/mcp-clients|MCP Clients]]
- [[wiki/concepts/mcp-agent-skills-integration|MCP + Agent Skills 整合]]
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]]
