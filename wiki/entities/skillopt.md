---
title: "SkillOpt — 微軟技能優化器"
type: entity
created: 2026-08-06
updated: 2026-08-06
sources: 1
tags: [skill, self-evolving, prompt-engineering]
collection: entities
topics: [agent-research, ai-agent]
canonical: entities/skillopt
---

> 微軟提出的 SkillOpt 框架，將 SKILL.md 視為可訓練的外部參數，不修改閉源模型權重實現 Agent 自我進化。

## 基本資訊
| 屬性 | 值 |
|------|-----|
| 機構 | Microsoft |
| 核心概念 | 文字空間優化（Text-Space Optimization）|
| 關鍵機制 | 雙軌制 + 驗證門禁 |
| GitHub | github.com/microsoft/SkillOpt |

## 雙軌制進化
1. **執行軌道**：凍結的閉源 LLM 執行任務
2. **優化軌道**：反思 LLM 根據報錯日誌更新 SKILL.md

## 技術流程
1. Rollout 採集（執行 + 收集報錯）
2. Reflection 與 Patch 生成
3. 驗證門禁與 Bounded Edit（文字學習率預算）

## 關鍵優勢
- 優化後的 SKILL.md 可跨框架遷移（Codex/Claude Code/Cursor）
- 零額外推斷成本
- 具備 Validation Gate 穩定性卓越

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]

## 相關頁面
- [[wiki/entities/hermes-agent|hermes-agent]]
