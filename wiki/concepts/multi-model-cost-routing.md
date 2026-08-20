---
title: "Multi-Model Cost Routing — 多模型基礎設施的成本工程"
type: concept
created: 2026-08-20
updated: 2026-08-20
sources: 2
tags: [multi-model, gateway, routing, caching, cost-optimization]
topics: [agent-runtime-implementations]
canonical: concepts/multi-model-cost-routing
---

# Multi-Model Cost Routing — 多模型基礎設施的成本工程

> 從 Coinbase 和 Vercel 的實踐中抽象出的多模型路由設計原則：gateway 作為 control plane、模型可替換化、評測先行於信任。

## 核心觀察

Frontier models 在日常工程工作的能力趨近、open-weight alternatives 大幅改善、價格差距持續擴大——這使得跨模型路由比綁定單一 vendor 更合理。

## 通用設計原則

### 1. Gateway 作為 Control Plane

Gateway intercepts 每個 prompt，根據 cache state、task complexity、real-time pricing 動態決策。模型只是 inference pipeline 中的一個可替換元件。

### 2. 模型可替換化

不對任何 single vendor 做 long-term commitment。今天的 best model 可能不是明天的。競爭優勢從模型本身轉向「決定用哪個模型的基礎設施」。

### 3. 評測先行於信任（Test Before You Trust）

低成本模型需要在自己的 code、data、workflows 上持續評測。Public benchmarks 是起點，不是終點。

### 4. 可觀測性

使用多個 model provider 改變了 observability 需求——需要追蹤 latency、uptime、token consumption、cost across all providers。

## 兩個獨立案例

### Coinbase
- 1,200 AI agents
- 三大槓桿：LLM gateway（GLM 5.2/Kimi 2.7）+ task routing + caching（5%→60%）
- 詳見 [[wiki/entities/coinbase-ai-engineering|Coinbase AI Engineering]]

### Vercel
- 日路由 >1 trillion tokens across millions of deployments
- 積極放棄單一 lab partnership
- CEO Rauch: "The model has become just one interchangeable component in a larger inference pipeline."

## 成本意識設計

| 策略 | 原理 | 效果 |
|------|------|------|
| 預設便宜模型 | 大多數任務不需要 frontier model | 3-6x 成本差距 |
| Task-based routing | 規劃用 expensive，執行用 cheap | 精準分配 |
| Aggressive caching | 同一 conversation 鎖定同一 model | Cache hit 5%→60% |
| Context lean | 切換任務開新 session | 減少 token 浪費 |

## 相關頁面

- [[wiki/entities/coinbase-ai-engineering|Coinbase AI Engineering]] — 案例 1
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — token 經濟學互補
- [[wiki/concepts/context-rot|Context Rot]] — context lean 是防範手段
- [[wiki/sources/2026-08-20-thenewstack-multi-model-ai-infrastructure|The New Stack 報導]]
