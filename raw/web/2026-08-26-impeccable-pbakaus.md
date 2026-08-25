---
title: "pbakaus/impeccable — GitHub repo"
description: "Paul Bakaus 開發的單一 skill，明確聲明「originated from Anthropic's frontend-design skill」。透過 /impeccable <command> <target> 提供 35 個命令 playbook（craft/critique/audit/polish/animate/colorize/typeset...），另有不需 LLM 的 59 條 deterministic detector CLI。"
url: https://github.com/pbakaus/impeccable
source_domain: github.com
author:
  - pbakaus
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
# pbakaus/impeccable

> Source: [github.com/pbakaus/impeccable](https://github.com/pbakaus/impeccable)
> Clipped: 2026-08-26（AI 結構化研究報告，所有引用皆來自實際 fetch 的 README / `skill/SKILL.src.md` / `skill/reference/*.md` / GitHub API）

## 摘要

"The design language that makes your AI harness better at design" — 作者 Paul Bakaus（paulbakaus.com，GSAP/web animation 圈知名人物）。README 自述「originated from Anthropic's frontend-design skill」，即在 Anthropic 官方 `frontend-design` skill 基礎上發展而來。Apache 2.0，GitHub API 核實：stargazers 62,573、forks 3,828、created_at 2025-11-16、pushed_at 2026-08-25（近乎即時維護）。

## 結構：單一 skill + 35 個命令 playbook（非各自獨立的子 skill）

Frontmatter（`skill/SKILL.src.md`）：
- name: `impeccable`
- description: "Use when the user wants to design, redesign, shape, critique, audit, polish, clarify, distill, harden, optimize, adapt, animate, colorize, extract, or otherwise improve a frontend interface... Not for backend-only or non-UI tasks."
- 呼叫方式：`/impeccable <command> <target>`

`skill/reference/` 下 35 個命令檔（分 4 類：Build/Evaluate/Refine/Enhance）：`craft.md`、`critique.md`、`audit.md`、`polish.md`、`bolder.md`、`quieter.md`、`distill.md`、`harden.md`、`animate.md`、`colorize.md`、`typeset.md`、`layout.md`、`delight.md`、`overdrive.md`、`clarify.md`、`adapt.md`（+ `.native.md`/`ios.md`/`android.md` 變體）、`craft-floor.md`、`new-work.md`、`routing.md`、`doctor.md`、`hooks.md`、`live.md`/`live-setup.md`、`visualize.md`。

## 核心規則（逐字引用自 `skill/reference/craft-floor.md`）

- 排版：「body measure 65–75ch, display max 6rem」；「tracking floor -0.04em」（註記 -0.02~-0.03em 通常更好讀）；避免系統顯示字型，"source and self-host a face whose character matches the approved lettering."
- 對比：「body and placeholder text ≥4.5:1, large text ≥3:1」；彩色背景上「tint secondary text from that hue or the foreground; never gray.」
- 間距：「tight groups, generous separation, more space above a heading than below it.」
- 陰影：「shadows carry an offset and a soft blur. A zero-offset colored halo is decoration」；「declare elevation once, border or shadow.」
- 動效：「one authored moment, not scattered effects」；「exponential ease-out from an already-visible default.」
- 明確拒絕的反模式：一模一樣的卡片網格、標題上方的 kicker/eyebrow 標籤（"delete the label and let the heading speak"）、gradient text、裝飾性玻璃擬態、Unicode 符號圖示。
- 核心立場：「the brief wins」優先於機械式反模式規避；區分 refinement（保留既有設計）vs. redesign。
- 另有 59 條不需 LLM/API key 的 deterministic detector 規則，可透過 CLI (`npx impeccable detect`) 或瀏覽器擴充套件執行。

> 注意：repo 根目錄的 `DESIGN.md`（"Neo Kinpaku" 暗漆+金箔美學）是該 repo 自己官網套用的設計系統，不是給使用者套用的通用規則集，兩者不可混淆。

## 驗證備註

repo 真實存在、公開、積極維護；所有引用內容皆從 raw GitHub URL 與 GitHub API 直接取得，未憑空杜撰。
