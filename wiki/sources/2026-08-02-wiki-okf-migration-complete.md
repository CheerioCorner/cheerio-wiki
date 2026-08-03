---
title: 2026-08-02 Wiki × OKF 架構優化完整工作紀錄
type: source
collection: sources
topics: [knowledge-mgmt, okf, migration, project-architecture]
status: complete
created: 2026-08-02
updated: 2026-08-02
sources: 1
provenance:
  - kind: raw
    path: raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md
provenance_raw: "raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md"verified:
  by: human:Cheer
  at: 2026-08-02
tags: [wiki, okf, migration, audit, provenance, project-bundle]
---

# 2026-08-02 Wiki × OKF 架構優化完整工作紀錄

> 本頁是本輪 Wiki × OKF 架構優化的完整 canonical work record。它記錄決策、實作、驗證、Git commits、保留邊界與未來工作；不是 package source code、PLAN 或 raw conversation 的替代品。

## 1. Goal

將現有 Obsidian Wiki 從以 `topics/` 為主要內容目錄的舊結構，整理成：

```text
raw/                    # immutable 原始資料與對話
├── web/                # browser / Web Clipper
├── youtube/            # YouTube 原始資料
├── conversations/      # annotator / agent 對話
├── assets/             # 原始附件
projects/<project-id>/  # 跨 session、跨環境 Project OKF Bundles
wiki/
├── concepts/           # 可重用抽象
├── entities/           # 人、工具、package、服務與實作
├── sources/            # 整理後的外部資料
├── decisions/          # 已確認全域／跨專案選擇
├── discussions/        # 尚未定案內容
├── topics/             # taxonomy／導航
├── visualizations/      # Canvas 視覺化投影
└── audits/              # audit 報告
```

## 2. Operating rules

- 每輪開始前先在 `C:/Cheerio/Obsidian` 執行 `git pull --ff-only`。
- `raw/` 永遠只讀；原始 annotator handoff 使用 `type: raw-conversation`、`immutable: true`。
- package repository 與原始碼仍是 package source of truth。
- package 內 `docs/` 是隨 package 發布的開發／維運 OKF bundle。
- Obsidian root `projects/` 是跨 session project knowledge bundle，不是第二份 package source of truth。
- 工作狀態與可追溯事件使用 `work/current.md` 與 `work/history/YYYY-MM.md`；不再依賴 todos/journal 作為 active workflow source of truth。
- 跨環境 canonical reference 優先使用 GitHub repository URL，不使用固定本地路徑。
- `verified.by` 使用 `human:Cheer`；Cheerio 是 AI assistant 名稱。
- 日期現行規則使用執行環境的 system local date/time；歷史文件不為了新規則而改寫。
- 不修改 `pi-todo-journal/PLAN.md`。
- 不建立 YouTube pipeline，直到 linking contract、lint 與 migration 規則穩定且另有確認。

## 3. Decision and review trail

### Original handoff

- [[raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff|Wiki × OKF × YouTube session handoff]]

### Formal decisions

- [[wiki/decisions/project-okf-bundle-boundary|Project OKF Bundle Boundary]]
- [[wiki/decisions/shared-wiki-collection-model|Shared Wiki Collection Model]]
- [[wiki/decisions/raw-conversation-promotion-workflow|Raw Conversation Promotion Workflow]]
- [[wiki/decisions/package-knowledge-boundary|Package Knowledge Boundary]]

### Open discussions

- [[wiki/discussions/youtube-to-wiki-pipeline-timing|YouTube-to-Wiki Pipeline Timing]]
- [[wiki/discussions/canonicalization-and-semantic-linking|Canonicalization and Semantic Linking]]
- [[wiki/discussions/topic-stub-cleanup|Existing Topic Stub Cleanup]]

### Annotator reviews

- Migration policy review：確認保留 `topics/` compatibility stubs、future concepts 先列 audit exceptions、`sources: N` 相容保留，以及只同步 migration 相關檔案。
- Source collection review：確認保留 `wiki/sources/`，並整理所有 source notes，避免 source island。
- Decision promotion review：確認正式建立四個 decision pages 與三個 discussion pages。

## 4. Implementation phases completed

### Phase A — Audit baseline

建立 audit 報告，掃描：

- collection 分布
- frontmatter type／欄位差異
- topics 未來歸類
- broken links、合法 Obsidian links、basename ambiguity
- orphan pages
- inbound／outbound links
- 重複概念候選
- log-only historical links
- `wiki/projects/` 遷移風險
- 低風險 migration phases

