---
title: "MemGraph-RAG — 記憶圖譜多智能體檢索增強生成"
type: entity
created: 2026-08-06
updated: 2026-08-06
sources: 1
tags: [rag, knowledge-graph, multi-agent, kdd]
collection: entities
topics: [agent-research]
canonical: entities/memgraph-rag
---

> KDD 2026 頂會論文，透過三層全局記憶架構與三大 AI 神探多智能體協作，實現 0.061 秒極速檢索。

## 基本資訊
| 屬性 | 值 |
|------|-----|
| 會議 | KDD 2026 |
| 核心 | 3-Layer Memory + Multi-Agent |
| 響應時間 | 0.061 秒 |
| GitHub | github.com/XMUDeepLIT/MemGraphRAG |

## 三層全局記憶架構
1. **Schema Layer（概念層）**：抽象本體概念
2. **Fact Layer（事實層）**：具體事實三元組
3. **Passage Layer（段落層）**：原始支撐文本

## 三大 AI 神探
1. **抽取 Agent**：實體與關係元組粗提
2. **衝突檢測 Agent**：搜尋邏輯矛盾
3. **衝突解決 Agent**：全域消歧與同義歸併

## 與其他 RAG 比較
| 維度 | Vanilla RAG | GraphRAG | MemGraph-RAG |
|------|-------------|----------|--------------|
| 檢索方式 | 向量相似度 | 圖結構 | 3-Layer 記憶 + PPR |
| 邏輯關聯 | 差 | 中 | 優 |
| 響應速度 | 快 | 慢 | 0.061s |

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]

## 相關頁面
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]]
