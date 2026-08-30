---
title: "Code Knowledge Graph — 代碼知識圖譜"
type: concept
created: 2026-08-06
updated: 2026-08-06
sources: 1
tags: [knowledge-graph, code-analysis, tree-sitter]
collection: concepts
topics: [agent-research]
canonical: concepts/code-knowledge-graph
---

> 結合 tree-sitter AST 解析與圖論算法，將代碼庫轉換為結構化知識圖譜，讓 AI Agent 精確理解代碼架構。

## 核心概念
- **AST（抽象語法樹）**：代碼的結構化表示
- **Call Graph**：函數呼叫關係圖
- **Import Dependency**：模組導入鏈
- **God Node**：代碼庫核心樞紐節點（透過 Leiden 演算法識別）

## 技術流程
```
原始碼 → tree-sitter 解析 → AST & Call Graph → Leiden 社群分群 → Knowledge Graph
```

## 與 Vector RAG 的差異
| 維度 | Vector RAG | Code KG |
|------|-----------|---------|
| 結構感知 | 低 | 高 |
| Token 效率 | 低 | 高 71.5x |
| 跨模組關聯 | 弱 | 強 |

## 距離「軟體世界模型」的差距
目前 Code KG 多為靜態語義拓撲圖，缺少：
- 動態執行期狀態變化（Heap State）
- 變數生命週期
- 動態分支執行（Dynamic Trace）
- 狀態轉移預測

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]

## 相關頁面
- [[wiki/entities/graphify|Graphify]]、[[wiki/entities/memgraph-rag|MemGraph-RAG]]
- [[wiki/concepts/graphify-obsidian-export|Graphify → Obsidian Export]] — KG 建好之後怎麼進 Obsidian 的工作流
