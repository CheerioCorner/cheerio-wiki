---
title: "Model Sharding — 多 GPU 切分巨型模型的兩種策略"
type: concept
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [sharding, gpu, model-parallelism, tensor-parallelism, pipeline-parallelism]
topics: [backend-systems, agent-infrastructure]
---

# Model Sharding

巨型模型（140 GB+）裝不進單一 GPU（80 GB）`[31:55]`。Sharding 把模型切成多片，分配到多個 GPU 上協同運算 `[32:10]`。

## 兩種切分方式

### Tensor Parallelism（層內切分）`[33:28]`
- 單層的運算分給多個 GPU，各算一半再合併
- GPU 之間需要頻繁通訊（chatty）
- **適用：同機器內**（NVLink 超高速互連）`[33:36]`

### Pipeline Parallelism（按層切分）`[33:11]`
- GPU 1 負責前幾層，GPU 2 負責後幾層
- 只傳遞一小段結果（light）
- **適用：跨機器**（普通網路速度不夠快）`[34:07]`

## 核心規則

> Chatty talk 放盒子裡（同機器 NVLink），Light talk 放盒子間（跨機器網路）`[34:07]`

## Kubernetes 上的實作

- [[wiki/entities/vllm|vLLM]] 可自動將模型切分到多個 GPU `[32:10]`
- K8s 用 **Leader Worker Set**（不是 StatefulSet）管理跨機器的 pod 群 `[45:33]`
- 整個 pod 群算力一個單位，缺一片就無法運作 `[45:23]`

## 與其他概念的關係

- 為什麼需要 sharding → [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]]（VRAM 容量限制）
- Sharding 後的路由 → [[wiki/entities/llm-d|LLM-D]]（智慧路由器處理分散的 pod）
- 企業硬體決策 → [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Source Note]]（H200/B200 選擇）

## 來源

- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]]
