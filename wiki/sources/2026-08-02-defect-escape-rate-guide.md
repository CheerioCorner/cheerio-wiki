---
title: "Defect Escape Rate（DER）測量指南"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-02-Step-by-step guide on how to measure Defect Escape Rate.md
    url: https://instatus.com/blog/der
provenance_raw: "raw/web/2026-08-02-Step-by-step guide on how to measure Defect Escape Rate.md"
provenance_url: https://instatus.com/blog/der
tags: [defect-metrics, der, quality-assurance, devops, testing]
collection: sources
topics: [ai-development-tools, code-review]
canonical: sources/2026-08-02-defect-escape-rate-guide
---

> 來源：[Step-by-step guide on how to measure Defect Escape Rate](https://instatus.com/blog/der)
> 原始剪藏：[[raw/web/2026-08-02-Step-by-step guide on how to measure Defect Escape Rate.md|raw clip]]

## 一句話

Defect Escape Rate（DER）= 逃到生產環境的缺陷 / 總缺陷數，是衡量 QA 效能的關鍵指標。

## 重點摘要

### DER 定義

- **Defect Escape Rate (DER)**：在測試階段未被發現，上線後才被使用者發現的缺陷比例
- 公式：`DER = (生產環境缺陷數 / 總缺陷數) × 100%`

### 為什麼重要

| 面向 | 說明 |
|------|------|
| **QA 效能** | DER 越高，表示測試覆蓋越不足 |
| **客戶滿意度** | 逃脫的缺陷直接影響使用者體驗 |
| **流程改進** | 追蹤 DER 趨勢，找出測試流程的弱點 |

### 測量步驟

1. **定義缺陷來源**
   - 生產環境回報的缺陷
   - 測試階段發現的缺陷

2. **分類缺陷**
   - 按嚴重程度（Critical / Major / Minor）
   - 按功能模組

3. **計算 DER**
   - 計算每個版本/週期的 DER
   - 追蹤趨勢

4. **分析與改善**
   - 找出高逃脫率的模組
   - 強化該區域的測試

### DER 與其他指標的關係

| 指標 | 說明 | 與 DER 的關係 |
|------|------|--------------|
| **Defect Density** | 每千行代碼的缺陷數 | DER 低但 DD 高 → 測試有效但代碼品質差 |
| **Test Coverage** | 測試覆蓋率 | Coverage 高但 DER 高 → 測試品質問題 |
| **MTTR** | 平均修復時間 | DER 高 + MTTR 長 → 嚴重品質問題 |

### 改善策略

1. **強化 Code Review** — 用 AI Code Review 擋住更多缺陷
2. **增加 Integration Test** — 減少整合階段的逃脫
3. **改善測試數據** — 用更真實的測試案例
4. **縮短回饋迴圈** — 更快發現問題

## 相關頁面

- [[wiki/concepts/defect-metrics|Defect Metrics]] — 缺陷密度與逃脫率指標
- [[wiki/concepts/ai-code-review|AI Code Review]] — AI 自動化代碼審查
- [[wiki/sources/2026-08-02-defect-density-escape-rate|Defect Density & Escape Rate Research]] — 深度研究
- [[wiki/topics/code-review|Code Review]] — Code Review 導航頁
