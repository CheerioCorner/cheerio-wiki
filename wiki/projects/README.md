# Legacy Projects 追蹤

> 舊版 `wiki/projects/` project documentation 入口。新的跨 session、跨環境 Project OKF Bundle 位於 root `projects/<project-id>/`。
>
> Shared entry point：[[wiki/index|Wiki index]]。本頁不再作為 canonical project collection。

## 活躍專案

| 專案 | 類型 | 狀態 | 備註 |
|------|------|------|------|
| [[projects/pi-plannotator-auto/index|pi-plannotator-auto]] | Extension | ✅ 已發布 | GitHub + NPM |
| [[wiki/projects/pi-web-access-zh-tw/README|pi-web-access-zh-tw]] | Extension | ⚠️ 需維護 | 有外部貢獻者 |

## 規劃中

| 專案 | 類型 | 狀態 | 備註 |
|------|------|------|------|
| Pi Web/Desktop | 專案 | 📋 規劃中 | 待 grill-me 追問 |
| pi-todo-journal | Pi Package | 🚧 Phase 1 | Todo / Journal session 同步 |
| 自己的 grill skill | Skill | 💡 想法 | 待原型開發 |

## 已歸檔

_（目前沒有）_

---

## Legacy structure

舊 project README 保留作相容與歷史導覽，未經確認不批量刪除或搬移。

## New Project OKF Bundle

新的 project bundle 至少使用：
```
projects/<project-id>/
├── index.md
├── decisions/
├── discussions/
├── references.md
└── log.md
```

Project Bundle 使用 GitHub repository URL 作為跨環境 canonical reference；不複製 package source code、完整 PLAN 或 package `docs/`。
