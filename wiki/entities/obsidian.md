---
title: "Obsidian — AI Agent 時代的知識管理基礎設施"
type: entity
created: 2026-08-09
updated: 2026-08-09
sources: 8
tags: [obsidian, knowledge-management, local-first, markdown, ai-agent, pkm]
collection: entities
topics: [knowledge-mgmt, ai-agent]
canonical: entities/obsidian
provenance:
  - kind: gemini-research
    path: raw/research/2026-08-09-obsidian-ai-knowledge-management.md
    url: https://obsidian.md
  - kind: notion-seed
    url: https://app.notion.com/p/3b75979e-3a8c81d8b7aaf1f4c88aa957
---

# Obsidian — AI Agent 時代的知識管理基礎設施

> 從疫情催生的個人專案，成長為 150 萬活躍用戶的 AI Agent 最佳知識儲存庫。

## 一句話

Obsidian 從 2020 年疫情期間的個人專案，成長為 150 萬活躍用戶的 AI Agent 最佳知識儲存庫；我們結合 OKF 的 raw → wiki → Notion 資料流，正是 Andrej Karpathy 提出的「LLM Wiki」範式的實踐。

---

## 為什麼 Obsidian 突然爆紅？

### 起源：疫情催生的「第二大腦」

Obsidian 由 Shida Li 和 Erica Xu 於 **2020 年 3 月** COVID-19 疫情期間推出。兩人在 University of Waterloo 相識，之前共同開發了大綱工具 Dynalist。隔離期間，他們決定實現長年構想的「個人知識庫」工具。

> *"I thought I was just weird to long for a personal knowledge base app like this, but you won't believe how many people told us they had tried or thought of doing the same thing for years or even decades."* — Erica Xu

### 爆紅的關鍵因素

| 因素 | 說明 | 時間點 |
|------|------|--------|
| **疫情紅利** | 全球居家隔離，人們有時間探索新工具 | 2020 |
| **本地端優先** | 資料存你自己的電腦，不綁雲端 | 核心理念 |
| **Markdown 永續性** | 純文字格式，100 年後仍可讀 | 核心理念 |
| **插件生態系** | 4,000+ 插件，像 VS Code 一樣可擴展 | 2020 起成長 |
| **PKM 運動** | Zettelkasten、Second Brain 概念流行 | 2020-2022 |
| **1.0 正式版** | 從 Beta 轉正，吸引主流用戶 | 2022-10 |
| **AI 時代契合** | LLM 天然吃 Markdown，成為 AI Agent 最佳伴侶 | 2024-2026 |

### 成長軌跡

```
2020-03  首次 Beta 發布（2 人團隊）
2022-10  v1.0.0 正式版（加入 Tabs、Canvas）
2023     ~100 萬用戶，Discord 11 萬成員
2023     Steph Ango（超級粉絲）成為 CEO
2025-05  Bases 功能（類 Notion 資料庫視圖）
2026-02  v1.12.0 官方 CLI
2026     ~150 萬活躍用戶，年增 22%
2026     插件下載量破 1.2 億次
```

### 為什麼它跟 Notion 不一樣？

> *"The fundamental difference between Obsidian and Notion is that Notion is a cloud service... With Obsidian, all your data is local."* — Steph Ango (CEO)

| 面向 | Obsidian | Notion |
|------|----------|--------|
| 資料位置 | 本地檔案（你擁有） | 雲端伺服器（他們擁有） |
| 離線使用 | 完全支援 | 有限 |
| 插件擴展 | 4,000+ 社群插件 | 封閉生態 |
| 定價 | 核心免費，Sync $4/月 | 全功能 $20/月/人 |
| AI 整合 | 由社群驅動，隱私優先 | 官方 AI Agent 套件 |
| 資料格式 | Markdown（開放標準） | 專有區塊格式 |

### CEO 的哲學

> *"We're not trying to take over the world. We're not trying to be the next Microsoft."*
> 
> *"If Obsidian went out of business someday, you would still have the app on your computer... you don't even have to launch the app."*

---

## LLM Wiki 新範式

