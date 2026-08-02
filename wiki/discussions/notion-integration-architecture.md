---
title: Notion Integration Architecture
type: discussion
collection: discussions
topics: [knowledge-mgmt, notion, skill]
status: open
created: 2026-08-03
updated: 2026-08-03
sources: 1
tags: [notion, knowledge-garden, architecture, sync]
provenance:
  - kind: conversation
    path: raw/conversations/
---

# Notion Integration Architecture

## 兩個系統的分工

| 系統 | 用途 | 主要使用者 |
|------|------|-----------|
| **Obsidian Wiki** | AI 運作、知識維護、raw → wiki 整理 | AI（讀寫） |
| **Notion 花園** | 人類閱讀、報告、知識分享、美化呈現 | 人類（閱讀） |

### 關鍵洞察
- 人類會請 AI「整理/美化」wiki 內容 → 放進 Notion
- Notion 是「成品展示區」，wiki 是「作業工廠」
- 雙向流程：wiki 整理完 → 美化到 Notion → 人類閱讀後 → 可能觸發新的研究

## 完整資料流

```
raw/ ──wiki ingest──► wiki/ ──美化/整理──► Notion 花園
 ▲                     │                      │
 │                     │                      │
 │    ┌────────────────┘                      │
 │    │                                       │
 │    └──── 「這個要深入研究」 ◄───────────────┘
 │          （人類給 URL 或指定頁面）
 │          （AI 去抓內容）
 │
 └─────────────────────────────────────────────┘
```

## 觸發詞定義

| 人類說 | AI 做 | 資料流 |
|--------|-------|--------|
| 「存進大腦」 | wiki ingest | raw → wiki |
| 「整理到花園」/「美化」 | 整理 + 美化 | wiki → Notion |
| 「花園裡有什麼」 | 查 manifest | wiki manifest → 回答 |
| 「花園裡那篇 [X] 要深入研究」 | 去 Notion 抓內容 → 建 raw | Notion → raw → wiki |

## Notion → Raw 流程

1. 人類提供 URL 或指定花園裡的某篇
2. AI 去抓 Notion 頁面內容（使用 MCP 或 ntn CLI）
3. 建立 raw 來源（`raw/web/` 或 `raw/conversations/`）
4. 進行 wiki ingest

## Garden Manifest 機制

在 `wiki/entities/knowledge-garden.md` 建立花園索引：
- 種子清單（標題、stage、Notion link）
- 最近更新紀錄
- 避免每次都查 Notion API

### 運作方式
1. **查詢時**：先讀 wiki manifest → 快速回答 → 需要詳細內容再去 Notion
2. **更新時**：修改 Notion 後 → 同步更新 wiki manifest

## Skills 分工

| Skill | 職責 | 引用 |
|-------|------|------|
| `knowledge-garden` | 花園操作流程（種子/灌溉/查詢） | 引用 `notion-cli` |
| `notion-cli` | CLI 命令參考（純 reference） | 獨立 |

## 相關頁面

- [[wiki/entities/knowledge-garden|knowledge-garden]] — 花園 manifest
- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Wiki 操作 skill

## 待確認

- [x] Notion → raw 抓取的具體實作方式 → **已解決**：新建 `notion-to-raw` skill（2026-08-05）
- [ ] 是否需要建立「研究觸發」skill
- [ ] Garden manifest 的更新時機與方式
