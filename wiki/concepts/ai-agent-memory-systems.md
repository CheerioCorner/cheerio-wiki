---
title: "AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式"
type: concept
created: 2026-08-17
updated: 2026-08-17
sources: 1
tags: [memory-system, rag, vector-store, graph-rag]
topics: [agent-memory-context, knowledge-mgmt]
canonical: concepts/ai-agent-memory-systems
---

# AI Agent Memory Systems

> AI agent 記憶架構的設計框架：三支柱分析 × 五種存儲方式 × 四種檢索方法 × 六種維護策略。

## 為什麼需要記憶？

任何 LLM 呼叫都不會長期攜帶記憶權重。ChatGPT 和 Claude Code 之所以能記住對話，是因為它們在底層構建了記憶系統。記憶是 AI agent harness 最有價值的資產。

## 三支柱分析框架

理解任何 agent 記憶系統，問三個問題：

```
What is it?  →  存儲形式（純文本 / 表格 / 向量 / 圖譜）
How to find it?  →  檢索方式（預載入 / keyword / RAG / Graph RAG）
How to maintain it?  →  維護策略（add / delete / override / retire / attribute / reflect）
```

## 五種存儲方式

### 1. 純文本 Markdown
- MEMORY.md / SOUL.md，直接餵入 context window
- 最簡單、最透明、最容易調試
- 代表：Hermes Agent、Waku Agent

### 2. 關聯式表格（SQLite + FTS5）
- Row-based 存儲 + 全文搜索
- 適合結構化事實，查詢速度快
- 代表：Hermes state.db

### 3. 向量存儲
- 將記憶嵌入高維向量空間，用相似度搜索
- 支持語義搜索（「食物」能找到「蘋果」）
- 代表：Supabase PGVector、Pinecone

### 4. 圖譜（Graph）
- 節點（nodes）+ 邊（edges），表達關係
- 可以向量化後做 Graph RAG
- 代表：Zep temporal graph

### 5. 混合（行記憶 + 圖記憶）
- 同時支援 plain facts 和關係圖
- 代表：mem0（行記憶免費 + 圖記憶付費）

## 四種檢索方式

| 方式 | 原理 | 適用場景 |
|------|------|----------|
| Do nothing | 預載入 context window | 記憶量小、簡單場景 |
| Keyword FTS5 | 關鍵字全文搜索 | 結構化事實、快速查詢 |
| RAG | 向量嵌入 + 相似度搜索 | 語義搜索、大規模記憶 |
| Graph RAG | 節點/邊向量 + 圖遍歷 | 需要關係上下文的場景 |

## 六種維護策略

| 策略 | 說明 |
|------|------|
| **Add** | 新增記憶 |
| **Delete** | 刪除記憶 |
| **Override** | 覆蓋舊值 |
| **Retire** | 標記失效但保留歷史（不同於 delete） |
| **Attribute** | 追蹤記憶來源（用戶對話 / 網路搜尋 / 行事曆） |
| **Reflect** | 整合去重、丟棄過時資訊（類似 dreaming） |

## 實測結論

| 工具 | 速度 | 推薦度 |
|------|------|--------|
| SQLite + FTS5 | ⚡ 快 | ⭐⭐⭐⭐⭐ 最務實的起步 |
| mem0 | ⚡ 快 | ⭐⭐⭐⭐ 功能完整 |
| LangMem | 🐌 慢 | ⭐⭐ 只是 wrapper |
| Zep | 🐌 很慢 | ⭐⭐ 圖譜漂亮但效能差 |

## 設計建議

1. **從純文本開始**：SOUL.md + MEMORY.md 就夠了
2. **按需升級**：記憶量大了再加 SQLite / 向量
3. **Retrieval gate 是可選的**：先判斷「需不需要 retrieve」
4. **不必追求最複雜**：選擇適合自己場景的組合
5. **記憶是最重要的資產**：好好保存、準備、照顧

## 相關頁面

- [[wiki/concepts/harness|Harness]] — Agent harness 根概念
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種工作流
- [[wiki/concepts/chunkless-rag|Chunkless RAG]] — 另一種 RAG 方法
- [[wiki/concepts/context-decay|Context Decay]] — 脈絡衰減問題
- [[wiki/entities/waku-agent|waku-agent]] — 實作此框架的代表 harness
- [[wiki/entities/hermes-agent|hermes-agent]] — 同類 local-first agent
- [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|來源筆記]]
