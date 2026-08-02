---
title: pi-todo-journal
type: entity
created: 2026-08-01
updated: 2026-08-02
sources: 1
tags: [pi, extension, todo, journal, obsidian, package]
---

# pi-todo-journal

`pi-todo-journal` 是一個 Pi package，用來可靠地保存 Pi session 工作狀態，並在明確確認後同步 Obsidian Markdown Todo 與每日 Journal。

## 核心原則

1. 不猜測任務是否完成；只有明確標記的任務才能自動完成。
2. `session_shutdown` 是 best-effort 收尾，不是唯一保存機制。
3. 用 checkpoint 降低非正常關閉造成的資料遺失。
4. 預設採 `confirm`，正式寫入前讓使用者確認。
5. 所有同步操作必須可重跑而不重複寫入（idempotent）。
6. 所有日曆日期與 ISO timestamp 使用系統本地時間，不使用 UTC。
7. Package 可保存使用者明確提供的 references，但不負責外部知識 ingest、概念統一、Wiki taxonomy 或語意 linking。

## Phase 1 — 已完成 ✅

### 建立的檔案

```text
C:/Cheerio/pi/packages/pi-todo-journal/
├── extensions/todo-journal.ts     ← Pi extension entrypoint
├── src/
│   ├── checkpoint.ts              ← checkpoint 讀/寫/清除
│   ├── config.ts                  ← workspace 路徑設定
│   ├── date.ts                    ← 臺北時間日期計算
│   ├── dry-run.ts                 ← 預覽模式（不改檔案）
│   ├── file-store.ts              ← atomic write + process lock
│   ├── journal-store.ts           ← Journal append + idempotent marker
│   ├── todo-markdown.ts           ← Markdown task parser / formatter
│   ├── todo-store.ts              ← task 完成與查詢
│   └── types.ts                   ← 所有型別定義
├── tests/                         ← 8 個 unit test + 1 個 smoke test
├── AGENTS.md                      ← AI 維運規則
├── docs/                          ← OKF Knowledge Bundle
│   ├── index.md                   ← AI 閱讀入口
│   ├── architecture.md            ← 系統架構與資料流
│   ├── tools.md                   ← commands / hooks 說明
│   ├── dependencies.md            ← 依賴與版本策略
│   ├── log.md                     ← 變更歷史
│   └── references/                ← Pi SDK / Markdown 格式參考
├── PLAN.md                        ← Phase 0–5 計畫
└── package.json                   ← pi manifest + npm metadata
```

### Extension 功能

| 功能 | 說明 |
|------|------|
| `session_start` hook | 建立 checkpoint，提示上一個 session 是否有未同步狀態 |
| `agent_settled` hook | 每次 agent 工作完成後更新 checkpoint |
| `session_shutdown` hook | 非正常中斷前，自動寫入 checkpoint + idempotent 日誌 |
| `/session-close` | 正式同步：預覽或確認寫入當日 Journal |
| `/session-close --dry-run` | 只預覽，不修改檔案 |
| `/todo-sync` | 顯示目前 workspace 與未完成任務數量 |
| `/task-done <exact title>` | 把指定的未完成任務移到 done 檔案 |

### 純函式 Markdown 操作

可直接用在任何 Node.js 程式中，不需要啟動 Pi：

- `parseTodoMarkdown` — 解析 checkbox、priority、tags、due date
- `completeTask` — 從 current 移除並寫入 done
- `listOpenTasks` — 列出未完成任務
- `appendJournalEntry` — 追加 Journal，使用 session marker 防重複

### 測試結果

```text
npm test         → 8 tests passed
npm run smoke    → passed
npm run typecheck → passed
npm pack --dry-run → passed
```

| 測試檔案 | 測什麼 |
|----------|--------|
| `todo-markdown.test.ts` | Markdown 解析與格式化 |
| `todo-store.test.ts` | Todo 移動與查詢 |
| `journal-store.test.ts` | Journal 寫入不重複 |
| `checkpoint.test.ts` | Checkpoint 讀寫刪除 |
| `date.test.ts` | 臺北時區日期計算 |
| `dry-run.test.ts` | Dry-run 不改檔案 |
| `smoke-test.ts` | 全流程整合（隔離 workspace） |

### 已知限制

- 尚未安裝到 Pi TUI 進行實際啟動測試（Phase 4）
- 尚未支援 Notion、Git 等外部後端
- 尚未支援 task start / pause / note
- 尚未支援多 session lock 並發測試

## 階段狀態

- **Phase 0：** ✅ 完成規劃與 package 命名
- **Phase 1：** ✅ 完成 — package skeleton、OKF、parser、journal、checkpoint、commands、測試
- **Phase 2：** ✅ 完成 — confirm UI、task start/pause/note、session 摘要與 recovery
- **Phase 3：** 📋 待開始 — TaskStore / JournalStore adapter 抽象化
- **Phase 4：** 📋 待開始 — 測試、CI、npm 發布、TUI 安裝驗證
- **Phase 5：** 📋 待決定 — Notion / Git / 其他 adapter

## 相關頁面

- [[topics/extension-dev/okf-open-knowledge-format|okf-open-knowledge-format]] — OKF 格式說明
- [[topics/extension-dev/npm-publishing-workflow|npm-publishing-workflow]] — npm 發布流程
- [[todos/README]] — 任務系統格式規範

## 知識邊界與未來相容性

`pi-todo-journal` 維持 session、Todo、Journal、checkpoint 與 explicit references 的責任邊界；不負責外部知識 ingest、Wiki taxonomy 或語意 linking。Project OKF Bundle 由 Obsidian 維護，package 內 `docs/` 是隨 package 發布的開發／維運 bundle，兩者不互相依賴。

未來 Cheerio 自建 Agent harness 時，可以消費本 package 使用的純 Markdown／JSON 資料與抽象 store contract；目前不將自有 harness 實作提前放入 package。

## 下一步

開始 Phase 3；新 session 閱讀 `AGENTS.md`、`docs/index.md`、`PLAN.md` 和 `todos/current.md` 後即可接續。