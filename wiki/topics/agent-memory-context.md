---
type: topic
title: Agent Memory & Context
topic: agent-memory-context
created: 2026-08-23
updated: 2026-09-04
---

# Agent Memory Context

> 與 Agent Memory Context 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/docling|Docling — IBM 文件解析與結構化框架]] — Docling 是 IBM Research Zurich 開發的開源文件解析框架，把 PDF 等多種格式的非結構化文件轉換成結構化的 DoclingDocument tree，支援 Chunkless RAG 與 AI 代理整合。現為 LF AI & Data Foundation 專案，GitHub 65k+ stars。
- [[wiki/entities/mem0|Mem0 — AI Agent 長期記憶框架]] — Mem0 是一個開源的 AI agent 長期記憶框架，提供行記憶（row memory）與圖記憶（graph memory）兩種儲存方式，支援 LLM-based extraction 與三階段 re-ranking 檢索。 🛠️
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — 模型的「記憶力」和「思考力」都是有限的，要學會分配。

## Concepts

- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]] — AI agent 記憶架構的設計框架：三支柱分析 × 五種存儲方式 × 四種檢索方法 × 六種維護策略。 🛠️
- [[wiki/concepts/chunkless-rag|Chunkless RAG — 保留文件結構的檢索增強生成]] — Chunkless RAG 是一種保留文件 tree structure 的 RAG 方法，讓 AI Agent 透過推理導航文件，而非切成 chunks 做相似度搜尋。
- [[wiki/concepts/coala-four-memory-types|CoALA 四種記憶類型 — AI Agent 的功能分類框架]] — AI Agent 記憶架構的**功能分類**框架：從「記憶做什麼」的角度切分四種類型。與 [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] 的**存儲機制**分類（五種存儲 × 四種檢索 × 六種維護）互補，不衝突。
- [[wiki/concepts/consumer-ai-memory-personalization|Consumer AI Memory Personalization — 消費級 AI 產品的記憶設計]] — ChatGPT 和 Claude 從相反設計出發，2026 年中收斂到相同模式（running profile + on-demand retrieval），但實作仍然不同。核心結論：memory is a function of compute。
- [[wiki/concepts/context-decay|Context Decay（脈絡衰減）]] — 決策脈絡在跨模型交接時逐漸遺失的問題，是多 AI 協作流程中比模型能力差異更大的風險。
- [[wiki/concepts/context-engineering|Context Engineering（脈絡工程）— 決定模型這一次看到什麼]] — 把 context-rot / context-decay 兩個病症的對策收在一起：Write / Read / Handoff / Maintain 四個對策族。 🛠️
- [[wiki/concepts/context-rot|Context Rot（脈絡惡化）— 長任務中的記憶腐化]] — 單一 agent 在長時間單一 session 內，因 context 累積導致模型自相矛盾、重複工作、偏離原問題的現象。與 [[wiki/concepts/context-decay|Context Decay]] 觸發條件不同但後果類似。
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder — 記憶檢索策略的效果階梯]] — 四階記憶檢索策略（no recall → vector RAG → decision-ledger → oracle）在長時任務中的實測效果排名。核心發現：好的 recall policy 不只更準，還更省 token。

## Sources

- [[wiki/sources/2026-08-10-chunkless-rag-docling|What Is Chunkless RAG? How Docling & AI Agents Navigate Documents]] — 來源：[YouTube — What Is Chunkless RAG? How Docling & AI Agents Navigate Documents](https://www.youtube.com/watch?v=vRZNJWw78BQ)
- [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|AI Agent Memory Systems — 5 種記憶架構設計方式]] — 來源：[YouTube](https://www.youtube.com/watch?v=072eNztI06k) | 作者：Sean Chen ([@ShenSeanChen](https://github.com/ShenSeanChen)) | 頻道：Sean's AI Stories | 時長：30:26 🛠️
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents（Stefania Druga, Sakana AI）]] — Sakana AI 研究科學家 Stefania Druga 講長時間運行的 research agent 記憶系統實驗：write-manage-read 迴圈、recall policy ladder、decision-ledger、context rot。
- [[wiki/sources/2026-08-20-shlok-khemani-chatgpt-and-claude-memory|Lessons from Studying Every Memory System — Shlok Khemani]] — ⚠️ 本文探討 ChatGPT/Claude 消費級產品的個人化記憶系統，**與 Stefania Druga 的 memory harnesses 研究無關**。不要與 recall-policy-ladder 或 context-rot 混淆。
- [[wiki/sources/2026-08-22-alejandro-ao-mem0-long-term-memory|AI Agent 長期記憶架構 — 以 Mem0 為例（Alejandro AO）]] — Alejandro AO 深入解析 Mem0 的長期記憶架構，涵蓋 entity memory 概念、entity boost 公式、retrieval pipeline 組裝、開源 embedding 模型推薦，以及 coreference resolution 實作。 🛠️
- [[wiki/sources/2026-08-22-mem0-deep-research-comparison|Mem0 深度研究——比較、限制、Decision-Ledger 適用性、授權定價]] — 來自已查證版 Gemini 深度研究報告，涵蓋四個角度：跟其他記憶系統比較、生產環境限制、decision-ledger 適用性、授權定價。原始 Gemini 輸出經 Claude WebFetch 逐一查證後修正，**只有本檔案的內容可進 wiki**。
- [[wiki/sources/2026-08-22-sean-ai-stories-ai-agent-memory-systems|AI Agent Memory Systems — 5 種記憶架構設計方式（Sean's AI Stories）]] — Sean Chen（Sean's AI Stories 頻道）講解 AI agent 記憶系統的分析框架，涵蓋三支柱、五種存儲方式、四種檢索策略、六種維護機制，並實測比較 SQLite、mem0、LangMem、Zep 與無記憶控制組。 🛠️
- [[wiki/sources/2026-08-25-four-types-memory-ai-agent|The Four Types of Memory Every AI Agent Needs]] — 來源：[YouTube](https://www.youtube.com/watch?v=BacJ6sEhqMo) | Duration: 10:41 | Language: en
- [[wiki/sources/2026-09-04-kodekloud-ai-agent-memory-context-window|AI Agent Memory: Why Context Window Expansion Is Not Enough（KodeKloud）]] — KodeKloud 入門講解：context vs memory 差異、三種早期權宜之計（RAG/Summary/Profile）的失效模式、記憶 write+use 迴圈。補充「記憶系統出現前的動機脈絡」。

## Others

- [[wiki/discussions/mem0-vs-decision-ledger-for-w074|Mem0 vs Decision-Ledger 對 W-074 架構的適用性]] — W-074 規劃四類記憶架構（Episodic / Semantic / Procedural / Artifact）。本頁探討 Mem0 與 Decision-Ledger 兩種記憶機制各自適合哪一類，以及為何應該疊加而非二選一。

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
