---
title: notion-to-raw — Notion → Raw 抓取 Skill
type: entity
created: 2026-08-05
updated: 2026-08-05
sources: 1
tags: [skill, notion, raw, ingest]
collection: entities
topics: [knowledge-mgmt]
canonical: entities/notion-to-raw
---

> 從 Notion 知識花園抓取頁面內容，建立 raw 來源檔案，準備進行 wiki ingest。

## 基本資訊

| 項目 | 值 |
|------|-----|
| Skill 位置 | `~/.agents/skills/notion-to-raw/SKILL.md` |
| 觸發詞 | 「花園裡那篇 X 要深入研究」、「從 Notion 研究」、Notion URL |
| 建立日期 | 2026-08-05 |
| 關聯工作 | W-2026-08-015 |

## 流程

```
使用者觸發（種子名稱 / Notion URL）
    ↓
Step 1: 查 manifest → 找到 Page ID
    ↓
Step 2: MCP 抓 Notion 頁面 markdown
    ↓
Step 3: 建立 raw/web/<YYYY-MM-DD>-<slug>.md
    ↓
Step 4: 更新花園 manifest（紀錄研究事件）
    ↓
Step 5: 提示下一步 → wiki-knowledge ingest
```

## 設計決策

| 決策 | 理由 |
|------|------|
| 獨立 skill（非擴展 youtube-to-wiki） | 抓取機制不同（MCP vs Python API）、raw 格式不同、觸發詞不同 |
| 不在 skill 內處理 wiki ingest | 職責分離：notion-to-raw 只負責 Notion → raw，wiki-knowledge 處理 raw → wiki |
| knowledge-garden 保持 wiki→Notion 方向 | 避免方向混亂 |

## 與其他 Skill 的關係

```
knowledge-garden（wiki → Notion）  ←→  notion-to-raw（Notion → raw）
         ↕                                    ↕
    wiki-knowledge（raw → wiki）         wiki-knowledge（raw → wiki）
```

## 來源

- 決策過程：2026-08-05 對話（W-2026-08-015）

## 相關頁面

- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — 後續 wiki ingest 流程
- [[wiki/entities/knowledge-garden|knowledge-garden]] — 花園 manifest（前段查詢）
- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器
- [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]] — 架構討論
