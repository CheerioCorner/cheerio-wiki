---
title: Plannotator
type: entity
created: 2026-07-11
updated: 2026-07-30
sources: 2
tags: [coding-agent, review-tool, browser-ui, open-source, pi-extension, custom-development]
collection: entities
topics: [extension-dev]
canonical: entities/plannotator
---

# Plannotator

> AI coding agent 的視覺化審閱工具，在瀏覽器中標注 plan、code review、HTML artifact。

## 一句話

Plannotator 是將 AI agent 的提案（plan、code diff、HTML）以瀏覽器 UI 呈現，讓人類可以在瀏覽器中標注、審閱、發送 feedback 給 agent。

## 支援的 Agent

| Agent | 整合方式 |
|-------|----------|
| Pi | `pi install npm:@plannotator/pi-extension` |
| Claude Code | Claude Code plugin marketplace |
| Copilot CLI | Copilot CLI plugin |
| Codex | 技能 (`$plannotator-annotate`) |
| Gemini CLI | TOML hooks |
| Kiro | CLI commands |
| Droid | plugin |
| OpenCode | hooks |
| Amp | TypeScript plugin |

## 核心功能

### Plan Review
Agent 进入 plan mode 时，在浏览器中审查 markdown checklist。

### Code Review
支持 git / jj / p4 / GitHub PR / GitLab MR，标注代码行并发送反馈。

### HTML Artifact Annotation
直接对 agent 生成的 HTML 进行可视化审阅。

### Markdown Annotation
对任意 `.md` 文件进行标注。

## 架構

```
plannotator/
├── apps/
│   ├── pi-extension/     # Pi 專屬整合
│   ├── hook/             # 通用 hooks 框架
│   ├── codex/            # Codex 整合
│   ├── copilot/          # Copilot CLI 整合
│   └── ...
├── packages/
│   ├── core/             # 核心邏輯
│   ├── shared/           # 共享事件 API
│   └── webtui/           # 瀏覽器 UI
```

### Shared Event API

透過 `plannotator:request` 事件通道，其他 extension 可以调用：
- `plan-review`
- `code-review`
- `annotate`
- `annotate-last`

## 安裝

```bash
# macOS / Linux / WSL
curl -fsSL https://plannotator.ai/install.sh | bash

# Windows PowerShell
irm https://plannotator.ai/install.ps1 | iex
```

## Pi 整合細節

- 安裝：`pi install npm:@plannotator/pi-extension`
- 指令：`/plannotator`（切換 plan mode）、`/plannotator-review`（代碼審查）
- 快捷鍵：`Ctrl+Alt+P`
- 狀態機：idle → planning → executing → idle

## 授權

Apache 2.0 + MIT 雙授權，**可自由 fork 與自定義開發**。

## 我們的實驗（2026-07-30）

### pi-plannotator-auto

我們基於 Plannotator 的 Shared Event API，開發了一個自訂 extension：**pi-plannotator-auto**。

**功能：**
- `open_annotate` 工具 — 讓 AI 在需要人類審閱時，自動觸發瀏覽器標注 UI
- `/annotate` 指令 — 手動標註最後一條 assistant 訊息
- 視覺化 approve/deny + feedback

**技術細節：**
- 從 `@plannotator/pi-extension` 的 Shared Event API 擴展
- auto-annotate.ts 從本地 package 載入
- 已發佈到 GitHub：https://github.com/CheerioCorner/pi-plannotator-auto

**安裝方式：**
```bash
pi install git:github.com/CheerioCorner/pi-plannotator-auto
```

### 實驗觀察

| 面向 | 發現 |
|------|------|
| Shared Event API | 設計良好，擴展性強 |
| Pi 整合 | `open_annotate` 工具正常觸發瀏覽器 UI |
| 開發體驗 | 模組化架構，容易理解 |
| 授權 | Apache 2.0 + MIT，可自由 fork |

### 未來可能的發展

1. **與知識花園整合** — 把 annotation 結果存到 Notion/Obsidian
2. **多人協作模式** — 利用 Shared Event API 實現跨裝置審閱
3. **AI 自動摘要** — 把人類的標注轉化為結構化 feedback

---

## 相關頁面

- [[entities/pi-agent-core]] — Pi agent 核心
- [[sources/2026-07-11-plannotator-research]] — 初始研究來源
- [[concepts/meta-harness]] — 另一種 agent 工具，與 Plannotator 的「加法」取徑形成對比
