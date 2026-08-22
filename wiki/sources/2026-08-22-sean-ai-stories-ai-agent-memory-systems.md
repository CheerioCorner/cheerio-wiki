---
title: "AI Agent Memory Systems — 5 種記憶架構設計方式（Sean's AI Stories）"
type: source
created: 2026-08-22
updated: 2026-08-22
sources: 1
tags: [ai-agent, memory, mem0, sqlite, langmem, zep]
topics: [ai-agent, agent-memory-context]
canonical: sources/2026-08-22-sean-ai-stories-ai-agent-memory-systems
provenance_raw: "raw/youtube/sean-ai-stories-ai-agent-memory-systems.md"
---

# AI Agent Memory Systems — 5 種記憶架構設計方式（Sean's AI Stories）

> Sean Chen（Sean's AI Stories 頻道）講解 AI agent 記憶系統的分析框架，涵蓋三支柱、五種存儲方式、四種檢索策略、六種維護機制，並實測比較 SQLite、mem0、LangMem、Zep 與無記憶控制組。

## 一句話

AI agent 記憶系統的設計框架：**三支柱**（what / how / maintain）× **五種存儲**（純文字 / SQL / 向量 / 圖形 / 混合）× **四種檢索**（do nothing / keyword FTS5 / semantic RAG / GraphRAG）× **六種維護**（add / delete / override / retire / attribute / reflect）。

## 重點摘要

### 三支柱分析框架 `[02:40]`

1. **What is it?** — 記憶以什麼形式儲存
   - 純文字 / Markdown（如 memory.MD、SOUL.MD）`[02:44]`
   - 表格 / SQL（如 SQLite state.db）`[03:14]`
   - 圖形（nodes + edges，如 Zep 的 temporal graph）`[03:33]`

2. **How to find it?** — 檢索方式
   - Do nothing（預載入 context window）`[05:02]`
   - Keyword search（SQLite FTS5）`[05:58]`
   - Semantic RAG（向量嵌入 + 相似度搜尋）`[06:39]`
   - Graph RAG（向量化 nodes + edges + traversal）`[06:59]`

3. **How to maintain it?** — 維護機制
   - Add / Delete / Override（直接覆蓋）
   - Retire（使舊資訊失效但保留歷史）`[07:44]`
   - Attribute（追蹤來源）`[08:25]`
   - Reflect（合併重複、丟棄過時資訊，類似 dreaming）`[08:56]`

### 記憶系統實測比較 `[18:23]`

| 系統 | 儲存方式 | 檢索方式 | 維護機制 | 實測結果 |
|------|----------|----------|----------|----------|
| SQLite | 純文字 state.db | keyword FTS5 | 直接編輯 | 4.5-10.3 秒，中文搜尋有問題 |
| mem0 | row memory + graph memory | 向量 + traversal | add/update/delete/noop/retire | 快速直接，稍慢於 SQLite |
| LangMem | 純套件（無資料庫） | 自行實作 | 自行實作 | 7.5 秒，較慢 |
| Zep | temporal graph memory | vector search + graph traversal | invalidate with time range | 建圖很慢，查詢 4.9-6.4 秒 |
| 控制組 | 無記憶 | — | — | 無法回答記憶相關問題 |

### 關鍵洞見

- **記憶是 harness 最有價值的資產** `[12:17]`：harness 的設計很靈活，但最終決定價值的是你累積的記憶
- **Retirement vs Deletion** `[07:44]`：退休（retire）不是刪除，而是使舊資訊失效但保留歷史脈絡
- **Reflect = Dreaming** `[08:56]`：反思機制類似 Anthropic 提出的 dreaming 概念——agent 不運行時進行後處理
- **Stateless LLM** `[00:13]`：任何 LLM 呼叫都不會長期攜帶記憶權重，這就是為什麼需要記憶系統

## 使用者觀點

Cheer 在觀看後的反思與延伸思考：

