---
title: "llama.cpp — 本地 LLM 推理引擎"
type: entity
created: 2026-09-05
updated: 2026-09-05
sources: 1
tags: [local-llm, llama-cpp, inference-engine, cuda, vulkan, router-mode]
topics: [agent-infrastructure, ai-agent]
canonical: entities/llama-cpp
---

# llama.cpp — 本地 LLM 推理引擎

> 一句話：llama.cpp 是 GGUF 格式的 C/C++ 推理引擎，支援 CUDA/Vulkan/SYCL/ROCm/CPU 多種 backend，提供 llama-server（HTTP API）與 llama-cli（終端機）兩種介面。**本頁面是 llama.cpp 本身的可重用知識頁，不含特定機器的實測流水帳。**

## 定位：llama.cpp vs Ollama vs vLLM

| 面向 | llama.cpp | Ollama | vLLM |
|------|-----------|--------|------|
| **語言** | C/C++ | Go（包裝 llama.cpp） | Python（PyTorch） |
| **模型格式** | GGUF | GGUF（底層用 llama.cpp） | HuggingFace / SafeTensors |
| **安裝複雜度** | 中（需選 backend build） | 低（一鍵安裝） | 高（需 CUDA Toolkit + Python） |
| **API 相容** | OpenAI-compatible | OpenAI-compatible | OpenAI-compatible |
| **多模型** | Router mode（內建） | 需多 instance | 需多 instance |
| **定位** | 底層引擎，最大化控制 | 開箱即用的包裝層 | 生產級高吞吐量推論 |

**核心差異**：llama.cpp 的 backend 是 **build 級別**——CUDA/Vulkan/SYCL/ROCm 都是編譯時選定的，不能透過 CLI 參數切換。裝錯 backend 就得換執行檔。

## 取得方式：安裝腳本 vs Release ZIP

### 自動安裝腳本（`install.ps1` / `install.sh`）

- 官方 repo：`ggml-org/llama-install.sh` ✅
- 安裝路徑（Windows）：`%LOCALAPPDATA%\llama-app`（暫存）→ 複製到 WindowsApps → 刪暫存
- **關鍵行為**：腳本會探測 CUDA Toolkit，偵測到 NVIDIA GPU 但沒有 CUDA Toolkit 時，exit code 5，**靜默降級到 Vulkan build**
- **重要**：沒有 uninstall 功能
- 產出的是 55 MB 靜態 exe（新統一 `llama` CLI），Vulkan build

### Release ZIP

