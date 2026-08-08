---
title: 知識花園 Skill 架構
type: concept
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [skill, knowledge-garden, notion, architecture]
topics: [knowledge-mgmt, skill]
canonical: concepts/knowledge-garden-skill-architecture
---

> 知識花園相關 skills 的架構全景圖，描述 11 個 skill 的職責、資料流和關聯。

---

## 架構全景圖

```
                    ┌─────────────────────────────────┐
                    │         Notion 知識花園           │
                    └──────────┬──────────┬────────────┘
                               │          │
              ┌────────────────┘          └────────────────┐
              │ wiki → Notion                              │ Notion → wiki
              ▼                                            ▼
    ┌─────────────────┐                        ┌─────────────────────┐
    │ knowledge-garden │◄── knowledge-garden-  │ knowledge-garden-   │
    │ (維護主 skill)    │    page-content       │ to-raw (抓取頁面)   │
    └───────┬─────────┘    (研究+寫入)         └────────┬────────────┘
            │                                           │
            │    ┌──────────────────────┐               │
            ├────│ knowledge-garden-    │               │
            │    │ visualmap            │               │
            │    │ (讀種子資料→畫圖)     │               │
            │    └──────────┬───────────┘               │
            │               │                           │
            │               ▼                           │
            │    ┌──────────────────────┐               │
            │    │    notion-cli        │               │
            │    │  (ntn pages update)  │◄──────────────┘
            │    │  (上傳 SVG)          │
            │    └──────────────────────┘
            │              ▲
            │              │
            │    ┌────────┴─────────────┐
            │    │ knowledge-garden-    │
            │    │ page-content         │
            │    │ (ntn pages update)    │
            │    └──────────────────────┘
            │
            ▼
    ┌─────────────────┐      ┌──────────────────────┐
    │   wiki-ingest    │◄─────│ knowledge-garden-     │
    │   wiki-query     │      │ trigger (觸發+回流)   │
    │   wiki-lint      │      └──────────────────────┘
    └─────────────────┘
```

---

## Skill 清單

### knowledge-garden-*（Notion 花園操作）

| Skill | 方向 | 核心功能 |
|-------|------|----------|
| `knowledge-garden` | wiki→Notion | 種子 CRUD、灌溉、查詢、巡檢、manifest 同步 |
| `knowledge-garden-trigger` | 雙向 | 偵測觸發條件（💡/過期/待同步）→ 執行回流 |
| `knowledge-garden-page-content` | wiki→Notion | 先研究來源，再根據成長階段產生完整頁面內容 |
| `knowledge-garden-to-raw` | Notion→wiki | 抓取 Notion 頁面 → raw → wiki ingest |
| `knowledge-garden-visualmap` | 產出 | Mermaid → SVG → Notion 視覺地圖 |

### wiki-*（大腦操作）

| Skill | 方向 | 核心功能 |
|-------|------|----------|
| `wiki-ingest` | raw→wiki | 讀 raw → 建/更新 wiki 頁面 → 更新 index/log |
| `wiki-query` | 查詢 | 讀 index → 讀頁面 → 回答問題 |
| `wiki-lint` | 檢查 | 掃描全部頁面 → 找問題 → 提清單 |
| `wiki-youtube` | YouTube→wiki | 抓字幕 → 建 source note → 建 entity pages |
| `wiki-pdf` | PDF→wiki | markitdown 轉 Markdown → 提取圖片 → 建頁面 |

### 工具

| Skill | 功能 |
|-------|------|
| `notion-cli` | ntn CLI 操作參考（被多個 skill 使用） |

---

## 資料流方向

### 路徑 A：wiki → Notion（整理到花園）
```
wiki 頁面 → knowledge-garden（美化/整理）→ knowledge-garden-page-content（研究+寫入）→ notion-cli → Notion
```

### 路徑 B：Notion → wiki（深入研究）
```
Notion 頁面 → knowledge-garden-to-raw（抓取）→ raw/web/ → wiki-ingest → wiki 頁面
```

### 路徑 C：觸發回流（自動/半自動）
```
Notion 觸發條件 → knowledge-garden-trigger（偵測+評估）→ 路徑 A 或 路徑 B
```

### 路徑 D：視覺地圖
```
種子/專題資料 → knowledge-garden-visualmap（Mermaid→SVG）→ notion-cli → Notion
```

### 路徑 E：外部來源 ingest
```
YouTube/PDF/URL → wiki-youtube / wiki-pdf → raw/ → wiki-ingest → wiki 頁面
```

---

## 關鍵設計決策

### 2026-08-08：大重構
1. **拆分** `wiki-knowledge` → `wiki-ingest` + `wiki-query` + `wiki-lint`
2. **合併** `knowledge-garden-trigger` + `notion-wiki-feedback` → `knowledge-garden-trigger`
3. **重寫** `notion-page-content` → `knowledge-garden-page-content`
4. **Rename** `notion-to-raw` → `knowledge-garden-to-raw`
5. **Rename** `youtube-to-wiki` → `wiki-youtube`、`pdf-to-wiki` → `wiki-pdf`
6. **Delete** `wiki-knowledge`、`notion-wiki-feedback`
7. **命名規則**：`knowledge-garden-*` = Notion 花園、`wiki-*` = 大腦

### 為什麼這樣設計
- **拆分**：ingest/query/lint 三個流程完全不同，拆開更容易維護
- **合併**：trigger 和 feedback 高度重疊，合併避免衝突
- **重寫**：舊模板只讀 Properties 就產內容，品質差；新版先 fetch 來源 + 讀 wiki
- **命名**：`knowledge-garden-*` = Notion 花園操作、`wiki-*` = 大腦操作（含未來 ppt/word/mp4）

---

## 相關頁面

- [[wiki/entities/knowledge-garden|knowledge-garden]] — 花園 manifest
- [[wiki/entities/knowledge-garden-to-raw|knowledge-garden-to-raw]] — Notion → raw 抓取
- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器
