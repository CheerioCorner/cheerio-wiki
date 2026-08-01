---
title: OKF (Open Knowledge Format) — 統一的知識表示格式
type: concept
created: 2026-08-01
updated: 2026-08-01
sources: 1
tags: [okf, knowledge-format, google, ai-agent, documentation]
source_url: https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf
---

# OKF (Open Knowledge Format) — 統一的知識表示格式

## 核心概念

**OKF** 是 Google 開發的一個開放標準，用於表示知識。它的核心理念是：

> 用 **Markdown + YAML frontmatter** 來表示知識，讓 **人類和 AI 都能讀**。

### 爲什麼需要 OKF？

在 AI 時代，知識不再是「寫一次然後被動閱讀」，而是「持續被 AI 和人類共同維護」。OKF 解決了這個問題：

| 傳統方式 | OKF 方式 |
|----------|----------|
| 知識散落在各處 | 統一格式，可攜帶 |
| 只有人能讀 | 人類和 AI 都能讀 |
| 版本控制困難 | 天然支援 git |
| 來源不明 | 內建 provenance（來源）追蹤 |
| 信任難驗證 | 內建 trust tier（信任等級） |

---

## OKF 的核心元素

### 1. Knowledge Bundle（知識包）

一個 OKF 知識包就是一個資料夾，裡面都是 Markdown 檔案：

```
knowledge-bundle/
├── index.md              # 入口（進階式揭露）
├── log.md                # 變更歷史
├── concept-1.md          # 概念文件
├── concept-2.md          # 概念文件
└── subdirectory/         # 子目錄
    ├── index.md
    └── concept-3.md
```

### 2. Concept Document（概念文件）

每個概念就是一個 Markdown 檔案，有兩個部分：

```markdown
---
type: Tool Reference              # 類型（必填）
title: open_annotate              # 標題
description: 開啟瀏覽器註解 UI     # 簡述
status: stable                    # 狀態：draft | stable | deprecated
generated:                        # 誰建立的
  by: human:cheerio
  at: 2026-08-01T12:00:00Z
verified:                         # 誰驗證的
  by: human:cheerio
  at: 2026-08-01T12:00:00Z
tags: [tool, annotation, pi-extension]
---

# Body 內容

這裡是 Markdown 內容，可以用一般語法...
```

### 3. Frontmatter（前言）

YAML 格式的 metadata，放在 `---` 之間：

| 欄位 | 必填 | 說明 |
|------|------|------|
| `type` | ✅ | 概念類型（如 Tool Reference、Architecture） |
| `title` | 建議 | 人類可讀的標題 |
| `description` | 建議 | 單句簡述 |
| `status` | 選填 | draft / stable / deprecated |
| `tags` | 選填 | 分類標籤 |
| `generated` | 選填 | 誰建立的、什麼時候 |
| `verified` | 選填 | 誰驗證的、什麼時候 |

### 4. Trust Tier（信任等級）

根據 `verified` 欄位自動推導：

| 等級 | 條件 | 說明 |
|------|------|------|
| **Unverified** | 沒有 `verified` | 未經驗證 |
| **Machine-confirmed** | 只有非 `human:` 的 verified | 機器驗證 |
| **Human-reviewed** | 有 `human:` 的 verified | 人類審核 |

### 5. Provenance（來源追蹤）

```yaml
sources:
  - id: pi-sdk-docs
    resource: https://pi.dev/docs/sdk
    title: Pi SDK 文件
    author: team:pi
    usage_count: 5000
    last_modified: 2026-07-30
```

這記錄了知識的來源，以及來源的可信度信號。

---

## 實際案例：pi-plannotator-auto

讓我們用實際的 extension 來示範 OKF：

### 檔案結構

```
pi-plannotator-auto/
├── docs/                   ← OKF Knowledge Bundle
│   ├── index.md            ← 入口
│   ├── architecture.md     ← 系統架構
│   ├── tools.md            ← Tool 參考
│   ├── dependencies.md     ← 依賴關係
│   ├── log.md              ← 變更歷史
│   └── references/         ← 外部資源
├── extensions/
│   └── auto-annotate.ts    ← 原始碼
└── package.json
```

### 範例：architecture.md

```markdown
---
type: Architecture
title: 系統架構
description: pi-plannotator-auto 的整體架構和模組依賴
status: stable
generated: { by: human:cheerio, at: 2026-08-01T12:00:00Z }
verified: { by: human:cheerio, at: 2026-08-01T12:00:00Z }
tags: [architecture, dependencies, pi-extension]
---

# Architecture Overview

## 系統架構

```
pi-plannotator-auto
├── extensions/
│   └── auto-annotate.ts    ← 主要 extension
├── docs/                   ← 本資料夾
├── package.json            ← pi manifest
└── .github/workflows/      ← CI/CD
```

## 模組依賴關係

```
auto-annotate.ts
    │
    ├── @earendil-works/pi-coding-agent (peerDependency)
    ├── @plannotator/pi-extension (dependency)
    └── typebox (devDependency)
```
```

### 範例：tools.md

```markdown
---
type: Tool Reference
title: Tools & Commands
description: 註冊的 Tool 和 Command 說明
status: stable
tags: [tools, commands, pi-extension]
---

# Tools & Commands

## Registered Tools

### `open_annotate`

**用途：** 開啟瀏覽器註解 UI

**參數：**
```typescript
{
  title: string
  content: string
  mode?: "annotate" | "annotate-last"
}
```
```

---

