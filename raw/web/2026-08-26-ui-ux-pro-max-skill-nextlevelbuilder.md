---
title: "nextlevelbuilder/ui-ux-pro-max-skill — GitHub repo"
description: "AI skill collection 提供跨平台/框架的 UI/UX 設計智慧：搜尋式推理引擎（79 種風格、192 色票、74 字型配對、119 條 UX 準則、105 icon、17 GSAP presets、25 圖表類型、22 技術棧），10 類優先排序規則（Accessibility 為 CRITICAL），三層 token 架構。"
url: https://github.com/nextlevelbuilder/ui-ux-pro-max-skill
source_domain: github.com
author:
  - nextlevelbuilder
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
# nextlevelbuilder/ui-ux-pro-max-skill

> Source: [github.com/nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)
> Clipped: 2026-08-26（AI 結構化研究報告，所有引用皆來自實際 fetch 的 README / `.claude/skills/*/SKILL.md` / GitHub API）

## 摘要

提供「design intelligence for building professional UI/UX across multiple platforms and frameworks」，透過可搜尋的推理引擎產生客製化設計系統（樣式、色票、字型、模式）。MIT license，homepage uupm.cc。

**GitHub API 二次獨立核實**：stargazers_count 120,912、forks_count 12,977、created_at 2025-11-30、pushed_at 2026-08-25、is_fork: false。與 taste-skill 同樣星數異常偏高（此 repo 建立僅約 9 個月），已用 API 直接複查兩次數字一致，無法排除 star 灌水，記錄供人類自行判斷。

## 結構：多個子 skill（`.claude/skills/` 下各自 SKILL.md）

| Skill | 用途 | Trigger（frontmatter 逐字引用） |
|---|---|---|
| `ui-ux-pro-max` | 旗艦/orchestrator | "This skill should be used when designing, building, reviewing, or fixing interfaces, including pages, components, design systems, accessibility, interaction, responsive layout, typography, color, charts, and stack-specific UI implementation." |
| `design-system` | Token 架構 + 簡報生成 | "Use for design tokens, systematic design, brand-compliant presentations." |
| `ui-styling` | shadcn/ui + Tailwind + canvas 視覺 | "Use when building user interfaces, implementing design systems, creating responsive layouts, adding accessible components... implementing dark mode, generating visual designs and posters." |
| `brand` | 品牌聲音/視覺識別治理 | "Activate for branded content, tone of voice, marketing assets, brand compliance, style guides." |
| `banner-design` | 社群/廣告/網頁/印刷橫幅 | "design, create, generate banner"（涵蓋 FB/X/LinkedIn/YouTube/IG/Google Display 等） |
| `slides` | HTML 簡報 + Chart.js | "Create strategic HTML presentations with Chart.js, design tokens, responsive layouts, copywriting formulas, and contextual slide strategies." |
| `design` | 總 orchestrator：logo（55 風格，Gemini）、CIP mockup（50 種交付）、slides、banners（22 風格）、icons（15 風格）、社群圖片 |  |

## 核心規則：10 類優先排序規則（引用自 `ui-ux-pro-max/SKILL.md` "Rule Categories by Priority"）

1. Accessibility（CRITICAL）— 對比 4.5:1、alt text、鍵盤導覽、ARIA；禁止移除 focus ring。
2. Touch & Interaction（CRITICAL）— 最小觸控目標 44×44px、間距 ≥8px、禁止純 hover 狀態。
3. Performance（HIGH）— WebP/AVIF、lazy load、CLS < 0.1。
4. Style Selection（HIGH）— 依產品類型匹配、SVG icon 而非 emoji。
5. Layout & Responsive（HIGH）— mobile-first、禁固定 px 寬度、絕不停用縮放。
6. Typography & Color（MEDIUM）— 16px 內文/1.5 行高、語意 token；拒絕 <12px 文字與元件內寫死 hex。
7. Animation（MEDIUM）— 情境感知時長、尊重 reduced-motion。
8. Forms & Feedback（MEDIUM）— 可見標籤、行內錯誤訊息、漸進揭露。
9. Navigation（HIGH）— 底部導覽 ≤5 項、可預期的返回行為。
10. Charts & Data（LOW）— 圖例/tooltip、不可僅用顏色表達意義。

底層是三層 token 架構（primitive → semantic → component，例：`#2563EB` → `--color-primary` → `--button-bg`），由 validator script 檢查是否有寫死 hex/字型；並以 Python+CSV 資料集驅動「產品類型 → 風格 → 色票 → 字型」的搜尋式匹配，而非靜態模板。

## 驗證備註

repo 真實存在、可 fetch，README/GitHub API/7 個 SKILL.md 皆為實際取得內容；唯一需要人類自行複核的是異常高的星數。
