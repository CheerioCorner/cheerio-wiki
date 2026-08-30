---
title: "Graphify + Obsidian + Claude Code = CHEAT CODE"
type: source
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [knowledge-graph, obsidian, claude-code, graphify, vault-integration]
collection: sources
topics: [agent-research, knowledge-mgmt]
canonical: sources/2026-08-25-graphify-obsidian-cheat-code
provenance_raw: "raw/youtube/2026-08-25-graphify-obsidian-claude-code-cheat-code.md"
provenance_url: "https://www.youtube.com/watch?v=mWLDn49_8HA"
---

# Graphify + Obsidian + Claude Code = CHEAT CODE

> 來源：[YouTube 影片](https://www.youtube.com/watch?v=mWLDn49_8HA)
> 原始逐字稿：[[raw/youtube/2026-08-25-graphify-obsidian-claude-code-cheat-code|raw transcript]]

## 一句話

將 Graphify 產生的代碼知識圖譜匯出為 Obsidian vault，讓 Claude Code 可以在更廣泛的 vault 脈絡中查詢知識圖譜。

## 為什麼要結合

Graphify 的知識圖譜雖然強大，但**只在孤立環境中運作**（in a vacuum）——它只知道自己被指向的那個 codebase 或文件集，不了解更大的專案脈絡 `[00:55]-[01:22]`。Obsidian vault 則提供了廣泛的專案脈絡。結合兩者後，Claude Code 可以在 vault 脈絡中查詢知識圖譜 `[00:29]-[00:53]`。

## 核心機制：`--obsidian` 參數

Graphify 內建 `--obsidian` 參數，自動將知識圖譜轉換為 Obsidian vault `[03:53]-[04:20]`：
- 每個 KG node 自動轉為一個 markdown 檔案
- 自動建立 backlinks（節點間的連接）
- 產生 591 個 markdown 檔案 + 685 條對應連結 `[07:25]-[07:54]`

⚠️ **Source-doc wiring 是獨立步驟**：預設只產生 bare nodes（標題 + 邊），沒有原始文件內容。需要額外指令「pull the source docs in and wire every node to its origin」才能讓每個 node 連結到原始文件 `[12:08]-[13:16]`。

## Demo 數據

以 Claude Code 官方文件為例 `[04:20]-[06:00]`：
- 下載 171 頁文件
- Graphify 提取 145 個文件 → 591 個 nodes、685 條 connections、67 個 communities
- 匯入 Obsidian 後產生 658 個 concept stubs + 146 個完整 source documents `[13:59]-[14:24]`

## 四種整合策略

將 Graphify 產出的 vault 整合進主 vault 的四種方式，依可逆性排列 `[08:21]-[10:47]`：

| 策略 | 說明 | 可逆性 |
|------|------|--------|
| **1. Standalone vault** | 預設行為，獨立 vault，不混入主 vault | ✅ 完全可逆 |
| **2. Quarantine subfolder** | 放入主 vault 的獨立子資料夾（如 `graph-imports/`） | ✅ 刪一個資料夾即可 |
| **3. Piecemeal harvest** | 讓 Claude Code 從中挑選需要的檔案，只匯入部分 | ⚠️ 部分可逆 |
| **4. Redistribution** | 讓 Claude Code 重新分配到 vault 各處 | ❌ 最難回退 |

講者推薦：先建 standalone vault → 再作為 subfolder 匯入主 vault `[10:47]-[11:14]`。

## 跨平台通用性

Graphify 支援 20+ 個 AI coding assistant 平台：Claude Code、Cursor、Codex、Gemini CLI、GitHub Copilot、Pi coding agent、Aider、Hermes 等。**不限定 Claude Code** `≈[03:53]`。

## 與先前影片的差異

本影片是先前 [Wow 5支AI Agent研究](https://www.youtube.com/watch?v=luN-yydHpYY) 中 Graphify 介紹的**延伸**。前一支影片專注於「如何建立知識圖譜」，本影片專注於「建立後如何匯入 Obsidian 並融入更大脈絡」。

## 陳述級溯源

| 陳述 | 時間戳 |
|------|--------|
| Graphify 產生 591 nodes、685 connections、67 communities | `[05:15]-[06:00]` |
| Source-doc wiring 是獨立步驟 | `[12:08]-[13:16]` |
| 四種整合策略 | `[08:21]-[10:47]` |
| 匯入後 658 concept stubs + 146 source documents | `[13:59]-[14:24]` |
| 支援 20+ 平台 | `≈[03:53]` |

## 來源

- [[raw/youtube/2026-08-25-graphify-obsidian-claude-code-cheat-code|原始逐字稿]]

## 相關頁面

- [[wiki/entities/graphify|Graphify]] — 實作此工作流的工具
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — 被匯出的上游知識
- [[wiki/entities/obsidian|Obsidian]] — 匯入目標系統
- [[wiki/concepts/graphify-obsidian-export|Graphify → Obsidian Export]] — 本影片描述的工作流概念
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]] — 先前 Graphify 介紹影片
