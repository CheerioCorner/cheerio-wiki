---
title: "2026-07-11 Plannotator 研究"
type: source
created: 2026-07-11
updated: 2026-07-11
sources: 1
tags: [plannotator, research]
---

# 2026-07-11 Plannotator 研究

## 研究動機

人類詢問 backnotprop/plannotator 的功能與自定義開發可行性。

## 主要來源

- GitHub repo: https://github.com/backnotprop/plannotator
- Plannotator 官網: https://plannotator.ai/
- Pi 整合文件: https://github.com/backnotprop/plannotator/blob/main/apps/pi-extension/README.md

## 關鍵發現

### 支援的 Agent
Pi、Claude Code、Copilot CLI、Codex、Gemini CLI、Kiro、Droid、OpenCode、Amp 共 9 種。

### Pi 整合方式
透過 `@plannotator/pi-extension` npm 套件，提供：
- Plan mode（`/plannotator` 或 `Ctrl+Alt+P`）
- Code review（`/plannotator-review`）
- Markdown annotation（`/plannotator-annotate`）
- Shared Event API

### 無 Obsidian/Notion 整合
Repo 中沒有 obsidian 或 notion 相關代碼。但支援 markdown 文件審閱，可間接用於 Obsidian vault。

### 自定義開發可行性
- ✅ Apache 2.0 + MIT 雙授權
- ✅ 模組化架構（`apps/` 目录，各 agent 獨立）
- ✅ Shared Event API 支援二層擴展
- ✅ packages/webtui 可自定義瀏覽器 UI

### 未納入 Wiki 的點
- 詳細的 Shared Event API 規格（見 [[plannotator]] 頁面）
- 各 agent 整合的技術細節（需進一步研究）

## 知識沉澱

- 主體頁面：[[plannotator]]
- Notion 同步：已建立「Plannotator Fork 開發規劃」頁面（用於多人協作/跨 IDE）
