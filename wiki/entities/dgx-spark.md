---
title: "NVIDIA DGX Spark — 128GB Unified Memory 的地端 AI 工作站"
type: entity
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [nvidia, dgx-spark, hardware, local-llm, cuda]
topics: [agent-infrastructure, ai-agent]
canonical: entities/dgx-spark
---

# NVIDIA DGX Spark

> NVIDIA 推出的平價地端 AI 工作站，128GB Unified Memory，讓地端模型從「玩具」變「可用工具」。

## 基本規格 `[41:58]`

| 屬性 | 數值 |
|------|------|
| 記憶體 | 128GB Unified Memory `[41:58]` |
| 架構 | ARM + CUDA `[44:06]` |
| 最高時脈 | 3003MHz `[108:39]` |
| 建議時脈 | ≤2100MHz（避免熱當）`[100:03]` |
| 儲存 | ~4TB `[78:47]` |
| 作業系統 | Linux（系統佔記憶體少）`[67:38]` |
| 價格 | ~13.5 萬台幣（2026 年初）→ ~20 萬台幣（2026 年 8 月）`[42:49]` |

## 為什麼重要

- **128GB Unified Memory** 讓 27B 模型輕鬆跑，甚至可同時掛其他模型 `[41:58]`
- **CUDA 生態完整**：大部分 inference engine 都有 CUDA 支援 `[44:06]`
- **POC 利器**：十幾二十萬先試，確認可行再部署到 GB300 cluster `[42:49]`
- **改變了地端模型的遊戲規則**：Josh 說「在買 DGX Spark 之前，地端模型跟我的生活完全沒有關係」`[41:58]`

## 熱管理經驗 `[100:03]`

- 機房無冷氣，只用電風扇吹，熱當過好幾次 `[100:03]`
- 3003MHz → 2100MHz：效能掉約 45%，但可接受 `[100:03]`
- 超過 2006MHz 大概就會熱當 `[100:03]`
- 60 度：手放機殼溫溫的；80-90 度：燙的（內部溫度）`[116:55]`
- 吹電風扇完全沒用，吹不到 GPU `[116:55]`
- 建議豎起來放 `[116:55]`

## 實際使用場景

- 常駐 vLLM + Qwen 27B，區域網路多台機器共用 `[76:32]`
- 去識別化（De-ID）：1000 筆 Excel 資料 `[110:29]`
- 翻譯、摘要、影片轉檔、Word/Excel 處理 `[45:28]`
- 8 個併發、100 tokens/s，5-8 人同時使用 `[125:44]`

## 地端硬體比較

| 硬體 | 記憶體 | 架構 | CUDA | 價格帶 |
|------|--------|------|------|--------|
| **DGX Spark** | 128GB | ARM | ✅ | ~20 萬台幣 |
| **Mac M-series** | 128GB+ | Apple Silicon | ❌（MLX）| 視機型 |
| **AMD AI395** | 128GB | x86 | ❌ | 視機型 |
| **RTX Spark** | — | ARM | ✅ | 即將推出 |
| **GB300 Desktop** | 768GB | — | ✅ | ~300 萬台幣 |

## 與其他概念的關係

- [[wiki/concepts/local-llm-deployment|Local LLM 部署]] 的具體硬體選項
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture]] 的 UMA 架構實例
- [[wiki/sources/2026-08-30-local-llm-agent-harness-dgx-spark|來源筆記]] 中有完整實機示範
