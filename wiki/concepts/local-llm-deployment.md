---
title: "Local LLM 部署 — 從雲端到地端的基礎設施選擇"
type: concept
created: 2026-08-30
updated: 2026-09-05
sources: 6
tags: [local-llm, open-weight-model, deployment, hardware, security, temperature]
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

## 低階硬體實務：Intel UHD 770 + 32GB RAM

> 詳見 [[wiki/sources/2026-09-01-local-llm-uhd770-research|研究報告]] 與 [[wiki/sources/2026-09-01-local-llm-round-table-synthesis|圓桌共識]]

### 硬體限制
- UHD 770（32 EU）：共享記憶體上限 16GB（Windows 11 預設 50%），i5-14500T 不支援 Override
- i5-14500T：不支援 AVX-512（Alder Lake 之後已熔斷），但完整支援 AVX2
- Mini PC 散熱限制：35W T 系列 CPU，長時間滿載會熱節流

### 量化策略
- **GPU 全卸載**：14B Q4_K_M (~9GB) 可 100% 裝入 16GB GPU（甜點選擇）
- **CPU-only**：32B Q4_K_M (~20.5GB) 可跑但 ~8-12 t/s，不建議日常使用

### 圓桌共識修正
- 報告推薦的 14B/32B 降級為 **7B 起步**（Qwen2.5-Coder-7B-Instruct Q4_K_M）
- Aider 分數不適用於 DevOps skill 編排能力評估
- 應採用 [[wiki/concepts/hybrid-intent-router|混合式四層架構]]（確定性路由 + LLM 輔助）

### 最佳 Backend：Vulkan / SYCL / CPU
- UHD 770 沒有 CUDA 支援，必須走 Vulkan、SYCL 或 CPU-only
- 與下方案例（RTX 3050 走 CUDA）完全不同

---

## 低階硬體實務：ASUS Vivobook K6502ZC / RTX 3050 4GB（CUDA）

> 🛠️ 詳見 [[wiki/entities/asus-vivobook-k6502zc|ASUS Vivobook K6502ZC]] 與 [[wiki/sources/2026-09-05-llamacpp-deployment-cuda-verification|CUDA build 實測]]
> ⚠️ **本節的所有結論只適用於這台機器，不可套用到上方 Intel UHD 770 Mini PC。**

### 硬體規格（✅ 實測）

| 項目 | 值 |
|------|-----|
| 機型 | ASUS Vivobook K6502ZC |
| CPU | Intel i7-12700H（14 核 / 20 執行緒）|
| 獨顯 | NVIDIA RTX 3050 Laptop GPU，4096 MiB VRAM |
| 內顯 | Intel Iris Xe |
| RAM | 32 GB |
| OS | Windows 11 家用版 10.0.26200 x64 |
| 驅動 | 572.83，CUDA Version 12.8 |

### 最佳 Backend：CUDA
- 安裝 `install.ps1` 時靜默降級為 Vulkan（因為沒裝 CUDA Toolkit），是實質損失
- 改用 release ZIP（`cuda-12.4`）即取得 CUDA 加速，**不必安裝 CUDA Toolkit**
- CUDA 比 Vulkan 約快 36%（prefill）與 10%（generation）[🔍](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/)

### 量化策略
- 4 GB VRAM 扣除桌面佔用，實際可用約 3.3 GB
- **3B–4B 級模型可全載 CUDA**（⚠️ 未實測 tok/s）[🔍](https://lmsa.app/blog/running-local-ai-on-a-4gb-vram-gpu-in-2026-the-real-world-guide-that-actually-works/)
- **明確不要硬跑 7B**——會持續 spill 到 RAM，不堪用
- MoE 模型可用 `-ncmoe` 把部分層留 CPU（32 GB RAM 相對充裕）

### 與 Mini PC 的關鍵差異

| 面向 | Intel UHD 770 Mini PC | RTX 3050 Vivobook |
|------|----------------------|-------------------|
| GPU 類型 | 內顯（共享 16 GB） | 獨顯（專用 4 GB） |
| 最佳 Backend | Vulkan / SYCL / CPU | CUDA |
| 模型甜蜜點 | 7B Q4_K_M（全載） | 3B–4B Q4_K_M（全載） |
| CPU 優勢 | AVX2，可跑 CPU-only | 14 核 H 系列，多核強 |

> **同一個 7B 模型在兩台機器上的正確跑法不一樣**：Mini PC 可以全載進共享記憶體，這台要嘛降到 3B–4B 全載 CUDA，要嘛用 `-ngl` 部分 offload + 32 GB RAM 承接，要嘛用 `-ncmoe` 跑 MoE。

## 推論參數調校：Temperature 與任務型態

地端 LLM 部署時，**Temperature（溫度）**是影響輸出品質最直接的推論參數之一。不同任務型態對溫度的容忍度不同：

| 任務型態 | 建議溫度 | 說明 |
|---------|---------|------|
| 結構化查詢 / 工具呼叫 | 0.1–0.3 | 低溫確保 token 生成穩定、Schema 遵從度高 |
| 一般對話 | 0.7 | Hugging Face 熱門模型預設基線 |
| 創意生成 / 發想 | 0.9–1.0 | 高溫激發多樣性，但增加不確定性 |

**地端特有考量：** 小模型（7B/8B Q4_K_M）在高溫下的幻覺率顯著高於雲端超大模型，因此地端執行嚴謹任務時溫度通常需更保守（0.0–0.2）。選型時應把「模型參數量 + 量化位元 + 溫度容忍度」一起評估。

> 詳見 [[wiki/concepts/llm-temperature|LLM Temperature — 推論參數調校]] 與 [[wiki/sources/2026-09-04-llm-temperature-explained-kodekloud|KodeKloud LLM Temperature Explained]]

## 與其他概念的關係

- 站在 [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] 之上——了解硬體限制後才能選擇部署方案
- 為 [[wiki/concepts/agent-security-levels|Agent 安全等級]] 提供 model 端的安全基礎
- 與 [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] 互補——前者講架構原理，本頁講實務部署選擇
- 新增 [[wiki/concepts/hybrid-intent-router|混合式意圖路由器]]：低階硬體上的務實架構選擇
- 新增 [[wiki/concepts/llm-temperature|LLM Temperature]]：推論參數調校維度
- 新增 [[wiki/entities/asus-vivobook-k6502zc|ASUS Vivobook K6502ZC]]：CUDA 測試工作站實例
- 新增 [[wiki/sources/2026-09-05-llamacpp-deployment-cuda-verification|CUDA build 實測]]：第二台機器的完整實測記錄
- 新增 [[wiki/discussions/local-model-runtime-in-w074-architecture|Local Model Runtime 在 W-074/W-080 中的定位]]：runtime vs expert 的架構決策
