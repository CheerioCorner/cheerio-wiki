---
title: knowledge-garden — Notion 知識花園 Manifest
type: entity
created: 2026-08-03
updated: 2026-08-06
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
| 建立日期 | 2026-07-11 |
| 維護者 | Cheerio + Pi |
| 最後同步 | 2026-08-06 |

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

---

## 維護規則

1. **每次 Notion 寫入操作後**，自動同步更新本頁面（詳見 `knowledge-garden` skill §Manifest 自動同步）
2. **查詢花園時**，先讀本頁 → 快速回答 → 需要詳細內容再去 Notion
3. **新種子**加入時，同時更新「最近更新紀錄」表格

---

## 相關頁面

- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Wiki 操作 skill
- [[wiki/entities/notion-to-raw|notion-to-raw]] — Notion → Raw 抓取 skill（「花園裡那篇 X 要深入研究」）
- [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]] — 架構討論
