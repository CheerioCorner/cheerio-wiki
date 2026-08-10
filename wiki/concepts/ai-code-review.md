---
title: AI Code Review
type: concept
created: 2026-08-10
updated: 2026-08-10
tags: [code-review, ai-agent, static-analysis, quality-assurance]
topics: [ai-development-tools, code-review]
canonical: concepts/ai-code-review
---

# AI Code Review

> 用 AI 自動化代碼審查，從「人工逐行檢查」進化到「AI 初審 + 人類終審」。

## 核心價值

| 傳統 Code Review | AI Code Review |
|-----------------|----------------|
| 人工逐行檢查 | AI 自動掃描全 diff |
| 依賴審查者經驗 | 基於最佳實踐 + 歷史數據 |
| 延遲數小時~數天 | 幾分鐘內完成 |
| 難以大規模一致 | 規則統一、可重複 |

---

## 三大技術路線

### 1. 確定性工程 × Agent 混合（OpenCodeReview）

```
Git Diff → 確定性管道（檔案篩選、打包、規則匹配）
              ↓
         Agent 動態決策（場景化 Prompt + Tool-use）
              ↓
         行級結構化評論
```

**代表工具**：OpenCodeReview (OCR)

| 優勢 | 劣勢 |
|------|------|
| Token 消耗僅 1/9 | 需維運 CI/CD |
| Precision 高（誤報少） | Recall 較低（漏報多） |
| 位置精準（AST 對齊） | 需要規則維護 |

### 2. 知識圖譜 + 爆炸半徑分析（code-review-graph）

```
Git Diff → Tree-sitter 解析 → 知識圖譜（SQLite）
              ↓
         爆炸半徑分析（Blast Radius）
              ↓
         只送必要上下文給 AI
```

**代表工具**：code-review-graph

| 優勢 | 劣勢 |
|------|------|
| Token 減少 8.2 倍 | 需要預建索引 |
| 支援 19 種語言 | 增量更新有延遲 |
| MCP 整合 | 專案特定配置 |

### 3. 純 Agent 驅動（Parallel Sub-agents）

```
Git Diff → Agent A（Standards 審查）
         → Agent B（Spec 審查）
              ↓
         兩份獨立報告 → 綜合
```

**代表工具**：code-review skill

| 優勢 | 劣勢 |
|------|------|
| 靈活、可自訂 | Token 消耗高 |
| 雙軸審查（Standards + Spec） | 品質依賴 LLM |
| 無需額外設定 | 無 Benchmark 數據 |

---

## Benchmark 比較

| 工具 | Precision | Recall | F1 | Token 消耗 | 位置準確性 |
|------|-----------|--------|-----|-----------|-----------|
| **OpenCodeReview** | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ | ~1/9 | ⭐⭐⭐（AST） |
| **code-review-graph** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ~1/8 | ⭐⭐⭐ |
| **code-review skill** | ⭐⭐ | ⭐⭐⭐ | ⭐⭐ | 1x（基準） | ⭐⭐ |
| **Copilot Review** | ⭐⭐ | ⭐⭐ | ⭐⭐ | 中 | ⭐⭐ |
| **CodeRabbit** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | 高（付費） | ⭐⭐⭐ |

---

## AI Coding Workflow 中的 Code Review

在結構化工作流中，Code Review 是最後一道防線：

```
想法 → grill with docs → spec → tickets → implement → 【Code Review】→ commit
```

### Code Review 的輸入

| 輸入 | 用途 |
|------|------|
| Git Diff | 審查的實際變更 |
| Spec | 確認實作是否符合規格 |
| Standards | 確認是否遵循最佳實踐 |

### Code Review 的輸出

| 輸出 | 用途 |
|------|------|
| 行級評論 | 指出具體問題 |
| 修復建議 | 提供改善方案 |
| 品質分數 | 整體評估 |
| Commit | 確認後提交 |

---

## 與 Defect Metrics 的關係

Code Review 是降低 **Defect Escape Rate** 的關鍵環節：

```
需求 → 設計 → 開發 → 【Code Review】→ 測試 → 上線 → 生產缺陷
                      ↑
              擋住 60-80% 的缺陷
```

| 指標 | Code Review 的影響 |
|------|-------------------|
| **Defect Density** | 降低每千行缺陷數 |
| **Defect Escape Rate** | 減少逃到生產環境的缺陷 |
| **Review Coverage** | AI 確保每個 PR 都被審查 |

---

## 工具選型指南

| 場景 | 推薦工具 | 原因 |
|------|---------|------|
| **CI/CD 自動化** | OpenCodeReview | 確定性管道、低成本 |
| **大型 Monorepo** | code-review-graph | 爆炸半徑分析、省 Token |
| **深度雙軸審查** | code-review skill | Standards + Spec 同時考量 |
| **人類介入審查** | Plannotator | 視覺化標註、feedback |
| **付費 SaaS** | CodeRabbit | 開箱即用、UI 友善 |

### 組合策略（Recommended）

```
1. OCR 自動初審（低成本、快速、高精準）
       ↓
2. code-review skill 深度審查（Standards + Spec）
       ↓
3. Plannotator 標註（需要人類判斷時）
       ↓
4. 修復 → 再次 OCR 確認
```

---

## 相關頁面

### 工具
- [[wiki/entities/open-code-review|OpenCodeReview]] — 阿里巴巴 AI 代碼審查 CLI 🛠️
- [[wiki/sources/2026-08-20-opencode-review-deep-research|OpenCodeReview Deep Research]] — Gemini 深度研究 🛠️
- [[wiki/sources/2026-08-04-code-review-graph|code-review-graph]] — Tree-sitter 知識圖譜省 8.2x Token 🛠️
- [[wiki/entities/plannotator|Plannotator]] — 視覺化標註審查工具

### 概念
- [[wiki/concepts/ai-coding-workflow|AI Coding Workflow]] — 結構化工作流（含 Code Review 階段）
- [[wiki/concepts/defect-metrics|Defect Metrics]] — 缺陷密度與逃脫率
- [[wiki/concepts/code-graph|Code Graph]] — AI Code Assistant 核心基礎設施
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — Tree-sitter + 圖論

### Topics
- [[wiki/topics/ai-development-tools|AI Development Tools]] — AI 程式碼輔助工具導航
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — AI Agent 技術基礎設施
