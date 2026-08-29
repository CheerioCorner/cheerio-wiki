---
type: topic
title: Meta Systems
topic: meta-systems
created: 2026-08-02
updated: 2026-08-05
---

# Meta Systems

> 與 Meta Systems 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/lsp|LSP (Language Server Protocol)]] — 微軟提出的語言伺服器協定，將程式語言語意分析從編輯器中解耦，實現 $M \times N \rightarrow M + N$ 的架構革新。 🛠️
- [[wiki/entities/omnigent|Omnigent]] — Databricks 開源的 meta-harness，位於現有 AI agents 之上，提供組合（Combine）、控制（Control）、協作（Share）三大能力。
- [[wiki/entities/tree-sitter|Tree-sitter]] — 高效能 Parser Generator，支援毫秒級增量解析，是現代 Code Intelligence 的核心基礎設施。 🛠️

## Concepts

- [[wiki/concepts/code-graph|Code Graph]] — 跨檔案、跨模組的代碼語意圖譜，結合 AST、控制流圖、資料流圖與全域符號表，是現代 Code Intelligence 與 AI RAG 的核心基礎。 🛠️
- [[wiki/concepts/harness|Harness — LLM 的驅動層]] — **成長階段：** 🌱 種子期 🛠️
- [[wiki/concepts/meta-harness|Meta-Harness]] — Meta-Harness 是「對 harness 本身的再抽象/最佳化層」——有兩個獨立但共享核心隱喻的主流定義。

## Sources

- [[wiki/sources/2026-07-13-omnigent-meta-harness-databricks|Omnigent: Databricks 開源 Meta-Harness]] — Databricks 官方部落格文章，宣布開源 Omnigent — 一個位於現有 AI agents 之上的 meta-harness 層。
- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]] — Gemini Deep Research 完成的 LSP（Language Server Protocol）與 Code Graph 技術研究報告，涵蓋架構、實作案例、AI 整合應用。 🛠️
- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF + LSP + Code Graph 在 AI Coding Agent 生態系的整合應用]] — Gemini Deep Research 完成的三位一體程式碼智慧架構研究，涵蓋 OKF、LSP、Code Graph 在主流 AI Coding Agent 的整合應用。 🛠️

## Others

- [[wiki/visualizations/harness-seed-map|Harness 視覺地圖 — 種子內部結構]] — 展示 Harness 種子內部的概念結構：核心定義、內部職責、三層分類與具體實例。 🛠️

## 相關 Topics
