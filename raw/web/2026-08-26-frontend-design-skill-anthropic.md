---
title: "anthropics/claude-code — plugins/frontend-design/skills/frontend-design/SKILL.md"
description: "Anthropic 官方隨 Claude Code 附帶的 frontend-design plugin，只有單一 skill（無其他子 skill 綁在同 plugin），走 brainstorm→critique 的原則式流程而非硬性數字規格。本機此 session 已安裝為 frontend-design:frontend-design skill。"
url: https://github.com/anthropics/claude-code/blob/main/plugins/frontend-design/skills/frontend-design/SKILL.md
source_domain: github.com
author:
  - anthropics
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
# anthropics/claude-code — frontend-design plugin

> Source: [github.com/anthropics/claude-code/.../frontend-design/SKILL.md](https://github.com/anthropics/claude-code/blob/main/plugins/frontend-design/skills/frontend-design/SKILL.md)
> Clipped: 2026-08-26（AI 結構化研究報告，所有引用皆來自 raw.githubusercontent.com 實際 fetch 的 SKILL.md 全文）

## 摘要

Anthropic 官方維護，plugin 自述：「Generates distinctive, production-grade frontend interfaces」，「avoids generic AI aesthetics」。範圍很窄：`plugins/frontend-design/` 底下只有一個 skill 資料夾 `skills/frontend-design/`，內含單一 `SKILL.md`，沒有其他綁定子 skill。README 另外連結一個外部 companion notebook：「Frontend Aesthetics Cookbook」（`anthropics/claude-cookbooks`）。此 skill 已對應本機 session 目前安裝的 `frontend-design:frontend-design` skill（描述逐字相符）。

## 結構

Frontmatter 逐字：
```
name: frontend-design
description: Guidance for distinctive, intentional visual design when building new UI or reshaping an existing one. Helps with aesthetic direction, typography, and making choices that don't read as templated defaults.
license: Complete terms in LICENSE.txt
```
章節順序：`# Frontend Design` → `## Ground it in the subject` → `## Design principles` → `## Process: brainstorm, explore, plan, critique, build, critique again` → `## Restraint and self-critique` → `## More on writing in design`。

## 核心規則（逐字引用）

- **具名 AI 設計反模式**：「AI-generated design right now clusters around three looks: (1) a warm cream background (near #F4F1EA) with a high-contrast serif display and a terracotta accent; (2) a near-black background with a single bright acid-green or vermilion accent; (3) a broadsheet-style layout with hairline rules, zero border-radius, and dense newspaper-like columns.」指示：「Where it leaves an axis free, don't spend that freedom on one of these defaults.」
- **色彩**：「describe the palette as 4–6 named hex values.」
- **字型**：「a characterful display face that's used with restraint, a complementary body face, and a utility face for captions or data if needed」；「Pair the display and body faces deliberately, not the same families you would reach for on any other project.」
- **結構**：「Structural devices, numbering, eyebrows, dividers, labels, should encode something true about the content, not decorate it.」
- **動效**：「An orchestrated moment usually lands harder than scattered effects」；「extra animation contributes to the feeling that the design is AI-generated.」
- **剋制原則**：「Spend your boldness in one place. Let the signature element be the one memorable thing, keep everything around it quiet and disciplined.」
- **流程**：兩階段 — 先「a compact token system with color, type, layout, and signature」，再對照 brief 做 critique 後才動工；「Critique your own work as you build, taking screenshots if your environment supports it.」
- **品質底線**：「responsive down to mobile, visible keyboard focus, reduced motion respected.」

與其他規則制系統（taste-skill/hallmark 的數字硬規則）不同，本 skill 不設固定 8px grid 或 HSL 公式，而是質性原則 + brainstorm→critique 工作流。

## 驗證備註

內容三次直接 fetch raw.githubusercontent.com 取得，兩次 fetch 目錄頁確認同一 plugin 資料夾下無其他 skill 檔案；未憑空杜撰。
