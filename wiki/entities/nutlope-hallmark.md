---
title: "nutlope/hallmark — Anti-AI-Slop Design Skill"
type: entity
created: 2026-08-26
updated: 2026-08-26
sources: 1
tags: [skills, ai-frontend, anti-slop, design-taste, oklch, agent-skill]
topics: [skill-cases-and-comparisons, skill-presentation-design]
canonical: entities/nutlope-hallmark
upstream: https://github.com/nutlope/hallmark
---

> Together AI 的 Anti-AI-Slop design skill——21 種 macrostructure + 58 道 slop test + 六軸自評分數。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | Hassan El Mghari (@nutlope, Together AI) |
| **Stars** | ~27,000 |
| **版本** | v1.1.0 |
| **官網** | usehallmark.com |

## 核心設計哲學

1. **Macrostructure**：21 種具名版面結構，連續兩次不可重複
2. **Slop Test**：58 道 post-emit 門檻，全部通過才能出貨
3. **六軸自評**：Philosophy/Hierarchy/Execution/Specificity/Restraint/Variety
4. **OKLCH 色彩**：透過 CSS 自訂屬性管理
5. **硬性限制**：4pt 間距、限 transform/opacity 動效、8 種互動狀態

## 輸出落款格式

```
/* Hallmark · macrostructure: [name] · tone: [tone] · anchor hue: [hue] */
```

## 與 taste-skill 的差異

| 維度 | hallmark | taste-skill |
|------|----------|-------------|
| 色彩系統 | OKLCH | LILA RULE（最多 1 accent） |
| 版面控制 | 21 種 macrostructure | 三旋鈕（1-10 分） |
| 品質關卡 | 58 道 slop test | 18 項 pre-flight |
| 自評機制 | 六軸分數 | 無 |
| 子技能 | 單一 + reference 檔 | 13 個獨立子 skill |

兩者在「反 AI 粗製濫造」目標上高度重複，美學偏向互斥，不建議同時啟用。

## 來源

- [[wiki/sources/2026-08-26-nutlope-hallmark|hallmark Source Note]]

## 相關頁面

- [[wiki/entities/leonxlnx-taste-skill|taste-skill]]（競爭）
- [[wiki/comparisons/frontend-ui-design-skills-comparison|前端 UI 設計 Skills 比較]]
