---
title: knowledge-garden — Cheerio 知識花園 Manifest
type: entity
created: 2026-08-03
updated: 2026-08-08
sources: 1
tags: [notion, knowledge-garden, manifest]
collection: entities
topics: [knowledge-mgmt]
canonical: entities/knowledge-garden
---

> **Notion 知識花園**的架構藍圖與離線快取。
> 
> ⚠️ 本頁面只記錄花園的**結構配置**，不記錄操作日誌。操作紀錄請查 `work/history/`。

---

## 花園基本資訊

| 項目 | 值 |
|------|-----|
| Notion 主頁 | https://app.notion.com/p/3ac5979e-3a8c-81d2-b96f-f6c7bdd8fd33（Cheerio 知識花園） |
| Page ID | `3ac5979e-3a8c-81d2-b96f-f6c7bdd8fd33` |
| 知識花園 Database | https://app.notion.com/p/f3aa419a348b4c66a8a131b67780ebf3 |
| Database ID | `f3aa419a-348b-4c66-a8a1-31b67780ebf3` |
| Data Source ID | `0785b58a-9976-4163-85be-6854410b6563` |
| 研究專題 Database | https://app.notion.com/p/f29355ae1f104ba69bcb960efd57e547 |
| 研究專題 Database ID | `f29355ae-1f10-4ba6-9bcb-960efd57e547` |
| 研究專題 Data Source ID | `438cfd50-5ff8-4d77-a31e-4123df13934b` |
| 視覺地圖 Database | https://app.notion.com/p/5f2a0e0f-91de-466f-879e-9042c8a02169 |
| 建立日期 | 2026-07-11 |
| 維護者 | Cheerio + Pi |

---

## 花園結構

```
🌳 知識花園
├── callout（標題 + 🌱→🌿→🌲 說明）
│   ├── 📊 知識花園 Database（inline）
│   ├── ### 種植守則
│   └── 相關連結
├── ## 研究專題
│   └── 🔬 各專題子頁面
└── 🗺️ 視覺地圖 Database
```

---

## 知識花園 Database Schema（10 Properties）

| 欄位 | 類型 | 說明 |
|------|------|------|
| 種子 | Title | 種子名稱 |
| 成長狀態 | Select | 🌱 種子期 / 🌿 成長期 / 🌳 成熟期 |
| Tags | Multi-select | 🔬 研究 / 🛠️ 實作 / 📝 筆記 / 💡 靈感 / 📚 學習 / 🎯 研究專題 |
| 來源 URL | URL | 原始來源 |
| 視覺地圖頁面 | URL | 該種子的視覺地圖連結 |
| 給我的啟發？ | Rich text | 心得、靈感 |
| 研究專題 | Relation | 所屬研究專題（多對多雙向關聯） |
| Wiki Path | URL | 對應的 wiki 頁面連結 |
| 種下日期 | Date | 首次建立時間 |
| 最後更新 | Date | 最近一次灌溉/更新 |
| Sync Status | Select | ✅ 已同步 / ⏳ 待同步 / ⚠️ 衝突 |

---

## 研究專題 Database Schema

| 欄位 | 類型 | 說明 |
|------|------|------|
| 專題名稱 | Title | 專題名稱 |
| 狀態 | Select | 📋 立案 / 🔬 研究 / 📚 結案 |
| 研究問題 | Rich text | 核心研究問題 |
| 下一步 | Rich text | 目前的下一步行動 |
| 相關種子 | Relation | 相關的種子（單向） |
| 🌳 知識花園 | Relation | 反向關聯知識花園（雙向） |
| Wiki Link | URL | 對應的 wiki topic 頁面 |
| 建立日期 | Date | 專題建立時間 |
| 最後更新 | Date | 最近更新時間 |

---

## 種子清單（離線快取）

### 🌿 成長期

