---
title: LSP (Language Server Protocol)
type: entity
created: 2026-08-04
updated: 2026-08-14
sources: 1
tags: [lsp, protocol, code-intelligence, microsoft]
topics: [agent-infrastructure, meta-systems]
canonical: entities/lsp
---

# LSP (Language Server Protocol)

> 微軟提出的語言伺服器協定，將程式語言語意分析從編輯器中解耦，實現 $M \times N \rightarrow M + N$ 的架構革新。

## 核心概念

**問題**：傳統 IDE 整合需要為 M 個編輯器 × N 種語言撰寫 M×N 個套件

**解決方案**：標準化 JSON-RPC 2.0 協定，分離 Client（編輯器）與 Server（語言服務）

## 架構

```
Editor Client ←→ JSON-RPC 2.0 ←→ Language Server
     │                                    │
     └── UI 呈現、文件同步              └── 型別推導、語意分析
```

## 通訊協定

### 訊息類型
1. **Request** — Client → Server（需回應）
2. **Response** — Server → Client（回應特定請求）
3. **Notification** — 單向訊息（無需回應）

### 生命週期
1. `initialize` — 傳遞 processId、rootUri、capabilities
2. `initialized` — 確認初始化完成
3. `shutdown` — 準備關閉
4. `exit` — 結束進程

## 核心功能

| 功能 | Method | 說明 |
|---|---|---|
| 自動補全 | `textDocument/completion` | 觸發字元（`.`、`->`）時提供選項 |
| 跳轉定義 | `textDocument/definition` | 查詢宣告位置 |
| 參考查找 | `textDocument/references` | 全專案引用點 |
| 診斷推送 | `textDocument/publishDiagnostics` | 語法錯誤、靜態警告 |
| Code Lens | `textDocument/codeLens` | 行上方嵌入元資料 |
| Hover | `textDocument/hover` | 滑鼠懸停顯示型別資訊 |

## 主要實作

- **typescript-language-server** — TypeScript/JavaScript（Node.js）
- **gopls** — Go（Go native）
- **rust-analyzer** — Rust（Rust native）
- **clangd** — C/C++（LLVM/Clang）

## 版本演進

- **v3.17** — Meta-model、Inlay Hints、Type Hierarchy、Notebook 支援
- **v3.18** — 虛擬文件存取、Inline Completion（AI 整合點）

## AI 整合應用

1. **LSP Tool Calling** — AI Agent 將 LSP 作為工具鏈
2. **Self-Correction Loop** — 修改後自動呼叫 Diagnostic 修復
3. **Graph-RAG** — 結合 Code Graph 精準檢索上下文

## 來源

- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]]
- [[raw/.trash/2026-08-04-microsoftlanguage-server|Microsoft LSP Specification]]

## 相關頁面

- [[wiki/entities/tree-sitter|Tree-sitter]] — 搭配的 Parser Generator
- [[wiki/concepts/code-graph|Code Graph]] — LSP 建構的語意圖譜
