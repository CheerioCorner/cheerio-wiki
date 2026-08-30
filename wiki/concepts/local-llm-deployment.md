---
title: "Local LLM 部署 — 從雲端到地端的基礎設施選擇"
type: concept
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [local-llm, open-weight-model, deployment, hardware, security]
topics: [agent-infrastructure, ai-agent]
canonical: concepts/local-llm-deployment
---

# Local LLM 部署 — 從雲端到地端的基礎設施選擇

> **成長階段：** 🌱 種子期

## 一句話定義

**Local LLM 部署**是將開放權重模型安裝在自有硬體（地端/私有雲）上執行，使資料不出公司邊界、成本可控、且能針對特定場景微調的基礎設施策略。

## 為什麼重要

1. **資安邊界**：模型和資料都不離開公司管轄範圍 `[40:00]`
2. **成本控制**：地端模型只有電費成本，遠低於 API 訂閱 `[101:43]`
3. **專用性**：只需服務小群人（幾百人的 agentic workflow），不需像閉源模型一樣面面俱到 `[40:00]`
4. **混合運用**：閉源 + 開源模型各自有適用場景，是未來方向

## 硬體選擇

| 硬體 | 記憶體 | 架構 | 優勢 | 適用場景 |
|------|--------|------|------|---------|
| **DGX Spark** | 128GB Unified | ARM + CUDA | CUDA 生態完整、系統佔記憶體少 | POC、小團隊正式部署 |
| **Mac M-series** | 128GB+ | Apple Silicon | MLX 模型多、不需額外硬體 | 個人使用、輕量部署 |
| **AMD AI395/495** | 128GB | x86 | 很多 inference engine 只有 x86 | x86 生態相容 |
| **RTX Spark**（即將推出）| — | ARM + CUDA | Windows on ARM + CUDA | Windows 環境 |
| **GB300 Desktop** | 768GB（技嘉） | — | 可跑 Kimi K3 級模型 | 企業正式部署 |
| **多台 DGX Spark** | 256GB+ | ARM + CUDA | 可放更大模型、提高併發 | 團隊規模擴大 |

> 參考：[[wiki/sources/2026-08-30-local-llm-agent-harness-dgx-spark|從模型部署到 Agent Harness]]

## 量化與記憶體計算

- **FP8**：記憶體用量 ≈ 參數量（GB ≈ B），是基準 `[54:04]`
- **NVFP4**：比 FP8 少一半多一點，精度掉約 2%，甜蜜點 `[38:28]`
- **Q4_K_M**：GGUF 格式的量化方式，M = 中等 `[50:09]`
- **Q4 甜蜜點**：Q4 以下品質急劇下降，Q1 基本不能用 `[109:06]`
- 必須額外預留 KV Cache 和 Context Window 空間 `[55:19]`

## 模型大小對照

| 模型 | 參數量 | FP8 記憶體 | 推薦硬體 |
|------|--------|-----------|---------|
| Qwen 3.8 27B | 27B | ~27GB | 5090（32GB）或 DGX Spark |
| DeepSeek V4 Flash | ~236B（MoE, ~21B active）| ~236GB | 多張 GPU 或 DGX Spark（Q2）|
| Kimi K3 | 2.8T（MoE）| ~2.8TB | 4× GB300 |

## 與其他概念的關係

- 站在 [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] 之上——了解硬體限制後才能選擇部署方案
- 為 [[wiki/concepts/agent-security-levels|Agent 安全等級]] 提供 model 端的安全基礎
- 與 [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] 互補——前者講架構原理，本頁講實務部署選擇
