---
title: "Mem0 — AI Agent 長期記憶框架"
type: entity
created: 2026-08-22
updated: 2026-08-22
sources: 2
tags: [memory, ai-agent, vector-store, entity-memory]
topics: [ai-agent, agent-memory-context]
canonical: entities/mem0
---

# Mem0

> Mem0 是一個開源的 AI agent 長期記憶框架，提供行記憶（row memory）與圖記憶（graph memory）兩種儲存方式，支援 LLM-based extraction 與三階段 re-ranking 檢索。

## 核心架構

### 三個 Store `[04:32]`

| Store | 類型 | 用途 |
|-------|------|------|
| **Main Memory** | 向量資料庫 | 儲存記憶本體 + metadata（hash、lemmatized version、歸因等） |
| **Entity Store** | 向量資料庫 | 從 Main Memory 萃取實體，建立 entity → memories 關聯 |
| **SQLite** | 關聯式資料庫 | 記錄變更歷史 + 保留最近 10 條訊息（coreference resolution 用） |

### 記憶類型 `[08:49]`

1. **Row memory**：純文字事實（例如「這位德國買家要求所有產品都要有素食認證」）
2. **Graph memory**：節點（nodes）+ 邊（edges）的關係圖（需要 Pro 版）

### Entity Memory 概念 `[06:19]`

- 從 Main Memory 自動萃取實體（places, people, proper names）
- 每個 entity 連結到一個或多個 main memories
- 例如：Paris → 關聯到「最喜歡的街區是 Montmartre」
- 查詢某個 entity 時，會同時取得所有關聯的記憶

### Entity Boost 公式 `[23:04]`

```
boost = similarity × W_entity × w_memory
w_memory = 1 / (1 + 0.001 × (n_linked - 1)²)
```

- 連結數越少的 entity，boost 越高
- 例如：Montmartre（2 個連結）的 boost > Paris（10 個連結）
- 目的：讓「具體且稀有」的記憶獲得更高權重

## Ingestion Pipeline `[08:07]`

三種擷取模式：
1. **Procedural memory**：擷取程序/動作（較少用）
2. **infer=false**：直接嵌入原始訊息
3. **infer=true**（推薦）：用 LLM 擷取結構化記憶

LLM extraction 的 context 組裝 `[10:42]`：
- LLM role prompt（memory extractor）
- User summary
- Input messages
- Recent memories（從 DB 查詢）
- Relevant memories（向量搜尋相關記憶）
- Last 10 messages（從 SQLite 取得，用於 coreference resolution）`[12:23]`
- Conversation date + current date

## Retrieval Pipeline `[15:44]`

兩個觸發時機：
1. **Explicit search**：agent 透過 tool 主動搜尋記憶
2. **Automatic retrieval**：每回合自動觸發，將相關記憶注入 context

三階段 re-ranking `[20:42]`：
1. **Vector search**：向量搜尋取得初始候選池（top K × 4 或最少 60 條）`[20:04]`
2. **Keyword matching score**：BM25 計算查詢與記憶的詞彙重疊度 `[21:47]`
3. **Entity boost**：根據 entity 的連結數給予額外分數 `[22:07]`

最終分數 `[24:02]`：
- Vector score (0-1) + Keyword score (0-1) + Entity boost (0-0.5) = 總分 (0-2.5)
- 再除以 2.5 正規化到 0-1

## 開源 Embedding 模型推薦 `[25:57]`

| 模型 | 用途 | 備註 |
|------|------|------|
| BAAI/bge-m3 | Embedding | 多語言支援 |
| intfloat/e5-large-v2 | Embedding | 英文為主 |
| nomic-ai/nomic-embed-text-v1.5 | Embedding | 輕量級 |
| gte-Qwen2-1.5B-instruct | Embedding | Qwen 系列 |
| Qwen3-8B | Extraction（LLM）`[26:43]` | 8B 參數適合 extraction |

## 實測結果（來自 Sean's AI Stories）`

| 指標 | 結果 |
|------|------|
| 查詢速度 | 快速直接，稍慢於 SQLite |
| 中文支援 | ✅ 支援（能回覆中文問題） |
| 圖記憶 | 需要 Pro 版 |
| 去重機制 | Hash dedup（只處理完全相同措辭） |

## 開發者資訊

- 開源：https://github.com/mem0ai/mem0
- 文件：https://docs.mem0.ai/
- 付費版：Pro 版支援圖記憶功能

## 來源

- [[raw/youtube/alejandro-ao-mem0-long-term-memory|YouTube 原始逐字稿]]（Alejandro AO）
- [[raw/youtube/sean-ai-stories-ai-agent-memory-systems|YouTube 原始逐字稿]]（Sean's AI Stories）
- [[wiki/sources/2026-08-22-alejandro-ao-mem0-long-term-memory|AI Agent 長期記憶架構 — 以 Mem0 為例（Alejandro AO）]]
- [[wiki/sources/2026-08-22-sean-ai-stories-ai-agent-memory-systems|AI Agent Memory Systems — 5 種記憶架構設計方式（Sean's AI Stories）]]

## 相關頁面

- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]]
- [[wiki/entities/memgraph-rag|MemGraph-RAG — 記憶圖譜多智能體檢索增強生成]]
- [[wiki/concepts/harness|Harness — LLM 的驅動層]]
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents]]