- 官方 repo releases：`ggml-org/llama.cpp/releases`
- 同時提供多個 backend 版本（`cuda-12.4`、`cuda-13.3`、`vulkan` 等）
- 安裝結構（Windows）：`%LOCALAPPDATA%\llama.cpp\<build>\`（實體版本）→ `current`（junction 指向該版本）
- **關鍵發現**：`cudart-*.zip`（CUDA runtime）已包含 `cudart64_12.dll`、`cublas64_12.dll` 等 DLL，**用 release ZIP 即可拿到 CUDA 加速，完全不必安裝數 GB 的 CUDA Toolkit**

### 選擇建議

| 情境 | 推薦方式 |
|------|---------|
| 有 NVIDIA GPU + 已裝 CUDA Toolkit | 安裝腳本（會偵測並給 CUDA build） |
| 有 NVIDIA GPU + 沒裝 CUDA Toolkit | Release ZIP + `cudart-*.zip`（不必裝 Toolkit） |
| 純 CPU / AMD / Intel 內顯 | 安裝腳本或 Vulkan release ZIP |

## Backend 選擇

| Backend | 適用場景 | 備註 |
|---------|---------|------|
| **CUDA** | NVIDIA 獨顯/內顯 | 效能最佳，比 Vulkan 快 ~36%（prefill）+ ~10%（generation）[🔍](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/) |
| **Vulkan** | 跨平台 GPU（NVIDIA/AMD/Intel） | 通用但效能較低 |
| **SYCL** | Intel GPU（含 Arc） | Intel 官方推薦 |
| **ROCm** | AMD GPU | Linux 為主 |
| **CPU-only** | 無 GPU 或 GPU 記憶體不足 | 最慢，但 32GB RAM 可跑 32B Q4 |

### 確認 backend

```bash
llama-server --list-devices
```

輸出範例（CUDA build）：
```
CUDA0: NVIDIA GeForce RTX 3050 Laptop GPU (4095 MiB, 3303 MiB free)
```

## Router Mode（多模型）

不帶 `-m` 啟動 `llama-server` 即進入 Router mode——多個模型共用一個 port。

| Flag | 說明 | 預設值 |
|------|------|--------|
| `--models-dir PATH` | router server 的模型目錄 | disabled |
| `--models-preset PATH` | INI 檔，定義各模型 preset | — |
| `--models-max N` | 同時載入模型數上限 | **4**，0=無限 |
| `--models-autoload` | 自動載入模型 | **enabled** |

`--models-max` 預設 4 推翻了部分部落格「同時只有一個模型在記憶體」的說法。[🔍](https://huggingface.co/blog/ggml-org/model-management-in-llamacpp)

### 與 llama-swap 的取捨

- llama-swap：第三方工具，用 YAML 設定多模型切換，適合需要精細排程的場景
- Router mode：內建，零額外依賴，適合「多模型常駐」的簡單場景

## 併發與 Context

| Flag | 說明 | 預設值 |
|------|------|--------|
| `-np, --parallel N` | server slot 數 | **-1 = auto** |
| `-kvu, --kv-unified` | 共用單一 KV buffer | slot 數 auto 時**啟用** |
| `--kv-unified-per-slot N` | 每個 slot 的 context 上限 | — |
| `-c, --ctx-size N` | 總 KV cache 預算 | 0 = 從模型讀取 |
| `-cb, --cont-batching` | 連續批次 | **enabled** |

**踩坑提醒**：`-c 32768 --parallel 8` → 每個請求實際只有 4096 token（[Issue #11681](https://github.com/ggml-org/llama.cpp/issues/11681)）。`-c` 是**總 KV 預算**，搭 `--parallel` 時被除。`--kv-unified-per-slot`（[PR #24124](https://github.com/ggml-org/llama.cpp/pull/24124)）提供更細的控制。✅ 本機 b10819 build 已有此 flag。

### 三種情境下 `-c` 的設法

1. **兩個獨立 server / llama-swap** → 各自 `-c`，記憶體加總不能超過硬體上限
2. **Router mode** → `-c` 寫進 preset 的各自 section（`ctx-size`），不是全域一個
3. **任一情境** → 都要先確認 `-np` 與 `--kv-unified` 的組合

## 記憶體自動配適（`--fit`）

| Flag | 說明 | 預設值 |
|------|------|--------|
| `-fit, --fit [on\|off]` | 自動調整未指定的參數以塞進裝置記憶體 | **on** |
| `-fitt, --fit-target` | 每個裝置的保留邊界 | **1024 MiB** |
| `-fitc, --fit-ctx N` | `--fit` 能設定的最小 ctx | **4096** |

**4 GB VRAM 特別重要**：`--fit` 預設 on 會自動嘗試把模型塞進 GPU。搭配 `-fitt` 調整保留邊界，避免 OOM。

### 其他記憶體相關

- `-ncmoe/--n-cpu-moe N`：把前 N 層的 MoE 權重留在 CPU——**4 GB VRAM + 32 GB RAM 跑 MoE 的關鍵手段**
- `-ot`：精確控制張量卸載

## 其他值得記住的參數

| Flag | 說明 | 預設值 |
|------|------|--------|
| `--jinja` | chat template 走 jinja 引擎 | **enabled** |
| `-ngl` | GPU 層數（精確控制） | — |
| `-dev/--device` | 指定使用哪些裝置 | — |
| `--api-key` / `--api-key-file` | API 認證 | — |
| Web UI | `--no-webui` 關閉 | **enabled** |
| `--host` | 監聽位址 | `127.0.0.1` |
| `--port` | 監聽 port | `8080` |

## 進程生命週期

### Windows 上殺進程樹

- Node 的 `process.kill()` 在 Windows 上**只殺該 PID，不殺子進程**
- 實務解法：`taskkill /PID <pid> /T /F`（`/T` 連子進程、`/F` 強制）
- **真正由 OS 保證的做法**：Windows Job Object + `KILL_ON_JOB_CLOSE`，但 Node.js 沒有原生 Job Object API，要用 native addon
- ⚠️ 已知地雷：某些 Node 24 build 上 `taskkill` 的 PATH 查找不可靠

### Router mode 進程行為

- 啟動 `llama-server --models-dir <空目錄>` 时為單一進程
- 官方說法：載入模型後為多進程架構（一個模型崩潰不影響其他），⚠️ **尚未在本機驗證**

## 版本資訊

本頁面參數來自 **b10819 CUDA build** 的 `llama-server --help` 實際輸出（2026-09-05 實測）。不同版本可能有差異。

## 與其他頁面的關係

- **定位對比**：[[wiki/entities/vllm|vLLM]]（高吞吐量生產級推論）、Ollama（開箱即用包裝層）
- **部署框架**：[[wiki/concepts/local-llm-deployment|Local LLM 部署]]
- **架構定位**：[[wiki/discussions/local-model-runtime-in-w074-architecture|Local Model Runtime 在 W-074/W-080 中的定位]]（runtime vs expert）
- **安全等級**：[[wiki/concepts/agent-security-levels|Agent 安全等級]] L5/L6 實例
- **硬體實例**：[[wiki/entities/asus-vivobook-k6502zc|ASUS Vivobook K6502ZC]]（RTX 3050 4GB CUDA 實測）

## 連結

- **花園內：**
  - [[wiki/entities/vllm|vLLM]] — 高吞吐量推論引擎對比
  - [[wiki/concepts/local-llm-deployment|Local LLM 部署]] — 部署策略與硬體選擇
  - [[wiki/concepts/harness|Harness — LLM 的驅動層]] — harness vs runtime 邊界
  - [[wiki/discussions/local-model-runtime-in-w074-architecture|Local Model Runtime 在 W-074/W-080 中的定位]]
  - [[wiki/entities/asus-vivobook-k6502zc|ASUS Vivobook K6502ZC]] — CUDA 測試硬體
- **外部來源：**
  - [ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp) — 官方 repo
  - [ggml-org/llama-install.sh](https://github.com/ggml-org/llama-install.sh) — 官方安裝腳本
  - [Model Management in llama.cpp](https://huggingface.co/blog/ggml-org/model-management-in-llamacpp) — Router mode 官方說明 🔍
  - [CUDA vs Vulkan benchmark](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/) 🔍

## 來源

- [[wiki/sources/2026-09-05-llamacpp-deployment-cuda-verification|llama.cpp 部署深度研究與 CUDA build 實測]]
