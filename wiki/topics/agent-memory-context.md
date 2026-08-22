---
type: topic
title: Agent Memory & Context
topic: agent-memory-context
created: 2026-08-23
updated: 2026-08-23
---

# Agent Memory & Context

> 記憶架構、context 管理、文件理解與 token 預算分配。

## Concepts

- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]] — 三支柱分析 × 五種存儲 × 四種檢索 × 六種維護策略
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder — 記憶檢索策略的效果階梯]] — 四階檢索策略在長時任務中的實測效果排名 🛠️
- [[wiki/concepts/consumer-ai-memory-personalization|Consumer AI Memory Personalization]] — 消費級 AI 產品的記憶設計（ChatGPT/Claude running profile，與 agent harness 記憶不同領域）🛠️
- [[wiki/concepts/context-decay|Context Decay（脈絡衰減）]] — 跨模型交接時 context 資訊逐漸失真的現象與對策
- [[wiki/concepts/context-rot|Context Rot（脈絡惡化）]] — 長任務中的記憶腐化（與 Context Decay 互補）🛠️
- [[wiki/concepts/chunkless-rag|Chunkless RAG — 保留文件結構的檢索增強生成]] — 保留文件 tree structure 的 RAG 方法

## Entities

- [[wiki/entities/mem0|Mem0 — AI Agent 長期記憶框架]] — 開源記憶框架，行記憶 + 圖記憶，LLM extraction + 三階段 re-ranking 🛠️
- [[wiki/entities/docling|Docling — IBM 文件解析與結構化框架]] — IBM Research Zurich 開發的文件解析框架，65k+ stars，支援 30+ 格式 → 結構化 DoclingDocument tree，整合 LangChain/LlamaIndex/Haystack/MCP
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — 模型資源分配與限制

## Sources

- [[wiki/sources/2026-08-22-sean-ai-stories-ai-agent-memory-systems|AI Agent Memory Systems — 5 種記憶架構設計方式（Sean's AI Stories）]] — 三支柱分析 × 五種存儲 × 四種檢索 × 六種維護，含實測比較
- [[wiki/sources/2026-08-22-alejandro-ao-mem0-long-term-memory|AI Agent 長期記憶架構 — 以 Mem0 為例（Alejandro AO）]] — Mem0 架構深入解析：entity memory、entity boost 公式、retrieval pipeline 🛠️
- [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|AI Agent Memory Systems — 5 種記憶架構設計方式]] — 記憶存儲、檢索、維護的完整比較與實測（舊版）
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents]] — Sakana AI 實驗：write-manage-read 迴圈、recall policy ladder 🛠️
- [[wiki/sources/2026-08-20-shlok-khemani-chatgpt-and-claude-memory|Shlok Khemani — ChatGPT/Claude 記憶系統分析]] — 消費級產品個人化記憶（不同領域）🛠️
- [[wiki/sources/2026-08-10-chunkless-rag-docling|What Is Chunkless RAG? How Docling & AI Agents Navigate Documents]] — IBM Docling 如何讓 AI Agent 保留文件結構做 Chunkless RAG

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
