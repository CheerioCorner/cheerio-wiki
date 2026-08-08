---
title: "ATDP — Agent Trajectory Data Protocol"
type: concept
created: 2026-08-06
updated: 2026-08-06
sources: 1
tags: [agent, data-protocol, reinforcement-learning]
collection: concepts
topics: [agent-research]
canonical: concepts/atdp
---

> Agent 軌跡數據協定，將 Agent 與環境互動的每一步結構化標準化，為自我進化奠定數據基礎。

## 核心概念
- **Prompt**：輸入提示
- **Tool Call**：工具調用
- **Observation**：環境觀察
- **Action**：執行動作
- **Reward**：獎勵信號

## 與純文本日誌的差異
| 綴度 | 純文本日誌 | ATDP |
|------|-----------|------|
| 結構化 | ❌ | ✅ |
| 可追溯 | 困難 | 完整 |
| RL 可用 | ❌ | ✅ |

## 應用價值
- 為強化學習提供結構化訓練數據
- 支援 Step-level / Trajectory-level 獎勵計算
- 實現 Agent 行為的精確回放與分析

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]

## 相關頁面
- [[wiki/entities/area-l|AReaL]]