### Andrej Karpathy 的洞見（2026-04）

> *"Obsidian 是 IDE，LLM 是程式員，Wiki 是程式碼庫。"*

| 維度 | 傳統 RAG | LLM Wiki |
|------|----------|----------|
| 運作 | 向量檢索 → LLM 臨時生成 | Agent 主動維護 Markdown |
| 知識成長 | 零 | **指數型累積** |
| 維護成本 | 低（但品質差） | AI 全權負責 |
| 適合工具 | 向量庫 + UI | **Obsidian + AI Agent** |

**關鍵洞察**：傳統 Zettelkasten 因人類缺乏精力維護而崩潰；LLM Wiki 範式下，維護工作全數交由 AI Agent 執行。

---

## 我們的實作 + OKF 整合

### 我們的資料流

```
raw/ ──wiki ingest──► wiki/ ──美化──► Notion 花園
 ▲                     │                  │
 │                     └──深入研究◄───────┘
 └─────────────────────────────────────────
```

### OKF 相容性：90%+

| OKF 要求 | 我們的現狀 | 狀態 |
|----------|-----------|------|
| type 屬性 | ✅ 已有 | ✅ |
| title | ✅ 已有 | ✅ |
| tags | ✅ 已有 | ✅ |
| timestamp | ✅ created/updated | ✅ |
| description | ⚠️ 部分有 | ⚠️ 需補齊 |
| provenance | ⚠️ 格式不一致 | ⚠️ 需統一 |

---

## 我們還需要投入什麼？（路線圖）

### 🔴 近期（1-2 週）
1. **Frontmatter 格式標準化** — 統一 provenance、description 欄位
2. **wiki-lint 強化** — 加入 frontmatter 格式自動檢查

### 🟡 中期（1-2 個月）
1. **OKF Enforcer 評估** — 自行開發或安裝插件
2. **孤兒筆記偵測** — 自動掃描無連結的頁面
3. **Plannotator → Notion 同步優化**

### 🟢 長期（3-6 個月）
1. **Local REST API 評估** — 如需精確區塊更動
2. **Dataview / Bases 應用** — 動態查詢
3. **學習日誌層** — 額外的知識沉澱層

---

## 來源

| # | Tier | 來源 | 日期 |
|---|------|------|------|
| 1 | T1 | Wikipedia: Obsidian (software) | 2026-08 |
| 2 | T2 | Ness Labs: Exploring the power of note-making with Erica Xu | 2021-08 |
| 3 | T2 | The Verge: Obsidian's CEO on productivity tools (Steph Ango) | 2025-08 |
| 4 | T2 | Medium: Andrej Karpathy - The LLM Wiki Pattern | 2026-04 |
| 5 | T1 | Google Cloud: OKF Specification v0.1 | 2026-06 |
| 6 | T1 | Obsidian Help Official Documentation | 2026 |
| 7 | T2 | scoding.kr: Obsidian 2026 - 1.5 Million Users | 2026-05 |
| 8 | T2 | XDA: Why people are obsessed with Obsidian | 2026 |

---

## 相關頁面

- [[wiki/topics/knowledge-mgmt|知識管理]] — 本種子所屬 topic
- [[wiki/concepts/okf-open-knowledge-format|OKF]] — Open Knowledge Format
- [[wiki/concepts/pi-project-workspace-model|Pi Project Workspace Model]] — 提到 Obsidian 作為跨專案知識方案
- [[wiki/sources/2026-08-08-plannotator-obsidian-presentation|Plannotator × Obsidian 簡報企劃]]
- [[wiki/visualizations/obsidian-seed-map|Obsidian 視覺地圖]] — 功能與延伸應用全景圖

---

## Notion 種子

- **Notion Link**：[Obsidian — AI Agent 時代的知識管理基礎設施](https://app.notion.com/p/3b75979e-3a8c81d8b7aaf1f4c88aa957)
- **成長狀態**：🌱 種子期
- **種下日期**：2026-08-09
- **視覺地圖**：[🗺️ 視覺地圖](https://app.notion.com/p/3b75979e-3a8c81d48183d3d1fe3ad84c)
