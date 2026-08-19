---
title: "AI Agent Memory Systems — 5 種記憶架構設計方式"
type: source
created: 2026-08-17
updated: 2026-08-17
sources: 1
tags: [memory-system, rag, vector-store, graph-rag, waku-agent, hermes-agent, mem0, zep, langmem]
topics: [agent-memory-context, knowledge-mgmt]
canonical: sources/2026-08-17-ai-agent-memory-systems-5-ways
provenance:
  - kind: raw
    path: raw/youtube/ai-agent-memory-systems.md
---

# AI Agent Memory Systems — 5 種記憶架構設計方式

> 來源：[YouTube](https://www.youtube.com/watch?v=072eNztI06k) | 作者：Sean Chen ([@ShenSeanChen](https://github.com/ShenSeanChen)) | 頻道：Sean's AI Stories | 時長：30:26

## 三支柱分析框架

Sean 提出一套理解 AI agent 記憶系統的分析框架，三個核心問題：

| 問題 | 意義 |
|------|------|
| **What is it?** | 記憶以什麼形式存儲？ |
| **How to find it?** | Agent 如何檢索記憶？ |
| **How to maintain it?** | 記憶如何更新、淘汰、整合？ |

## 1. 存儲形式（What is it）

| 類型 | 實作方式 | 代表產品 |
|------|----------|----------|
| **純文本 Markdown** | MEMORY.md / SOUL.md，直接餵入 context window | Hermes, Waku Agent |
| **表格/關聯式** | SQLite + FTS5，row-based 關鍵字搜索 | Hermes state.db |
| **向量存儲** | 向量嵌入 + 相似度搜索 | Supabase PGVector, Pinecone |
| **圖譜** | 節點（nodes）+ 邊（edges），向量化後遍歷 | Zep temporal graph |

> 向量存儲本質上仍是表格（PGVector 在 Supabase 裡是 row-based），差別在於某些欄位是向量。

## 2. 檢索方式（How to find it）

| 方法 | 原理 | 優缺 |
|------|------|------|
| **Do nothing** | 記憶直接預載入 context window | 簡單但受 token 限制 |
| **Keyword FTS5** | SQLite 全文搜索，關鍵字匹配 | 快但不支持語義 |
| **RAG（向量相似度）** | 將問題嵌入向量空間，計算與記憶的相似度 | 支持語義搜索 |
| **Graph RAG** | 節點 + 邊都做向量嵌入，返回關係圖 | 上下文最豐富但最慢 |

> Hermes 和 Waku Agent 不使用 RAG/embedding，而是用「do nothing + keyword」的組合。

## 3. 維護策略（How to maintain it）

| 策略 | 說明 | 舉例 |
|------|------|------|
| **Add** | 新增記憶 | 用戶說了新事實 |
| **Delete** | 刪除記憶 | 確認資訊錯誤 |
| **Override** | 覆蓋舊值 | 地址變更 |
| **Retire** | 標記失效但保留歷史（不同於 delete） | Waku 從 1000⭐ → 1300⭐，不刪舊數據但標記為歷史 |
| **Attribute** | 追蹤來源（用戶對話？網路搜尋？行事曆？） | 資料溯源 |
| **Reflect** | 整合去重、丟棄過時資訊（類似 Anthropic 的 dreaming） | Agent 閒置時排程整合任務 |

## 4. 實測比較（Memory Arena）

Waku Agent 建立了「記憶競技場」，用相同的事實集和問題測試不同存儲方案：

| 工具 | 存儲類型 | 檢索方式 | 回答速度 | 評價 |
|------|----------|----------|----------|------|
| **SQLite** | 關聯式 | keyword FTS5 | ~4.5s | 簡單可靠，中文搜尋略弱 |
| **mem0** | 行記憶 | 向量/直接 | ~5s | 功能豐富，回答精準 |
| **LangMem** | wrapper（無自有存儲） | 依賴底層 | ~7.5s | 最慢，只是封裝層 |
| **Zep** | temporal graph | 圖遍歷 | ~20s+ | 圖譜直觀但效能極差 |
| **Control** | 無記憶 | — | — | 正確（無資料就不回答） |

### 關鍵發現

- **SQLite + FTS5** 是最務實的起步方案：簡單、快、可靠
- **mem0** 功能最完整（行記憶 + 圖記憶雙模式），但圖功能需付費
- **Zep** 的 temporal graph 視覺化很漂亮，但建圖 + 查詢太慢，「殺雞用牛刀」
- **LangMem** 只是 LangChain 的封裝層，沒有自己的存儲引擎
- **Retrieval gate** 是可選的優化層：先判斷「需不需要 retrieve」，避免每次全量查詢

## 核心洞察

> 「記憶是任何 AI agent harness 最有價值的資產。如果你好好保存、準備、照顧這些記憶，它們就是最寶貴的資產。」

- 從純文本開始，按需升級
- 不必追求最複雜的方案，選擇適合自己場景的組合
- Agent harness 非常靈活，無需遵循任何標準，可以自己定義

## 相關頁面

- [[wiki/entities/waku-agent|waku-agent]] — 本影片的主角 harness
- [[wiki/entities/hermes-agent|hermes-agent]] — 同類 local-first agent
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 概念彙整頁
- [[wiki/concepts/harness|Harness]] — Agent harness 根概念
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種工作流模式
- [[wiki/concepts/chunkless-rag|Chunkless RAG]] — 另一種 RAG 方法
