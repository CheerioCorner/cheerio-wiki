---
title: "dominikmartn/nothing-design-skill — GitHub repo"
description: "單一、窄範圍 skill，套用消費電子品牌 Nothing（Nothing Phone）的點陣/單色/工業風格；明確要求「NEVER trigger automatically」，必須使用者明示才啟動——與其他自主觸發的 anti-slop skill 定位不同（是「品牌皮膚」而非「通用品味把關」）。"
url: https://github.com/dominikmartn/nothing-design-skill
source_domain: github.com
author:
  - dominikmartn
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
# dominikmartn/nothing-design-skill

> Source: [github.com/dominikmartn/nothing-design-skill](https://github.com/dominikmartn/nothing-design-skill)
> Clipped: 2026-08-26（AI 結構化研究報告；`gh api` 取得 repo 樹狀結構作為 ground truth，與 README/SKILL.md 的 WebFetch 內容交叉核實一致）

## 摘要

Claude Code skill，依 Nothing（手機品牌）的單色、排版導向、工業風視覺語言生成 UI。作者 dominikmartn（`gh api` 核實）。2,748 stars、165 forks、MIT license、3 open issues，未封存。建立與推送日期同為 2026-04-01（單次初始 push，之後無新 commit），`updated_at` 2026-08-25（可能只是 star/watch 活動，非新 commit）。

## 結構：單一 skill（非集合）

`nothing-design/SKILL.md` 一份，加三個 reference 檔（非獨立 skill）：`references/tokens.md`、`references/components.md`、`references/platform-mapping.md`。

- name: `nothing-design`
- 用途：生成 HTML/CSS、SwiftUI 或 React/Tailwind 的 Nothing 品牌風格 UI
- Trigger（frontmatter 逐字引用）：「This skill should be used when the user explicitly says 'Nothing style', 'Nothing design', '/nothing-design', or directly asks to use/apply the Nothing design system. NEVER trigger automatically for generic UI or design tasks.」
- `version: 3.0.0`，`allowed-tools: [Read, Write, Edit, Glob, Grep]`

## 核心規則

- 字型：Space Grotesk + Space Mono，Doto（點陣字型）保留給「hero moments」。單一畫面最多 2 個字型家族、3 個尺寸、2 個字重。
- 「三層規則」：Primary（顯示尺寸）、Secondary（內文，8–16px 分組）、Tertiary（Space Mono，全大寫，貼齊邊緣）——「that's it.」
- 間距刻度即語意：Tight (4-8px) = "belong together"、Medium (16px)、Wide (32-48px) = "new group"、Vast (64-96px) = "new context"。規則：「If a divider line is needed, the spacing is probably wrong.」
- 色彩層級：`--text-display (100%) → --text-primary (90%) → --text-secondary (60%) → --text-disabled (40%)`，強調色紅 `#D71921` 特別排除在層級之外：「Red is not part of the hierarchy. It's an interrupt — 'look HERE, NOW.'」
- 反模式清單：禁止漸層、陰影/模糊、skeleton loader（改用 `[LOADING...]` 文字）、toast 彈窗（改用 `[SAVED]`/`[ERROR: ...]` 行內訊息）、填色多色圖示或 emoji、彈簧/回彈 easing；圓角上限 16px（或 pill 999px / 技術感 4-8px）。
- 「Percussive, not fluid」動效哲學——「Click not swoosh, tick not chime.」

## 定位（供比較用）

這是「品牌皮膚」型 skill，不是通用的「品味把關」skill——明確禁止自動觸發，必須使用者主動指名。與 taste-skill 的 brutalist-skill 子技能同屬「特定美學風格」類別，但 nothing-design-skill 更窄、更明確綁定單一真實品牌識別（而非泛用的工業/軍事美學）。

## 驗證備註

repo 真實存在、公開，維護活動有限（單次初始 push 後未見新 commit）；所有引用內容皆直接 fetch 取得並經 `gh api` 交叉核實，未憑空杜撰。
