---
title: "LLM-D — Kubernetes 上的 AI 推理智慧路由器"
type: entity
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [llm-d, kubernetes, routing, inference, open-source]
topics: [backend-systems, agent-infrastructure]
---

# LLM-D

LLM-D 是 Red Hat、Google、IBM、Nvidia 共同開發的開源專案 `[37:52]`，運行在 Kubernetes 上，為 LLM 推理叢架提供智慧路由。官網：[llm-d.ai](https://llm-d.ai) `[48:24]`。

## 為什麼需要 LLM-D？

傳統 Load Balancer 兩大問題：
1. **Cache 不 aware：** 每台伺服器的 KV Cache 不同，LB 會把請求導到沒有 Cache 的伺服器，浪費已存的工作 `[35:37]`
2. **無法區分請求大小：** 一個 "hello" 和一個 "摘要 50 頁文件" 對 LB 來說一樣大，導致大請求卡住其他用戶 `[36:19]`

## 三個智慧路由條件 `[38:09]`

1. **Cache-aware routing：** 追蹤各伺服器的 Cache，把後續訊息導回已有 Cache 的伺服器 → 吞吐量 3x，首回應時間 2x 快 `[39:03]`
2. **Load-aware routing：** 檢查各伺服器的記憶體使用量與佇列長度 `[39:09]`
3. **Prefill/Decode 分離：** 一池做 Prefill（計算密集），一池做 Decode（記憶體密集），各自用最適合的 GPU → 70% 更多 tokens/s `[40:00]`

## 架構

```
Client → Gateway (統一入口)
           ↓
       LLM-D Scheduler (Cache-aware + Load-aware)
           ↓
    ┌──────┴──────┐
  Prefill Pool   Decode Pool
  (vLLM pods)   (vLLM pods)
    └──────┬──────┘
           ↓
    GPU Cluster (K8s pods)
```

## Well-lit Paths `[42:06]`

預調好的配置模板，避免手動調參：
- **Optimized baseline：** 單池 + Cache-aware routing
- **Disaggregated：** Prefill 池 + Decode 池分離
- **Sharded：** 巨型模型跨多 GPU（Leader Worker Set）`[45:33]`

## 部署

兩個 Helm 指令 `[43:10]`：
1. `helm install router` — 安裝 LLM-D router + vLLM 伺服器
2. `helm install model` — 安裝模型

K8s 自動管理 pod 的生命週期：crash → restart，機器死 → 移 pod `[46:51]`。

## 對企業的意義

- 66% 的 GenAI 組織使用 K8s 管理推理工作負載 `[40:48]`
- LLM-D 讓基礎設施團隊用既有 K8s 技能管理 AI 推理 `[49:03]`
- 連結到 Cheer 的企業思考：自購硬體（H200/B200）vs 租賃雲端 GPU 的基礎設施規劃

## 來源

- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]]
