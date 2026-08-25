---
title: "leonxlnx/taste-skill — GitHub repo"
description: "Anti-Slop Frontend Framework for AI Agents. 13 個子 skill 的集合：核心 taste-skill（三旋鈕系統 + LILA RULE + em-dash 全面禁令）、soft-skill（Haptic Depth 精緻感）、minimalist-skill（Notion/Linear 風）、brutalist-skill（瑞士印刷/CRT 終端）、image-to-code-skill、redesign-skill、gpt-tasteskill、output-skill、stitch-skill、imagegen-frontend-web/mobile、brandkit。"
url: https://github.com/leonxlnx/taste-skill
source_domain: github.com
author:
  - leonxlnx
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
# leonxlnx/taste-skill

> Source: [github.com/leonxlnx/taste-skill](https://github.com/leonxlnx/taste-skill)
> Clipped: 2026-08-26（AI 結構化研究報告，非逐字複製；所有引用皆來自實際 fetch 的 README / SKILL.md / GitHub API，未憑空杜撰）

## 摘要

"The Anti-Slop Frontend Framework for AI Agents" — 一套阻止 AI coding agent 產出「通用模板感」UI 的可攜式設計指令集。作者 Leonxlnx（X: @lexnlin），MIT license。

**GitHub API 二次獨立核實**：stargazers_count 80,534、forks_count 5,519、created_at 2026-02-19、pushed_at 2026-08-24（本文撰寫前一天，持續維護中）、is_fork: false。此星數對一個 2026-02 才建立的 repo 而言明顯偏高，已用 API 直接複查兩次數字一致，但無法排除 star 灌水的可能性，記錄供人類自行判斷。

## 結構：13 個子 skill（`skills/` 目錄下各自獨立資料夾 + SKILL.md）

| Skill | 用途 | Trigger / 說明 |
|---|---|---|
| `taste-skill`（`design-taste-frontend`） | v2 預設 anti-slop skill，landing page/portfolio/redesign | "The agent reads the brief, infers the right design direction, and ships interfaces that do not look templated." |
| `taste-skill-v1` | 舊版存檔，向下相容 |  |
| `gpt-tasteskill`（`gpt-taste`） | 針對 GPT/Codex 調整的更嚴格版本 |  |
| `image-to-code-skill` | 先生成設計參考圖 → 分析 → 再寫程式碼的強制流程 |  |
| `redesign-skill`（`redesign-existing-projects`） | 稽核/改善既有專案 |  |
| `soft-skill`（`high-end-visual-design`） | 高階、剋制的精緻美感（Haptic Depth） |  |
| `minimalist-skill`（`minimalist-ui`） | 編輯感、Notion 風產品 UI |  |
| `brutalist-skill`（`industrial-brutalist-ui`） | 瑞士印刷 + 軍事終端美學，"For data-heavy dashboards, portfolios, or editorial sites that need to feel like declassified blueprints." Trigger: "high-density data presentation, retro-futuristic aesthetics, or deliberate rejection of contemporary consumer UI patterns." |
| `stitch-skill` | 與 Google Stitch 相容變體 |  |
| `output-skill`（`full-output-enforcement`） | 防止 AI 輸出半成品/placeholder |  |
| `imagegen-frontend-web` / `imagegen-frontend-mobile` | 圖片生成技能，web/mobile 介面概念圖 |  |
| `brandkit` | 品牌識別系統（Logo/配色/字型） |  |

## 核心規則（引用自 `taste-skill/SKILL.md`）

- **三旋鈕系統**：DESIGN_VARIANCE、MOTION_INTENSITY、VISUAL_DENSITY，1–10 分，baseline `8/6/4`。
- **LILA RULE**：禁止 AI 慣用的紫/藍光暈美學，最多一個 accent color，飽和度 < 80%。
- **字型**：「Inter is discouraged as default... reach for Geist, Outfit, Cabinet Grotesk, or Satoshi first」；預設禁用 Fraunces / Instrument_Serif。
- **佈局硬規則**：hero 標題 ≤2 行、副標 ≤20 字/≤4 行、hero 頂部 padding 上限 `pt-24`、nav 單行 ≤80px、禁止 3+ 個連續 zigzag 圖文區塊。
- **不可談判禁令**：「Em-dash (—) is COMPLETELY banned... the single most-violated Tell in production tests.」
- **18 項 pre-flight checklist**（零 em-dash、WCAG AA 對比、eyebrow 數量 ≤ ceil(sections/3)、每個動畫都要有理由等），"must pass or the output fails."
- Brutalist 子技能有自己的嚴格色票/幾何規則（精確 hex、"Absolute rejection of `border-radius`"）。

## 相關

- 中文導讀文章已另存於 [[raw/web/2026-08-26-AI 做的東西太醜？Taste開源專案正在教它什麼是「品味」|AI 做的東西太醜？Taste開源專案正在教它什麼是「品味」]]（vocus.cc 二手報導，可互相印證）。
