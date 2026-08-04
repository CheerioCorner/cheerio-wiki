---
title: knowledge-garden — Notion 知識花園 Manifest
type: entity
created: 2026-08-03
updated: 2026-08-11
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
| 導覽 Database | https://app.notion.com/p/4f0c3d638d5f4e02b6a9e7d6540bebb3 |
| Database ID | `4f0c3d63-8d5f-4e02-b6a9-e7d6540bebb3` |
| 導覽頁 | https://app.notion.com/p/3b25979e3a8c81b6a2eeff4945a79e43 |
| 研究專題管理 | https://app.notion.com/p/3b25979e3a8c81a69f98c1016d370dff |
| 建立日期 | 2026-07-11 |
| 維護者 | Cheerio + Pi |
| 最後同步 | 2026-08-11 |

---

## 導覽 Database 結構

### Properties

| 欄位 | 類型 | 說明 |
|------|------|------|
| 成長狀態 | Select | 🌱 種子期 / 🌿 成長期 / 🌳 成熟期 |
| Notion 頁面 | URL | 種子/知識的名稱（主要連結） |
| 靈感/心得 | Text | 這顆種子帶來的啟發 |
| 研究專題 | URL | 所屬研究專題頁面（如有） |
| Tags | Multi-select | 🔬 研究, 🛠️ 實作, 📝 筆記, 💡 靈感, 📚 學習, 🎯 研究專題 |
| 一句話 | Text | 30 字以內摘要（給 LLM 快速理解） |
| Wiki GitHub | URL | 對應的 wiki 頁面 GitHub 連結（導覽頁不顯示） |
| 種下日期 | Date | 首次建立時間 |
| 最後更新 | Date | 最近一次灌溉/更新 |

### Views

1. 🗺️ 花園全景（Gallery，按成長狀態分組）
2. 📋 清單視圖（Table，完整資料）
3. 🏷️ 按 Tags（Board，按 Tags 分組）
4. 🔍 LLM 索引（Table，給 AI 用）
5. 📅 時間線（Timeline，按種下日期）
6. 💡 靈感集（Gallery，篩選有靈感/心得的）

---

## 花園地圖

### 🌳 已成形的想法
*（目前還沒有，但會有的。）*

### 🌿 成長中的植物

| 種子名稱 | Notion Link | Stage | Wiki 來源 |
|----------|-------------|-------|-----------|
| Plannotator — AI Agent 視覺化審閱工具 | [連結](https://app.notion.com/p/3ad5979e3a8c81528374f39d4c1216c0) | 🌿 成長期 | [[wiki/entities/plannotator\|plannotator]] |
| NPM Publishing Workflow — 從零建立 NPM 套件發佈流程 | [連結](https://app.notion.com/p/3ae5979e3a8c81a1929ce3b66b9d2875) | 🌿 成長期 | [[wiki/concepts/npm-publishing-workflow\|npm-publishing-workflow]] |

### 🌱 新種下的種子

| 種子名稱 | Notion Link | Stage | Wiki 來源 |
|----------|-------------|-------|-----------|
| mattpocock/skills — 第一棵樹苗 | [連結](https://app.notion.com/p/3ac5979e3a8c817d9c40d22e1eed94fb) | 🌱 種子期 | [[wiki/entities/mattpocock-skills\|mattpocock-skills]] |
| Omnigent — Databricks 開源 meta-harness | [連結](https://app.notion.com/p/3ad5979e3a8c8172a580ea3e890bb2d1) | 🌱 種子期 | [[wiki/entities/omnigent\|omnigent]] |
| Agent Extension 安裝位置與方式 | [連結](https://app.notion.com/p/3ae5979e3a8c81368c3ad7f614773c07) | 🌱 種子期 | [[wiki/concepts/agent-extension-installation\|agent-extension-installation]] |
| OKF (Open Knowledge Format) — AI 時代的知識格式 | [連結](https://app.notion.com/p/3b05979e3a8c81d1a4cef54284281238) | 🌿 成長期 | [[wiki/concepts/okf-open-knowledge-format\|okf-open-knowledge-format]] |

---

## 研究專題

### 🔬 Meta-Harness（元鞍具）
> 對 harness 本身的再抽象/最佳化層。有學術（自動演化 harness）與業界（多 agent orchestration）兩種定義。

- **狀態：** 研究中
- **樹苗：** Omnigent（🌱 種子期）
- **Wiki：** [[wiki/concepts/meta-harness|meta-harness]]

---

## 最近更新紀錄

| 日期 | 事件 | 種子 |
|------|------|------|
| 2026-07-11 | 花園建立 | — |
| 2026-07-11 | 種下 mattpocock/skills | mattpocock/skills |
| 2026-07-13 | 種下 Omnigent | Omnigent |
| 2026-07-30 | 種下 Plannotator | Plannotator |
| 2026-07-30 | 種下 Omnigent（研究專題） | Omnigent |
| 2026-07-31 | 種下 NPM Publishing Workflow | NPM Publishing |
| 2026-07-31 | 種下 Agent Extension 安裝位置 | Agent Extension |
| 2026-08-01 | 種下 OKF Extension 開發 | OKF Extension |
| 2026-08-06 | E2E 測試完成（raw/wiki/Notion 全部清理） | E2E Test Artifact |
| 2026-08-10 | 種下 OKF (Open Knowledge Format) | OKF |
| 2026-08-11 | 建立導覽 Database + 導覽頁 + 研究專題管理頁 | — |

---

## 維護規則

1. **每次 Notion 寫入操作後**，自動同步更新本頁面（詳見 `knowledge-garden` skill §Manifest 自動同步）
2. **查詢花園時**，先讀本頁 → 快速回答 → 需要詳細內容再去 Notion
3. **新種子**加入時，同時更新「最近更新紀錄」表格
4. **導覽 Database** 為主要資料來源，本 manifest 為離線快取

---

## 相關頁面

- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Wiki 操作 skill
- [[wiki/entities/notion-to-raw|notion-to-raw]] — Notion → Raw 抓取 skill（「花園裡那篇 X 要深入研究」）
- [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]] — 架構討論
- [導覽頁](https://app.notion.com/p/3b25979e3a8c81b6a2eeff4945a79e43) — Notion 導覽頁
- [研究專題管理](https://app.notion.com/p/3b25979e3a8c81a69f98c1016d370dff) — Notion 研究專題管理頁
- [Design Spec](work/designs/knowledge-garden-navigator.md) — 導覽頁設計規格
