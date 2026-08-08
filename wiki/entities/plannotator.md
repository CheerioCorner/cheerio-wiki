---
title: Plannotator
type: entity
created: 2026-07-11
updated: 2026-08-08
sources: 4
tags: [coding-agent, review-tool, browser-ui, open-source, pi-extension, custom-development, copilot, installation]
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

### GitHub Copilot CLI 整合安裝（Windows）

**快速安裝（3 步）：**

1. 安裝 Plannotator：`irm https://plannotator.ai/install.ps1 | iex`
2. 停用 Share：`[Environment]::SetEnvironmentVariable("PLANNOTATOR_SHARE", "disabled", "User")` 或編輯 `~/.plannotator/config.json` 設定 `{ "share": "disabled" }`
3. 安裝 Copilot CLI 插件：
   ```
   /plugin marketplace add backnotprop/plannotator
   /plugin install plannotator-copilot@plannotator
   ```

**安裝 Skill：**
```
npx skills@latest add https://github.com/EVACopilot/skills-itd-aas --skill plannotator-copilot-setup
```

**完整設定流程（12 步，3 階段）：**

| 階段 | 步驟 | 內容 |
|------|------|------|
| 基礎安裝 | 1-4 | 安裝 Plannotator → 停用 Share → 安裝插件 → 確認 App PATH |
| 整合測試 | 5 | Shift+Tab 進入 plan mode → 瀏覽器標注 → exit_plan_mode 發送 |
| 進階整合 | 6-12 | 與 Obsidian 整合、slash commands、進階設定 |

**架構原理：**
```
Copilot CLI/App → localhost → Plannotator → 瀏覽器 UI → 人類標注 → 發送 Prompt
```

Plannotator 在 Copilot 的 plan mode 中攔截 AI 請求，人類在瀏覽器中完成標注後才發送給 AI。

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

### 已實現：Note 儲存

Plannotator 已支援將 plan 存到 Obsidian 或 Bear：

**Obsidian：**
- Settings → Enable Obsidian → 選擇 vault 與 destination folder（預設 `plannotator`）
- 存檔包含 YAML frontmatter（creation time, source, tags）
- 自動從 plan title + code languages 產生 tags
- 自動加入 `[[Plannotator Plans]]` backlink

**Bear：**
- Settings → Enable Bear（僅 macOS，使用 Bear URL scheme）
- 建立 Bear note with title + body + tags

**Octarine：**
- 也支援 Octarine，需提供 workspace name

> 這些 note actions 存的是**副本**，agent 的 source plan 和 Plannotator 本地 history 保持分離。

### 已實現：Plannotator → Wiki 整合

透過 `plannotator-sync` skill（`~/.agents/skills/plannotator-sync/`），Plannotator 存到 `plannotator/` 的檔案可以同步到 `raw/conversations/`，再 ingest 進 wiki：

1. 從 Plannotator UI 存檔到 `plannotator/`
2. 說「sync」→ skill 自動搬到 `raw/conversations/` 並修正 frontmatter
3. 說「ingest」→ wiki-knowledge 處理進 wiki

### 未來可能的發展

1. **多人協作模式** — 利用 Shared Event API 實現跨裝置審閱
2. **AI 自動摘要** — 把人類的標注轉化為結構化 feedback

---

## 相關頁面

- [[wiki/entities/pi-agent-core]] — Pi agent 核心
- [[wiki/sources/2026-07-11-plannotator-research]] — 初始研究來源
- [[wiki/sources/2026-07-23-plannotator-copilot-setup]] — GitHub Copilot CLI 整合安裝手冊
- [[wiki/concepts/meta-harness]] — 另一種 agent 工具，與 Plannotator 的「加法」取徑形成對比
