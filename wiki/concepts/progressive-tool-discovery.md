---
title: "Progressive Tool Discovery — 漸進式工具發現"
type: concept
created: 2026-09-10
updated: 2026-09-10
sources: 1
tags: [mcp, tool-calling, client-side, context-window]
topics: [mcp-ecosystem, agent-architecture]
canonical: concepts/progressive-tool-discovery
---

> MCP Client 端實作的漸進式工具發現模式——透過搜尋（Search）、檢視（Inspect）、執行（Execute）三階段動態載入工具定義，取代把所有 tool 一次性塞進 context window。

## 定義

Progressive Tool Discovery 是一種由 MCP Client 主導的工具發現模式。傳統做法是 server 把所有 tool 一次性暴露，client 在 session 開始時全部載入 context window。Progressive Tool Discovery 則讓 client 提供三階段介面 `[01:03]-[01:26]`：

1. **Search**（搜尋）：搜尋所有可用的 endpoints/tools
2. **Inspect**（檢視）：查看特定 endpoint 的定義與參數
3. **Execute**（執行）：呼叫具體的請求

## 為什麼需要

- **Context Window 節省**：如果有數百個 tool，一次性全部載入會在 session 開始前就塞爆 context window `[00:57]`
- **減少混淆**：相似的 tool 會讓 agent 選錯工具，三階段模式讓 client 先搜尋再精確選取 `[00:57]`
- **責任轉移**：原本這個分層模式實作在 MCP server 端，現在改為 client 端實作，成為推薦最佳實踐 `[01:26]-[01:49]`

## 與 Code Mode 的關係

Progressive Tool Discovery 處理**工具選擇**（tool call selection），Code Mode（程式化工具調用）處理**工具組合**（tool composition）`[02:19]`。兩者共同構成現代 MCP client 的核心能力。

| 模式 | 解決的問題 | 負責方 |
|------|-----------|--------|
| Progressive Tool Discovery | 工具選擇（選哪個 tool） | MCP Client |
| Code Mode | 工具組合（怎麼串連多個 tool） | MCP Client |
| Ergonomic Workflow Tools | 常見工作流程封裝 | MCP Server |

## 實作案例

- **Codex**（OpenAI）：在 client 端實作 progressive tool discovery `[01:49]`
- **Claude Code**（Anthropic）：同上 `[01:49]`

## Server 端的對應設計

當 client 端處理了工具發現與組合後，server 端不需要再做分層 tool call。server 端的職責轉為：定義並掌控可用工具的邊界與抽象，提供含人體工學捷徑的高品質 tool 集合 `[05:27]`。例如 Neon 的 `create_with_compute` 方法。

## 來源

- [[wiki/sources/2026-09-08-mcp-just-got-a-whole-lot-better|MCP Just Got a Whole Lot Better]]

## 相關頁面

- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]]
- [[wiki/concepts/mcp-servers|MCP Servers]]
- [[wiki/concepts/mcp-clients|MCP Clients]]