Audit：[[wiki/audits/2026-08-02-wiki-collection-topic-link-audit|Wiki collection/topic/link audit]]。

### Phase B — Shared collections

建立：

- `wiki/concepts/`
- `wiki/entities/`
- `wiki/sources/`
- `wiki/decisions/`
- `wiki/discussions/`

原有 15 個 `topics/` 內容頁建立 canonical copies：

- 7 個 concepts
- 8 個 entities

舊 `topics/` 頁面保留為 compatibility stubs，並指向 canonical pages。`topics/` 不再作主要內容 collection。

### Phase C — Project OKF Bundles

建立三個 root bundles：

```text
projects/
├── pi-plannotator-auto/
├── pi-todo-journal/
└── pi-web-access-zh-tw/
```

每個 bundle 包含適量的：

- `index.md`
- `references.md`
- `decisions/`
- `discussions/`
- `log.md`

沒有複製 package source code、完整 PLAN、Todo 或 package `docs/`。

### Phase D — Canonical links and provenance

- 現行 index、collection README、project README 與正文 links 改用 canonical collection paths。
- Source notes 保留 `sources: N` 相容欄位。
- 7 個 source notes 補上 `collection: sources` 與 `provenance`。
- Future concepts 改用 inline code 或明確的「尚未建立」描述，不自動建立推測頁。
- 歷史 `wiki/log.md` 保持 append-only，不為了新 canonical path 重寫歷史事實。

### Phase E — Formal promotion

將 handoff 中已確認內容升格到 `wiki/decisions/`：

- Project Bundle boundary
- Shared Wiki collection model
- Raw conversation promotion workflow
- Package knowledge boundary

將尚未定案內容整理到 `wiki/discussions/`：

- YouTube pipeline timing
- Canonicalization and semantic linking
- Topic stub cleanup

每個正式 promotion page 都透過 provenance 回指 immutable raw handoff。

### Phase F — Final health audit

完成最後 audit：

- Wiki Markdown：57
- concepts canonical pages：7
- entities canonical pages：8
- source notes：7
- decisions：4
- discussions：3
- Project Bundles：3
- 掃描 links：233
- 現行正文 broken links：0
- Project Bundle links：全部可解析
- 歷史 log unresolved links：3（`guide`、`快速開始`、`overview`）

Audit 結論：目前 migration scope 完成；剩餘項目是刻意保留的 future work，而非未處理的 deterministic migration defect。

## 5. Git history

本輪主要 commits：

| Commit | Work |
|---|---|
| `0853c7a` | migrate Wiki to collections and Project Bundles |
| `74635b2` | normalize provenance and canonical links |
| `8286923` | add source provenance metadata |
| `6aa3742` | promote Wiki architecture decisions |
| `0db8fa9` | add pi-todo-journal Project Bundle |
| `b59db40` | add pi-web-access Project Bundle |
| `dca2e36` | finalize Wiki navigation audit |
| `fed1175` | record final Wiki health audit |

All commits were pushed to `origin/master`.

## 6. Validation and boundaries

- `git pull --ff-only`：每輪開始前執行。
- `git diff --check`：migration commits 通過。
- `raw/`：未修改；`raw/conversations/` 原始檔沒有加入本輪 migration commit。
- `PLAN.md`：未修改。
- package source repositories：未修改。
- `.obsidian` UI state changes：排除在 migration commits 外。
- YouTube pipeline：未建立。
- Future concept pages：未因 broken-link noise 而自動建立。

## 7. Remaining intentional work

以下不屬於本輪已完成的 deterministic migration：

1. `topics/` compatibility stubs 最終要永久保留、改成 redirect 或移除。
2. Future concepts 是否值得建立 canonical pages。
3. `sources: N` 是否在未來升級為完全 provenance-first schema。
4. `youtube-to-wiki` 的獨立 skill/workflow 設計與實作。
5. 各 Project Bundle 的持續 maintenance updates。
6. `pi-web-access-zh-tw` 外部貢獻者 changes 的實際審查與合併。

## 8. Completion statement

截至 2026-08-02，Wiki × OKF 架構 migration 的既定 scope 已完成：collections、taxonomy、provenance、formal decisions、open discussions、Project Bundles、canonical links 與 final health audit 均已落地並同步到 GitHub。

後續工作應以新的架構契約為前提，不應回到 `topics/` 作為主要內容 collection，也不應將 package docs、PLAN 或 raw conversation 複製成第二份 source of truth。
