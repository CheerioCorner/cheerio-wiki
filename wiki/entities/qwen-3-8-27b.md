---
title: "Qwen 3.8 27B — 完全開源的 Dense 模型"
type: entity
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [qwen, open-source, dense-model, local-llm]
topics: [ai-agent, agent-infrastructure]
canonical: entities/qwen-3-8-27b
---

# Qwen 3.8 27B

> 阿里巴巴千問系列的完全開源 Dense 模型，27B 參數，是目前地端部署的甜蜜點選擇。

## 基本規格 `[35:45]`

| 屬性 | 數值 |
|------|------|
| 參數量 | 27B（Dense，每次跑過全參數）|
| Hidden Size | 5120 |
| Tokenizer | 24 萬 tokens `[35:45]` |
| Context Window | 262K `[37:03]` |
| 架構 | Dense + KV cache 優化 `[35:45]` |
| 多模態 | 文字 + 圖片（看得懂圖）`[37:03]` |
| 授權 | 完全開源（不只是開放權重）`[37:03]` |
| 思考模式 | 可開不同強度思考模式 `[37:03]` |

## 為什麼是甜蜜點

1. **硬體門檻低**：5090（32GB）可跑 FP8，4090 可跑 NVFP4 `[38:28]`
2. **品質好**：比 DeepSeek V4 在地端的表現好很多（DeepSeek 128GB 只能跑 Q2，品質有問題）`[109:06]`
3. **完全開源**：可商用、可微調、可再訓練 `[37:03]`
4. **速度不錯**：併發可達 80-90 tokens/s，甚至 100 tokens/s `[41:58]`
5. **agi workflow 表現優秀**：跑 agentic workflow 時非常棒 `[37:03]`

## 量化版本建議

| 量化 | 記憶體 | 品質 | 推薦硬體 |
|------|--------|------|---------|
| FP8 | ~27GB | 最佳 | 5090（32GB）`[38:28]` |
| NVFP4 | ~14GB | 掉 2% `[38:28]` | 4090（24GB）|
| Q4 | — | 甜蜜點 `[109:06]` | 任何 24GB+ GPU |
| Q2 | — | 不建議 | — |

## 與 DeepSeek V4 的比較

| 維度 | Qwen 3.8 27B | DeepSeek V4 |
|------|-------------|-------------|
| 架構 | Dense | MoE `[23:28]` |
| 地端品質 | 好（Q4 即可）`[109:06]` | 差（128GB 只能跑 Q2）`[109:06]` |
| 開源程度 | 完全開源 `[37:03]` | 開放權重 |
| 去識別化 | 完全不會漏 `[116:55]` | 有些名字會漏 `[109:06]` |
| MTP 支援 | 有，速度快很多 `[33:03]` | 有 |

## 與其他概念的關係

- [[wiki/concepts/local-llm-deployment|Local LLM 部署]] 中的具體模型選擇
- [[wiki/concepts/agent-security-levels|Agent 安全等級]] 示範中使用的模型
- [[wiki/sources/2026-08-30-local-llm-agent-harness-dgx-spark|來源筆記]] 中有完整實機示範
