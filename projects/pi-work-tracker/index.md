---
title: pi-work-tracker Project OKF Bundle
type: project-bundle
collection: projects
status: planned
project_id: pi-work-tracker
repository: https://github.com/CheerioCorner/pi-work-tracker
package_source_of_truth: https://github.com/CheerioCorner/pi-work-tracker
created: 2026-08-03
updated: 2026-08-03
tags: [project-bundle, pi-work-tracker, pi-package, work-tracking]
---

# pi-work-tracker Project OKF Bundle

> 新的 Pi extension project：只處理可追溯工作狀態、歷史事件與 session recovery，不處理個人日誌、Obsidian ingest 或 wiki semantic linking。

## Identity

- GitHub：<https://github.com/CheerioCorner/pi-work-tracker>
- Package source of truth：新 repository 與其原始碼
- Package OKF bundle：repository 內的 `docs/`
- Replaces: `pi-todo-journal`

## Product boundary

```text
WorkStore       → work/current.md
HistoryStore    → work/history/YYYY-MM.md
CheckpointStore → session recovery state
References      → work item / history event refs
```

## Migration source

從舊 `pi-todo-journal` 搬移仍適用的：

- checkpoint / pending recovery
- session summary
- confirm UI
- task activity state
- atomic write / lock / dry-run
- local date handling
- Pi lifecycle integration
- 可重用測試與 smoke test

不搬移：

- JournalStore 與 daily journal writer
- Todo current/backlog/done parser
- `/todo-sync`
- done-file move semantics
- journal marker

## Planned commands

- `/work-list`
- `/work-start <work-id>`
- `/work-pause <work-id>`
- `/work-note <text>`
- `/work-done <work-id>`
- `/session-close`

`/work-done` 必須要求 work ID、result、refs 與明確確認。

## Status

- Plan / impact map：完成
- 新 repository：待建立
- source migration：待開始
- tests / TUI validation：待開始
- 舊 `pi-todo-journal` repository：新 package 驗證完成後才刪除
