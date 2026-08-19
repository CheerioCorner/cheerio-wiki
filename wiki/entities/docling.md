---
title: "Docling — IBM 文件解析與結構化框架"
type: entity
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [document-understanding, ibm, parsing, ocr, rag]
collection: entities
topics: [agent-memory-context]
canonical: entities/docling
---

> Docling 是 IBM 開源的文件解析框架，把 PDF 等非結構化文件轉換成結構化的 tree document，支援 Chunkless RAG。

## 基本資訊

| 欄位 | 值 |
|------|-----|
| 開發者 | IBM Research |
| 類型 | 開源文件解析框架 |
| 輸入 | PDF（及其他文件格式） |
| 輸出 | 結構化 Docling Document（tree structure） |
| 核心能力 | 還原 sections、headings、reading order、tables、images |
| GitHub | https://github.com/DS4SD/docling |

## 核心功能

1. **PDF → 結構化 Document**
   - 還原 PDF 中被埋藏的層級結構
   - 保留 reading order
   - 表格仍然是表格（不是被打散的文字）

2. **Docling Document**
   - 標準化的中間表示格式
   - Tree structure：title → sections → subsections → paragraphs/tables/images
   - 可被程式化操作（寫、編輯、提取欄位、豐富化）

3. **Docling Agent**
   - 基於結構化 document 的 AI agent
   - 執行 Chunkless RAG 的導航邏輯
   - 推理 → 找到 section → 讀取 → 必要時跨 section 導航

## 核心概念

### 為什麼 PDF 是 hard problem

- PDF 格式的本質是「把字元和圖形放到頁面上的指令」
- 沒有內建的語義結構（heading、section、paragraph 的關係）
- Docling 的工程量主要在：從這些 placement instructions 重建出語義結構

### 與 Chunkless RAG 的關係

- Docling 提供「tree」，Chunkless RAG 在 tree 上導航
- 沒有 Docling → 沒有乾淨的 tree → Chunkless RAG 無法運作
- Docling 是 Chunkless RAG 的基礎設施

## 來源

- [[wiki/sources/2026-08-10-chunkless-rag-docling|What Is Chunkless RAG? — IBM Technology]]

## 相關頁面

- [[wiki/concepts/chunkless-rag|Chunkless RAG]] — Docling 之上的 RAG 方法論
- [[wiki/entities/memgraph-rag|MemGraph-RAG]] — 另一種 RAG 進化方向
