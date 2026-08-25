---
title: leonxlnx/taste-skill — Anti-Slop Frontend Framework
type: source
created: 2026-08-26
updated: 2026-08-26
sources: 1
provenance_raw: "raw/web/2026-08-26-taste-skill-leonxlnx.md"
tags: [skills, ai-frontend, anti-slop, design-taste, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
upstream: https://github.com/leonxlnx/taste-skill
---

# leonxlnx/taste-skill

> "The Anti-Slop Frontend Framework for AI Agents" — 一套阻止 AI coding agent 產出「通用模板感」UI 的可攜式設計指令集。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | Leonxlnx (@lexnlin) |
| **Stars** | 80,534 ⚠️ |
| **Forks** | 5,519 |
| **建立** | 2026-02-19 |
| **最後推送** | 2026-08-24 |
| **授權** | MIT |

> ⚠️ **Star 數異常**：2026-02 建立即達 80k+ stars，GitHub API 兩次核實數字一致，但無法排除 star 灌水可能性。記錄供 Cheer 自行判斷。

## 結構：13 個子 skill

| Skill | 用途 |
|-------|------|
| `taste-skill`（核心） | v2 預設 anti-slop skill，三旋鈕系統 + LILA RULE |
| `taste-skill-v1` | 舊版存檔 |
| `gpt-tasteskill` | 針對 GPT/Codex 調整的更嚴格版本 |
| `image-to-code-skill` | 先生成設計參考圖 → 分析 → 再寫程式碼 |
| `redesign-skill` | 稽核/改善既有專案 |
| `soft-skill` | 高階、剋制的精緻美感（Haptic Depth） |
| `minimalist-skill` | 編輯感、Notion 風產品 UI |
| `brutalist-skill` | 瑞士印刷 + 軍事終端美學 |
| `stitch-skill` | 與 Google Stitch 相容變體 |
| `output-skill` | 防止 AI 輸出半成品/placeholder |
| `imagegen-frontend-web` | Web 介面概念圖生成 |
| `imagegen-frontend-mobile` | Mobile 介面概念圖生成 |
| `brandkit` | 品牌識別系統 |

## 核心規則

- **三旋鈕系統**：DESIGN_VARIANCE、MOTION_INTENSITY、VISUAL_DENSITY，1–10 分，baseline `8/6/4`
- **LILA RULE**：禁止 AI 慣用的紫/藍光暈美學，最多一個 accent color，飽和度 < 80%
- **字型**：Inter discouraged，優先 Geist/Outfit/Cabinet Grotesk/Satoshi
- **佈局硬規則**：hero 標題 ≤2 行、副標 ≤20 字、禁止 3+ 個連續 zigzag 圖文區塊
- **不可談判禁令**：Em-dash (—) 全面禁用
- **18 項 pre-flight checklist**：零 em-dash、WCAG AA 對比、eyebrow 數量限制等

## 來源

- [[raw/web/2026-08-26-taste-skill-leonxlnx|raw: taste-skill GitHub repo 研究報告]]

## 相關頁面

- [[wiki/entities/leonxlnx-taste-skill|leonxlnx/taste-skill Entity]]
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
- [[wiki/sources/2026-08-26-google-labs-code-stitch-skills|stitch-skills]]（taste-design 疑似同源）
