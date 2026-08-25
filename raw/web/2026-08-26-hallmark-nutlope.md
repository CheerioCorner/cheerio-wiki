---
title: "nutlope/hallmark — GitHub repo"
description: "Together AI (nutlope) 開發的單一 skill，anti-AI-slop，內建 21 種具名 macrostructure、diversification 規則（避免連續兩次相同版型）、58 道 post-emit「slop test」門檻、六軸自評分數（<3 分需重做）。"
url: https://github.com/nutlope/hallmark
source_domain: github.com
author:
  - nutlope
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
# nutlope/hallmark

> Source: [github.com/nutlope/hallmark](https://github.com/nutlope/hallmark)
> Clipped: 2026-08-26（AI 結構化研究報告，所有引用皆來自實際 fetch 的 README / `skills/hallmark/SKILL.md` / `references/*.md`）

## 摘要

「Anti-AI-slop design skill for Claude Code, Cursor, and Codex that refuses to look AI-generated.」作者 Together AI 的 Hassan El Mghari（nutlope）。約 27,000 stars，積極維護（v1.1.0，有 ROADMAP.md，官網 usehallmark.com 有 live demo）。是真正的 agent skill 套件，不是獨立的 markdown-to-image app。

## 結構：單一 skill（`skills/hallmark/SKILL.md`）+ ~25 個條件載入的 reference 檔案

Frontmatter：
```yaml
name: hallmark
description: "Anti-AI-slop design skill for greenfield pages, audits, redesigns, and design extraction from URLs or screenshots. Use when the user asks to build a new app or landing page, wants to redesign something, invokes Hallmark by name, or uses audit/redesign/study."
version: 1.1.0
```
內部以「動詞」而非獨立 skill 區分行為：預設（建新 UI）、`audit`、`redesign`、`study`、`component`。Reference 檔包含 `typography.md`、`color.md`、`motion.md`、`anti-patterns.md`、`slop-test.md`、`macrostructures.md`、`component-cookbook.md`、`custom-theme.md`、`study.md`、`verbs/audit.md`、`verbs/redesign.md`、`contract.md` 等，是規則檔而非各自獨立可觸發的 skill。

## 核心規則

- **21 種具名「macrostructure」**（版面結構），連續兩次建置不可重複同一種（比對 `.hallmark/log.json`）。
- **多樣化規則**：主題不可重複，除非至少在三軸之一有差異：「paper band (dark/mid/light), display style (serif/sans/mono etc.), accent hue (warm/cool/neutral).」
- **58 道 post-emit「slop test」**：「Every answer must be no... If any gate fails: Fix it. Do not ship slop.」具名門檻範例：gate 34（禁止橫向捲動）、gate 38a（標題必須 `font-style: normal`，禁止斜體標題）、gate 49（禁止兩行可點擊文字）、gate 54（自動判定 hanging-header 標籤為失敗）。
- **硬規則**：顏色只能用 OKLCH 透過 CSS 自訂屬性；間距用 4pt 刻度；動效限 `transform`/`opacity`，只准三種具名 easing；每個互動元素須涵蓋 8 種狀態（default/hover/focus-visible/active/disabled/loading/error/success）；hero 標題「≤7 words, ≤50 chars.」
- **輸出前自評**：六軸各 1–5 分（Philosophy, Hierarchy, Execution, Specificity, Restraint, Variety），任一軸 <3 分觸發修改。
- 輸出以註解落款，如 `/* Hallmark · macrostructure: [name] · tone: [tone] · anchor hue: [hue] */`。

## 驗證備註

repo 真實存在、公開；README 與 SKILL.md 皆透過 raw.githubusercontent.com 實際 fetch 取得，未憑空杜撰。
