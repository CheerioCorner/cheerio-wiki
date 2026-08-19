---
title: "Chunkless RAG — 保留文件結構的檢索增強生成"
type: concept
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [rag, document-understanding, ai-agent, retrieval, structure]
collection: concepts
topics: [agent-memory-context]
canonical: concepts/chunkless-rag
---

> Chunkless RAG 是一種保留文件 tree structure 的 RAG 方法，讓 AI Agent 透過推理導航文件，而非切成 chunks 做相似度搜尋。

## 核心思想

**傳統 RAG**：把文件 flatten → 向量化 → 相似度搜尋 → 拿到碎片
**Chunkless RAG**：保留 tree → Agent 推理導航 → 拿到有脈絡的完整 section

## 機制

### 1. 建立 Tree（Docling 負責）

- 把 PDF 解析成結構化的 tree document
- 每個 section 附帶短摘要
- Agent 不需要讀完整文件就能「看到」文件的形狀

### 2. 推理導航

```
問題進來
  → Agent 看 tree outline + section summaries
  → 推理哪個 section 最可能有答案
  → 打開那個 section 讀取
  → 夠了就回答
  → 不夠就繼續走下一個 section
  → 必要時跨 branch 導航（policy 在 A section，reasoning 在 C section）
```

### 3. 輸出

- 模型拿到的是：一個有 heading 的完整 section
- 而非：失去位置的 500 字碎片

## 與傳統 RAG 的比較

| 維度 | 傳統 RAG（Chunk-based） | Chunkless RAG |
|------|------------------------|---------------|
| 結構保留 | ❌ Flatten tree | ✅ 保留 tree |
| 上下文 | ❌ Chunk 不知道自己的位置 | ✅ 知道 section/subsection path |
| 跨 section | ❌ 相似度搜尋不知道 sections 關聯 | ✅ Agent 可以跨 branch 導航 |
| 延遲 | ✅ 單次向量查詢 | ❌ 多步推理，更多 calls |
| 適用規模 | ✅ 百萬文件、模糊查詢 | ❌ 適合單一長文件 |
| 工程量 | ✅ 成熟工具鏈 | ❌ 需要好的文件解析（Docling） |

## 何時用哪種

```
文件數量多 + 模糊查詢 → Chunk-based RAG
單一長文件 + precision 重要 → Chunkless RAG
真實世界系統 → 兩者混用（chunk 找文件 → structure 導航文件內部）
```

## 限制

1. **Tree 品質**：解析真實世界文件成乾淨結構是 hard problem（大部分工程量在這裡）
2. **Latency**：多步推理比單次向量查詢慢
3. **成本**：更多 model calls

## 來源

- [[wiki/sources/2026-08-10-chunkless-rag-docling|What Is Chunkless RAG? — IBM Technology]]

## 相關頁面

- [[wiki/entities/docling|Docling]] — 提供 tree 的文件解析框架
- [[wiki/entities/memgraph-rag|MemGraph-RAG]] — 另一種 RAG 進化方向（記憶圖譜）
- [[wiki/concepts/knowledge-system-architecture|知識系統架構]] — 另一種「保留結構」的思路
