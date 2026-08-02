# Work System

> `work/` 是唯一的工作狀態與可追溯事件系統。不保存個人日誌，也不重新建立舊工作目錄。

## Structure

```text
work/
├── README.md
├── current.md
└── history/
    └── YYYY-MM.md
```

## `current.md`

每個 work item 使用 Obsidian Markdown task syntax，並且必須包含：

- 穩定 ID：`W-YYYY-MM-NNN`
- task title
- `next:` 下一步
- `refs:` 至少一個 vault-root wikilink

狀態由 `In progress`、`Backlog`、`Blocked` section 表示；不另外加入 `status` 欄位，避免重複狀態來源。

## `history/YYYY-MM.md`

按月分片、append-only 的工作事件。每個事件必須包含：

- heading：日期 + work ID
- `event:` 發生什麼事
- `result:` 產生什麼結果
- `refs:` 至少一個 raw、project 或 wiki link
- `status:` `completed`、`decision-recorded`、`blocked` 或 `note`

完成工作時，從 `current.md` 移除 item，並在當月 history 留下事件；不建立 daily journal。

## Boundary

- `work/`：目前工作、完成事件、決策與可追溯處理結果
- `raw/`：不可變的原始輸入
- `wiki/`：整理後的 canonical knowledge
- `projects/`：專案 bundle
- Pi session checkpoint：暫存的 operational recovery state，不自動寫入 history
