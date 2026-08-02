---
title: "Defect Density & Escape Rate — Agile 品質指標指南"
type: source
created: 2026-08-02
updated: 2026-08-05
sources: 2
tags: [software-quality, metrics, agile, devops, defect-tracking, testing]
collection: sources
topics: []
canonical: sources/2026-08-02-defect-density-escape-rate
provenance_raw: "raw/web/2026-08-02-Defect Density & Escape Rate Agile Metrics Guide 2026.md"---

# Defect Density & Escape Rate — Agile 品質指標指南

> 兩篇關於軟體品質指標的文章彙整：Defect Density（缺陷密度）和 Escape Rate（逃脫率）。

## 重點摘要

- **Defect Density：** 每單位程式碼的缺陷數量，衡量程式碼品質和測試完整度
- **Escape Rate：** 逃到生產環境的缺陷佔總缺陷的比例，衡量測試有效性
- **AI 影響：** AI 輔助 coding 改變了缺陷分佈模式，集中在整合點和邊界情況
- **CI 整合：** SonarCloud PR quality gates + Sentry/Datadog 連動 closing the loop

## 關鍵公式

### Defect Density
```
Defect Density = Total Defects / Size of Software Component
```
- 範例：1000 LOC 中有 20 個缺陷 → 0.02 defects/LOC
- 常見標準：1 defect per KLOC（1000 lines of code）

### Escape Rate
```
Escape Rate = (Number of Defects Found in Production / Total Number of Defects) × 100
```
- 範例：100 個缺陷中 20 個在 production 發現 → 20%
- 範例：110 個缺陷中 10 個在 production 發現 → 9%

## AI 對缺陷密度的影響（2026 更新）

- AI 產生的程式碼量通常比人工多，缺陷表面跟著增長
- 邏輯錯誤集中在 AI 缺乏 context 的地方：API contract、domain rules、cross-service interactions
- AI 生成的 unit tests 有時測試的是 AI 的錯誤實作，而非預期行為
- **最佳實踐：** AI-assisted code review + human review of integration points + track defect origin

## 改進策略

1. **Code reviews + pair programming**
2. **Robust testing strategies**（unit + integration + regression）
3. **Continuous improvement culture**
4. **Automation**（test + defect tracking）
5. **Refactor to reduce complexity**
6. **CI/CD pipelines**

## 測量工具

| 指標 | 工具 |
|------|------|
| Defect Density | SonarQube, CodeCoverage, JUnit, TestNG, Jira |
| Escape Rate | Bugzilla, Mantis, Selenium, Appium, Jira |

## 來源

- [Defect Density & Escape Rate: Agile Metrics Guide 2026](https://daily.dev/blog/defect-density-and-escape-rate-agile-metrics-guide-2024/) — Alex Carter, daily.dev
- [Step-by-step guide on how to measure Defect Escape Rate](https://instatus.com/blog/der) — instatus
