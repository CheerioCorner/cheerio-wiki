---
title: "What Is Chunkless RAG? How Docling & AI Agents Navigate Documents"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [rag, document-understanding, ai-agent, ibm, docling]
collection: sources
topics: [agent-memory-context]
canonical: sources/2026-08-10-chunkless-rag-docling
provenance:
  - kind: raw
    path: raw/youtube/chunkless-rag-docling.md
    url: https://www.youtube.com/watch?v=vRZNJWw78BQ
provenance_raw: "raw/youtube/chunkless-rag-docling.md"
provenance_url: https://www.youtube.com/watch?v=vRZNJWw78BQ
---

> 來源：[YouTube — What Is Chunkless RAG? How Docling & AI Agents Navigate Documents](https://www.youtube.com/watch?v=vRZNJWw78BQ)
> 原始字幕：[[raw/youtube/chunkless-rag-docling|raw transcript]]

## 一句話

Chunkless RAG 保留文件的樹狀結構，讓 AI Agent 透過推理導航文件，而非把文件切成 chunks 做相似度搜尋。

## 重點摘要

### 傳統 RAG 的問題

- 把文件切成 chunks（每 500 字或按段落），轉成向量做相似度搜尋 `[00:29]`
- **致命缺陷**：切 chunk 的瞬間就丟棄了文件的結構資訊 `[00:59]`
  - 標題與它介紹的段落分離 `[01:02]`
  - 表格與解釋它的句子分離 `[01:05]`
  - 跨多個 section 的答案，相似度搜尋不知道它們屬於同一個脈絡 `[01:08]`
- 文件本來就是結構化的（tree structure），chunking 把 tree flatten 掉 `[01:29]`

### Chunkless RAG 的做法

- **不 flatten 文件，保留 tree** `[02:00]`
- 讓模型「推理」到正確的位置，而非「相似度匹配」`[02:02]`
- 模擬人類閱讀方式：看目錄 → 找到相關 section → 只讀那個 section → 必要時跟隨 footnote `[02:14]`
- Agent 開始時拿到 tree outline + 每個 section 的短摘要 `[02:35]`
- 推理哪個 section 最可能有答案，打開它讀取 `[02:43]`
- 不夠就繼續走下一個 section，幾步之內完成 `[02:51]`

### Chunkless RAG 的優勢

1. **上下文免費附贈**：Agent 讀一段落時，知道它在哪個 section、哪個 subsection（因為它走了整棵 tree）`[03:02]`
2. **跨文件問題**：Agent 可以hold住位置，去讀另一個 branch 再回來 `[03:20]`
3. **答案更乾淨**：模型拿到的是一個有 heading 的完整 section，而不是失去位置的碎片 `[03:49]`

### Docling 的角色

- 大多數文件是 PDF，而 PDF 只是「把字元和圖形放到頁面上的指令」，沒有乾淨結構 `[04:13]`
- **Docling** 把 PDF 解析成結構化的 Docling document（tree structure）`[04:25]`
- 還原 sections、headings、reading order、tables `[04:42]`
- Docling agent 可以在這個結構上直接操作（寫、編輯、提取欄位、豐富化 sections）`[05:05]`

### 何時用哪種方法

| 方法 | 適合場景 |
|------|---------|
| 相似度搜尋（chunk-based） | 百萬文件、模糊查詢（"find me anything about X"）`[06:08]` |
| 結構化導航（Chunkless RAG） | 長且有組織的文件、precision 重要、需要 parts 之間的關聯 `[06:15]` |
| **兩者混用** | 相似度搜尋找對文件 → 結構化導航在文件內部導航 `[06:24]` |

### 代價

- 需要好的 tree（解析真實世界文件成乾淨結構是 hard problem）`[05:46]`
- 更多 latency 和更多 model calls（多步推理 vs 單次向量查詢）`[06:01]`

## 來源

- [[raw/youtube/chunkless-rag-docling|Raw transcript — YouTube vRZNJWw78BQ]]

## 相關頁面

- [[wiki/entities/docling|Docling]]
- [[wiki/concepts/chunkless-rag|Chunkless RAG]]
- [[wiki/entities/memgraph-rag|MemGraph-RAG]] — 另一種 RAG 進化方向
