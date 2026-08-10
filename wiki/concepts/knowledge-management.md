---
title: Knowledge Management
type: concept
created: 2026-08-10
updated: 2026-08-10
tags: [knowledge-management, wiki, notion, ingest, query, lint]
topics: [knowledge-mgmt]
canonical: concepts/knowledge-management
---

# Knowledge Management

> 系統化地收集、組織、檢索、維護知識的方法論與工具鏈。

## 核心公式

```
Knowledge Management = Ingest + Query + Lint + 回流
```

知識管理不是「存檔」，而是「讓知識流動並相互幫助」。

---

## 雙層架構

```
┌─────────────────────────────────────────────┐
│              Notion 知識花園                  │
│    （種子、研究專題、視覺地圖、成長追蹤）     │
└──────────────────┬──────────────────────────┘
                   │ 回流（種子成熟 → 大腦）
                   ▼
┌─────────────────────────────────────────────┐
│              Obsidian 大腦                   │
│    （wiki/、sources/、concepts/、entities/） │
└─────────────────────────────────────────────┘
```

| 層級 | 角色 | 工具 |
|------|------|------|
| **花園（Notion）** | 培育種子、視覺化成長 | Notion + knowledge-garden skills |
| **大腦（Obsidian）** | 結構化知識、快速檢索 | Obsidian + wiki-knowledge skills |

---

## 三個操作

### 1. Ingest（吸收）

> 讓所有資料都能被看見

```
raw/ → 查詢既有 wiki → 合成新知識 → Staging → 人類確認 → 寫入 wiki
```

| 步驟 | 說明 |
|------|------|
| 讀取 raw | 從 raw/web、raw/youtube、raw/notion-ingest 讀取 |
| 查詢 wiki | 避免重複，自動建立交叉引用 |
| 合成知識 | 不篩選，只結構化 |
| Staging Buffer | 安全隔離，21 天 TTL |
| 人類確認 | 最終寫入決策權在人類 |

### 2. Query（查詢）

> 看見關聯，突破創新

```
問問題 → 讀 index → 讀相關頁面 → 合成有引用回答 → 回填
```

| 步驟 | 說明 |
|------|------|
| 讀 index | 快速定位相關頁面 |
| 追溯交叉引用 | 發現隱藏關聯 |
| 合成回答 | 每個主張都要有引用 |
| 回填 | 高價值洞察 → Staging Buffer |

### 3. Lint（健康檢查）

> 確保知識品質，持續成長

| 檢查項目 | 說明 |
|---------|------|
| 結構完整性 | frontmatter、provenance 格式 |
| 孤立頁面 | 沒有 inbound links 的頁面 |
| 矛盾偵測 | 頁面間互相矛盾的述句 |
| 過時檢查 | 半衰期衰減評估 |
| Source Fidelity | 忠實原始來源 |

---

## 一個機制：知識幫助知識

```
查詢結果 → 信心度評估 → Staging → 人類確認 → 正式知識
```

| 信心度 | 處理方式 |
|--------|---------|
| S ≥ 0.88 | auto_verified → Staging Buffer |
| 0.70 ≤ S < 0.88 | draft_backfill → Staging Buffer |
| S < 0.70 | 廢棄（但要告訴人類為什麼） |

---

## 一個回流：花園 → 大腦

```
Notion 花園（種子成熟）→ 觸發 Ingest → Obsidian 大腦
```

### 觸發條件

1. 種子狀態從 🌱 變為 🌳（成熟）
2. 研究專題有新發現
3. 視覺地圖顯示新的關聯
4. 人類主動說「這個值得深入研究」

---

## 工具鏈

### Wiki 操作（大腦）

| Skill | 職責 |
|-------|------|
| `wiki-ingest` | 從 raw 建立/更新 wiki 頁面 |
| `wiki-query` | 查詢 wiki 內容 |
| `wiki-lint` | 健康檢查 |

### 花園操作（Notion）

| Skill | 職責 |
|-------|------|
| `knowledge-garden` | 維護 Notion 知識花園 |
| `knowledge-garden-to-raw` | Notion → raw 抓取 |
| `knowledge-garden-page-content` | 產生並寫入頁面內容 |
| `knowledge-garden-visualmap` | 產生視覺地圖 |
| `knowledge-garden-trigger` | 偵測觸發條件並執行回流 |
| `notion-cli` | Notion API 操作 |

### 學習系統

| Skill | 職責 |
|-------|------|
| `learning-loop` | 自動學習優化系統 |
| `work-tracker` | 可追溯工作管理 |

---

## 知識類型

| 類型 | 目錄 | 說明 |
|------|------|------|
| **Concepts** | `wiki/concepts/` | 抽象知識（概念、方法論） |
| **Entities** | `wiki/entities/` | 具體事物（工具、人物、專案） |
| **Sources** | `wiki/sources/` | 原始資料的消化結果 |
| **Decisions** | `wiki/decisions/` | 決策紀錄 |
| **Discussions** | `wiki/discussions/` | 討論紀錄 |
| **Topics** | `wiki/topics/` | 主題導航頁 |

---

## 資料流向

```
外部資料
    ↓
raw/（原始資料，唯讀）
    ↓ Ingest
wiki/sources/（消化後的 source notes）
    ↓ 合成
wiki/concepts/ + wiki/entities/（結構化知識）
    ↓ 組織
wiki/topics/（主題導航）
    ↓ 維護
wiki/lint（品質檢查）
    ↓ 回流
Notion 花園（種子培育）
```

---

## 相關頁面

### 核心概念
- [[wiki/concepts/knowledge-system-architecture|知識系統架構]] — 三個操作 + 一個機制 + 一個回流 🛠️
- [[wiki/concepts/knowledge-garden-skill-architecture|知識花園 Skill 架構]] — 11 個 skill 的全景圖 🛠️
- [[wiki/concepts/okf-open-knowledge-format|OKF]] — Open Knowledge Format
- [[wiki/concepts/learning-loop|learning-loop]] — 自動學習優化系統

### 工具
- [[wiki/entities/knowledge-garden|knowledge-garden]] — Notion 知識花園 Manifest
- [[wiki/entities/knowledge-garden-to-raw|knowledge-garden-to-raw]] — Notion → raw 抓取流程 🛠️
- [[wiki/entities/notionApi|notionApi]] — Notion MCP server
- [[wiki/entities/markitdown|MarkItDown]] — 萬能格式→Markdown 轉換器
- [[wiki/entities/obsidian|Obsidian]] — 知識管理平台

### Decisions
- [[wiki/decisions/knowledge-system-architecture-decision|知識系統架構改進決策]] — 4 輪圓桌會議決策
- [[wiki/decisions/shared-wiki-collection-model|Shared Wiki Collection Model]] — 共享 wiki collection 架構
- [[wiki/decisions/raw-conversation-promotion-workflow|Raw Conversation Promotion Workflow]] — 對話紀錄提升流程

### Topics
- [[wiki/topics/knowledge-mgmt|Knowledge Management]] — 知識管理導航頁
