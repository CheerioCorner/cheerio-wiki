---
title: 知識花園 Skill 架構
type: concept
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [skill, knowledge-garden, notion, architecture]
topics: [knowledge-mgmt, skill-cases-and-comparisons]
canonical: concepts/knowledge-garden-skill-architecture
---

> 知識花園相關 skills 的架構全景圖，描述 11 個 skill 的職責、資料流和關聯。
> 
> 2026-08-08：經 Copilot + Gemini 三輪討論，確立最終架構決策。

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

### Content 設計（2026-08-08 Redesign）

所有標的物使用**四層通用骨架**：

| 層 | 內容 |
|----|------|
| 第一層 | 標的識別與中繼資料 |
| 第二層 | 客觀事實層 |
| 第三層 | 主觀現實層（人的決策、想法、判斷） |
| 第四層 | 延伸路線圖（下一步） |

詳細設計見 [[wiki/concepts/content-redesign|Content 設計 Redesign]]。

### 工具

| Skill | 功能 |
|-------|------|
| `notion-cli` | ntn CLI 操作參考（被多個 skill 使用） |

---

## 全域資料流圖（2026-08-08 三輪討論後更新）

```
[輸入源 / User / Web Search]
        │
        ▼
[Agent 處理層]
  ├─ 啟發式判斷 / 顯式覆寫 (Mode: Seed / Topic / Map)
  ├─ 讀取 Schema (via knowledge-garden/schemas/seed_schema.yaml)
  └─ Evaluator-Optimizer Loop (非阻斷品質檢查)
        │
        ├─────────────────────────────────────────┐
        ▼                                         ▼
[元資料與結構判斷]                     [內容與模板生成]
 (knowledge-garden)                    (page-content)
        │                                         │
        └────────────────────┬────────────────────┘
                             ▼
              [持久層：Local Wiki (.md)]
                 ├─ 種子筆記 (Seed Notes)
                 ├─ 研究專題 (Research Topics)
                 └─ 視覺地圖 (Visual Maps - Mermaid/SVG)
                             │
                             ▼ (Cron Polling 同步引擎)
            [展示層：Notion Workspace]
                 ├─ 知識資料庫
                 ├─ 專題畫布頁面
                 └─ 視覺地圖區塊
```

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

## 三輪討論最終決策（Copilot + Gemini 共識）

### 決策 A：Schema 位置
- **決策**：`~/.agents/skills/knowledge-garden/schemas/seed_schema.yaml`（跟著主 skill 走）
- **理由**：全域資產，所有 skill 統一訪問，支援專案級覆寫
- **結構**：`properties`（Notion 欄位）+ `content_body`（頁面模板）

### 決策 B：knowledge-garden 模板處理
- **決策**：改為引用 page-content skill，刪除模板細節
- **理由**：職責分離，消除重複維護（SSOT）

### 決策 C：資料流擴展
- **決策**：Wiki = 持久層，Notion = 展示層，納入研究專題+視覺地圖
- **理由**：不需第三個儲存層，研究專題存在 wiki/topics/，視覺地圖存在 wiki/visualizations/

### 決策 D：事件同步
- **決策**：不需要事件總線/webhook，用 cron poll
- **理由**：個人 agent 系統不需要 webhook 架構

### 決策 E：品質關卡
- **決策**：Evaluator-Optimizer Loop（prompt 自我評估），非阻斷式
- **理由**：無程式碼環境下最實際的品質控制方式

### 決策 F：Mode 切換
- **決策**：啟發式判斷 + 顯式覆寫
- **理由**：兼顾自然語言流暢性與精確控制

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
