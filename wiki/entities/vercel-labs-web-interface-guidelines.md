---
title: "vercel-labs/web-interface-guidelines — Web UI 合規稽核清單"
type: entity
created: 2026-08-26
updated: 2026-08-26
sources: 1
tags: [skills, ai-frontend, vercel, accessibility, code-review, lint, agent-skill]
topics: [skill-cases-and-comparisons]
canonical: entities/vercel-labs-web-interface-guidelines
upstream: https://github.com/vercel-labs/web-interface-guidelines
---

> Vercel 的扁平合規稽核清單——不是生成技能，是生成完之後的審查工具。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **維護者** | Vercel (vercel-labs) |
| **Stars** | 807 |
| **Commits** | 58 |
| **授權** | MIT |

## 核心特色

- **不是 skills/ 目錄**：單一 `command.md` 準則文件
- **Fan-out 安裝**：`install.sh` 把同一份文件複製成 7 種工具格式
- **用法**：`Review these files for compliance: $ARGUMENTS`
- **稽核維度**：20 章節（Accessibility、Focus、Forms、Animation、Typography 等）

## 與其他 skill 的關係

- **互補**：先用生成型 skill（taste-skill/hallmark/impeccable）建 UI，再用本清單稽核
- **CI/CD 整合**：可作為 Linter 掛在 pipeline 裡
- **非競爭**：與任何品味把關 skill 都不衝突

## 來源

- [[wiki/sources/2026-08-26-vercel-labs-web-interface-guidelines|web-interface-guidelines Source Note]]

## 相關頁面

- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
