---
title: "CodeGraph — 代碼知識圖譜 MCP Server（colbymchenry/codegraph）"
type: entity
created: 2026-08-25
updated: 2026-08-25
sources: 2
tags: [knowledge-graph, code-analysis, tree-sitter, mcp-server, codebase-intelligence]
collection: entities
topics: [ai-development-tools, coding-agent]
canonical: entities/codegraph
---

> colbymchenry/codegraph——先驅級代碼知識圖譜 MCP server，TypeScript 實作（binary 發布），tree-sitter 解析 20+ 語言、SQLite+FTS5 存儲、框架路由識別、file watcher 即時同步，68k stars，2026-01 建立。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| GitHub Stars | 68,000+ |
| Forks | 4,300+ |
| License | MIT |
| 建立於 | 2026-01（三者中最早） |
| 實作語言 | TypeScript（CLI/MCP 以自包含 binary 發布，嵌入式 Library API 需 Node 22.5+） |
| 安裝 | `npm install -g @colbymchenry/codegraph` |

## 核心功能

### 1. Tree-Sitter 知識圖譜
- **20+ 語言**：含 COBOL 等罕見語言
- **框架路由識別**：動態分發、跨語言橋接（React Native ↔ iOS 原生等）
- **工程化解析規則**：強調框架適配、路由識別，每新增框架需維護規則

### 2. MCP 介面
- **一個大工具 `codegraph_explore` 搞定**哲學——另有 7 個隱藏工具
- 以「少介面、多功能」為設計哲學

### 3. SQLite + FTS5 存儲
- 專案內 `.codegraph/` 目錄
- 本地存儲，支援全文搜尋

### 4. Background File Watcher
- 即時偵測檔案變更並更新圖譜節點
- 每位開發者各跑一遍索引

## 與 codebase-memory-mcp 的比較

| 面向 | CodeGraph | codebase-memory-mcp |
|------|-----------|---------------------|
| **核心技術** | tree-sitter + 框架路由規則 | tree-sitter + Hybrid LSP 語意解析 |
| **實作語言** | TypeScript（binary 發布） | 純 C/C++ |
| **語言覆蓋** | 20+（含 COBOL） | 158（含 CUDA, Erlang） |
| **MCP 介面** | 一個大工具 + 7 個隱藏 | 15 個明確工具 |
| **存儲** | 專案內 `.codegraph/` | 共用 `~/.cache/` + team artifact |
| **即時同步** | ✅ file watcher | ✅ background watcher |
| **圖譜共享** | ❌ 每人各跑一遍 | ✅ graph.db.zst 可提交 Git |
| **視覺化** | ❌ 需外掛 | ✅ 內建 3D UI |
| **遙測** | ⚠️ **預設開啟匿名遙測**（可關） | ✅ 零遙測 |
| **部署形態** | npm install（需 Node.js 環境） | native binary（零依賴） |
| **Stars** | 68k（先驅，更成熟） | 40.5k（新秀，性能敘事） |
| **定位** | Agent 工作流 + 框架適配 | 性能 + 低 Token + 團隊共享 |

## 安全性備註

### ⚠️ 預設開啟匿名遙測（重要差異）
CodeGraph **預設開啟匿名遙測**，與 codebase-memory-mcp 的「零遙測」是關鍵差異。可用以下指令關閉：
```bash
codegraph telemetry off
```
**建議**：安裝後第一件事就是執行 `codegraph telemetry off`，避免不必要的資料外傳。這在企業環境中尤其重要（長榮 IT 應該關閉）。

### 其他
- MIT License，原始碼可審查
- npm 安裝依賴 npm registry 供應鏈安全

## 我們的判斷

- **跟 codebase-memory-mcp 是「不同賽道」**，不是「性能好就用誰」的競爭關係
- CodeGraph 在**框架路由**（React Native 跨語言橋接等）和**開發者體驗**（file watcher 即時性）上有優勢
- codebase-memory-mcp 在**性能**（C/C++ 靜態 binary）、**Token 效率**（Hybrid LSP 語意解析）、**團隊共享**（graph.db.zst）上有優勢
- **安裝優先序不變**：OCR → codebase-memory-mcp → Graphify 建議跳過；CodeGraph 可作為「如果需要框架路由能力」的後續選項
- **安全性**：CodeGraph 預設遙測是扣分項，安裝前需評估

## 來源
- [[wiki/sources/2026-08-25-codebase-memory-mcp-vs-codegraph|深入拆解 Codebase-Memory-MCP vs CodeGraph]]（YouTube 影片 ingested）

## 相關頁面
- [[wiki/entities/codebase-memory-mcp|codebase-memory-mcp]] — 同品類工具，性能敘事路線
- [[wiki/entities/graphify|Graphify]] — 同品類工具
- [[wiki/entities/open-code-review|OpenCodeReview]] — Code review CLI（不同品類）
