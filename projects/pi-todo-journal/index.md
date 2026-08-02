---
title: pi-todo-journal Project OKF Bundle
type: project-bundle
collection: projects
status: active
project_id: pi-todo-journal
repository: https://github.com/CheerioCorner/pi-todo-journal
package_source_of_truth: https://github.com/CheerioCorner/pi-todo-journal
created: 2026-08-02
updated: 2026-08-02
tags: [project-bundle, pi-todo-journal, okf, pi-package]
---

# pi-todo-journal Project OKF Bundle

> Obsidian root Project Bundle。保存跨 session、跨本地環境的 project knowledge；不取代 package repository、原始碼或 package 內的 `docs/`。

## Identity

- GitHub：<https://github.com/CheerioCorner/pi-todo-journal>
- Package source of truth：GitHub repository 與其原始碼
- Package OKF bundle：repository 內的 `docs/`
- Obsidian shared project entity：[[wiki/projects/pi-todo-journal/README|pi-todo-journal project README]]
- Knowledge boundary：[[decisions/package-knowledge-boundary|Package Knowledge Boundary]]

## Current state

- Phase 0：完成
- Phase 1：完成
- Phase 2：完成
- Phase 3：待開始（TaskStore / JournalStore adapters）
- Phase 4：待開始（測試、CI、npm 發布、TUI 驗證）
- Phase 5：待決定（Notion / Git / 其他 adapters）

## Boundaries

- 負責 session state、explicit tasks、Journal、checkpoint 與使用者明確提供的 references。
- 不負責外部知識 ingest、concept canonicalization、Wiki taxonomy 或 semantic linking。
- `youtube-to-wiki` 是獨立 skill/workflow，不屬於本 package 功能。
- 不在此複製 source code、`PLAN.md`、Todo 或完整 package docs。

## Bundle contents

- [[projects/pi-todo-journal/references|References]]
- [[projects/pi-todo-journal/decisions/README|Project-local decisions]]
- [[projects/pi-todo-journal/discussions/README|Project discussions]]
- [[projects/pi-todo-journal/log|Bundle log]]
