---
title: "rust-analyzer — Rust 語言的 LSP 實作"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-04-Introduction - rust-analyzer.md
    url: https://rust-analyzer.github.io/book/
provenance_raw: "raw/web/2026-08-04-Introduction - rust-analyzer.md"
provenance_url: https://rust-analyzer.github.io/book/
tags: [lsp, rust, language-server, code-analysis, ide]
collection: sources
topics: [agent-infrastructure]
canonical: sources/2026-08-04-rust-analyzer
---

> 來源：[rust-analyzer Introduction](https://rust-analyzer.github.io/book/)
> 原始剪藏：[[raw/web/2026-08-04-Introduction - rust-analyzer.md|raw clip]]

## 一句話

rust-analyzer 是 Rust 語言的 Language Server Protocol（LSP）實作，提供 go-to-definition、find-references、refactoring 等 IDE 功能，是高效能 LSP 的代表。

## 重點摘要

### 基本資訊

| 項目 | 內容 |
|------|------|
| 名稱 | rust-analyzer |
| 語言 | Rust（自舉） |
| 協定 | Language Server Protocol (LSP) |
| 編輯器 | VS Code, Vim, Emacs, Zed 等所有支援 LSP 的編輯器 |
| 效能 | 極高（Rust native） |

### 核心功能

| 功能 | 說明 |
|------|------|
| **Go-to-definition** | 跳轉到定義 |
| **Find all references** | 找出所有引用 |
| **Refactorings** | 重構建議 |
| **Code completion** | 程式碼補全 |
| **Formatting** | 整合 rustfmt |
| **Diagnostics** | 整合 rustc + clippy |

### 架構

```
rust-analyzer
    ├── ra_ap_syntax  ← Rust 語法解析
    ├── ra_db         ← 資料庫抽象
    └── ra_hir        ← 高階中間表示
```

- 內部是一組分析 Rust 程式碼的函式庫
- 增量分析：只重新分析變更的部分

### 在 LSP 生態中的定位

| LSP Server | 語言 | 效能 | 特色 |
|-----------|------|------|------|
| **rust-analyzer** | Rust | 極高 | Rust native，增量分析 |
| gopls | Go | 極高 | Go native |
| clangd | C/C++ | 極高 | LLVM/Clang |
| typescript-language-server | TS/JS | 中等 | 整合 tsc |

### 對 AI Agent 的意義

- LSP 是 **Code Graph** 的基礎設施
- Agent 可透過 LSP 取得精確的程式碼結構資訊
- 比單純的 text search 更準確

## 相關頁面

- [[wiki/entities/lsp|LSP]] — Language Server Protocol
- [[wiki/concepts/code-graph|Code Graph]] — AI Code Assistant 核心基礎設施
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — tree-sitter + 圖論
- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]] — LSP 深度研究 🛠️
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — AI Agent 技術基礎設施
