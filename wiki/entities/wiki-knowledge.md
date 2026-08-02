---
title: wiki-knowledge — Obsidian 知識庫操作 Skill
type: entity
created: 2026-07-18
updated: 2026-08-02
sources: 1
tags: [pi, skill, knowledge-base, obsidian, ingest, query, lint]
collection: entities
topics: [knowledge-mgmt]
canonical: entities/wiki-knowledge
---

> **內建 Skill**，位於 `C:/Users/User/.agents/skills/wiki-knowledge/`，封裝 **ingest / query / lint** 三大標準流程，專門維護 `C:/Cheerio/Obsidian/` 知識庫。

## 觸發關鍵字
人類說出以下任一詞彙即自動載入：
- 「更新知識庫」「存到 wiki」「ingest」
- 「查 wiki」「查詢 wiki」
- 「lint wiki」「健康檢查」

## 三大標準流程

### 1. Ingest（吸收新資料）
**前置**：`cd C:/Cheerio/Obsidian && git pull`
**步驟**：
1. 讀取 `raw/` 新檔案
2. 與人類確認重點
3. 建立／更新正確的 canonical collection：`wiki/concepts/`、`wiki/entities/`、`wiki/sources/`、`wiki/decisions/`、`wiki/discussions/`；`wiki/topics/` 只作導航／taxonomy
4. 更新 `wiki/index.md`
5. 寫入 `wiki/log.md`（append-only）
6. `git add -A && git commit && git push`

### 2. Query（查詢）
1. 讀 `wiki/index.md` 找相關頁
2. 讀取頁面 + 追溯 wikilinks
3. 給出有引用的回答
4. **可回填**：人類說「存到 wiki」即建新頁 + 更新 index + 寫 log

### 3. Lint（健康檢查）
掃描 `wiki/` 找：
- 頁面間矛盾
- 過時主張未標記
- 孤立頁面（無 inbound link）
- 出現多次無自己頁面的概念
- 缺漏交叉引用
- Frontmatter 格式不一致（缺少必填欄位、格式錯誤）
→ 提出清單 → 對需要人工判斷的項目使用 annotator review → 修改 → git push

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
│   ├── topics/             # 導航／taxonomy 層
│   │   ├── ai-agent/
│   │   ├── extension-dev/
│   │   ├── meta-systems/
│   │   └── knowledge-mgmt/
│   ├── projects/           # legacy project documentation（新 bundle 在 root projects/）
│   ├── sources/            # 來源筆記
│   ├── index.md            # 內容索引
│   └── log.md              # 時間日誌
├── todos/                  # 任務系統
│   ├── current.md          # 目前進行中
│   ├── backlog.md          # 待辦清單
│   └── done/               # 已完成
├── journal/                # 日記系統
│   ├── daily/
│   └── templates/
└── AGENTS.md               # 工作守則（共同演化）
```

`projects/<project-id>/` 使用 GitHub repository URL 作為跨環境 canonical reference，不取代 package repository、原始碼或 package 內的 `docs/`。原始 annotator feedback 保留在 `raw/conversations/`，並使用 `type: raw-conversation`、`immutable: true`。

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
- 優先使用 vault-root 完整路徑，例如 `[[entities/pi-mono|pi-mono]]`；basename 只適合唯一 target
- 提到重要概念／實體時**必須建連結**
- compatibility stubs、Canvas 與歷史 log 可能造成合法 ambiguity；lint 應分別統計，不應自動建立 future concept

## 相關頁面
- Source: [[sources/2026-07-18-pi-resource-inventory]]
- Entities: [[entities/pi-web-access-zh-tw]], [[entities/notionApi]], [[entities/pi-mono]]
- Concepts: [[concepts/meta-harness]], [[concepts/minimal-agent-philosophy]]
- Wiki 系統頁: [[wiki/index|Wiki index]], [[wiki/log|Wiki log]]
