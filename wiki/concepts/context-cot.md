---
title: "Context-CoT — 上下文思維鏈"
type: concept
created: 2026-08-06
updated: 2026-08-06
sources: 1
tags: [llm, reasoning, in-context-learning]
collection: concepts
topics: [agent-research]
canonical: concepts/context-cot
---

> 透過「三階煉獄」推理合成 pipeline，強制 LLM 嚴格錨定上下文進行推理，解決「作弊」問題。

## 核心問題
LLM 在面對新知識時，表面按 Context 推理，實則偷偷調用預訓練記憶答題（Supervisor Signal Leakage）。

## 三階煉獄
1. **多階段 CoT 採集**：從長文本提煉推理路徑
2. **最少洩漏過濾**：隱藏答案，淘汰依賴預訓練知識的偽推理
3. **學生感知選擇**：篩選與目標模型分佈匹配的 CoT

## 與其他 CoT 變體比較
| 方法 | 優點 | 缺點 |
|------|------|------|
| Zero-shot CoT | 簡單 | 放大參數記憶偏見 |
| Tree of Thought | 樹狀探索 | 成本高 |
| Context-CoT | 嚴格錨定 Context | 訓練成本較高 |

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]

## 相關頁面
- [[wiki/entities/skillopt|SkillOpt]]
