---
title: "DeepSeek V4 — MoE 架構的中國開放權重模型"
type: entity
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [deepseek, moe, open-weight-model, chinese-model]
topics: [ai-agent, agent-infrastructure]
canonical: entities/deepseek-v4
---

# DeepSeek V4

> DeepSeek 推出的 MoE（Mixture of Experts）架構模型，2026 年 7 月 31 日出 Flash，8 月 13 日出 Pro。以受限硬體（H100、昇騰 910C）做出與頂級模型同一領先群的表現。

## 基本資訊 `[23:28]`

| 屬性 | 說明 |
|------|------|
| 架構 | MoE（Mixture of Experts）`[23:28]` |
| 版本 | Flash（7/31）、Pro（8/13）`[04:25]` |
| 授權 | 開放權重（非完全開源）|
| 特色 | 在受限硬體上做架構優化 `[28:31]` |

## MoE 架構解釋 `[23:28]`

- 大腦只取出一部分神經元應付目前狀況 `[23:28]`
- 像開車時只注意路況，不會同時想古典樂 `[23:28]`
- MoE 模型跑過的參數約為 Dense 模型的 1/4 `[34:21]`
- 但**載入時仍需全部載入**（如 550B/A55B 需準備 550GB 記憶體）`[51:01]`

## 中國模型的優勢 `[28:31]`

- 硬體受限（H100、昇騰 910C）→ 在架構上做極致優化 `[28:31]`
- 電費幾乎不要錢（發電量是美國+印度總和）`[29:15]`
- 工程師薪水約為美國 1/10 `[29:21]`
- CSA、HCA、mHC、MLA 等技術都是為了讓 KV cache 變小 `[30:10]`

## 地端使用限制

- 128GB（DGX Spark）只能跑 Q2 量化 `[109:06]`
- Q2 品質有問題，去識別化時有些名字會漏掉 `[109:06]`
- **結論：地端部署建議用千問 27B，DeepSeek 太大** `[109:06]`

## Arena.ai 表現

- 在 Agent 執行項目進入前 10 名 `[18:58]`
- 在 WebDev coding 項目表現優秀 `[18:58]`
- 已接近 Fable 當時的表現水平 `[35:45]`

## 與其他概念的關係

- [[wiki/entities/qwen-3-8-27b|Qwen 3.8 27B]] 的對照組（MoE vs Dense）
- [[wiki/concepts/local-llm-deployment|Local LLM 部署]] 中的模型選項考量
- [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]：受限硬體上的架構優化
