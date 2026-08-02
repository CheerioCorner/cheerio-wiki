---
title: "Defect Metrics — 軟體品質測量"
type: concept
created: 2026-08-02
updated: 2026-08-05
sources: 2
tags: [software-quality, metrics, agile, devops, defect-tracking, testing]
collection: concepts
topics: []
canonical: concepts/defect-metrics
---

# Defect Metrics — 軟體品質測量

> 兩個關鍵的軟體品質指標：Defect Density（缺陷密度）和 Escape Rate（逃脫率）。

## 核心概念

### Defect Density（缺陷密度）

衡量每單位程式碼的缺陷數量，識別需要改善的區域。

```
Defect Density = Total Defects / Size of Software Component
```

- 範例：1000 LOC 中有 20 個缺陷 → 0.02 defects/LOC
- 常見標準：1 defect per KLOC（1000 lines of code）
- **衡量：** 程式碼品質、測試完整度

### Escape Rate（逃脫率）

衡量逃到生產環境的缺陷比例，評估測試有效性。

```
Escape Rate = (Defects in Production / Total Defects) × 100
```

- 範例：100 個缺陷中 20 個在 production 發現 → 20%
- **衡量：** 測試有效性、品質保證

## 比較

| 指標 | 定義 | 公式 | 關注點 |
|------|------|------|--------|
| Defect Density | 每單位程式碼的缺陷數 | Total Defects / Code Size | 程式碼品質 |
| Escape Rate | 逃到 production 的缺陷比例 | (Production Defects / Total) × 100 | 測試有效性 |

## AI 對缺陷分佈的影響（2026）

AI 輔助 coding 改變了缺陷模式：
- 缺陷量可能增加（因為 AI 產生更多程式碼），但 per-line density 可能不變
- 邏輯錯誤集中在 AI 缺乏 context 的地方：API contract、domain rules、cross-service interactions
- AI 生成的 unit tests 有時測試的是 AI 的錯誤實作
- **解方：** AI-assisted code review + human review of integration points

## 改進策略

1. Code reviews + pair programming
2. Robust testing strategies（unit + integration + regression）
3. Continuous improvement culture
4. Automation（test + defect tracking）
5. Refactor to reduce complexity
6. CI/CD pipelines（SonarCloud PR quality gates）

## 相關頁面

- [[wiki/sources/2026-08-02-defect-density-escape-rate]] — 完整指南來源
- [[wiki/concepts/ai-coding-workflow]] — 結構化 AI 編碼工作流程
- [[wiki/concepts/vibe-coding]] — AI 生成程式碼改變了缺陷分佈模式
