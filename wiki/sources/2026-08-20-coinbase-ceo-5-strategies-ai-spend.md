---
title: "Coinbase CEO outlined 5 strategies to keep AI spend low"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [coinbase, armstrong, ai-cost, strategy, chinese-llm]
topics: [agent-runtime-implementations]
provenance_url: "https://finance.yahoo.com/technology/ai/articles/coinbases-ceo-outlined-5-strategies-053434539.html"
---

# Coinbase CEO outlined 5 strategies to keep AI spend low

> Yahoo Finance 報導（2026-06-29）：Brian Armstrong 的 5 個降低成本策略，不限制 token 用量。

## 五大策略

1. **更好的預設 LLM** — 實驗用 Chinese LLMs（GLM 5.2, Kimi 2.7）作為預設，透過 LLM gateway 路由 `https://finance.yahoo.com/technology/ai/articles/coinbases-ceo-outlined-5-strategies-053434539.html`
2. **Routing prompts based on difficulty** — Frontier model 做規劃，cheap model 做執行。最終目標：AI 自動選擇 model
3. **Better caching** — 降低推理成本
4. **Keep context lean** — 切換任務時開新 sessions，不要讓 context 累積（⚠️ 與 [[wiki/concepts/context-rot|Context Rot]] 防範策略呼應）
5. **Improve visibility into AI spending** — Token 用量不設上限但要可被看見，花得多要有對應 impact

## 關鍵引言

> "The goal isn't to suppress usage. It's to build the infrastructure that makes exponential growth sustainable."

## 背景

- 2026-05: Coinbase 裁員 14%，部分因 AI 改變工作方式
- Armstrong: "Over the past year, I've watched engineers use AI to ship in days what used to take a team weeks."
- 圖表顯示 token 用量達到歷史新高，但 AI 支出降至近峰值的一半

## 與其他來源的關係

- [[wiki/sources/2026-08-20-thenewstack-multi-model-ai-infrastructure|The New Stack]] 提供更多技術細節（1,200 agents、cache hit rate 5%→60%、GLM 5.2 成本數據）
- [[wiki/sources/2026-08-20-how-coinbase-cut-its-ai-spend-by-50|付費牆版]] 僅有摘要段落

## 相關頁面

- [[wiki/entities/coinbase-ai-engineering|Coinbase AI Engineering]]
- [[wiki/concepts/multi-model-cost-routing|Multi-Model Cost Routing]]
- [[wiki/concepts/context-rot|Context Rot]] — 第 4 點「keep context lean」是防範 Context Rot 的工程手段