1. **Harness 開發 vs AI Assistant 開發**
   > 「這讓我對於 harness 的開發有了新的認識...我其實在想的是，我們是要開發 harness 呢？還是開發屬於我們自己的 AI Assistant?」
   
   → 這個問題觸及了 agent 開發的核心定位：harness 是「驅動層」，而 AI Assistant 是「應用層」。兩者的差異在於 harness 關注的是如何讓 LLM 做實事（loop、tools、memory），而 AI Assistant 關注的是如何服務特定使用者的需求。

2. **Entity Memory 與大腦的萃取/注入**
   > 「他在影片的大概 6 分 44 秒講到...讓我覺得長期記憶的做法有從 Entity Memory 直接提取了一些 Main Memory 然後注入到不同的 Entities。我覺得這個做法很像是我們目前的大腦，也就是我們在 Operation 的大腦的一個萃取跟注入方式嗎？」
   
   → 這個觀察非常精準。Mem0 的 Entity Memory 確實是從 Main Memory 萃取實體（places, people, proper names），然後建立 entity → memories 的關聯。這與我們 Obsidian 知識庫的架構有異曲同工之妙：raw → wiki 的過程就是一種萃取與注入。

3. **GitHub Copilot 的記憶實作**
   > 「我記得 GitHub Copilot，它在提取記憶的時候，靠的是不是也是 SQLite？」
   
   → 這是一個值得追蹤的問題。Copilot 的記憶實作確實值得研究，特別是它如何在 IDE 環境中處理長期記憶。

4. **長期記憶與 DevOps Skill**
   > 「這讓我更深刻了解到，隨著對話變長，如果我們不能有效在前面幾輪就讓 AI 清楚知道我們需要的資訊，那麼你必須要給的 context 就可能越長...這讓我想到之前在做 DevOps skill 的時候...我有想過下一輪可以寫一個小型 agent，但又覺得會不會殺雞焉用牛刀，有需要寫一個有長期記憶的 agent 嗎？」
   
   → 這正是 context decay / context rot 的核心問題。隨著對話變長，context window 被塞滿，AI 開始遺忘早期的重要資訊。長期記憶系統（如 mem0）可以解決這個問題，但引入的複雜度也值得評估。

5. **長期記憶在工作追蹤的價值**
   > 「我發現如果 AI 具備長期記憶，在工作追蹤或協助處理上的效果會好很多...就像我最近在用 M365 Copilot 的過程中發現，如果 AI 能調用我過去的對話、之前的 Email、跟同事在 Teams 上的紀錄，還有我的一些報告文件，我只要給它少許 prompt，它就能一次幫我完成想要的簡報內容」
   
   → 這描述了「記憶驅動的生產力」願景。M365 Copilot 的整合能力（Outlook + Teams + OneDrive）正是長期記憶的實際應用案例。

6. **LangMem 與檢索方式的關聯**
   > 「這部分是不是跟我們前幾天關於 LangMem 的檢索方式息息相關？」
   
   → 是的。LangMem 作為 LangChain 的記憶套件，提供了一個靈活的框架讓使用者自行實作儲存與檢索邏輯，這與影片中提到的「agent harness 靈活度」概念一致。

## 來源

- [[raw/youtube/sean-ai-stories-ai-agent-memory-systems|YouTube 原始逐字稿]]（30:26，122 paragraphs，en-US，手動字幕）
- 頻道：Sean's AI Stories（Sean Chen）
- URL：https://www.youtube.com/watch?v=072eNztI06k

## 相關頁面

- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]]
- [[wiki/concepts/harness|Harness — LLM 的驅動層]]
- [[wiki/concepts/context-decay|Context Decay（脈絡衰減）]]
- [[wiki/concepts/context-rot|Context Rot（脈絡惡化）]]
- [[wiki/entities/waku-agent|waku-agent — Local-first personal AI agent]]
- [[wiki/entities/memgraph-rag|MemGraph-RAG — 記憶圖譜多智能體檢索增強生成]]
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents]]
