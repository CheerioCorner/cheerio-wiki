---
title: nutlope/hallmark — Anti-AI-Slop Design Skill
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-hallmark-nutlope.md"
tags: [skills, ai-frontend, anti-slop, design-taste, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
upstream: https://github.com/nutlope/hallmark
---

# nutlope/hallmark

> Anti-AI-slop design skill for Claude Code, Cursor, and Codex — 21 種 macrostructure + 58 道 slop test + 六軸自評分數。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | Hassan El Mghari (Together AI, @nutlope) |
| **Stars** | ~27,000 |
| **版本** | v1.1.0 |
| **官網** | usehallmark.com |
| **ROADMAP** | 有 |

## 結構：單一 skill + ~25 個 conditionally loaded reference 檔

- **Frontmatter**：name: hallmark, version: 1.1.0
- **行為模式**：預設（建新 UI）、audit、redesign、study、component
- **Reference 檔**：typography.md、color.md、motion.md、anti-patterns.md、slop-test.md、macrostructures.md、component-cookbook.md 等

## 核心規則

- **21 種 macrostructure**：連續兩次不可重複（比對 .hallmark/log.json）
- **多樣化規則**：主題不可重複，至少三軸有差異
- **58 道 slop test**：gate 34（禁橫向捲動）、gate 38a（禁斜體標題）、gate 49（禁兩行可點擊文字）等
- **硬規則**：OKLCH 色彩 + CSS 自訂屬性、4pt 間距刻度、限 transform/opacity 動效
- **每個互動元素須涵蓋 8 種狀態**
- **hero 標題 ≤7 words, ≤50 chars**
- **六軸自評分**：Philosophy, Hierarchy, Execution, Specificity, Restraint, Variety（<3 分觸發修改）
- **輸出註解落款**：`/* Hallmark · macrostructure: [name] · tone: [tone] · anchor hue: [hue] */`

## 來源

- [[raw/web/2026-08-26-hallmark-nutlope|raw: hallmark GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/nutlope-hallmark|hallmark Entity]]
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
