---
title: "google-labs-code/stitch-skills — Stitch UI MCP Skill Library"
type: entity
created: 2026-08-26
updated: 2026-08-26
sources: 1
tags: [skills, ai-frontend, google-labs, stitch, mcp, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
canonical: entities/google-labs-code-stitch-skills
upstream: https://github.com/google-labs-code/stitch-skills
---

> Google Labs Stitch UI 設計工具 MCP server 的 skill library——三個 plugin 約 14 個子 skill。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **維護者** | Google Labs Code |
| **Stars** | 8,181 |
| **Forks** | 1,097 |
| **授權** | Apache-2.0 |
| **注意** | "Not an officially supported Google product" |

## 結構

| Plugin | Skills |
|--------|--------|
| stitch-design | code-to-design、generate-design、manage-design-system、extract-design-md、extract-static-html、upload-to-stitch |
| stitch-build | react-components、react-native、remotion、shadcn-ui、react-vite-dashboard |
| stitch-utilities | design-md、enhance-prompt、stitch-loop、taste-design |

## 核心特色

- **taste-design**：與 taste-skill LILA RULE 高度相似（疑似同源，未查證）
- **generate-design**：嚴格分工——token 不混進生成 prompt
- **與 Stitch MCP 深度整合**：設計→建置一站式工作流

## 來源

- [[wiki/sources/2026-08-26-google-labs-code-stitch-skills|stitch-skills Source Note]]

## 相關頁面

- [[wiki/entities/leonxlnx-taste-skill|taste-skill]]（疑似同源）
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
