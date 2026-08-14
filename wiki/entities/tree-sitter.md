---
title: Tree-sitter
type: entity
created: 2026-08-04
updated: 2026-08-14
sources: 1
tags: [tree-sitter, parser, incremental-parsing, code-analysis]
topics: [agent-infrastructure, meta-systems]
canonical: entities/tree-sitter
---

# Tree-sitter

> 高效能 Parser Generator，支援毫秒級增量解析，是現代 Code Intelligence 的核心基礎設施。

## 核心特性

### 1. GLR 解析演算法
- **Generalized LR**：支援處理語法模糊性（Ambiguity）
- 生成 **Concrete Syntax Tree (CST)** 與 **Abstract Syntax Tree (AST)**
- CST 保留空白與分號，有利代碼格式化與重構

### 2. 增量解析 (Incremental Parsing)
- 編輯時僅提供變動範圍（Edit Range）
- 透過重用舊語法樹節點，**O(log N)** 時間複雜度完成局部重建
- 回應速度 < 1ms，適合即時編輯器整合

### 3. 語言文法支援
- 使用 `.scm` 檔案定義文法規則
- 支援 100+ 語言（Python、TypeScript、Go、Rust 等）
- Parser Generator 架構：文法 → C 程式碼 → 增量解析器

## 從 AST 到 Code Graph

```
CST → AST → Scoped Symbol Table → Def-Use Chain → Call Graph → Module Dependency
```

### 建構路徑
1. **AST 節點標註** — 使用 Tree-sitter Query 識別 Definition/Reference
2. **作用域鏈分析** — 建構 Scope Tree（區塊、函式、類別、模組）
3. **符號解析** — 將 Reference 連結至 Definition
4. **關係邊構建** — DEFINED_IN / REFERENCES / CALLS / INHERITS

## Python Binding 範例

```python
import tree_sitter_python as tspython
from tree_sitter import Language, Parser

# 初始化解析器
PY_LANGUAGE = Language(tspython.language())
parser = Parser(PY_LANGUAGE)

# 解析程式碼
code = bytes("def hello(): pass", "utf8")
tree = parser.parse(code)

# 使用 Query 擷取函式定義
query = PY_LANGUAGE.query("""
(function_definition
  name: (identifier) @func_name)
""")
captures = query.captures(tree.root_node)
```

## 主要使用場景

1. **編輯器語法高亮** — Neovim、Helix、Zed
2. **LSP 整合** — 搭配 Language Server 提供即時解析
3. **Code Analysis** — 靜態分析、重構工具
4. **AI Code Assistant** — 建構 Code Graph 供 AI 檢索

## 相關工具

- **tree-sitter-cli** — CLI 工具與 Playground
- **tree-sitter-languages** — 預編譯語言包
- **web-tree-sitter** — WASM 版本，支援瀏覽器

## 來源

- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]]
- [[raw/.trash/2026-08-04-introduction-tree-sitter|Tree-sitter Documentation]]

## 相關頁面

- [[wiki/entities/lsp|LSP]] — 搭配的語言伺服器協定
- [[wiki/concepts/code-graph|Code Graph]] — Tree-sitter 建構的語意圖譜
