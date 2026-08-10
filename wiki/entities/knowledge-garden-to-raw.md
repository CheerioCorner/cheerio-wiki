---
title: knowledge-garden-to-raw — Notion → Raw 抓取 Skill
type: entity
created: 2026-08-05
updated: 2026-08-09
sources: 1
tags: [skill, notion, raw, ingest]
collection: entities
topics: [knowledge-mgmt]
canonical: entities/knowledge-garden-to-raw
---

> 從 Notion 知識花園抓取頁面內容，建立 raw 來源檔案，準備進行 wiki ingest。
> **原名 `notion-to-raw`，2026-08-09 更名為 `knowledge-garden-to-raw`。**

## 基本資訊

| 項目 | 值 |
|------|-----|
| Skill 位置 | `~/.agents/skills/knowledge-garden-to-raw/SKILL.md` |
| 觸發詞 | 「花園裡那篇 X 要深入研究」、「從 Notion 研究」、Notion URL |
| 建立日期 | 2026-08-05 |
| 更名日期 | 2026-08-09 |
| 關聯工作 | W-2026-08-015 |

## 流程

```
Notion 頁面
    │
    ├──→ 📖 只是看看（摘要 + 回答問題）→ 結束
    │
    ├──→ 📝 直接寫 raw → wiki ingest
    │
    └──→ 🔬 深入研究（抓引用 URL → 搜尋 → 整合）
              │
              └──→ 決定是否寫 raw
```

### Phase 1：取得內容（永遠先做）
1. 解析輸入（URL / 種子名稱 / 選擇）
2. MCP 抓 Notion 頁面 markdown

### Phase 2：呈現 + 問下一步
3. 摘要呈現 + 問使用者想要做什麼

### Phase 3：根據選擇執行
- **A) 只是看看** → 回答問題，結束
- **B) 寫進 raw** → 建 raw/web/ → 提示 wiki-knowledge ingest
- **C) Deep research** → 抓引用 URL → 搜尋 → 整合 → 再問一次

## 設計決策

| 決策 | 理由 |
|------|------|
| 獨立 skill（非擴展 wiki-youtube） | 抓取機制不同（MCP vs Python API）、raw 格式不同、觸發詞不同 |
| 三條路徑（只是看看 / 寫 raw / deep research） | Notion 頁面是起點，不一定每次都要寫進 raw |
| 不在 skill 內處理 wiki ingest | 職責分離：knowledge-garden-to-raw 只負責 Notion → raw，wiki-knowledge 處理 raw → wiki |
| knowledge-garden 保持 wiki→Notion 方向 | 避免方向混亂 |

## 與其他 Skill 的關係

```
knowledge-garden（wiki → Notion）  ←→  knowledge-garden-to-raw（Notion → raw）
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
