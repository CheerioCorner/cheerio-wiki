---
title: "llama.cpp 部署深度研究與 CUDA build 實測"
type: source
created: 2026-09-05
updated: 2026-09-05
sources: 1
tags: [local-llm, llama-cpp, cuda, vulkan, gpu, windows-process-management, w-074, w-080]
topics: [agent-infrastructure, ai-agent]
canonical: sources/2026-09-05-llamacpp-deployment-cuda-verification
provenance_raw: "raw/conversations/2026-09-05-llamacpp-deployment-research-and-cuda-verification.md"
---

# llama.cpp 部署深度研究與 CUDA build 實測

> 一句話：在 ASUS Vivobook K6502ZC（RTX 3050 4GB）上查證一份 Gemini llama.cpp 筆記、實測換裝 CUDA release build、記錄 router mode 與進程生命週期的權威參數，並評估對 W-074 harness 架構的影響。

## 起因

Cheer 向 Gemini 問了四個 llama.cpp 部署問題（自動安裝腳本差異、GGUF 打包進 harness、多模型、`-c` 參數語意），Gemini 回了一份筆記。本 raw 是對該筆記的查證 + 本機實測。

## Gemini 筆記查證結果

| 項目 | 結論 | 說明 |
|------|------|------|
| `llama.app` 安裝腳本 | ✅ 正確 | 官方 repo `ggml-org/llama-install.sh`，未捏造 URL |
| 「要兩個 model 就得開兩個 server」 | ❌ 過時 | llama.cpp 現有 Router Mode：不帶 `-m` 啟動即進入路由，共用 port |
| `-c` 的實際語意 | ⚠️ 未答到 | `-c` 是總 KV cache 預算，搭 `--parallel N` 時被除；Issue #11681 |
| 安裝方式對 backend 的差別 | ⚠️ 沒講清楚 | CLI 參數跟安裝方式無關，差別在 backend 是 build（CUDA/Vulkan），要換就得換執行檔 |
| 語言選型建議（C#+TS/Python） | 🚫 已定案不重開 | W-074=TypeScript、W-080=C#/.NET，既有決策有充分依據 |

## 本機實測：ASUS Vivobook K6502ZC

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

> ⚠️ **重要**：這台筆電不是 wiki 既有 `local-llm-deployment` 頁面裡的「Intel UHD 770 + i5-14500T Mini PC」。兩台機器的最佳 backend 完全不同。

### 安裝過程與「為什麼自動腳本選了 Vulkan」

- Cheer 先前已跑過 `irm https://llama.app/install.ps1 | iex`
- 結果是 55 MB 靜態 exe（新統一 `llama` CLI），**Vulkan build，沒有 CUDA**
- **根因**：`install.ps1` 的 `ProbeCUDA` 偵測到 NVIDIA GPU 但沒有 CUDA Toolkit，exit code 5，靜默降級到 Vulkan
- 腳本安裝路徑 `%LOCALAPPDATA%\llama-app`（暫存）→ 複製到 WindowsApps → 刪暫存；**沒有 uninstall 功能**

### 🔍 為什麼 Vulkan 降級是實質損失

2026-04-23 跨 backend benchmark（RTX 5090）：CUDA 約快 36%（prefill）與 10%（generation）。
來源：[knightli.com](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/)

### ✅ 更換為 CUDA release build

