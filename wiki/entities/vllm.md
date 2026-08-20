---
title: "vLLM — 高吞吐量 LLM 推理引擎"
type: entity
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [vllm, llm-serving, inference, open-source]
topics: [backend-systems, agent-infrastructure]
---

# vLLM

vLLM 是建在 PyTorch 之上的 LLM 模型伺服器，提供 OpenAI API 相容的 Web 介面，讓任何能對接 OpenAI 的工具無需改動即可使用自己的伺服器 `[15:50]`。

## 基本操作

一行指令啟動：`vllm serve <model>` `[16:17]`。啟動時將模型權重從磁碟載入 GPU 記憶體（約 16 GB，需 1-2 分鐘）`[16:31]`。

**關鍵特性：**
- 整個模型常駐 GPU 記憶體 `[17:01]`
- 相容 OpenAI API 格式 `[16:42]`
- 支援 Batching（多用戶共用 GPU）`[28:27]`
- 支援自動 Model Sharding（跨多 GPU 切分模型）`[32:10]`

## 架構定位

```
Client (OpenAI API)
    ↓
vLLM Server (模型常駐 GPU)
    ↓
PyTorch + GPU (VRAM)
    ↓
Model Weights (磁碟 → 記憶體)
```

## 與 LLM-D 的關係

vLLM 是**單台伺服器**的模型伺服器。當需要多台伺服器組成叢架時，[[wiki/entities/llm-d|LLM-D]] 在 vLLM 之上提供智慧路由 `[37:52]`。LLM-D 的 Helm 部署會同時安裝 vLLM 作為底層伺服器 `[43:27]`。

## 來源

- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]]
