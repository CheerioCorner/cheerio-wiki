---
title: "mattpocock/skills — 完整 AI Coding 工作流程教學"
type: source
created: 2026-07-22
updated: 2026-07-22
sources: 1
provenance:
  - kind: raw
    path: raw/youtube/mattpocock-skills.md
tags: [skills, ai-coding, workflow, matt-pocock, tutorial]
collection: sources
topics: [agent-runtime-implementations]
canonical: sources/2026-07-22-mattpocock-skills-tutorial
provenance_raw: "raw/youtube/mattpocock-skills.md"
provenance_url: https://www.youtube.com/watch?v=M6mYodf0dJM
---

> 來源：[YouTube — mattpocock/skills: A complete AI Coding workflow, end-to-end](https://www.youtube.com/watch?v=M6mYodf0dJM)
> 原始字幕：[[raw/youtube/mattpocock-skills|raw transcript]]

## 一句話

Matt Pocock 示範 `mattpocock/skills` 的主要工作流程：從安裝設定到 grill with docs → spec → tickets → implement → code review 的完整端到端流程。

## 重點摘要

### 安裝與設定

```bash
npx skills@latest add mattpocock/skills
```

- 需要 Node.js（npx 來自 Node.js）
- 安裝後執行 `/setup-matt-pocock-skills` 進行初始化
- 選擇 issue tracker（GitHub / Local Markdown / Jira / Linear 等）
- 選擇安裝範圍（project vs global）— 團隊用 project，個人用 global
- 選擇 symlink（推薦）vs copy

### 主要工作流程（Main Flow）

```
grill with docs → to spec → to tickets → implement → code review
```

#### 1. Grill with Docs（訪談階段）
- 深度追問，把模糊想法變成清晰計畫
- 自動探索 codebase，建立領域模型（context.md + ADR）
- 通常 6-20 個問題，直到達成「shared understanding」
- 不需要用 plan mode，auto mode 即可

#### 2. To Spec（規格化）
- 把討論內容壓縮成正式規格書
- 包含：problem statement、solution、user stories、implementation decisions、testing decisions
- 發布到 issue tracker（本地 markdown 或遠端）

#### 3. To Tickets（拆票）
- 把 spec 拆成可管理的 tickets
- 每個 ticket = 一個 context window / smart zone 的工作量
- 可以調整 ticket 數量（例如 3 張 → 1 張）

#### 4. Implement（實作）
- 依照 tickets 逐張實作
- 每張 ticket 完成後可選擇是否清除 context
- 內建 type check、build、verification

#### 5. Code Review（審查）
- 雙軸審查：Spec compliance + Coding standards
- 用 sub-agent 做 review（避免 self-review 偏見）
- 自動 commit 到當前 branch

### Context Window 管理

- **Smart zone**：~140k tokens 以內
- 超過 140k 會出現 attention degradation、hallucination
- 建議：每張 ticket 清除一次 context
- 大型工作 → 拆成多個 session，用 spec + tickets 衔接

### Ask Matt（教學 skill）

- 內建的教學路由器
- 啟動後會推薦該用哪個 skill
- 可以當作互動式教學使用

### Issue Tracker 整合

- 預設支援：GitHub Issues、Local Markdown
- 可擴展：Jira、Linear、Beads 等
- 設定方式：執行 setup 時告訴 agent 要用哪個 tracker

### 關鍵差異化

- **User-invoked** 為主：skill 不會自動滲透 context，佔用極少 token（660 tokens）
- **模型無關**：支援 Claude Code、Codex、Cursor 等多種 agent
- **可組合**：skill 之間可自由搭配，不強制流程

## 來源

- [[raw/youtube/mattpocock-skills|Raw transcript — YouTube M6mYodf0dJM]]
- [[wiki/sources/2026-07-11-mattpocock-skills|mattpocock/skills — 工程實踐 Skills 集合]]（repo 研究）

## 相關頁面

- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — Skills repo entity
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — AI 編碼工作流程概念
