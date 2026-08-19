---
title: Agent Memory & Context
topic: agent-memory-context
created: 2026-08-23
updated: 2026-08-23
---

# Agent Memory & Context

> 記憶架構、context 管理、文件理解與 token 預算分配。

## Concepts

- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]] — 三支柱分析 × 五種存儲 × 四種檢索 × 六種維護策略
- [[wiki/concepts/context-decay|Context Decay（脈絡衰減）]] — 長 session 中 context 資訊逐漸失真的現象與對策
- [[wiki/concepts/chunkless-rag|Chunkless RAG — 保留文件結構的檢索增強生成]] — 保留文件 tree structure 的 RAG 方法

## Entities

- [[wiki/entities/docling|Docling — IBM 文件解析與結構化框架]] — IBM Research Zurich 開發的文件解析框架，65k+ stars，支援 30+ 格式 → 結構化 DoclingDocument tree，整合 LangChain/LlamaIndex/Haystack/MCP
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — 模型資源分配與限制

## Sources

- [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|AI Agent Memory Systems — 5 種記憶架構設計方式]] — 記憶存儲、檢索、維護的完整比較與實測
- [[wiki/sources/2026-08-10-chunkless-rag-docling|What Is Chunkless RAG? How Docling & AI Agents Navigate Documents]] — IBM Docling 如何讓 AI Agent 保留文件結構做 Chunkless RAG

## 相關 Topics

- [[wiki/topics/ai-agent|AI Agent]] — AI Agent 總覽 hub
