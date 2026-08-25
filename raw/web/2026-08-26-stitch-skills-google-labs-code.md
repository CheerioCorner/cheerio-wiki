---
title: "google-labs-code/stitch-skills — GitHub repo"
description: "Google Labs 為其 Stitch UI 設計工具 MCP server 開發的 skill library，分三個 plugin（stitch-design/stitch-build/stitch-utilities）共約 14 個子 skill。其中 taste-design skill 的規則與 taste-skill 高度相似（LILA RULE 式禁令、Inter 禁用），疑似同源。"
url: https://github.com/google-labs-code/stitch-skills
source_domain: github.com
author:
  - google-labs-code
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
# google-labs-code/stitch-skills

> Source: [github.com/google-labs-code/stitch-skills](https://github.com/google-labs-code/stitch-skills)
> Clipped: 2026-08-26（AI 結構化研究報告；README.md 與兩份 SKILL.md 皆從 raw.githubusercontent.com 實際 fetch，並用 GitHub API 核實 repo 存在與活躍度。注意：WebFetch 透過小模型摘要而非逐字元回傳，引用內容準確但不保證逐字元精確）

## 摘要

「A library of Agent Skills designed to work with the Stitch MCP server」，服務 Google 的 Stitch UI 設計工具。README 聲明「This is not an officially supported Google product」，Apache-2.0。GitHub API 核實：8,181 stars、1,097 forks、last pushed 2026-08-17。

## 結構：三個 plugin，共約 14 個子 skill（`skills/<name>/SKILL.md`，各自附 `scripts/`、`resources/`、`examples/`）

- **stitch-design plugin**：`stitch::code-to-design`、`stitch::generate-design`、`stitch::manage-design-system`、`stitch::extract-design-md`、`stitch::extract-static-html`、`stitch::upload-to-stitch`
- **stitch-build plugin**：`stitch::react-components`、`stitch::react-native`、`remotion`、`shadcn-ui`、`react-vite-dashboard`
- **stitch-utilities plugin**：`design-md`、`enhance-prompt`、`stitch-loop`、`taste-design`

Frontmatter 範例：
- `taste-design` — "Semantic Design System Skill for Google Stitch. Generates agent-friendly DESIGN.md files that enforce premium, anti-generic UI standards — strict typography, calibrated color, asymmetric layouts, perpetual micro-motion, and hardware-accelerated performance."
- `stitch::generate-design` — "Generate new screens from text prompts or images, edit existing screens with prompts and design system tokens, and generate design variants using Stitch MCP..."

## 核心規則（引用自 `taste-design/SKILL.md`）

- 色彩：「Maximum one accent (saturation <80%). Banned: AI purple/blue neon aesthetics, pure black (#000000).」
- 字型：「Inter is BANNED for premium/creative contexts.」強制優先 Geist、Outfit、Cabinet Grotesk、Satoshi；儀表板禁用一般 serif。
- 版面：「No overlapping elements. Asymmetric Hero sections mandatory when variance exceeds 4. Grid-first over flexbox.」
- 動效：「Spring physics (stiffness: 100, damping: 20). Hardware-accelerated transforms/opacity only.」
- 反模式清單：禁止捏造數據、填充式 UI 文案（"Scroll to explore"）、emoji、霓虹光暈、"Elevate"/"Unleash" 之類陳腔濫調。
- `generate-design/SKILL.md` 另有嚴格分工規則：「Do NOT include hex codes, font names, color palettes, roundness values, or any design system tokens in a generation prompt」——token 只在專案/design-system 層管理，不混進生成 prompt。

## 與 taste-skill 的相似性（供人類判斷是否同源）

`taste-design` 的規則措辭（「Maximum one accent (saturation <80%)」「Inter is BANNED」）與 [[raw/web/2026-08-26-taste-skill-leonxlnx|leonxlnx/taste-skill]] 的 LILA RULE 幾乎一致，可能是同一套規則被移植/fork 到 Stitch 生態系，或兩者共同參考同一份社群共識文件；未進一步查證確切因果關係，僅記錄觀察。

## 驗證備註

repo 真實存在、公開；所有引用內容皆從 raw.githubusercontent.com 與 GitHub API 直接取得，未憑空杜撰。
