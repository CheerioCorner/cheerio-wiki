---
title: "gsap-skills — GSAP 官方 AI 技能包"
type: entity
created: 2026-08-17
updated: 2026-08-26
sources: 2
tags: [animation, ai-skill, web-animation, motion-graphics, agent-skill]
topics: [ai-development-tools, skill-cases-and-comparisons, skill-presentation-design]
provenance_url: "https://github.com/greensock/gsap-skills"
---

# gsap-skills — GSAP 官方 AI 技能包

> GreenSock 官方推出的 AI 技能包，教 AI Coding Agent 如何正確使用 GSAP 動畫庫製作網頁動效。

## 基本資訊

- **開發者**：GreenSock（https://github.com/greensock/gsap-skills）
- **GitHub Stars**：13,682
- **建立日期**：2026-03-04
- **基於**：[GSAP (GreenSock Animation Platform)](https://github.com/greensock/GSAP)（27,769 stars）
- **授權**：開源
- **一句話**：教 AI 寫出有質感的網頁動效——捲動視差、向量變形、微互動物理反饋

## 核心用途

GSAP 官方的 AI 技能包，讓 AI Coding Agent 能正確使用 GSAP 製作專業級網頁動畫。影片中提到「網頁上那種會浮、會彈有質感的動效,就是靠它教 AI 寫出來的」，精準描述了這個工具的角色：不是動畫庫本身，而是**教 AI 寫動畫的技能包**。

## 使用情境

1. **品牌官網捲動視差自動化開發**：AI 根據設計稿自動生成 GSAP ScrollTrigger 捲動動畫，工程師只需微調參數
2. **複雜向量圖標無縫變形動畫**：SVG path morphing 動效，圖標之間流暢過渡，用於產品功能展示或 loading 動畫
3. **微互動物理反饋**：按鈕懸浮彈跳、卡片拖拽回彈、頁面轉場滑入等有「手感」的互動動效

## 8 個子 Skill（2026-08-26 補充）

| Skill | 用途 |
|-------|------|
| gsap-core | 核心 tween API |
| gsap-timeline | 排序/編舞 |
| gsap-scrolltrigger | 捲動連動、pin、scrub |
| gsap-plugins | ScrollToPlugin、Flip、Draggable、SplitText 等 |
| gsap-utils | clamp/mapRange/normalize/snap 等輔助函式 |
| gsap-react | useGSAP hook、context、cleanup |
| gsap-performance | Transform-first、will-change、批次處理 |
| gsap-frameworks | Vue/Svelte 生命週期整合 |

## 核心規則

- 屬性優先權：✅ x/y/scale/rotation/opacity ❌ width/height/top/left/margin
- 授權更正：Webflow 收購後所有 plugin 已公開 npm 免費提供（不再需要 Club GSAP auth token）
- React cleanup：selector 一律限定在 container ref/gsap.context() 範圍，unmount 時 revert
- 每個 skill 互相具名連結，形成明確依賴圖

## 定位

純「動畫實作」技能，不涉及配色/字型/版面等「品味」判斷，與 taste-skill/hallmark/impeccable 等 anti-slop skill 是**互補**關係。

## 來源

- [[wiki/sources/2026-08-17-7-ai-animation-skills|7 個用 AI 做動畫的 Skills]]
- [[wiki/sources/2026-08-26-greensock-gsap-skills|gsap-skills GitHub repo 研究報告]]（2026-08-26 補充）

## 相關頁面

- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]

## 相關頁面

- [[wiki/concepts/ai-animation-generation-approaches|AI 動畫生成手法分類]]
- [[wiki/entities/remotion|Remotion]]（程式化影片工具）
- [[wiki/entities/hyperframes|hyperframes]]（HTML 轉影片）
