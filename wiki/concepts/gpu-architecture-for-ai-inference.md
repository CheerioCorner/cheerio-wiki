---
title: "GPU Architecture for AI Inference — 為什麼 AI 需要 GPU 而不是 CPU"
type: concept
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [gpu, cpu, ai-infrastructure, vram, inference]
topics: [backend-systems, agent-infrastructure]
---

# GPU Architecture for AI Inference

AI 推理的核心瓶頸是**記憶體頻寬**，不是算力。GPU 的優勢在於大量平行核心 + 高頻寬 VRAM，完美匹配模型推理的「大量小乘法」特性。

## CPU vs GPU 核心差異

| 維度 | CPU | GPU |
|------|-----|-----|
| 核心數 | 少數強大核心（4-64） | 數千個小型核心（2,500+） |
| 適合場景 | 序列化複雜任務 | 大量平行簡單運算 |
| 模型推理 | 逐序列處理，極慢 | 同時處理所有乘法，快 ~100 倍 |

模型推理本質是數十億次互不依賴的乘法運算 `[08:45]`。CPU 逐序列處理這些運算，GPU 則同時平行處理 `[09:36]`。

## 三個關鍵指標

每個 GPU 都由三個數字描述 `[12:22]`：

1. **Compute（算力）**：每秒能做多少數學運算（TFLOPS）
2. **Capacity（容量）**：VRAM 能裝多少權重（GB）
3. **Bandwidth（頻寬）**：GPU 讀取自身記憶體的速度（TB/s）

## VRAM 是核心瓶頸

系統 RAM 與 GPU 核心之間的管道僅 64 Gbps，比核心需求慢約 50 倍 `[10:35]`。VRAM 直接建在 GPU 卡上，頻寬達 TB/s 級別 `[11:19]`，但容量很小（T4 僅 16 GB）`[12:05]`。

**記憶體頻寬決定 Decode 速度：** 3 TB/s 頻寬 vs 16 GB 模型 = 每秒最多讀取 ~200 次 = 200 tokens/s `[23:25]`。這是 TPoT（Time Per Output Token）≈ 5 ms 的物理上限。

## GPU 世代比較

| GPU | TFLOPS | VRAM | Bandwidth |
|-----|--------|------|-----------|
| A100 | 312 | 80 GB | 2 TB/s |
| H100 | 990 | 80 GB | ~3.35 TB/s |
| H200 | 990 | 141 GB | ~4.8 TB/s |
| B200 | 2,250 | 192 GB | 8 TB/s |

H200 vs H100 的差異主要在 VRAM 容量（141 GB vs 80 GB）`[14:11]`，讓更大的模型能裝進單一 GPU。

## 與其他概念的關係

- 模型推理的兩階段瓶頸 → [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]]
- 記憶體不足時的解法 → [[wiki/concepts/model-sharding|Model Sharding]]
- 企業 GPU 採購決策 → [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Source Note]]

## 來源

- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]]
