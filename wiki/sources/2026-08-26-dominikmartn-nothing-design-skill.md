---
title: dominikmartn/nothing-design-skill — Nothing 品牌風格皮膚
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-nothing-design-skill-dominikmartn.md"
tags: [skills, ai-frontend, nothing, brand-skin, design-system, agent-skill]
topics: [skill-cases-and-comparisons]
upstream: https://github.com/dominikmartn/nothing-design-skill
---

# dominikmartn/nothing-design-skill

> 單一窄範圍 skill，套用 Nothing（手機品牌）的點陣/單色/工業風格——品牌皮膚型，非通用品味把關。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | dominikmartn |
| **Stars** | 2,748 |
| **Forks** | 165 |
| **建立** | 2026-04-01 |
| **最後推送** | 2026-04-01（單次初始 push，無新 commit） |
| **授權** | MIT |
| **版本** | 3.0.0 |

## 結構：單一 skill + 3 個 reference 檔

- `nothing-design/SKILL.md`
- `references/tokens.md`、`references/components.md`、`references/platform-mapping.md`

## 核心規則

- **字型**：Space Grotesk + Space Mono，Doto 點陣字型保留給 hero moments
- **三層規則**：Primary（顯示尺寸）、Secondary（8–16px）、Tertiary（Space Mono 全大寫）
- **間距刻度即語意**：Tight (4-8px) = belong together → Vast (64-96px) = new context
- **色彩層級**：display 100% → primary 90% → secondary 60% → disabled 40%；強調色紅 #D71921 獨立
- **反模式**：禁止漸層、陰影/模糊、skeleton loader（改用 [LOADING...] 文字）、toast（改用行內訊息）
- **動效哲學**："Percussive, not fluid" — Click not swoosh, tick not chime
- **觸發規則**：NEVER trigger automatically，必須使用者明示 "Nothing style" 或 "/nothing-design"

## 來源

- [[raw/web/2026-08-26-nothing-design-skill-dominikmartn|raw: nothing-design-skill GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/dominikmartn-nothing-design-skill|nothing-design-skill Entity]]
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
