---
title: "AI Agent 長期記憶架構 — 以 Mem0 為例（Alejandro AO）"
type: source
created: 2026-08-22
updated: 2026-08-22
sources: 1
tags: [ai-agent, memory, mem0, entity-memory, embedding, coreference-resolution]
topics: [ai-agent, agent-memory-context]
canonical: sources/2026-08-22-alejandro-ao-mem0-long-term-memory
provenance_raw: "raw/youtube/alejandro-ao-mem0-long-term-memory.md"
---

# AI Agent 長期記憶架構 — 以 Mem0 為例（Alejandro AO）

> Alejandro AO 深入解析 Mem0 的長期記憶架構，涵蓋 entity memory 概念、entity boost 公式、retrieval pipeline 組裝、開源 embedding 模型推薦，以及 coreference resolution 實作。

## 一句話

Mem0 的長期記憶架構：**三個 store**（Main Memory + Entity Store + SQLite）× **Ingestion pipeline**（LLM extraction）× **Retrieval pipeline**（vector search + keyword matching + entity boost 三階段 re-ranking）。

## 重點摘要

### 為什麼需要長期記憶？`[00:56]`

- LLM 是 stateless 機器 `[01:09]`：每次 prompt 都是獨立的，不會記得上次的對話
- Conversational memory 是把歷史整個帶上下一次 prompt `[01:27]`
- Long-form memory 是獨立於 session 的 persistent 記憶 `[02:41]`，可跨 session、跨 agent 共享

### Mem0 的三個 Store `[04:32]`

1. **Main Memory（向量資料庫）** `[04:38]`
   - 儲存記憶本體（短段落/句子）+ metadata
   - Metadata 包含：建立日期、所屬者（user/agent）、hash（去重用）、lemmatized version（關鍵字搜尋用）、過期日期、歸因

2. **Entity Store（向量資料庫）** `[06:19]`
   - 從 Main Memory 萃取實體（places, people, proper names）
   - 每個 entity 連結到一個或多個 main memories
   - 例如：Paris → 關聯到「最喜歡的街區是 Montmartre」`[06:56]`

3. **SQLite（日誌 + 最近訊息）** `[07:25]`
   - 記錄所有向量資料庫的變更歷史
   - 保留最近 10 條訊息（用於 coreference resolution）

### Ingestion Pipeline `[08:07]`

三種擷取模式 `[08:49]`：
1. **Procedural memory**：擷取程序/動作（較少用）
2. **infer=false**：直接嵌入原始訊息
3. **infer=true**（推薦）：用 LLM 擷取結構化記憶 `[10:09]`

LLM extraction 的 context 組裝 `[10:42]`：
- LLM role prompt（memory extractor）
- User summary
- Input messages
- Recent memories（從 DB 查詢）
- Relevant memories（向量搜尋相關記憶）
- Last 10 messages（從 SQLite 取得，用於 coreference resolution）`[12:23]`
- Conversation date + current date

### Retrieval Pipeline `[15:44]`

兩個觸發時機 `[15:53]`：
1. **Explicit search**：agent 透過 tool 主動搜尋記憶
2. **Automatic retrieval**：每回合自動觸發，將相關記憶注入 context

三階段 re-ranking `[20:42]`：
1. **Vector search**：向量搜尋取得初始候選池（top K × 4 或最少 60 條）`[20:04]`
2. **Keyword matching score**：BM25 計算查詢與記憶的詞彙重疊度 `[21:47]`
3. **Entity boost**：根據 entity 的連結數給予額外分數 `[22:07]`

**Entity Boost 公式** `[23:04]`：
```
boost = similarity × W_entity × w_memory
w_memory = 1 / (1 + 0.001 × (n_linked - 1)²)
```
- 連結數越少的 entity，boost 越高
- 例如：Montmartre（2 個連結）的 boost > Paris（10 個連結）`[23:31]`

最終分數 `[24:02]`：
- Vector score (0-1) + Keyword score (0-1) + Entity boost (0-0.5) = 總分 (0-2.5)
- 再除以 2.5 正規化到 0-1

### 開源 Embedding 模型推薦 `[25:57]`

