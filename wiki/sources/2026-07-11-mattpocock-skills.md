---
title: mattpocock/skills — 工程實踐 Skills 集合
type: source
created: 2026-07-11
updated: 2026-07-11
sources: 1
tags:
  - skills
  - engineering-practices
  - coding-agent
  - matt-pocock
upstream: https://github.com/mattpocock/skills
last_checked: 2026-07-11
collection: sources
provenance_url:
---

# mattpocock/skills

> 視覺化：[[wiki/visualizations/mattpocock-skills.canvas|mattpocock/skills Canvas]]

Matt Pocock（Total TypeScript 作者）維護的 coding agent skills 集合。核心理念：用工程方法做 real engineering，不是 vibe coding。

## 設計哲學

1. **小而可組合** — 每個 skill 做一件事，可自由搭配
2. **模型無關** — 適用於 Claude Code / Codex / Pi 等任何 coding agent
3. **強調基礎** — TDD、code review、領域建模、架構設計
4. **非流程綁定** — 不像 GSD/BMAD 那樣強制接管整個流程，而是讓人保留控制權

## 安裝方式

```bash
# 方式 1：skills.sh（可編輯，推薦）
npx skills@latest add mattpocock/skills

# 方式 2：Claude Code plugin（只讀，自動更新）
claude plugins install mattpocock-skills

# 更新
npx skills update
```

安裝後需在 agent 中執行 `/setup-matt-pocock-skills` 進行初始化設定。

---

## Skills 完整清單

> **更新指引：** 當上游 repo 有變動時，比對此表與 `https://api.github.com/repos/mattpocock/skills/git/trees/main?recursive=1` 的 SKILL.md 列表，更新「上游狀態」欄位。

### Engineering — 工程類

| Skill | 類型 | 安裝狀態 | 上游狀態 | 說明 |
|-------|------|---------|---------|------|
| `setup-matt-pocock-skills` | User | ✅ 已安裝 | ✅ 活躍 | 初始化設定（issue tracker、labels、文件位置）。**每個 repo 要跑一次** |
| `ask-matt` | User | ⬜ 未安裝 | ✅ 活躍 | 路由器 — 詢問情境後推薦該用哪個 skill |
| `grill-with-docs` | User | ⬜ 未安裝 | ✅ 活躍 | 深度追問 + 自動建立專案領域模型（CONTEXT.md + ADR） |
| `to-spec` | User | ✅ 已安裝 | ✅ 活躍 | 把對話轉成規格書，發到 issue tracker |
| `to-tickets` | User | ✅ 已安裝 | ✅ 活躍 | 把 spec 拆成一張張 tracer-bullet tickets |
| `implement` | User | ⬜ 未安裝 | ✅ 活躍 | 依照 spec/tickets 實作，驅動 TDD + code review |
| `wayfinder` | User | ⬜ 未安裝 | ✅ 活躍 | 規劃大型工作（跨多個 session），建立 investigation tickets |
| `triage` | User | ⬜ 未安裝 | ✅ 活躍 | Issue 分流狀態機 |
| `improve-codebase-architecture` | User | ✅ 已安裝 | ✅ 活躍 | 掃描 codebase 找可改善之處，產生 HTML 報告 |
| `tdd` | Model | ✅ 已安裝 | ✅ 活躍 | 紅-綠-重構的 TDD 循環 |
| `code-review` | Model | ✅ 已安裝 | ✅ 活躍 | 雙軸 review：Standards + Spec |
| `diagnosing-bugs` | Model | ⬜ 未安裝 | ✅ 活躍 | 紀律化的 bug 診斷循環：reproduce → minimise → fix |
| `prototype` | Model | ⬜ 未安裝 | ✅ 活躍 | 建 throwaway prototype 驗證設計問題 |
| `research` | Model | ⬜ 未安裝 | ✅ 活躍 | 針對問題做深度調查，產出引用式 Markdown |
| `domain-modeling` | Model | ⬜ 未安裝 | ✅ 活躍 | 建立並維護專案領域模型 |
| `codebase-design` | Model | ⬜ 未安裝 | ✅ 活躍 | 設計 deep modules：大量行為藏在小介面後 |
| `resolving-merge-conflicts` | Model | ⬜ 未安裝 | ✅ 活躍 | 逐 hunk 解決 git conflict |

### Productivity — 生產力類

