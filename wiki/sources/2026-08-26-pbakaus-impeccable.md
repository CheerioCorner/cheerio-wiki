---
title: pbakaus/impeccable — 基於 Anthropic frontend-design 的設計語言
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-impeccable-pbakaus.md"
tags: [skills, ai-frontend, design-taste, deterministic-detector, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
upstream: https://github.com/pbakaus/impeccable
---

# pbakaus/impeccable

> "The design language that makes your AI harness better at design" — 源自 Anthropic 官方 frontend-design skill 的進化版本。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | Paul Bakaus (paulbakaus.com，GSAP/web animation 圈知名人物) |
| **Stars** | 62,573 |
| **Forks** | 3,828 |
| **建立** | 2025-11-16 |
| **最後推送** | 2026-08-25 |
| **授權** | Apache 2.0 |

## 結構：單一 skill + 35 個命令 playbook + 59 條 deterministic detector

- **呼叫方式**：`/impeccable <command> <target>`
- **命令分 4 類**：Build / Evaluate / Refine / Enhance
- **35 個 playbook**：craft、critique、audit、polish、animate、colorize、typeset 等
- **59 條 deterministic detector**：不需 LLM/API key，可透過 CLI `npx impeccable detect` 執行

## 核心規則

- 排版：body measure 65–75ch、display max 6rem、tracking floor -0.04em
- 對比：body/placeholder ≥4.5:1、large text ≥3:1
- 陰影：zero-offset colored halo = decoration（禁止）
- 動效：one authored moment, not scattered effects
- 明確拒絕：卡片網格、kicker/eyebrow 標籤、gradient text、裝飾性玻璃擬態
- 核心立場：「the brief wins」優先於機械式反模式規避

## 來源

- [[raw/web/2026-08-26-impeccable-pbakaus|raw: impeccable GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/pbakaus-impeccable|impeccable Entity]]
- [[wiki/entities/anthropics-claude-code-frontend-design|Anthropic frontend-design]]（上游）
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
