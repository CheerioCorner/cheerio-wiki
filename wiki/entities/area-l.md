---
title: "AReaL — 開源 Agent 強化學習基礎設施"
type: entity
created: 2026-08-06
updated: 2026-08-06
sources: 1
tags: [reinforcement-learning, agent, self-evolving]
collection: entities
topics: [agent-research]
canonical: entities/area-l
---

> Ant Group 等機構發表的 AReaL2.0 系統，實現 Agent 自我進化的線上強化學習框架。

## 基本資訊
| 屬性 | 值 |
|------|-----|
| 機構 | Ant Group |
| 版本 | AReaL2.0 |
| 核心 | Online RL + ATDP |
| GitHub | github.com/alipay/AReaL |

## 核心架構
1. **執行與解耦層**：Agent 生產執行 → 異步 ATDP 軌跡推送
2. **數據治理與獎勵計算**：安全掩碼 + Step-level/Trajectory-level Reward
3. **統一進化控制平面**：動態監控 → 自適應 Policy Gradient 更新

## 關鍵洞察
- Agent 智能化是**系統工程問題**，不是單純算法問題
- 微服務化異步 RL 框架，解耦 LLM 推理與梯度訓練

## 來源
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]]

## 相關頁面
- [[wiki/concepts/atdp|ATDP]]
