---
title: knowledge-garden — Notion 知識花園 Manifest
type: entity
created: 2026-08-03
updated: 2026-08-07
sources: 1
tags: [notion, knowledge-garden, manifest]
collection: entities
topics: [knowledge-mgmt]
canonical: entities/knowledge-garden
---

> **Notion 知識花園**的本地索引/快取。避免每次都查詢 Notion API。

## 花園基本資訊

| 項目 | 值 |
|------|-----|
| Notion 主頁 | https://app.notion.com/p/3ac5979e-3a8c-81d2-b96f-f6c7bdd8fd33 |
| Page ID | `3ac5979e-3a8c-81d2-b96f-f6c7bdd8fd33` |
| 知識花園 Database | https://app.notion.com/p/f3aa419a348b4c66a8a131b67780ebf3 |
| Database ID | `f3aa419a-348b-4c66-a8a1-31b67780ebf3` |
| Data Source ID | `0785b58a-9976-4163-85be-6854410b6563` |
| 建立日期 | 2026-07-11 |
| 維護者 | Cheerio + Pi |
| 最後同步 | 2026-08-05 |

---

## 花園結構

```
🌳 知識花園
├── callout（標題 + 🌱→🌿→🌲 說明）
│   ├── 📊 知識花園 Database（inline）
│   ├── ### 種植守則
│   └── 相關連結
├── ## 研究專題
│   └── 🔬 Meta-Harness
└── 研究專題（子頁面）
```

---

## Database 結構

### Properties（11 個）

| 欄位 | 類型 | 說明 |
|------|------|------|
| 種子 | Title | 種子名稱 |
| 成長狀態 | Select | 🌱 種子期 / 🌿 成長期 / 🌳 成熟期 |
| Tags | Multi-select | 🔬 研究 / 🛠️ 實作 / 📝 筆記 / 💡 靈感 / 📚 學習 / 🎯 研究專題 |
| 來源 URL | URL | 原始來源（最初發現/學習的來源） |
| 視覺地圖頁面 | URL | 該種子的視覺地圖/架構圖頁面連結 |
| 給我的啟發？ | Rich text | 心得、靈感 |
| 備註 | Rich text | 額外備註 |
| 🔬 研究專題 | Relation | 所屬研究專題（雙向關聯） |
| Wiki GitHub | URL | 對應的 wiki 頁面 GitHub 連結 |
| 種下日期 | Date | 首次建立時間 |
| 最後更新 | Date | 最近一次灌溉/更新 |

---

## 花園地圖

### 🌿 成長期

