---
title: "Coinbase runs 1,200 agents and just slashed its AI bill in half"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [coinbase, vercel, multi-model, gateway, routing, caching]
topics: [agent-runtime-implementations]
provenance_url: "https://thenewstack.io/multi-model-ai-infrastructure/"
---

# Coinbase runs 1,200 agents and just slashed its AI bill in half

> The New Stack 報導（2026-07-07）：Coinbase 1,200 AI agents，三大槓桿降半成本。Vercel 日路由破兆 token。

## Coinbase AI 工程實踐

### 規模

Coinbase 現有約 **1,200 個全職 AI agents**（用 40-60hr/週工時正規化計算）`https://thenewstack.io/multi-model-ai-infrastructure/`。

### 三大槓桿

**1. LLM Gateway 預設便宜模型**
- 預設模型：GLM 5.2（Z.ai）+ Kimi 2.7（Moonshot AI）
- GLM 5.2 成本：~$1.40/M input tokens, ~$4.40/M output tokens
- Opus 4.8 成本：~$5/M input, ~$25/M output
- **3-6 倍成本差距**
- GLM 5.2 SWE-bench Pro 得分 62.1（vs GPT-5.5 的 58.6）
- 自架模型 = 資料不外流

**2. Task-based Routing**
- 複雜規劃 → frontier model
- 純執行任務 → cheap model（表現一樣好）
- 「Humans shouldn't be choosing models — AI can automate this task.」

**3. Aggressive Caching**
- Cache hit rate: **5% → 60%**（12 倍跳躍）
- 同一 conversation 鎖定同一 model（只要 cached context 有效）

### Armstrong 關鍵定調

> "The goal isn't to suppress usage. It's to build the infrastructure that makes exponential growth sustainable."

### 背景脈絡

- 2026-05: Coinbase 裁員 14%，部分因 AI 改變工作方式
- Armstrong: "I've watched engineers use AI to ship in days what used to take a team weeks"

## Vercel 的平行案例

- Vercel CEO Guillermo Rauch：日路由 **>1 trillion tokens** across millions of deployments
- 積極放棄單一 lab partnership
- 「The model has become just one interchangeable component in a larger inference pipeline.」

## 共通架構原則

Coinbase 和 Vercel 解決不同問題，但到達相似架構：
- Gateway 作為 control plane
- 模型可替換化
- 評測先行於信任（"test before you trust"）
- 可觀測性（latency/uptime/token/cost across providers）

## 相關頁面

- [[wiki/entities/coinbase-ai-engineering|Coinbase AI Engineering]]
- [[wiki/concepts/multi-model-cost-routing|Multi-Model Cost Routing]]
- [[wiki/sources/2026-08-20-coinbase-ceo-5-strategies-ai-spend|Yahoo Finance 5 策略]]
- [[wiki/sources/2026-08-20-how-coinbase-cut-its-ai-spend-by-50|付費牆版佐證]]
