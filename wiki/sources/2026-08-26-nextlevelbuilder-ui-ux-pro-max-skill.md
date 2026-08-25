---
title: nextlevelbuilder/ui-ux-pro-max-skill — 跨平台 UI/UX 設計 Intelligence
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-ui-ux-pro-max-skill-nextlevelbuilder.md"
tags: [skills, ai-frontend, ui-ux, design-system, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
upstream: https://github.com/nextlevelbuilder/ui-ux-pro-max-skill
---

# nextlevelbuilder/ui-ux-pro-max-skill

> 提供「design intelligence for building professional UI/UX across multiple platforms and frameworks」，透過可搜尋的推理引擎產生客製化設計系統。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | nextlevelbuilder |
| **Stars** | 120,912 ⚠️ |
| **Forks** | 12,977 |
| **建立** | 2025-11-30 |
| **最後推送** | 2026-08-25 |
| **授權** | MIT |
| **官網** | uupm.cc |

> ⚠️ **Star 數異常**：9 個月即達 120k+ stars，GitHub API 兩次核實數字一致，但無法排除 star 灌水可能性。記錄供 Cheer 自行判斷。

## 結構：多個子 skill

| Skill | 用途 |
|-------|------|
| `ui-ux-pro-max` | 旗艦/orchestrator |
| `design-system` | Token 架構 + 簡報生成 |
| `ui-styling` | shadcn/ui + Tailwind + canvas 視覺 |
| `brand` | 品牌聲音/視覺識別治理 |
| `banner-design` | 社群/廣告/網頁/印刷橫幅 |
| `slides` | HTML 簡報 + Chart.js |
| `design` | 總 orchestrator：logo/CIP mockup/slides/banners/icons |

## 核心規則：10 類優先排序

1. **Accessibility (CRITICAL)** — 對比 4.5:1、alt text、鍵盤導覽、ARIA
2. **Touch & Interaction (CRITICAL)** — 最小觸控 44×44px、間距 ≥8px
3. **Performance (HIGH)** — WebP/AVIF、lazy load、CLS < 0.1
4. **Style Selection (HIGH)** — 依產品類型匹配、SVG icon
5. **Layout & Responsive (HIGH)** — mobile-first、禁固定 px
6. **Typography & Color (MEDIUM)** — 16px 內文/1.5 行高、語意 token
7. **Animation (MEDIUM)** — 情境感知時長、尊重 reduced-motion
8. **Forms & Feedback (MEDIUM)** — 可見標籤、行內錯誤
9. **Navigation (HIGH)** — 底部導覽 ≤5 項
10. **Charts & Data (LOW)** — 圖例/tooltip

底層是三層 token 架構（primitive → semantic → component），Python+CSV 資料集驅動搜尋式匹配。

## 來源

- [[raw/web/2026-08-26-ui-ux-pro-max-skill-nextlevelbuilder|raw: ui-ux-pro-max-skill GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/nextlevelbuilder-ui-ux-pro-max-skill|ui-ux-pro-max-skill Entity]]
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
