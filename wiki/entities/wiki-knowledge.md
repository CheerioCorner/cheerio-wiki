---
title: wiki-knowledge — Obsidian 知識庫操作 Skill（已刪除）
type: entity
created: 2026-07-18
updated: 2026-08-08
sources: 1
tags: [pi, skill, knowledge-base, obsidian, ingest, query, lint]
collection: entities
topics: [skill, knowledge-mgmt]
canonical: entities/wiki-knowledge
---

> ⚠️ **已刪除**（2026-08-08）。原始的三大流程已拆成三個獨立 skill，dispatcher 功能不再需要。
> 
> 替代方案：`wiki-ingest`、`wiki-query`、`wiki-lint`

## 觸發關鍵字
人類說出以下任一詞彙即自動載入：
- 「更新知識庫」「存到 wiki」「ingest」
- 「查 wiki」「查詢 wiki」
- 「lint wiki」「健康檢查」

## 子 Skills（2026-08-08 拆分）

| 使用者說 | 子 Skill | 功能 |
|---------|---------|------|
| 「處理這個」、「存進 wiki」、「ingest」 | **wiki-ingest** | 讀 raw → 建/更新 wiki 頁面 → 更新 index/log |
| 「查 wiki」、「wiki 裡有什麼」 | **wiki-query** | 讀 index → 讀頁面 → 回答問題 |
| 「lint wiki」、「整理 wiki」 | **wiki-lint** | 掃描全部頁面 → 找問題 → 提清單 |

其他相關 skills：
- **youtube-to-wiki** — YouTube 影片 → wiki 頁面（`~/.agents/skills/youtube-to-wiki/SKILL.md`）
- **plannotator-sync** — Plannotator 存檔 → raw/conversations/（`~/.agents/skills/plannotator-sync/SKILL.md`）

## 知識庫架構（受管轄）
```
Obsidian/
├── raw/                    # 唯讀，永不寫入
│   ├── assets/
│   └── YYYY-MM-DD-title.md
├── wiki/                   # Shared cross-project knowledge graph
│   ├── concepts/           # 可跨專案重用的抽象知識
│   ├── entities/           # 人、工具、package 與具體實作
│   ├── decisions/          # 全域／跨專案已確認決策
│   ├── discussions/        # 尚未定案的討論
│   ├── topics/             # 導航／taxonomy 層，只放導航頁與 Canvas
│   │   ├── ai-agent/
│   │   ├── extension-dev/
│   │   ├── meta-systems/
│   │   └── knowledge-mgmt/
│   ├── sources/            # 來源筆記
│   ├── index.md            # 內容索引
│   └── log.md              # 時間日誌
├── work/                   # 工作狀態與可追溯 history events
│   ├── README.md
│   ├── current.md
│   └── history/YYYY-MM.md
└── AGENTS.md               # 工作守則（共同演化）
```

`projects/<project-id>/` 使用 GitHub repository URL 作為跨環境 canonical reference，不取代 package repository、原始碼或 package 內的 `docs/`。原始 annotator feedback 保留在 `raw/conversations/`，並使用 `type: raw-conversation`、`immutable: true`。

工作結果與決策若需追溯，追加至 `work/history/YYYY-MM.md`，並使用 `refs:` 連到 raw、project 或 wiki；不寫入 todos / journal。

## Git 同步設定
- **Remote**: `https://github.com/CheerioCorner/cheerio-wiki`（私有）
- **Branch**: `master`
- **SOP**: 每次 ingest/lint 結束自動 commit + push

## 頁面規範（關鍵）

### Frontmatter（必要）
```yaml
---
title: 頁面標題
type: entity | concept | source | comparison | synthesis | decision | discussion | audit | project-bundle
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: N
tags: [topic-a, topic-b]
---
```

**必填欄位：** title, type, created, updated, sources, tags
- `collection`、`topics`、`canonical`、`provenance` 可依頁面需求加入；現階段保留 `sources: N` 相容性。
- 若填寫 `verified.by`，人類驗證者使用 `human:Cheer`。
**格式檢查：** lint 流程會自動檢查格式一致性

### 交叉引用
- 一律用 Obsidian Wikilink 雙中括號；文件示例中的 target 使用 `wikilink` code text，不代表實際頁面
- 優先使用 vault-root 完整路徑，例如 `[[wiki/entities/pi-mono|pi-mono]]`；basename 只適合唯一 target
- 提到重要概念／實體時**必須建連結**
- Canvas、工作 history 與 raw conversation 可能造成合法 ambiguity；lint 應分別統計，不應自動建立 future concept
- 工作 history event 的 `refs:` 一律優先使用 canonical vault-root path

## 相關頁面
- Source: [[wiki/sources/2026-07-18-pi-resource-inventory]]
- Entities: [[wiki/entities/pi-web-access-zh-tw]], [[wiki/entities/notionApi]], [[wiki/entities/pi-mono]]
- Concepts: [[wiki/concepts/meta-harness]], [[wiki/concepts/minimal-agent-philosophy]]
- Wiki 系統頁: [[wiki/index|Wiki index]], [[wiki/log|Wiki log]]