| Skill | 類型 | 安裝狀態 | 上游狀態 | 說明 |
|-------|------|---------|---------|------|
| `grill-me` | User | ✅ 已安裝 | ✅ 活躍 | 深度追問（不含領域模型建立） |
| `grilling` | Model | ⬜ 未安裝 | ✅ 活躍 | 追問的核心循環（被 grill-me / grill-with-docs 呼叫） |
| `handoff` | User | ⬜ 未安裝 | ✅ 活躍 | 把對話壓縮成交接文件，供另一個 agent 續作 |
| `teach` | User | ⬜ 未安裝 | ✅ 活躍 | 多 session 教學，用當前目錄當狀態化工作區 |
| `writing-great-skills` | User | ⬜ 未安裝 | ✅ 活躍 | 如何寫好 skill 的參考指南 |

### In-progress — 實驗性（不建議安裝）

| Skill | 狀態 | 說明 |
|-------|------|------|
| `batch-grill-me` | 🚧 實驗中 | 批次版 grill-me |
| `claude-handoff` | 🚧 實驗中 | Claude Code 專用 handoff |
| `loop-me` | 🚧 實驗中 | 迴圈式追問 |
| `setup-ts-deep-modules` | 🚧 實驗中 | TS deep modules 設定 |
| `to-questionnaire` | 🚧 實驗中 | 對話轉問卷 |
| `wizard` | 🚧 實驗中 | 導引式操作 |
| `writing-beats` | 🚧 實驗中 | 寫作節奏 |
| `writing-fragments` | 🚧 實驗中 | 片段寫作 |
| `writing-shape` | 🚧 實驗中 | 形狀寫作 |

### Misc — 雜項

| Skill | 狀態 | 說明 |
|-------|------|------|
| `git-guardrails-claude-code` | ✅ 活躍 | Claude Code 的 git 保護措施 |
| `migrate-to-shoehorn` | ✅ 活躍 | 遷移到 Shoehorn |
| `scaffold-exercises` | ✅ 活躍 | 腳手架練習 |
| `setup-pre-commit` | ✅ 活躍 | pre-commit hooks 設定 |

### Personal — 個人用途（與工程無關）

| Skill | 狀態 | 說明 |
|-------|------|------|
| `edit-article` | ✅ 活躍 | 編輯文章 |
| `obsidian-vault` | ✅ 活躍 | Obsidian vault 操作 |

---

## User-invoked vs Model-invoked

- **User-invoked** — 只能由使用者手動呼叫（如 `/grill-me`）。負責**編排流程**。
- **Model-invoked** — 可由使用者手動呼叫，或 agent 自動根據情境選用。持有**可重用的紀律**。
- User-invoked skill 可呼叫 model-invoked skill，但不會呼叫另一個 user-invoked skill。

---

## 對本專案的建議安裝清單

> 對象專案：Pi Web/Desktop 前端（React + Node.js）

### 強烈建議（7 個）

| Skill | 為什麼需要 |
|-------|-----------|
| `setup-matt-pocock-skills` | 初始化設定 |
| `grill-me` | 開工前追問清楚需求 |
| `to-spec` | 需求轉正式 spec |
| `to-tickets` | 拆 tickets 管理進度 |
| `tdd` | 測試驅動開發 |
| `code-review` | 程式碼品質保障 |
| `improve-codebase-architecture` | 長期維護 codebase 設計 |

### 視情況安裝

| Skill | 場景 |
|-------|------|
| `implement` | 讓 agent 自動實作 tickets |
| `wayfinder` | 專案夠大時 |
| `prototype` | 驗證 UI 設計時 |
| `diagnosing-bugs` | 除錯時 |
| `research` | 調查技術選型時 |

---

## 維護指南

### 當上游有更新時

1. 抓取最新 SKILL.md 列表：
   ```bash
   curl -sL "https://api.github.com/repos/mattpocock/skills/git/trees/main?recursive=1" | \
     python3 -c "import sys,json; [print(x['path']) for x in json.load(sys.stdin).get('tree',[]) if 'SKILL.md' in x['path']]"
   ```
2. 比對上方表格，更新「上游狀態」欄位（✅ 活躍 / ❌ 已移除 / 🆕 新增）
3. 如有新 skill，判斷是否需要安裝並更新「安裝狀態」
4. 更新 `last_checked` frontmatter
5. 在 `wiki/log.md` 附加一條更新紀錄

### 安裝/卸載 skill 後

1. 更新上方表格的「安裝狀態」欄位（✅ 已安裝 / ⬜ 未安裝）
2. 更新 `wiki/index.md` 的統計數字
3. 在 `wiki/log.md` 附加一條紀錄

---

## 相關頁面

- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|mattpocock/skills — 完整 AI Coding 工作流程教學]] — 官方教學影片
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — Skills repo entity page
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — AI 編碼工作流程概念
- `pi-coding-agent` — Pi harness 本體（尚未建立獨立 entity 頁）
- [[wiki/entities/wiki-knowledge]] — 知識庫操作 skill
- `skill-creator` — 如何建立 skill（外部 skill，尚未建立獨立 entity 頁）
