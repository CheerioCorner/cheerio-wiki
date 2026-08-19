---
title: "SkillOpt Meta-skill — 技能優化元技能"
type: concept
created: 2026-08-06
updated: 2026-08-06
sources: 1
tags: [skill, meta-learning, self-evolving]
collection: concepts
topics: [agent-research, skill-system]
canonical: concepts/skillopt-meta-skill
---

> 關於「如何編寫與重構技能規範」的高階指導法則，控制 LLM 以受控預算進行 SKILL.md 的文字級修復。

## 核心機制
- **文字學習率預算（Textual LR Budget）**：限制每次修改幅度
- **Bounded Edit**：只允許 add / delete / replace 操作
- **Validation Gate**：補丁必須通過 Hold-out 驗證集

## 與 Model Fine-Tuning 的差異
| 維度 | Fine-Tuning | SkillOpt |
|------|-------------|----------|
| 成本 | 高 | 低 |
| 閉源模型 | ❌ | ✅ |
| 可遷移 | 困難 | 跨框架 |
| 額外推斷成本 | 無 | 無 |

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]

## 相關頁面
- [[wiki/entities/skillopt|SkillOpt]]
