---
title: "codebase-memory-mcp — 高效能代碼知識圖譜 MCP Server"
type: entity
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [knowledge-graph, code-analysis, tree-sitter, mcp-server, codebase-intelligence]
collection: entities
topics: [ai-development-tools, coding-agent]
canonical: entities/codebase-memory-mcp
---

> DeusData 開源的高性能代碼知識圖譜 MCP server，基於 tree-sitter AST 分析將代碼庫索引為持久化知識圖譜，158 種語言、毫秒級查詢、99% Token 節省，零 API key。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| GitHub Stars | 40,500+ |
| Forks | 3,300+ |
| License | MIT |
| 語言 | C（native executable） |
| 建立於 | 2026-02 |
| 維護方 | DeusData |
| 官方文件 | [SECURITY.md](https://github.com/DeusData/codebase-memory-mcp/blob/main/SECURITY.md)、[CONFIGURATION.md](https://github.com/DeusData/codebase-memory-mcp/blob/main/docs/CONFIGURATION.md) |
| 學術論文 | [arXiv:2603.27277](https://arxiv.org/abs/2603.27277)（31 個真實倉庫驗證） |

## 核心功能

### 1. Tree-Sitter AST 知識圖譜
- **158 種語言**：vendored tree-sitter grammars 編譯進 binary，零依賴
- **Hybrid LSP 語意解析**：對 Python/TypeScript/JavaScript/PHP/C#/Go/C/C++/Java/Kotlin/Rust/Perl 12 種語言提供輕量 C 實作的型別解析，結構上兼容 tsserver/pyright/gopls/Roslyn/rust-analyzer 等主流 LSP
- **Infrastructure-as-code 索引**：Dockerfile、K8s manifest、Kustomize overlays 作為圖譜節點
- **節點類型**：Project、Package、Folder、File、Module、Class、Function、Method、Interface、Enum、Type、Route、Resource
- **邊類型**：CALLS、HTTP_CALLS、ASYNC_CALLS、IMPORTS、IMPLEMENTS、DATA_FLOWS、SIMILAR_TO、SEMANTICALLY_RELATED 等 20+ 種

### 2. 15 個 MCP Tools
| 類別 | 工具 |
|------|------|
| 索引 | `index_repository`、`list_projects`、`delete_project`、`index_status` |
| 查詢 | `search_graph`、`trace_path`、`detect_changes`、`query_graph`（Cypher-like） |
| 分析 | `get_architecture`、`get_code_snippet`、`search_code`、`manage_adr`、`get_graph_schema` |
| 進階 | `ingest_traces`（runtime trace 驗證 HTTP\_CALLS） |

### 3. SQLite 持久化知識圖譜
- **零基礎設施**：SQLite-backed，持久化至 `~/.cache/codebase-memory-mcp/`
- **Auto-sync**：背景 watcher 偵測 git 變更自動 re-index
- **Team-shared artifact**：`.codebase-memory/graph.db.zst` 可 commit 進 repo，team 重複使用（zstd 8-13:1 壓縮比）

### 4. 內建 3D 圖譜視覺化 UI
- `localhost:9749`，直接從 binary serve
- 支援 cross-repo multi-galaxy 3D layout

### 5. 零 API Key / 零 LLM
- 內建 Nomic `nomic-embed-code` embeddings（40K tokens, 768d int8），semantic search 完全本地
- 11-signal combined scoring（TF-IDF、RRI、API/Type signatures、AST profiles、data flow、Halstead-lite、MinHash 等）

### 6. Multi-Agent 支援
- **43 個 client surface**：Claude Code、Codex CLI、Gemini CLI、VS Code、Cursor、Windsurf、Pi、GitHub Copilot CLI 等
- 自動偵測已安裝的 coding agent 並配置 MCP entries + durable context
- 三層 Scout/Verify/Auditor graph profiles

## 效能基準

| 操作 | 時間 | 備註 |
|------|------|------|
| Linux kernel 全索引 | 3 分鐘 | 28M LOC, 75K files → 4.81M nodes, 7.72M edges |
| Django 全索引 | ~6 秒 | 49K nodes, 196K edges |
| Cypher query | <1ms | 關係遍歷 |
| Trace call path（depth=5） | <10ms | BFS traversal |
| Dead code detection | ~150ms | 全圖掃描 + degree filtering |
| **Token 節省** | **99.2%** | 5 次結構查詢：~3,400 tokens vs ~412,000 tokens（逐檔 grep） |

## 安全性備註

- **Windows Defender 誤判**：Microsoft Defender 可能將執行檔標記為 `Trojan:Script/Wacatac.B!ml`——這是**已知 false positive**，官方 SECURITY.md 承認，跟 `gh`、`llama.cpp`、Godot、Microsoft 自己的 Go toolchain 屬同一誤判家族（通常 61/62 引擎回報 clean）
- **checksums.txt**：每個 release 附 SHA-256，可自行驗證
- **零遙測**：100% 本機執行，不會主動發起網路請求、不會檢查更新、不會回報使用資料
- **SLSA Level 3**：加密 build provenance，可用 `gh attestation verify` 驗證

## 與 Graphify 的比較

兩者是**幾乎同品類**的工具——都是 tree-sitter AST 解析 → 知識圖譜。關鍵差異：

| 面向 | codebase-memory-mcp | Graphify |
|------|---------------------|----------|
| **核心技術** | tree-sitter + Hybrid LSP | tree-sitter + Leiden 社群分群 |
| **輸出** | SQLite 持久化圖譜 + MCP tools | graph.json + GRAPH_REPORT.md |
| **LLM 依賴** | 零 LLM（semantic search 用 bundled embeddings） | 選配 LLM 語意標註 |
| **MCP 原生** | ✅ 15 個 MCP tools | ❌ 非 MCP 架構 |
| **Auto-index** | ✅ background watcher | ❌ 手動 |
| **持久化** | ✅ SQLite + team-shared artifact | ❌ JSON 檔案 |
| **視覺化** | ✅ 內建 3D UI | ❌ 需外掛 |
| **Stars** | 40.5k | ~100k |
| **安裝** | native binary / npm / PyPI | 需建置 |

**結論**：codebase-memory-mcp 在 MCP 整合、持久化、auto-sync、視覺化等面向都更成熟。Graphify 的選配 LLM 語意標註是獨特差異，但整體功能大致是前者的子集。**建議安裝 codebase-memory-mcp、跳過 Graphify**。

## 安裝方式

```bash
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.sh | bash

# Windows (PowerShell)
irm https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.ps1 | iex

# npm
npm install -g codebase-memory-mcp@latest

# PyPI
pip install -U codebase-memory-mcp
```

安裝後重啟 coding agent，說「Index this project」即可。

## 我們的判斷

- **安裝優先序**：OCR（W-030，ADO Agent 前置）→ **codebase-memory-mcp**（本項目）→ Graphify（建議跳過）
- **理由**：ADO Agent PoC（W-080）需要先評估 OCR 做程式碼審查；codebase-memory-mcp 在開始寫 C#/.NET 程式碼後，能讓 Claude Code / Pi 瞬間理解 codebase 結構、省 99% token、零 API cost
- **Graphify 不裝**：跟 codebase-memory-mcp 高度重疊，功能是其子集，裝兩個只增加維護成本
- **待辦**：評估安裝（見 W-2026-08-087）

## 來源
- [[raw/web/2026-08-25-DeusDatacodebase-memory-mcp High-performance code intelligence MCP server|GitHub README 原文]]

## 相關頁面
- [[wiki/entities/graphify|Graphify]] — 同品類工具（tree-sitter 知識圖譜），建議跳過
- [[wiki/entities/codegraph|CodeGraph]] — 同品類工具，先驅者，框架路由優勢
- [[wiki/entities/open-code-review|OpenCodeReview]] — Code review CLI，跟本工具零重疊（一個理解 code、一個審查 code）
- [[wiki/topics/ai-development-tools|AI Development Tools]]
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — 概念頁

## 影片補充（2026-08-25 YouTube ingest）

根據 [[wiki/sources/2026-08-25-codebase-memory-mcp-vs-codegraph|深入拆解 Codebase-Memory-MCP vs CodeGraph]] 影片，補充以下技術細節：

- **底層演算法**：LZ4 壓縮 + Aho-Corasick 多模式匹配自動機（工業級方案，單次掃描找到所有匹配）`≈[02:28]`
- **索引記憶體管理**：RAM-first pipeline，索引 Linux kernel 時記憶體佔用控制嚴格，建完圖立即釋放 `≈[02:28]`
- **定位轉變**：從「個人工具」到「團隊資產」——graph.db.zst 設計改變了知識圖譜的共享模型 `≈[05:04]`
- **跟 CodeGraph 的核心差異**：不是競爭關係而是不同賽道——CodeGraph 面向 Agent 工作流（框架路由、file watcher、開發者體驗），codebase-memory-mcp 面向性能敘事（C/C++ 靜態 binary、Hybrid LSP、團隊共享）`≈[06:23]`