| 種子名稱 | Notion Link | Wiki 來源 | 視覺地圖 |
|----------|-------------|-----------|----------|
| Plannotator — AI Agent 視覺化審閱工具 | [連結](https://app.notion.com/p/3b35979e3a8c817c8ffce30d8361b92a) | [[wiki/entities/plannotator\|plannotator]] | 🗺️ [視覺地圖](https://app.notion.com/p/3ad5979e3a8c811f-a76e-e94a6bb808df) |
| NPM Publishing Workflow — 從零建立 NPM 套件發佈流程 | [連結](https://app.notion.com/p/3b35979e3a8c811abe94cf22af3ce7ef) | — |

### 🌱 種子期

| 種子名稱 | Notion Link | Wiki 來源 |
|----------|-------------|-----------|
| OKF (Open Knowledge Format) — AI 時代的知識格式 | [連結](https://app.notion.com/p/3b35979e3a8c81e49ef9cadf81c4c318) | [[wiki/concepts/okf-open-knowledge-format\|okf-open-knowledge-format]] |
| mattpocock/skills — 第一棵樹苗 | [連結](https://app.notion.com/p/3b35979e3a8c81a5b198f2c28fa2c192) | [[wiki/entities/mattpocock-skills\|mattpocock-skills]] |
| Omnigent — Databricks 開源 meta-harness | [連結](https://app.notion.com/p/3b35979e3a8c811c9240d23bc23317ae) | [[wiki/entities/omnigent\|omnigent]] |
| Agent Extension 安裝位置與方式 | [連結](https://app.notion.com/p/3b35979e3a8c81538dd9cd03e458d911) | [[wiki/concepts/agent-extension-installation\|agent-extension-installation]] |
| OpenCodeReview (OCR) — AI 代碼審查 CLI 工具 | [連結](https://app.notion.com/p/3b55979e3a8c81c88283f0e3eaf4f9d6) | [[wiki/entities/open-code-review\|open-code-review]] |

---

## 研究專題

### 🔬 Meta-Harness（元鞍具）
> 對 harness 本身的再抽象/最佳化層。有學術（自動演化 harness）與業界（多 agent orchestration）兩種定義。

- **狀態：** 研究中
- **樹苗：** Omnigent（🌱 種子期）
- **Wiki：** [[wiki/concepts/meta-harness|meta-harness]]

---

## 研究專題 Database

| 項目 | 值 |
|------|-----|
| Database ID | `f29355ae-1f10-4ba6-9bcb-960efd57e547` |
| Data Source ID | `438cfd50-5ff8-4d77-a31e-4123df13934b` |
| 位置 | Cheer Corner 主頁（inline） |

### 專題清單

| 專題 | 狀態 | 進度 | 相關種子 |
|------|------|------|----------|
| 🤖 AI Agent 架構研究 | 🔬 研究中 | 🌿 發展中 | Plannotator, mattpocock/skills, Omnigent |
| 🔌 Extension 開發生態 | 🔬 研究中 | 🌱 起步 | NPM Publishing, OKF, Agent Extension |
| 🧠 Meta-Harness 元鞍具 | 🔬 研究中 | 🌱 起步 | Omnigent |
| 🛠️ AI Coding Workflow | 🔬 研究中 | 🌿 發展中 | mattpocock/skills |
| 📚 知識管理系統 | 🌿 進行中 | 🌿 發展中 | OKF |

---

## 種植守則

1. **交叉授粉** — 不同植物之間的連結，往往是最有價值的
2. **定期修剪** — 有些想法需要斷捨離
3. **允許變形** — 種子長出來可能跟預期不同，那是好事
4. **不急於收割** — 知識需要時間沉澱

---

## 最近更新紀錄

| 日期 | 事件 | 種子 |
|------|------|------|
| 2026-07-11 | 花園建立 | — |
| 2026-07-11 | 種下 mattpocock/skills | mattpocock/skills |
| 2026-07-30 | 種下 Plannotator | Plannotator |
| 2026-07-30 | 種下 Omnigent | Omnigent |
| 2026-07-31 | 種下 NPM Publishing Workflow | NPM Publishing |
| 2026-07-31 | 種下 Agent Extension 安裝位置 | Agent Extension |
| 2026-08-10 | 種下 OKF (Open Knowledge Format) | OKF |
| 2026-08-05 | 建立新 Database（inline 於花園主頁） | — |
| 2026-08-05 | 遷移 6 顆種子到新 Database | 全部 |
| 2026-08-05 | 建立 6 個新頁面（套用模板格式） | 全部 |
| 2026-08-05 | 補齊「給我的啟發？」欄位 | 全部 |
| 2026-08-05 | 更新 SKILL.md（結構、來源 URL 定義） | — |
| 2026-08-06 | 調整 Database 欄位：移除「一句話」，新增「視覺地圖頁面」 | — |
| 2026-08-06 | 重建 mattpocock/skills 視覺地圖頁面 | mattpocock/skills |
| 2026-08-06 | 建立研究專題 Database（5 個專題） | — |
| 2026-08-06 | 映射 6 顆種子到專題 | 全部 |
| 2026-08-07 | 種下 OpenCodeReview (OCR) | OpenCodeReview |
| 2026-08-07 | 花園內容檢查：發現連結缺口 | — |
| 2026-08-07 | 建立種子 ↔ 專題雙向 Relation 關聯 | 全部 |
| 2026-08-07 | 建立 Plannotator 視覺地圖頁面 | Plannotator |

---

## ⚠️ 待修問題

| 問題 | 說明 | 處理方式 |
|------|------|----------|
 | OKF 缺專題連結 | OKF 種子沒有連結到 Extension 開發生態 | 更新 Database relation |
| OpenCodeReview 缺專題連結 | OCR 種子沒有連結到 AI Agent 架構研究 | 更新 Database relation |
| 知識管理系統缺種子 | 該專題沒有任何種子 | 尋找或建立相關種子 |

---

## 維護規則

1. **每次 Notion 寫入操作後**，自動同步更新本頁面（詳見 `knowledge-garden` skill §Manifest 自動同步）
2. **查詢花園時**，先讀本頁 → 快速回答 → 需要詳細內容再去 Notion
3. **新種子**加入時，同時更新「最近更新紀錄」表格
4. **Database** 為主要資料來源，本 manifest 為離線快取

---

## 相關頁面

- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Wiki 操作 skill
- [[wiki/entities/notion-to-raw|notion-to-raw]] — Notion → Raw 抓取 skill
- [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]] — 架構討論
- [Design Spec](work/designs/knowledge-garden-navigator.md) — 導覽頁設計規格
