---
title: vercel-labs/web-interface-guidelines — Web UI 合規稽核清單
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-web-interface-guidelines-vercel-labs.md"
tags: [skills, ai-frontend, vercel, accessibility, code-review, agent-skill]
topics: [skill-cases-and-comparisons]
upstream: https://github.com/vercel-labs/web-interface-guidelines
---

# vercel-labs/web-interface-guidelines

> Vercel 維護的單一扁平準則文件，透過 install.sh fan-out 成多工具格式，用途是程式碼審查/合規檢查。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **維護者** | Vercel (vercel-labs org) |
| **Stars** | 807 |
| **Commits** | 58 |
| **授權** | MIT |
| **官網** | vercel.com/design/guidelines |

## 結構：不是多技能集合

單一扁平準則文件 `command.md`，透過 `install.sh` fan-out 成 7 種工具格式：
- Claude Code slash command
- Amp/Cursor/OpenCode command
- Windsurf global_rules.md
- Antigravity SKILL.md（唯一在安裝時真正生成 SKILL.md 的路徑）
- AGENTS.md（給 Codex 貼進專案用）

## 核心規則：20 章節稽核清單

MUST/NEVER 祈使句格式：
- Never `outline-none` without focus replacement
- Never `transition: all` — list properties explicitly
- Submit button stays enabled until request starts
- Large lists (>50 items): virtualize
- `…` not `...` / Curly quotes not straight
- Active voice: 'Install the CLI' not 'The CLI will be installed'

用法：`Review these files for compliance: $ARGUMENTS`，agent 逐檔案 grep 式稽核。

## 定位

**不是**「生成新 UI」的技能，是「生成完之後拿來審查」的稽核 checklist。與 taste-skill/hallmark/impeccable 等生成型 skill 是**互補、串接**關係（先生成、後用本清單審查）。

## 來源

- [[raw/web/2026-08-26-web-interface-guidelines-vercel-labs|raw: web-interface-guidelines GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/vercel-labs-web-interface-guidelines|web-interface-guidelines Entity]]
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
