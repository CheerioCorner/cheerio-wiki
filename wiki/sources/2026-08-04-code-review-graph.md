---
title: "code-review-graph：用知識圖譜幫 AI Code Review 省下 8 倍 Token"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-04-別再讓-ai-讀整個專案了-code-review-graph.md
    url: https://repoinside.com/tirth8205/code-review-graph
provenance_raw: "raw/web/2026-08-04-別再讓-ai-讀整個專案了-code-review-graph.md"
provenance_url: https://repoinside.com/tirth8205/code-review-graph
tags: [tree-sitter, code-graph, code-review, mcp, token-optimization]
collection: sources
topics: [agent-infrastructure, ai-development-tools]
canonical: sources/2026-08-04-code-review-graph
---

> 來源：[別再讓 AI 讀整個專案了！code-review-graph 用知識圖譜幫你省下 8 倍 Token](https://repoinside.com/tirth8205/code-review-graph)
> 原始剪藏：[[raw/web/2026-08-04-別再讓-ai-讀整個專案了-code-review-graph|raw clip]]

## 一句話

code-review-graph 用 Tree-sitter 建構程式碼知識圖譜，透過爆炸半徑分析精準定位變更影響範圍，讓 AI Code Review 只讀取必要上下文，平均省下 8.2 倍 Token。

## 重點摘要

### 核心問題

- AI Code Review 工具（Claude Code、Cursor）每次審查都重新讀取整個專案
- 大型專案（數千檔案）導致 Token 浪費、回應慢、成本高
- 修改一個函式後，難以快速知道哪些相關程式碼會被影響

### 解決方案

- **Tree-sitter 語法解析**：將程式碼建構成結構化知識圖譜，儲存於本地 SQLite
- **爆炸半徑分析（Blast Radius Analysis）**：修改某函式時，精確計算哪些函式、類別、測試會被影響
- **增量更新**：無需每次重新建立索引
- **MCP 整合**：透過 Model Context Protocol 與 Claude Code、Cursor、Windsurf、Zed 等無縫整合

### 關鍵數據

- 平均減少 **8.2 倍** Token 用量
- 支援 **19 程式語言**（Python、TypeScript、Go、Rust、Java、Vue、Solidity 等）
- 可解析 Jupyter Notebook

### 解決的痛點

1. Token 浪費嚴重
2. 缺乏精準的變更影響分析
3. 增量更新困難
4. 跨語言專案統一分析不易
5. 程式碼審查缺乏結構化風險評估
6. 新進開發者上手困難

## 相關頁面

- [[wiki/concepts/code-graph|Code Graph]] — AI Code Assistant 的核心基礎設施
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — tree-sitter AST + 圖論算法
- [[wiki/entities/tree-sitter|Tree-sitter]] — 高效能 Parser Generator
- [[wiki/entities/mcp-model-context-protocol|MCP]] — Model Context Protocol
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — AI Agent 技術基礎設施