## OKF 的優勢

### 1. 人類和 AI 都能讀

- **人類**：用任何 Markdown 編輯器打開就能讀
- **AI**：直接把檔案內容放进 context 就能理解

### 2. 天然版本控制

因為是純文字檔案，git 可以：
- 追蹤每次改動
- 顯示 diff
- blame（誰改了什麼）
- PR review

### 3. 可攜帶

一個 knowledge bundle 就是一個資料夾：
- 可以 git clone
- 可以打包成 tarball
- 可以放在任何地方
- 沒有 vendor lock-in

### 4. 進階式揭露（Progressive Disclosure）

透過 `index.md`，可以一層一層探索：
- 先看 index.md 知道有什麼
- 再看特定概念的詳細內容
- 不需要一次載入全部

### 5. 內建信任機制

- `generated`：誰建立的
- `verified`：誰驗證的
- `trust tier`：自動推導信任等級

---

## OKF 適用的場景 ✅

### 1. Extension/Plugin 開發文件

**就像我們做的 pi-plannotator-auto**

```
docs/
├── index.md            ← 快速入門
├── architecture.md     ← 架構說明
├── tools.md            ← API 參考
├── dependencies.md     ← 依賴關係
└── log.md              ← 變更歷史
```

**優勢：**
- AI 開發時可以快速理解專案
- 不需要每次都重新掃描原始碼
- 文件和程式碼同步更新

### 2. AI Agent 的知識庫

**讓 AI 有結構化的知識**

```
knowledge/
├── index.md
├── concepts/
│   ├── concept-a.md
│   └── concept-b.md
├── procedures/
│   ├── procedure-1.md
│   └── procedure-2.md
└── references/
    └── external-docs.md
```

**優勢：**
- AI 可以精確找到需要的知識
- 有來源追蹤，知道知識從哪來
- 可以驗證知識的時效性

### 3. 團隊知識管理

**替代 Confluence/Notion 的方案**

```
team-knowledge/
├── index.md
├── onboarding/
│   ├── new-member-guide.md
│   └── setup-instructions.md
├── architecture/
│   ├── system-design.md
│   └── api-design.md
└── processes/
    ├── code-review.md
    └── deployment.md
```

**優勢：**
- 版本控制（git）
- 可以 fork 和貢獻
- 不被特定平台綁定

### 4. 資料目錄（Data Catalog）

**Google 的原始用途**

```
data-catalog/
├── index.md
├── datasets/
│   ├── customers.md
│   └── orders.md
├── tables/
│   ├── customers-table.md
│   └── orders-table.md
└── metrics/
    └── revenue.md
```

**優勢：**
- 資料來源追蹤
- 信任等級
- 自動化驗證

---

## OKF 不太適用的場景 ❌

### 1. 需要即時更新的內容

**不適合：即時股票價格、即時新聞**

OKF 是靜態檔案，更新需要 commit + push。如果內容每秒都在變，OKF 不適合。

**替代方案：** 資料庫 + API

### 2. 需要複雜查詢的內容

**不適合：需要全文搜尋、複雜過濾的大型知識庫**

OKF 是檔案系統導向，沒有內建的查詢引擎。

**替代方案：** 資料庫 + 搜尋引擎（Elasticsearch）

### 3. 需要存取控制的內容

**不適合：敏感資料、需要權限管理的內容**

OKF 是純文字檔案，沒有內建的存取控制。

**替代方案：** 私有 repo + GitHub 權限

### 4. 需要互動式 UI 的內容

**不適合：需要表單、互動式圖表的內容**

OKF 是靜態 Markdown，沒有互動功能。

**替代方案：** 自訂 UI + 後端 API

### 5. 需要即時協作的內容

**不適合：多人同時編輯同一個檔案**

OKF 依賴 git，多人同時編輯會有衝突。

**替代方案：** Google Docs、Notion（即時協作）

---

## 總結：什麼時候用 OKF？

| 場景 | 適用？ | 原因 |
|------|--------|------|
| Extension 開發文件 | ✅ 非常適合 | AI 可以快速理解 |
| AI Agent 知識庫 | ✅ 非常適合 | 結構化、可驗證 |
| 團隊知識管理 | ✅ 適合 | 版本控制、可攜帶 |
| 資料目錄 | ✅ 適合 | 來源追蹤、信任機制 |
| 即時資料 | ❌ 不適合 | 靜態檔案，更新慢 |
| 複雜查詢 | ❌ 不適合 | 沒有查詢引擎 |
| 敏感資料 | ❌ 不適合 | 沒有存取控制 |
| 互動式 UI | ❌ 不適合 | 純 Markdown |
| 即時協作 | ❌ 不適合 | 依賴 git |

---

## 我們的應用

在 pi-plannotator-auto 中，我們：

1. **建立 OKF Knowledge Bundle** — docs/ 資料夾
2. **每個文件都有 frontmatter** — 類型、標題、狀態、標籤
3. **有 index.md** — 進階式揭露
4. **有 log.md** — 變更歷史
5. **有 references/** — 外部資源

**效果：**
- AI 可以快速理解專案結構
- 不需要每次都掃描原始碼
- 文件和程式碼保持同步
- 可以版本控制

---

## 相關資源

- [OKF 官方文件](https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf)
- [OKF v0.2 規範](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)
- [Pi-plannotator-auto OKF 實作](https://github.com/CheerioCorner/pi-plannotator-auto/tree/master/docs)

---

*Last updated: 2026-08-01*
