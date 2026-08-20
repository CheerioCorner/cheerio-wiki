---
title: "Coinbase AI Engineering — 多模型基礎設施與成本工程實踐"
type: entity
created: 2026-08-20
updated: 2026-08-20
sources: 3
tags: [coinbase, multi-model, gateway, routing, caching, ai-cost]
topics: [agent-runtime-implementations]
provenance_url: "https://thenewstack.io/multi-model-ai-infrastructure/"
---

# Coinbase AI Engineering — 多模型基礎設施與成本工程實踐

> Coinbase 用 LLM gateway、task-based routing、aggressive caching 三大槓桿，在增加 AI 用量的同時降低 50% 花費。

## 基本資訊

| 欄位 | 值 |
|------|-----|
| 公司 | Coinbase（加密貨幣交易所） |
| CEO | Brian Armstrong |
| AI Agents 數量 | ~1,200 個全職（40-60hr/週正規化） |
| AI 支出變化 | 降至近峰值的一半，同時 token 用量達歷史新高 |

## 五大策略（Armstrong, 2026-06-28）

1. **更好的預設 LLM** — 實驗 Chinese LLMs（GLM 5.2, Kimi 2.7）作為預設，透過 LLM gateway 路由
2. **Task-based routing** — Frontier model 做規劃，cheap model 做執行
3. **Better caching** — Cache hit rate 從 5% 提升至 60%
4. **Keep context lean** — 切換任務時開新 sessions（防範 [[wiki/concepts/context-rot|Context Rot]]）
5. **Improve visibility** — Token 用量不設上限但要可被看見，花得多要有對應 impact

> "The goal isn't to suppress usage. It's to build the infrastructure that makes exponential growth sustainable." — Brian Armstrong

## 成本對比

| 模型 | Input Cost | Output Cost | 備註 |
|------|-----------|------------|------|
| GLM 5.2（Z.ai） | ~$1.40/M | ~$4.40/M | SWE-bench Pro 62.1 |
| Kimi 2.7（Moonshot AI） | — | — | 預設模型之一 |
| Opus 4.8（Anthropic） | ~$5/M | ~$25/M | Frontier model |

自架模型 = 資料不外流。

## 三大技術槓桿

### 1. LLM Gateway

內部 LLM gateway 作為 control plane， intercepts 每個 prompt，根據 cache state、task complexity、real-time pricing 動態決策用哪個 model。

### 2. Task-based Routing

「You may want a frontier model for planning, but not for execution where they can be overkill. Ultimately, humans shouldn't be choosing models — AI can automate this task.」

### 3. Aggressive Caching

同一 conversation 鎖定同一 model（只要 cached context 有效），cache hit rate 從 5% → 60%（12 倍跳躍）。

## 背景脈絡

- 2026-05: Coinbase 裁員 14%，部分因 AI 改變工作方式
- Armstrong: "I've watched engineers use AI to ship in days what used to take a team weeks."
- 與 Vercel 的平行案例：Vercel 日路由 >1 trillion tokens，也在放棄單一 lab partnership

## 相關頁面

- [[wiki/concepts/multi-model-cost-routing|Multi-Model Cost Routing]] — 通用設計原則
- [[wiki/sources/2026-08-20-thenewstack-multi-model-ai-infrastructure|The New Stack 報導]]
- [[wiki/sources/2026-08-20-coinbase-ceo-5-strategies-ai-spend|Yahoo Finance 5 策略]]
- [[wiki/sources/2026-08-20-how-coinbase-cut-its-ai-spend-by-50|付費牆版佐證]]
- [[wiki/concepts/context-rot|Context Rot]] — 第 4 點「keep context lean」是防範手段