| 模型 | 用途 | 備註 |
|------|------|------|
| BAAI/bge-m3 | Embedding | 多語言支援 |
| intfloat/e5-large-v2 | Embedding | 英文為主 |
| nomic-ai/nomic-embed-text-v1.5 | Embedding | 輕量級 |
| gte-Qwen2-1.5B-instruct | Embedding | Qwen 系列 |
| Qwen3-8B | Extraction（LLM）`[26:43]` | 8B 參數適合 extraction |

### Coreference Resolution 展示 `[13:00]`

- 當輸入包含代名詞（it, he）時，需要最近 10 條訊息來解析指代
- 例如：「he's really good at this」需要知道「he」指的是誰
- 這就是為什麼 SQLite 要保留最近訊息的原因

## 使用者觀點

Cheer 在觀看後的反思與延伸思考：

1. **Entity Memory 與大腦的萃取/注入**
   > 「他在影片的大概 6 分 44 秒講到...讓我覺得長期記憶的做法有從 Entity Memory 直接提取了一些 Main Memory 然後注入到不同的 Entities。我覺得這個做法很像是我們目前的大腦，也就是我們在 Operation 的大腦的一個萃取跟注入方式嗎？」
   
   → Mem0 的 Entity Store 正是從 Main Memory 萃取實體，然後建立 entity → memories 的關聯。當查詢某個 entity 時，會同時取得所有關聯的記憶。這與我們 Obsidian 知識庫的架構有異曲同工之妙：raw → wiki 的過程就是一種萃取與注入。

2. **GitHub Copilot 的記憶實作**
   > 「我記得 GitHub Copilot，它在提取記憶的時候，靠的是不是也是 SQLite？」
   
   → 這是一個值得追蹤的問題。Copilot 的記憶實作確實值得研究，特別是它如何在 IDE 環境中處理長期記憶。

3. **長期記憶與 DevOps Skill**
   > 「這讓我更深刻了解到，隨著對話變長，如果我們不能有效在前面幾輪就讓 AI 清楚知道我們需要的資訊，那麼你必須要給的 context 就可能越長...這讓我想到之前在做 DevOps skill 的時候...我有想過下一輪可以寫一個小型 agent，但又覺得會不會殺雞焉用牛刀，有需要寫一個有長期記憶的 agent 嗎？」
   
   → 這正是 context decay / context rot 的核心問題。隨著對話變長，context window 被塞滿，AI 開始遺忘早期的重要資訊。長期記憶系統（如 mem0）可以解決這個問題，但引入的複雜度也值得評估。

4. **長期記憶在工作追蹤的價值**
   > 「我發現如果 AI 具備長期記憶，在工作追蹤或協助處理上的效果會好很多...就像我最近在用 M365 Copilot 的過程中發現，如果 AI 能調用我過去的對話、之前的 Email、跟同事在 Teams 上的紀錄，還有我的一些報告文件，我只要給它少許 prompt，它就能一次幫我完成想要的簡報內容」
   
   → 這描述了「記憶驅動的生產力」願景。M365 Copilot 的整合能力（Outlook + Teams + OneDrive）正是長期記憶的實際應用案例。

5. **LangMem 與檢索方式的關聯**
   > 「這部分是不是跟我們前幾天關於 LangMem 的檢索方式息息相關？」
   
   → 是的。LangMem 作為 LangChain 的記憶套件，提供了一個靈活的框架讓使用者自行實作儲存與檢索邏輯，這與影片中提到的「agent harness 靈活度」概念一致。

## 來源

- [[raw/youtube/alejandro-ao-mem0-long-term-memory|YouTube 原始逐字稿]]（28:32，246 paragraphs，en，手動字幕）
- 頻道：Alejandro AO
- URL：https://www.youtube.com/watch?v=aYfZN8t6AQs

## 相關頁面

- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]]
- [[wiki/concepts/harness|Harness — LLM 的驅動層]]
- [[wiki/concepts/context-decay|Context Decay（脈絡衰減）]]
- [[wiki/concepts/context-rot|Context Rot（脈絡惡化）]]
- [[wiki/sources/2026-08-22-sean-ai-stories-ai-agent-memory-systems|AI Agent Memory Systems — 5 種記憶架構設計方式（Sean's AI Stories）]]
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents]]
