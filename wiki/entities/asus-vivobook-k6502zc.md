---
title: "ASUS Vivobook K6502ZC — CUDA 測試工作站"
type: entity
created: 2026-09-05
updated: 2026-09-05
sources: 1
tags: [hardware, testbed, rtx-3050, cuda, laptop, local-llm]
topics: [agent-infrastructure, ai-agent]
canonical: entities/asus-vivobook-k6502zc
---

# ASUS Vivobook K6502ZC — CUDA 測試工作站

> 一句話：Cheer 的個人筆電，搭載 RTX 3050 4GB Laptop GPU，是本機 CUDA 實測的硬體基礎。**不可與 Intel UHD 770 Mini PC 混淆。**

## 硬體規格（✅ 實測）

| 項目 | 值 |
|------|-----|
| 機型 | ASUS Vivobook K6502ZC |
| CPU | Intel i7-12700H（14 核 / 20 執行緒）|
| 獨顯 | NVIDIA RTX 3050 Laptop GPU，4096 MiB VRAM |
| 內顯 | Intel Iris Xe |
| RAM | 32 GB |
| OS | Windows 11 家用版 10.0.26200 x64 |
| 驅動 | NVIDIA 572.83，CUDA Version 12.8 |

## 實測結果

### CUDA build 驗證

- 安裝 `llama-b10819-bin-win-cuda-12.4-x64.zip`（242 MB）+ `cudart-llama-bin-win-cuda-12.4-x64.zip`（373 MB）
- `llama-server.exe --list-devices` 輸出 `CUDA0: NVIDIA GeForce RTX 3050 Laptop GPU (4095 MiB, 3303 MiB free)`
- 安裝結構：%LOCALAPPDATA%\llama.cpp\b10819\（實體版本）→ `current`（junction）
- **重要發現**：`cudart-*.zip` 已包含 CUDA runtime DLL，用 release ZIP 即可拿到 CUDA 加速，不必安裝 CUDA Toolkit

### Router mode 進程行為

- `llama-server --models-dir <空目錄> --port 18080 --no-webui`：單一進程（PID 10864，RSS 179 MB）
- `taskkill /PID <pid> /T /F` 終止後進程數歸零
- ⚠️ **未測到載入模型後的多進程行為**

### Vulkan → CUDA 降級根因

- `install.ps1` 的 `ProbeCUDA` 偵測到 NVIDIA GPU 但無 CUDA Toolkit，exit code 5，靜默降級到 Vulkan
- CUDA 約比 Vulkan 快 36%（prefill）與 10%（generation）[🔍](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/)

## 4 GB VRAM 的部署限制

- 實際可用約 3.3 GB（扣除桌面佔用）
- 3B–4B 級模型可全載 CUDA [🔍](https://lmsa.app/blog/running-local-ai-on-a-4gb-vram-gpu-in-2026-the-real-world-guide-that-actually-works/)
- **明確不要硬跑 7B**——會 spill 到 RAM，不堪用
- MoE 模型可用 `-ncmoe` 把部分層留 CPU

## ⚠️ 待驗證（不可當結論）

1. 3B/4B 全載 CUDA 的實際 tok/s
2. 7B 部分 offload 的實際表現
3. `--fit` 在 4 GB 卡上的實際行為
4. 模型切換延遲
5. `taskkill /T` 含 model 子進程時的清理效果

## 與其他頁面的關係

- **同層級對比**：[[wiki/entities/intel-uhd770-mini-pc|Intel UHD 770 Mini PC]]（不同機器，不同 backend）
- **部署框架**：[[wiki/concepts/local-llm-deployment|Local LLM 部署]]
- **安全等級**：[[wiki/concepts/agent-security-levels|Agent 安全等級]] L5/L6 實例
- **工具知識**：[[wiki/entities/llama-cpp|llama.cpp]]

## 來源

- [[wiki/sources/2026-09-05-llamacpp-deployment-cuda-verification|llama.cpp 部署深度研究與 CUDA build 實測]]
