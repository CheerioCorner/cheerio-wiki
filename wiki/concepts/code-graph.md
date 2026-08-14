---
title: Code Graph
type: concept
created: 2026-08-04
updated: 2026-08-14
sources: 1
tags: [code-graph, code-intelligence, semantic-analysis, rag]
topics: [agent-infrastructure, meta-systems]
canonical: concepts/code-graph
---

# Code Graph

> 跨檔案、跨模組的代碼語意圖譜，結合 AST、控制流圖、資料流圖與全域符號表，是現代 Code Intelligence 與 AI RAG 的核心基礎。

## 核心概念

**問題**：單檔 AST 無法滿足深度代碼理解需求

**解決方案**：建立跨檔案的知識網路，支持語意搜尋與 AI 檢索

## 架構層級

```
Concrete Syntax Tree (CST)
    ↓
Abstract Syntax Tree (AST)
    ↓
Scoped Symbol Table
    ↓
Definition-Use Chain Graph
    ↓
Function Call Graph
    ↓
Cross-File Module Dependency Graph
```

## 核心組件

### 1. 符號表 (Symbol Table)
- **作用域樹 (Scope Tree)**：區塊、函式、類別、模組層級
- **符號解析**：將 Reference 連結至 Definition
- **邊類型**：DEFINED_IN / REFERENCES / CALLS / INHERITS

### 2. Def-Use Chain（定義-使用鏈）
- 追蹤變數從定義到所有使用點
- 支援重構安全性驗證

### 3. Call Graph（呼叫圖）
- 函式間的呼叫關係
- 上游 Caller / 下游 Callee 遞迴查詢

### 4. Module Dependency Graph
- 跨檔案模組依賴
- 影響範圍分析

## 建構流程

1. **Tree-sitter 增量解析** — 生成 CST/AST
2. **AST 節點標註** — Tree-sitter Query 識別 Definition/Reference
3. **作用域鏈分析** — 建構 Scope Tree
4. **符號解析** — 連結 Reference 至 Definition
5. **關係邊構建** — 建立 Def-Use、Call Graph、Dependency

## AI 整合應用

### Graph-RAG
- 結合 Call Graph 自動抓取上下游 2 層連動函數
- Context Window 利用率提高 60%+
- 精準上下文，降低 Token 成本

### LSP Self-Correction Loop
```
AI 修改程式碼 → LSP Diagnostic → 語法/型別錯誤 → AI 自動修復
```

### SCIP / LSIF 靜態索引
- **LSIF** — 微軟提出的持久化 LSP 索引格式
- **SCIP** — Sourcegraph 的下一代格式（Protobuf，效能 10x）
- 支援跨 Repo 導航與全域搜尋

## 效益評估

| 維度 | 傳統 RAG | Code Graph + RAG |
|---|---|---|
| 準確度 | 文字模糊比對 | 課精準語意關聯 |
| Context 利用率 | 低（大量無關程式碼） | 高（精準 2 層擴展） |
| Token 成本 | 高 | 降低 60%+ |
| 適用場景 | 通用文件 | 程式碼專案 |

## 相關標準

- **LSP** — 語言伺服器協定（即時互動）
- **SCIP** — 靜態持久化索引（跨 Repo）
- **LSIF** — 微軟旧版索引格式

## 來源

- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]]
- [[raw/.trash/2026-08-04-scip-codescip SCIP Code Intelligence Protocol|SCIP Specification]]

## 相關頁面

- [[wiki/entities/lsp|LSP]] — 即時語意互動協定
- [[wiki/entities/tree-sitter|Tree-sitter]] — 增量解析引擎
