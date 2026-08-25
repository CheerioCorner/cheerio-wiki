---
title: "vercel-labs/web-interface-guidelines — GitHub repo"
description: "Vercel 維護的單一扁平準則文件（非 skills/ 目錄多技能集合），透過 install.sh 安裝腳本 fan-out 成約 7 種工具格式（Claude Code slash command、Amp/Cursor/OpenCode command、Windsurf global_rules.md、Antigravity 唯一在安裝時真正生成 SKILL.md）。用途是程式碼審查/合規檢查，非生成式設計技能。"
url: https://github.com/vercel-labs/web-interface-guidelines
source_domain: github.com
author:
  - vercel-labs
published:
clipped: 2026-08-26
type: raw-web
source_kind: web
immutable: "true"
tags:
  - raw
  - web-clip
  - ai-frontend
  - agent-skill
---
# vercel-labs/web-interface-guidelines

> Source: [github.com/vercel-labs/web-interface-guidelines](https://github.com/vercel-labs/web-interface-guidelines)
> Clipped: 2026-08-26（AI 結構化研究報告，README.md、AGENTS.md、command.md、install.sh 皆從 raw.githubusercontent.com/github.com 實際 fetch，未憑空杜撰檔名或規則）

## 摘要

Vercel（vercel-labs org）維護的一份務實的無障礙/效能 web UI 準則清單，設計成同時給人類與 AI coding agent 使用，連結 vercel.com/design/guidelines。807 stars，MIT license，58 commits——積極維護，未廢棄。

## 結構：**不是**多技能集合

Repo 根目錄**沒有** `skills/` 目錄或多個 `SKILL.md`。本質是單一扁平準則文件 `command.md`，帶真實 YAML frontmatter：
```
---
description: Review UI code for Vercel Web Interface Guidelines compliance
argument-hint: <file-or-pattern>
---
```
`install.sh` 才是把這一份文件「fan-out」成各工具格式的機制：逐字複製進 `~/.claude/commands/web-interface-guidelines.md`（Claude Code slash command）、Amp/Cursor/OpenCode 的 command 目錄、附加進 Windsurf 的 `global_rules.md`；對 Antigravity 則用 `sed` 改寫 frontmatter（插入 `name:`、移除 `argument-hint:`）產生 `~/.gemini/antigravity/global_skills/web-interface-guidelines/SKILL.md`——**只有 Antigravity 這條路徑會在安裝當下真正生出一個 SKILL.md，repo 本身並沒有**。`AGENTS.md` 是給 Codex 等其他 agent 貼進專案自己 AGENTS.md 用的近乎重複副本。`README.md` 是人類看的著陸頁。

## 核心規則

扁平合規檢查清單，分 20 個章節（Accessibility、Focus States、Forms、Animation、Typography、Content Handling、Images、Performance、Navigation & State、Touch & Interaction、Safe Areas、Dark Mode、Locale/i18n、Hydration Safety、Hover States、Content & Copy、Anti-patterns，另加 Output Format）。規則是給自動化 code review 用的簡潔 MUST/NEVER 祈使句，例：
- 「Never `outline-none` / `outline: none` without focus replacement」
- 「Never `transition: all`—list properties explicitly」
- 「Submit button stays enabled until request starts; spinner during request」
- 「Large lists (>50 items): virtualize」
- 「`…` not `...`」/「Curly quotes... not straight」
- 「Active voice: 'Install the CLI' not 'The CLI will be installed'」

用法（`command.md`）就是字面上的 `Review these files for compliance: $ARGUMENTS`——agent 逐檔案 grep 式稽核，輸出 `file:line` 發現，乾淨的檔案結尾標 `✓ pass`。

## 定位（供比較用）

這不是「生成新 UI」的技能，是「生成完之後拿來審查」的稽核 checklist，與 taste-skill/hallmark/impeccable 等生成型 skill 是互補、串接關係（先生成、後用本清單審查），不是彼此替代。

## 驗證備註

repo 真實存在、公開；所有引用內容皆直接 fetch 取得，未憑空杜撰。