| 種子名稱 | Notion Link | Wiki 來源 | 視覺地圖 |
|----------|-------------|-----------|----------|
| Plannotator | [連結](https://app.notion.com/p/3b35979e3a8c817c8ffce30d8361b92a) | [[wiki/entities/plannotator\|plannotator]] | 🗺️ [視覺地圖](https://app.notion.com/p/3ad5979e3a8c811f-a76e-e94a6bb808df) |
| NPM Publishing | [連結](https://app.notion.com/p/3b35979e3a8c811abe94cf22af3ce7ef) | [[wiki/concepts/npm-publishing-workflow\|npm-publishing-workflow]] | — |

### 🌱 種子期

| 種子名稱 | Notion Link | Wiki 來源 | 視覺地圖 |
|----------|-------------|-----------|----------|
| OKF | [連結](https://app.notion.com/p/3b35979e3a8c81e49ef9cadf81c4c318) | [[wiki/concepts/okf-open-knowledge-format\|okf-open-knowledge-format]] | — |
| mattpocock/skills | [連結](https://app.notion.com/p/3b35979e3a8c81a5b198f2c28fa2c192) | [[wiki/entities/mattpocock-skills\|mattpocock-skills]] | 🗺️ [[wiki/visualizations/mattpocock-skills.canvas\|視覺地圖]] |
| Omnigent | [連結](https://app.notion.com/p/3b35979e3a8c811c9240d23bc23317ae) | [[wiki/entities/omnigent\|omnigent]] | 🗺️ [[wiki/visualizations/omnigent.canvas\|視覺地圖]] |
| Agent Extension 安裝 | [連結](https://app.notion.com/p/3b35979e3a8c81538dd9cd03e458d911) | [[wiki/concepts/agent-extension-installation\|agent-extension-installation]] | 🗺️ [[wiki/visualizations/agent-extension-installation.canvas\|視覺地圖]] |
| OpenCodeReview | [連結](https://app.notion.com/p/3b55979e3a8c81c88283f0e3eaf4f9d6) | [[wiki/entities/open-code-review\|open-code-review]] | 🗺️ [[wiki/visualizations/open-code-review.canvas\|視覺地圖]] |
| **Obsidian** | [連結](https://app.notion.com/p/3b75979e3a8c81d8b7aaf1f4c88aa957) | [[wiki/entities/obsidian\|obsidian]] | 🗺️ [[wiki/visualizations/obsidian-seed-map\|視覺地圖]] |

---

## 研究專題清單（離線快取）

| 專題 | 狀態 | Wiki Topic | 相關種子 |
|------|------|------------|----------|
| 🤖 AI Agent 架構研究 | 🔬 研究 | [[wiki/topics/ai-agent\|ai-agent]] | Plannotator, Omnigent, mattpocock/skills, OpenCodeReview |
| 🔌 Extension 開發生態 | 🔬 研究 | [[wiki/topics/extension-dev\|extension-dev]] | NPM Publishing, OKF, Agent Extension |
| 🧠 Meta-Harness 元鞍具 | 🔬 研究 | [[wiki/topics/meta-systems\|meta-systems]] | Omnigent |
| 🛠️ AI Coding Workflow | 🔬 研究 | [[wiki/topics/ai-development-tools\|ai-development-tools]] | mattpocock/skills |
| 📚 知識管理系統 | 📋 立案 | [[wiki/topics/knowledge-mgmt\|knowledge-mgmt]] | OKF |

> ✅ 所有 Relation 已於 2026-08-08 修復完成（多對多） |

---

## 種植守則

1. **交叉授粉** — 不同植物之間的連結，往往是最有價值的
2. **定期修剪** — 有些想法需要斷捨離
3. **允許變形** — 種子長出來可能跟預期不同，那是好事
4. **不急於收割** — 知識需要時間沉澱

---

## 相關頁面

- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Wiki 操作 skill
- [[wiki/entities/knowledge-garden-to-raw|knowledge-garden-to-raw]] — Notion → Raw 抓取 skill（原 notion-to-raw）
- [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]] — 架構討論
- [Design Spec](work/designs/knowledge-garden-navigator.md) — 導覽頁設計規格
