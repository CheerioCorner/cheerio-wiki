---
title: google-labs-code/stitch-skills — Stitch UI 設計工具 Skill Library
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-stitch-skills-google-labs-code.md"
tags: [skills, ai-frontend, google-labs, stitch, mcp, design-system, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
upstream: https://github.com/google-labs-code/stitch-skills
---

# google-labs-code/stitch-skills

> Google Labs 為 Stitch UI 設計工具 MCP server 開發的 skill library，taste-design skill 的規則與 taste-skill LILA RULE 高度相似。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **維護者** | Google Labs Code |
| **Stars** | 8,181 |
| **Forks** | 1,097 |
| **最後推送** | 2026-08-17 |
| **授權** | Apache-2.0 |
| **注意** | "This is not an officially supported Google product" |

## 結構：三個 plugin，共約 14 個子 skill

### stitch-design plugin
`stitch::code-to-design`、`stitch::generate-design`、`stitch::manage-design-system`、`stitch::extract-design-md`、`stitch::extract-static-html`、`stitch::upload-to-stitch`

### stitch-build plugin
`stitch::react-components`、`stitch::react-native`、`remotion`、`shadcn-ui`、`react-vite-dashboard`

### stitch-utilities plugin
`design-md`、`enhance-prompt`、`stitch-loop`、`taste-design`

## 核心規則（taste-design）

- 色彩：Maximum one accent (saturation <80%)，禁止 AI purple/blue neon
- 字型：Inter BANNED，優先 Geist/Outfit/Cabinet Grotesk/Satoshi
- 版面：Asymmetric Hero sections mandatory，Grid-first over flexbox
- 動效：Spring physics (stiffness: 100, damping: 20)，Hardware-accelerated only
- 反模式：禁止捏造數據、填充式文案、emoji、霓虹光暈

## ⚠️ 與 taste-skill 的相似性

taste-design 的規則措辭（Maximum one accent <80%、Inter BANNED）與 leonxlnx/taste-skill 的 LILA RULE **幾乎一致**。可能是同一套規則被移植/fork 到 Stitch 生態系，或兩者共同參考同一份社群共識文件。**未查證確切因果關係，僅記錄觀察。**

## 來源

- [[raw/web/2026-08-26-stitch-skills-google-labs-code|raw: stitch-skills GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/google-labs-code-stitch-skills|stitch-skills Entity]]
- [[wiki/sources/2026-08-26-leonxlnx-taste-skill|taste-skill]]（疑似同源）
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
