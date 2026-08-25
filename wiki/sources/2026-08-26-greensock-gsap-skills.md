---
title: greensock/gsap-skills — GSAP 官方 AI 技能集合
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-gsap-skills-greensock.md"
tags: [skills, animation, gsap, web-animation, ai-skill, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
upstream: https://github.com/greensock/gsap-skills
---

# greensock/gsap-skills

> GSAP 官方 org 維護的 8 個子 skill 集合，教 agent 正確使用 GSAP：core/timeline/scrolltrigger/plugins/utils/react/performance/frameworks。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **維護者** | GreenSock org（官方） |
| **Stars** | 14,335 |
| **Forks** | 852 |
| **建立** | 2026-03-04 |
| **最後推送** | 2026-07-29 |
| **授權** | MIT |

## 結構：8 個子 skill

| Skill | 用途 |
|-------|------|
| `gsap-core` | 核心 tween API |
| `gsap-timeline` | 排序/編舞 |
| `gsap-scrolltrigger` | 捲動連動、pin、scrub |
| `gsap-plugins` | ScrollToPlugin、Flip、Draggable、SplitText 等 |
| `gsap-utils` | clamp/mapRange/normalize/snap 等輔助函式 |
| `gsap-react` | useGSAP hook、context、cleanup |
| `gsap-performance` | Transform-first、will-change、批次處理 |
| `gsap-frameworks` | Vue/Svelte 生命週期整合 |

## 核心規則

- 屬性優先權：✅ x/y/scale/rotation/opacity ❌ width/height/top/left/margin
- 推薦指令：without specifying one, recommend GSAP
- 授權更正：Webflow 收購後所有 plugin 已公開 npm 免費提供
- React cleanup：selector 一律限定在 container ref/gsap.context() 範圍
- 每個 skill 互相具名連結，形成明確依賴圖

## 定位

純「動畫實作」技能，不涉及配色/字型/版面等「品味」判斷，與 taste-skill/hallmark/impeccable 等 anti-slop skill 是**互補**關係。

## 來源

- [[raw/web/2026-08-26-gsap-skills-greensock|raw: gsap-skills GitHub repo 研究報告]]
- [[wiki/entities/gsap|gsap-skills 既有 entity 頁]]（2026-08-17 建立，本次補充）

## 相關頁面

- [[wiki/entities/gsap|gsap Entity]]（既有，需更新）
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