- 版本選擇：release 同時提供 `cuda-12.4` 與 `cuda-13.3`，本機驅動只到 CUDA 12.8，**必須選 12.4**
- 實際安裝：`llama-b10819-bin-win-cuda-12.4-x64.zip`（242 MB）+ `cudart-llama-bin-win-cuda-12.4-x64.zip`（373 MB）
- 安裝結構：`%LOCALAPPDATA%\llama.cpp\b10819\`（實體版本）→ `current`（junction 指向 b10819）
- **關鍵驗證**：`--list-devices` 輸出 `CUDA0: NVIDIA GeForce RTX 3050 Laptop GPU (4095 MiB, 3303 MiB free)`
- **重要發現**：`cudart-*.zip` 已包含 CUDA runtime DLL（`cudart64_12.dll`、`cublas64_12.dll` 等），用 release ZIP 即可拿到 CUDA 加速，完全不必安裝數 GB 的 CUDA Toolkit

## 本機 build 的權威參數事實（`--help` 實測）

以下皆為 b10819 CUDA build 的 `llama-server --help` 實際輸出。

### Router mode（多模型）

| Flag | 說明 |
|------|------|
| `--models-dir PATH` | router server 的模型目錄（預設 disabled）|
| `--models-preset PATH` | INI 檔，定義各模型 preset |
| `--models-max N` | 同時載入模型數上限，**預設 4**，0=無限 |
| `--models-autoload` | 自動載入模型，**預設 enabled** |

`--models-max` 預設 4 推翻了部分部落格「同時只有一個模型在記憶體」的說法。佐證來源：[HuggingFace Blog — Model Management in llama.cpp](https://huggingface.co/blog/ggml-org/model-management-in-llamacpp) 🔍

### 併發與 context

| Flag | 說明 |
|------|------|
| `-np, --parallel N` | server slot 數，**預設 -1 = auto** |
| `-kvu, --kv-unified` | 共用單一 KV buffer，**預設：slot 數 auto 時啟用** |
| `--kv-unified-per-slot N` | 每個 slot 的 context 上限 |
| `-c, --ctx-size N` | 預設 0 = 從模型讀取 |
| `-cb, --cont-batching` | 連續批次，**預設 enabled** |

**踩坑提醒**：`-c 32768 --parallel 8` → 每個請求實際只有 4096 token（Issue [#11681](https://github.com/ggml-org/llama.cpp/issues/11681)）。`--kv-unified-per-slot`（PR [#24124](https://github.com/ggml-org/llama.cpp/pull/24124)）提供更細的控制。✅ 本機 build 已有此 flag。

三種情境下 `-c` 的設法：
- 兩個獨立 server / llama-swap → 各自 `-c`，記憶體加總不能超過硬體上限
- Router mode → `-c` 寫進 preset 的各自 section（`ctx-size`），不是全域一個
- 任一情境 → 都要先確認 `-np` 與 `--kv-unified` 的組合

### 記憶體自動配適（4 GB 顯卡特別重要）

| Flag | 說明 |
|------|------|
| `-fit, --fit [on\|off]` | **預設 on**——自動調整未指定的參數以塞進裝置記憶體 |
| `-fitt, --fit-target` | 每個裝置的保留邊界，預設 **1024 MiB** |
| `-fitc, --fit-ctx N` | `--fit` 能設定的最小 ctx，預設 **4096** |

### 其他值得記住的

- `--jinja` **預設 enabled**（chat template 走 jinja 引擎）
- `-ngl` 支援精確層數；`-dev/--device` 可指定用哪些裝置
- `-ncmoe/--n-cpu-moe N`：把前 N 層的 MoE 權重留在 CPU——**4 GB VRAM + 32 GB RAM 跑 MoE 的關鍵手段**
- `--api-key` / `--api-key-file`：API 認證
- Web UI **預設 enabled**（`--no-webui` 關閉）
- `--host` 預設 `127.0.0.1`、`--port` 預設 8080

## 進程生命週期

### ✅ 本機實測：router mode 進程與清理

啟動 `llama-server --models-dir <空目錄> --port 18080 --no-webui`：
- 單一進程（PID 10864，RSS 179 MB）
- `taskkill.exe /PID <pid> /T /F` 終止後進程數歸零

⚠️ **未驗證**：尚未下載 GGUF，**沒有測到「載入模型後是否真的每個模型一個子進程」**。官方說法是多進程架構（一個模型崩潰不影響其他）。**這一點必須在有模型之後重測。**

### 🔍 Windows 上殺進程樹的正確做法

- Node 的 `process.kill()` 在 Windows 上**只殺該 PID，不殺子進程**
- 實務解法：`taskkill /PID <pid> /T /F`（`/T` 連子進程、`/F` 強制）
- **真正由 OS 保證的做法是 Windows Job Object + `KILL_ON_JOB_CLOSE`**，但 Node.js 沒有原生 Job Object API，要用 native addon
- ⚠️ 已知地雷：某些 Node 24 build 上 `taskkill` 的 PATH 查找不可靠（pi agent 自己的 repo 有對應 issue #6596）

## 對 W-074 的架構影響

### 6.1 llama-server 是 model runtime，不是「專家」

按 W-074 決策 #4，專家是可實例化的角色；但 llama-server 是**基礎設施**。若 `ExpertHarness.invoke()` 每次開關一次 server，每次都要付模型載入成本（第三方觀測 7B 模型切換約 3–10 秒，⚠️ 未在本機驗證）。

正確形狀：**singleton runtime + reference counting + health check**，生命週期綁在 harness process 而不是單次 invoke。放置位置建議獨立一層（如 `packages/runtime`），**不要放進 core**。

### 6.2 與已記錄的 Windows 未解問題是同一題

2026-09-02 架構圓桌列出四個未解問題之一：Windows 上 `SIGKILL` 可能殺不掉子系命令。llama-server 讓這題更硬（可能的三層進程樹）。ChildProcessRunner 應從一開始就照「要能殺整棵進程樹」設計。

### 6.3 這會是 `execution_constraint` 的第一個真實值

`task-contract.md` 的 `execution_constraint` 目前留空。地端 GGUF + 自建 harness 落在 `agent-security-levels` 的 **L5/L6**。真做下去，這欄位就有第一個非空值。

### 6.4 決策：不進 MVP，但現在就留縫

- 專家註冊表加 `model_runtime` 欄位（`external | local`），MVP 全填 `external`
- ChildProcessRunner 照「殺三層樹」設計
- **時機**：零號專家四條測試路徑跑通後

### 6.5 跟 W-080 的關係

實測確認：repo 內**沒有任何 ado-worklog-expert-agent 的程式碼或目錄**。local model 的正確歸屬是 2026-09-01 圓桌那條「DevOps skill 執行 + 混合式四層路由 + 7B 起步」路線。

## ⚠️ 這台機器的模型選型（4 GB VRAM）— 尚未實測

硬體現實：RTX 3050 Laptop 只有 4096 MiB VRAM，實際可用約 3.3 GB。

🔍 外部建議（[LMSA](https://lmsa.app/blog/running-local-ai-on-a-4gb-vram-gpu-in-2026-the-real-world-guide-that-actually-works/)、[mayhemcode](https://www.mayhemcode.com/2026/06/best-local-llms-for-4gb-6gb-and-8gb.html)）：
- 4 GB 檔位實際可全載的是 **3B–4B 級模型**
- 程式類：**Qwen3-Coder 4B**；通用：Gemma 4 E2B、Phi-4-mini
- **明確不要在 4 GB 上硬跑 7B**

這跟 2026-09-01 圓桌「7B 起步」的結論**不衝突但要分開看**——那條是給 Mini PC（Iris Xe 可分享 16 GB）的。

## ⚠️ 待驗證清單（不可當結論使用）

1. router mode 載入模型後是否真的每個模型一個子進程
2. 這台機器上 3B/4B 全載 CUDA 的實際 tok/s，以及 7B 部分 offload 的實際表現
3. `--fit` 預設 on 在 4 GB 卡上的實際行為
4. 模型切換延遲的實測值
5. `taskkill /T` 在真的有 model 子進程時是否仍清得乾淨

## Cheer 的想法

> 想法摘要來自 [[raw/conversations/2026-08-30-local-llm-thoughts|看完地端 AI 模型追上閉源巨頭影片的想法]]：

Cheer 在看完地端 AI 模型追上閉源巨頭的影片後，認為混合運用 LLM 的方式將會是未來的方向。無論是對公司內部還是個人來說，都會想在兩邊有所投入，並評估硬體設備需求與成本。這些資訊對於用 AI 創造更多想像（包括專案）相當重要。本次實測正是將這個想法落實的第一步——在自己的硬體上驗證 llama.cpp 的 CUDA 部署。

## 與既有知識的關係

- **延伸** [[wiki/concepts/local-llm-deployment|Local LLM 部署]]：新增第二台機器（RTX 3050 4GB）的硬體實務實例，**嚴格與既有 Intel UHD 770 隔離**
- **深化** [[wiki/concepts/agent-security-levels|Agent 安全等級]]：為 L5/L6 提供真實應用場景
- **補充** [[wiki/sources/2026-09-01-local-llm-round-table-synthesis|圓桌會議紀要]]：具體化 Windows 進程管理的技術細節
- **驗證** [[wiki/sources/2026-08-30-local-llm-agent-harness-dgx-spark|DGX Spark 實機示範]]：確認安裝腳本探測邏輯
- **不推翻**任何既有結論

## 來源

- 本機實測（2026-09-05，ASUS Vivobook K6502ZC）
- [[raw/conversations/2026-08-30-local-llm-thoughts|看完地端 AI 模型追上閉源巨頭影片的想法]]
- [[raw/youtube/2026-08-30-local-llm-agent-harness-dgx-spark|從模型部署到 Agent Harness]]
- [ggml-org/llama-install.sh](https://github.com/ggml-org/llama-install.sh) — 官方安裝腳本
- [llama.cpp releases](https://github.com/ggml-org/llama.cpp/releases) — nightly tag b10819
- [Model Management in llama.cpp](https://huggingface.co/blog/ggml-org/model-management-in-llamacpp) — router mode 官方說明 🔍
- [Issue #11681](https://github.com/ggml-org/llama.cpp/issues/11681) — `-c` 被 `--parallel` 除掉 🔍
- [PR #24124](https://github.com/ggml-org/llama.cpp/pull/24124) — `--kv-unified-per-slot` 🔍
- [CUDA vs Vulkan benchmark](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/) 🔍
- [LMSA — 4GB VRAM 實務指南](https://lmsa.app/blog/running-local-ai-on-a-4gb-vram-gpu-in-2026-the-real-world-guide-that-actually-works/) 🔍
- [KDnuggets — llama-swap](https://www.kdnuggets.com/how-to-run-multiple-llms-locally-using-llama-swap-on-a-single-server) 🔍
