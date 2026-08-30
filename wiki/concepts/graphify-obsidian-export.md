---
title: "Graphify → Obsidian Export — 知識圖譜轉 Vault 工作流"
type: concept
created: 2026-08-25
updated: 2026-08-30
sources: 1
tags: [knowledge-graph, obsidian, vault-integration, export-workflow]
collection: concepts
topics: [agent-research, knowledge-mgmt]
canonical: concepts/graphify-obsidian-export
---

> 將代碼知識圖譜（Knowledge Graph）匯出為 Obsidian vault，讓 AI Coding Agent 可以在更廣泛的專案脈絡中查詢圖譜。

## 核心概念

知識圖譜工具（如 Graphify）建立的圖譜通常**只在孤立環境中運作**——只知道自己被指向的 codebase 或文件集，不了解更大的專案脈絡。透過 `--obsidian` 參數，可以將 KG 自動轉換為 Obsidian vault 格式，讓圖譜節點成為可查詢的 markdown 檔案，並透過 backlinks 保持節點間的連接關係。

## 工作流程

```
Codebase / Docs
      │
      ▼
Graphify (--code-only 或含 LLM 語義提取)
      │
      ▼
Knowledge Graph (graph.json: nodes + edges + communities)
      │
      ▼
--obsidian flag
      │
      ▼
Obsidian Vault (每個 node → 一個 .md + backlinks)
      │
      ▼
Source-doc wiring (獨立步驟：連結 node 到原始文件)
      │
      ▼
可查詢的 Obsidian vault
```

## 關鍵技術細節

### Source-doc wiring 是必要但獨立的步驟
預設的 `--obsidian` 只產生**bare nodes**（標題 + 邊），對 Claude Code 的價值有限。必須額外執行 source-doc wiring，讓每個 node markdown 連結到原始文件，才能提供完整的查詢能力。

### 四種整合策略

將產出的 vault 整合進主 vault 的方式，依可逆性排列：

| 策略 | 說明 | 適用場景 |
|------|------|---------|
| **Standalone vault** | 獨立 vault，不混入主 vault | 只需要在 Obsidian 生態系中使用 |
| **Quarantine subfolder** | 放入主 vault 的獨立子資料夾 | 需要更大脈絡但想保持可刪除性 |
| **Piecemeal harvest** | 挑選需要的檔案，只匯入部分 | 大型 vault，只需要部分知識 |
| **Redistribution** | 重新分配到 vault 各處 | 需要完全整合，但難以回退 |

### 與 Vector RAG 的差異

| 維度 | Vector RAG | KG → Obsidian Export |
|------|-----------|---------------------|
| 結構感知 | 低 | 高（保留圖譜結構） |
| 可查詢性 | 需要額外工具 | 原生 Obsidian 查詢 |
| 人類可讀 | 低（embedding 空間） | 高（markdown + backlinks） |
| 跨模組關聯 | 弱 | 強（community detection） |

## 這個工作流適用於我們自己的 wiki 嗎？（2026-08-30 判斷）

**不適用。** `C:/Cheerio/Obsidian` 本身已經是一個手工策展的 Obsidian vault（entity/concept/source 分類、provenance、雙模型共識品質把關），不是「還沒變成知識庫」的原始素材，套用這套匯出工作流只會製造大量自動節點稀釋既有結構的訊噪比。這套工作流的適用對象是 Cheerio 手上**另外的程式碼專案**（跟這個 wiki 是不同的 repo），而且要搭配 `--code-only` 或限定掃描範圍，輸出放獨立 sidecar，不直接匯入主 vault。完整判斷過程見 [[wiki/decisions/graphify-vs-codebase-memory-mcp|Graphify vs codebase-memory-mcp 整合決策]]。

## 與「軟體世界模型」的差距

目前 KG → Obsidian Export 多為靜態語義拓撲圖，缺少：
- 動態執行期狀態變化
- 變數生命週期
- 動態分支執行（Dynamic Trace）
- 狀態轉移預測

（參考 [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] 的差距分析）

## 來源

- [[wiki/sources/2026-08-25-graphify-obsidian-cheat-code|Graphify + Obsidian + Claude Code = CHEAT CODE]]

## 相關頁面

- [[wiki/decisions/graphify-vs-codebase-memory-mcp|Graphify vs codebase-memory-mcp 整合決策]] — 是否採用此工作流的完整查證與結論
- [[wiki/entities/graphify|Graphify]] — 實作此工作流的工具
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — 被匯出的上游知識
- [[wiki/entities/obsidian|Obsidian]] — 匯入目標系統
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]] — 先前 Graphify 介紹
