---
title: wiki-knowledge — Obsidian 知識庫操作 Skill
type: entity
created: 2026-07-18
updated: 2026-07-18
sources: 1
tags: [pi, skill, knowledge-base, obsidian, ingest, query, lint]
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
3. 建立/更新 `wiki/sources/`、`wiki/entities/`、`wiki/concepts/`
4. 更新 `wiki/index.md`
5. 寫入 `wiki/log.md`（append-only）
6. `git add -A && git commit && git push`

### 2. Query（查詢）
1. 讀 `wiki/index.md` 找相關頁
2. 讀取頁面 + 追溯 `[[wikilink]]`
3. 給出有引用的回答
4. **可回填**：人類說「存到 wiki」即建新頁 + 更新 index + 寫 log

### 3. Lint（健康檢查）
掃描 `wiki/` 找：
- 頁面間矛盾
- 過時主張未標記
- 孤立頁面（無 inbound link）
- 出現多次無自己頁面的概念
- 缺漏交叉引用
→ 提出清單 → 人類確認 → 修改 → git push

## 知識庫架構（受管轄）
```
Obsidian/
├── raw/                    # 唯讀，永不寫入
│   ├── assets/
│   └── YYYY-MM-DD-title.md
├── wiki/                   # LLM 全權撰寫
│   ├── index.md            # 內容索引
│   ├── log.md              # 時間日誌
│   ├── overview.md
│   ├── sources/            # 來源筆記
│   ├── entities/           # 實體頁
│   └── concepts/           # 概念頁
└── AGENTS.md               # 工作守則（共同演化）
```

## Git 同步設定
- **Remote**: `https://github.com/CheerioCorner/cheerio-wiki`（私有）
- **Branch**: `master`
- **SOP**: 每次 ingest/lint 結束自動 commit + push

## 頁面規範（關鍵）

### Frontmatter（必要）
```yaml
---
title: 頁面標題
type: entity | concept | source | comparison | synthesis
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: N
tags: [topic-a, topic-b]
---
```

### 交叉引用
- 一律用 `[[wikilink]]` 雙中括號
- 提到重要概念/實體時**必須建連結**

## 相關頁面
- Source: [[2026-07-18-pi-resource-inventory]]
- Entities: [[pi-web-access-zh-tw]], [[notionApi]], [[pi-mono]]
- Concepts: [[meta-harness]], [[minimal-agent-philosophy]]
- Wiki 系統頁: [[index]], [[log]], [[overview]]