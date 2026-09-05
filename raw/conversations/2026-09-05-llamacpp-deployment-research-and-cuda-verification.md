---
title: "llama.cpp 部署深度研究與 CUDA build 實測"
type: conversation
created: 2026-09-05
source_kind: conversation
related_raw:
  - raw/conversations/2026-08-30-local-llm-thoughts.md
  - raw/youtube/2026-08-30-local-llm-agent-harness-dgx-spark
---

# llama.cpp 部署深度研究與 CUDA build 實測

> 2026-09-05，Cheer × Claude。內容包含：對 Gemini 一份 llama.cpp 筆記的查證、本機硬體與安裝現況實測、release build 更換過程、以及對 W-074 自建 harness 的架構影響。
> **本檔的事實分三類**：✅ 本機實測（最高可信）／🔍 查證過的外部來源（附 URL）／⚠️ 未驗證（明確標記，不可當結論用）。

---

## 1. 起因

Cheer 在研究 llama.cpp 時問 Gemini 四個問題：

1. 自動安裝腳本（`irm https://llama.app/install.ps1 | iex`）跟直接下載 release ZIP 有什麼不一樣？
2. 如果要把指定的 GGUF + llama.cpp 一起包進自建 AI Harness（如 ado-worklog-expert-agent），怎麼做？怎麼確保 model API 隨 harness 結束而正常結束？
3. 能不能同時啟兩個 model／切換 model？還是只能一個 model 一個 server？
4. 如果同時有兩個 model，`-c` 參數要怎麼設？安裝型跟下載型在手動調參上是否一樣、能不能後期調整？

Gemini 給了一份筆記（涵蓋安裝差異、多模型、進程生命週期的 C#/TypeScript 實作、語言選型建議）。Cheer 認為這跟 W-074 的規劃相關，要求分析。

---

## 2. Gemini 筆記的查證結果

### ✅ 正確：`llama.app` 安裝腳本是真的

`irm https://llama.app/install.ps1 | iex` 確實存在，官方 repo 為 [ggml-org/llama-install.sh](https://github.com/ggml-org/llama-install.sh)。腳本會偵測 OS／架構／GPU（CUDA、ROCm、Vulkan、Metal）。

（特別查證是因為 Gemini 有捏造 URL 的前科，見 `gemini-deep-research-citations-can-be-fabricated`。這次沒有捏造。）

### ❌ 過時：「要兩個 model 就得開兩個 server」

llama.cpp 現在有 **Router Mode**：`llama-server` **不帶 `-m` 啟動**即進入路由模式，用 `--models-preset`（INI 檔）或 `--models-dir` 註冊多個模型，client 用 OpenAI 相容 API 的 `model` 欄位選模型，共用同一個 port。

✅ **本機實測確認這組 flag 存在**（見 §4）。

外部替代方案 `llama-swap` 仍存在（獨立 binary proxy，每個 model 完全獨立的 server flag、idle timeout 卸載、crash 互不影響）。選型原則：**要 crash 隔離 + 每個模型參數差很多 → llama-swap；只是切換兩顆模型 → router mode 少一個依賴。**

### ⚠️ 沒答到：`-c` 的實際語意

這是 Cheer 問的第 4 題，Gemini 筆記裡沒有。正確答案：

- `-c` 是**總 KV cache 預算**，不是每個請求保證拿到的 context。
- 搭 `--parallel N` 時，每個 slot 拿到 `-c / N`。`-c 32768 --parallel 8` → 每個請求實際只有 4096 token。
- 這是 llama.cpp 的已知踩坑（[Issue #11681](https://github.com/ggml-org/llama.cpp/issues/11681)），後來加了 [`--kv-unified-per-slot`](https://github.com/ggml-org/llama.cpp/pull/24124) 做更細的控制。
- ✅ **本機 build 已有 `--kv-unified-per-slot`**，且 `-kvu/--kv-unified` 的預設是「當 slot 數為 auto 時啟用」——**所以「`-c` 被 `-np` 除掉」是在 slot 數非 auto 的情況下才成立**，unified KV 模式下是共享池。設定時兩者要一起看。

三種情境下 `-c` 的設法：
- 兩個獨立 server / llama-swap → 各自 `-c`，記憶體加總不能超過硬體上限
- Router mode → `-c` 寫進 preset 的**各自 section**（`ctx-size`），不是全域一個
- 任一情境 → 都要先確認 `-np` 與 `--kv-unified` 的組合，才知道每個請求真正拿得到多少

### ⚠️ 沒講到重點：安裝方式對「參數」沒差，對「backend」有決定性差別

所有 `-c` / `-ngl` / `--parallel` / `--port` 都是**啟動時給的 CLI 參數**，跟 binary 怎麼取得零關係，裝完照樣調。

**真正的差別是編譯進去的 backend（CUDA / Vulkan / ROCm / CPU）——backend 不是參數，是 build，要換就得換執行檔。** 這一點在本機造成了實際後果，見 §3。

### 🚫 已定案、不需重開：語言選型建議

Gemini 建議「C# 做底層基礎設施 + TS/Python 做 Agent 邏輯」。這在 Cheer 已拍板的兩份決策之外：

- W-074 核心本體 = **TypeScript**（2026-09-02 決定，依據 Zod + XState + Anthropic 官方 SDK 首發語言）
- W-080 ADO Agent = **C#/.NET 10 + React/TS**（2026-08-22 訂正，依據 ADO 是微軟產品、公司主力是 Copilot）

Gemini 不知道這兩份依據，給的是通論。它說「C# 子進程管理較強健」事實層面沒錯，但不足以推翻既有三條理由；而 Node 在這題上的弱點正好是已記錄在案的 Windows 進程樹問題，屬可解的工程細節，不是語言選型層級。**此段建議歸檔，不進 W-074。**

---

## 3. 本機實測：硬體、安裝現況、換 CUDA build

### ✅ 硬體實測（2026-09-05）

| 項目 | 值 |
|------|-----|
| 機型 | ASUS Vivobook K6502ZC |
| CPU | Intel i7-12700H（14 核 / 20 執行緒） |
| 獨顯 | **NVIDIA RTX 3050 Laptop GPU，4096 MiB VRAM** |
| 內顯 | Intel Iris Xe |
| RAM | 32 GB |
| OS | Windows 11 家用版 10.0.26200 x64 |
| 驅動 | 572.83，**CUDA Version 12.8** |

> ⚠️ **重要澄清**：這台筆電**不是** wiki 既有 `local-llm-deployment` 頁面裡寫的「Intel UHD 770 + i5-14500T Mini PC」。那是另一台機器。本次所有結論只適用於這台 Vivobook。兩台機器的最佳 backend 選擇完全不同（Mini PC 走 Vulkan/SYCL，這台走 CUDA）。

### ✅ 安裝現況與「為什麼自動腳本選了 Vulkan」

Cheer 先前已跑過 `irm https://llama.app/install.ps1 | iex`。實測發現：

- 安裝結果是**單一 55 MB 靜態 exe**，位置 `%LOCALAPPDATA%\Microsoft\WindowsApps\llama.exe`（該目錄預設在 PATH 上），旁邊沒有任何 DLL
- 版本 `0.3.0-dev (build 10679, commit 50f068fff)`
- 它是**新的統一 `llama` CLI**（子指令 `serve` / `cli` / `update` / `download` / `bench` / `fit-params` / `quantize` / `perplexity`），不是傳統的 `llama-server.exe` / `llama-cli.exe`
- `llama serve --list-devices` 輸出：
  ```
  Vulkan0: Intel(R) Iris(R) Xe Graphics (16197 MiB, 15429 MiB free)
  Vulkan1: NVIDIA GeForce RTX 3050 Laptop GPU (3964 MiB, 3369 MiB free)
  ```
  → **是 Vulkan build，沒有 CUDA**

**根因（讀 install.ps1 原始碼確認）**：腳本的探測順序是 `ProbeCUDA` → `ProbeVulkan` → `ProbeCPU`。`ProbeCUDA` 會下載一支 `cuda-probe.exe`，**當 exit code 為 5 時代表「偵測到 NVIDIA GPU，但沒有安裝 CUDA Toolkit」**，直接放棄 CUDA 往下掉到 Vulkan。腳本原始碼中該分支的訊息是：

> NVIDIA GPU detected, but the CUDA Toolkit is not installed.

Cheer 這台有 NVIDIA 驅動（含 CUDA 12.8 runtime），但沒裝 CUDA Toolkit，所以**自動安裝腳本靜默降級成 Vulkan**。腳本安裝路徑為 `%LOCALAPPDATA%\llama-app`（暫存）→ 複製 exe 到 WindowsApps → 刪除暫存目錄；**沒有提供 uninstall 功能**。

### 🔍 為什麼 Vulkan 降級是實質損失

2026-04-23 的跨 backend benchmark（RTX 5090）：CUDA `pp512` 約 14,073 tok/s、`tg128` 約 290 tok/s；Vulkan 約 10,382 / 264。**CUDA 約快 36%（prefill）與 10%（generation）**，Flash Attention 會再擴大 prefill 領先。結論是在 NVIDIA 卡上沒有討論空間，該用 CUDA。
來源：[Comparing llama.cpp GPU Performance: CUDA, ROCm, Vulkan](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/)

### ✅ 更換為 CUDA release build（已完成並驗證）

決定改用 release ZIP，取回 backend 主控權。

**版本選擇的關鍵判斷**：release 同時提供 `cuda-12.4` 與 `cuda-13.3` 的 Windows x64 build。本機驅動只到 **CUDA 12.8**，因此**必須選 12.4，選 13.3 會跑不起來**（CUDA 只在同一 major 版本內向前相容）。

實際安裝的檔案（nightly tag `b10819`）：
- `llama-b10819-bin-win-cuda-12.4-x64.zip`（242 MB）
- `cudart-llama-bin-win-cuda-12.4-x64.zip`（373 MB）

安裝位置與結構：
```
%LOCALAPPDATA%\llama.cpp\b10819\      # 實體版本目錄
%LOCALAPPDATA%\llama.cpp\current      # junction → b10819，PATH 指向這裡
```
用 junction 而不是直接把版本目錄加進 PATH，**升級時只要換 junction 指向，不用改環境變數**。junction 在 Windows 上建立不需要管理員權限（symlink 需要）。

**✅ 關鍵驗證結果**：
```
> llama-server.exe --list-devices
Available devices:
  CUDA0: NVIDIA GeForce RTX 3050 Laptop GPU (4095 MiB, 3303 MiB free)

> llama-server.exe --version
version: 0.4.0-dev (build 10819, commit 6a1a922d2)
```

**這證明了一件實務上很有價值的事：`cudart-*.zip` 已經包含 CUDA runtime DLL（`cudart64_12.dll`、`cublas64_12.dll` 95 MB、`cublasLt64_12.dll` 452 MB、`ggml-cuda.dll` 520 MB），所以用 release ZIP 可以拿到 CUDA 加速而完全不必安裝數 GB 的 CUDA Toolkit。** 自動安裝腳本要求 Toolkit 才給 CUDA，是它自己的探測策略限制，不是 llama.cpp 的硬需求。

舊的 Vulkan `llama.exe`（52.7 MB）已從 WindowsApps 移除，確認刪除成功。

release ZIP 內含的執行檔：`llama-server`、`llama-cli`、`llama-bench`、`llama-batched-bench`、`llama-quantize`、`llama-perplexity`、`llama-fit-params`、`llama-tokenize`、`llama-mtmd-cli`、`ggml-rpc-server` 等，**以及新的統一 `llama.exe`**——所以 release ZIP 是舊工具鏈與新 CLI 的超集，功能上沒有損失。

---

## 4. 本機 build 的權威參數事實（`--help` 實測，非部落格轉述）

以下皆為 b10819 CUDA build 的 `llama-server --help` 實際輸出。**這比任何外部文章可信，因為它就是我們要跑的那顆 binary。**

### Router mode（多模型）
| Flag | 說明 |
|------|------|
| `--models-dir PATH` | router server 的模型目錄（預設 disabled） |
| `--models-preset PATH` | INI 檔，定義各模型的 preset（預設 disabled） |
| `--models-max N` | router 同時載入的模型數上限，**預設 4**，0 = 無限 |
| `--models-autoload` | 是否自動載入模型，**預設 enabled** |

`--models-max` 預設 4 這點，佐證了官方說法（多模型並存 + LRU 淘汰），**推翻了某些第三方部落格「同時只有一個模型在記憶體」的說法**。查證時兩份來源矛盾，最後以本機 `--help` 為準。

### 併發與 context
| Flag | 說明 |
|------|------|
| `-np, --parallel N` | server slot 數，**預設 -1 = auto** |
| `-kvu, --kv-unified` | 共用單一 KV buffer，**預設：slot 數為 auto 時啟用** |
| `--kv-unified-per-slot N` | 每個 slot 的 context 上限；未搭 `-c` 時共享 KV 池大小為 `n_parallel * N` |
| `-c, --ctx-size N` | 預設 0 = 從模型讀取 |
| `-cb, --cont-batching` | 連續批次（動態批次），**預設 enabled** |

### 記憶體自動配適（對 4 GB 顯卡特別重要）
| Flag | 說明 |
|------|------|
| `-fit, --fit [on\|off]` | **預設 on**——自動調整未指定的參數以塞進裝置記憶體 |
| `-fitt, --fit-target` | 每個裝置的保留邊界，預設 **1024 MiB** |
| `-fitc, --fit-ctx N` | `--fit` 能設定的最小 ctx，預設 **4096** |

`--fit` 預設開啟是近期的重要改變：**在 4 GB 卡上不指定 `-ngl` 時，llama.cpp 會自己算該offload 幾層**。另有獨立的 `llama-fit-params` 工具可事先計算。

### 其他值得記住的
- `--jinja` **預設 enabled**（chat template 走 jinja 引擎）
- `-ngl` 支援精確層數；`-dev/--device` 可指定用哪些裝置（本機可用來強制只用 CUDA0、不碰內顯）
- `-ncmoe/--n-cpu-moe N`：把前 N 層的 MoE 權重留在 CPU——**在 4 GB VRAM + 32 GB RAM 的機器上跑 MoE 模型的關鍵手段**
- `-ot/--override-tensor`：更細的張量 buffer 指派
- `--api-key` / `--api-key-file`：API 認證
- Web UI **預設 enabled**（`--no-webui` 關閉）；`--slots` 監控端點預設開啟
- `--host` 預設 `127.0.0.1`、`--port` 預設 8080

---

## 5. 進程生命週期（對自建 harness 最關鍵的一段）

### ✅ 本機實測：router mode 進程與清理

啟動 `llama-server --models-dir <空目錄> --port 18080 --no-webui`：
- 起來後是**單一進程**（PID 10864，RSS 179 MB），`/v1/models` 回 `{"data":[],"object":"list"}`
- 用 `taskkill.exe /PID <pid> /T /F` 終止後，`llama-server.exe` 進程數歸零，**清理乾淨**

⚠️ **未驗證**：因為當時尚未下載任何 GGUF，**沒有測到「載入模型後是否真的每個模型一個子進程」**。官方說法是多進程架構（一個模型崩潰不影響其他），若屬實，進程樹會是 `harness → llama-server(router) → 各 model 子進程` 三層。**這一點必須在有模型之後重測，不能當已知事實使用。**

### 🔍 Windows 上殺進程樹的正確做法

- Node 的 `process.kill()` 在 Windows 上**只殺該 PID，不殺子進程**——孤兒進程就是這樣產生的
- 實務解法：`taskkill /PID <pid> /T /F`（`/T` 連子進程一起、`/F` 強制）
- **真正由 OS 保證的做法是 Windows Job Object + `KILL_ON_JOB_CLOSE`**，父進程崩潰時 OS 直接連坐銷毀整個 job。但 **Node.js 沒有原生 Job Object API，要用 native addon**，所以純 JS 的 CLI 只能退而求其次用 taskkill
- ⚠️ 已知地雷：**某些 Node 24 build 上 `taskkill` 的 PATH 查找不可靠**，會以非同步的 `error` event 形式失敗而不是同步拋出。解法是用絕對路徑 `C:\Windows\System32\taskkill.exe` 並掛 error handler。這個問題在 Pi Agent 自己的 repo 有對應 issue（earendil-works/pi #6596）——**代表我們的技術棧已經踩過這顆雷**

---

## 6. 對 W-074 的架構影響

### 6.1 llama-server 不是「專家」，是 model runtime

按 W-074 決策 #4，專家是可實例化的角色；但 llama-server 是**基礎設施**。若 `ExpertHarness.invoke()` 每次開關一次 server，每次調用都要付模型載入成本（第三方觀測 7B 模型切換約 3–10 秒，⚠️ 未在本機驗證）。

正確形狀：**singleton runtime + reference counting + health check**，生命週期綁在 harness process 而不是單次 invoke。放置位置建議獨立一層（如 `packages/runtime`），**不要放進 `core`**——core「零外部相依」是四層 package 中最有價值的約束。

### 6.2 這跟已記錄的 Windows 未解問題是同一題

2026-09-02 軟體架構圓桌列出的四個未解問題之一：

> Windows 上 `SIGKILL` 可能殺不掉子系命令，需 `taskkill /F /T /PID` 輔助

llama-server 讓這題更硬（可能的三層進程樹）。開工六步的第 ⑤ 步（`packages/experts`：ChildProcessRunner + 零號專家）本來就要解逾時 SIGKILL，測試路徑 B（逾時重試流）也在等這個。

**具體建議：ChildProcessRunner 從一開始就照「要能殺整棵進程樹」設計，並使用 `taskkill` 的絕對路徑 + error handler。** 這是唯一一個事後補會痛的部分，其餘都可延後。

### 6.3 這會是 `execution_constraint` 的第一個真實值

`task-contract.md` 的 `execution_constraint` 目前留空（TBD），因為 MVP 一案一專家單一環境用不上。地端 GGUF + 自建 harness 落在 `agent-security-levels` 的 **L5/L6**。真做下去，這欄位就有第一個非空值，也是第一次讓決策 #12「Tier 是專家 × harness 綁定的屬性、可升級」有實例可驗。

### 6.4 建議：不進 MVP，但現在就留縫

**不進的理由**：MVP 範圍是「一案一專家、全自動、跑通六步閉環」，零號專家刻意選最弱的 Tier C。現在塞 local model 等於同時引入 runtime 生命週期、Windows 進程樹、模型載入延遲三個新變數，違反「先跑通閉環再擴」。

**留縫的做法（成本近乎零）**：
1. 專家註冊表加 `model_runtime` 欄位（`external | local`），MVP 全填 `external`
2. ChildProcessRunner 照「殺三層樹」設計（見 6.2）

**做的時機**：零號專家四條測試路徑跑通之後，當作第一次「換 runtime」的練習。那時 EventStream 已在跑，換前換後的差異有證據可看。

### 6.5 跟 W-080 的關係

實測確認：**repo 內目前沒有任何 ado-worklog-expert-agent 的程式碼或目錄**，W-ADO-000～012 全部未打勾。所以「把 GGUF + llama.cpp 包進 ADO Agent 的 harness」現在沒有可包的東西。

而且 2026-09-01 圓桌已經把 local model 定位在「DevOps skill 執行 + 混合式四層路由 + 7B 起步」那條線。**local model 的正確歸屬是那裡，不應該從 W-074 核心再長出第二條路線。**

---

## 7. 這台機器的模型選型（4 GB VRAM）

⚠️ **本節尚未實測，只是外部來源整理 + 硬體推算，實際跑過才算數。**

硬體現實：RTX 3050 Laptop 只有 **4096 MiB VRAM，扣掉桌面佔用實際可用約 3.3 GB**；系統 RAM 32 GB 相對充裕。

🔍 外部建議（[LMSA](https://lmsa.app/blog/running-local-ai-on-a-4gb-vram-gpu-in-2026-the-real-world-guide-that-actually-works/)、[mayhemcode](https://www.mayhemcode.com/2026/06/best-local-llms-for-4gb-6gb-and-8gb.html)）：
- 4 GB 檔位實際可全載的是 **3B–4B 級模型**
- 程式類：**Qwen3-Coder 4B**；通用：Gemma 4 E2B、Phi-4-mini
- **明確不要在 4 GB 上硬跑 7B**：會持續 spill 到 RAM，實測約 4–5 tok/s，不堪用

這跟 2026-09-01 圓桌「7B 起步（Qwen2.5-Coder-7B Q4_K_M）」的結論**不衝突但要分開看**——那條結論是給 Mini PC（Iris Xe 可分享 16 GB）的，這台獨顯只有 4 GB。**同一個 7B 模型在兩台機器上的正確跑法不一樣**：Mini PC 可以全載進共享記憶體，這台要嘛降到 3B–4B 全載 CUDA，要嘛用 `-ngl` 部分 offload + 32 GB RAM 承接，要嘛用 `-ncmoe` 跑 MoE。

---

## 8. 待驗證清單（不可當結論使用）

1. **router mode 載入模型後是否真的每個模型一個子進程** —— 決定 harness 的殺進程策略要處理幾層。有 GGUF 之後必測。
2. **這台機器上 3B/4B 全載 CUDA 的實際 tok/s**，以及 7B 部分 offload 的實際表現 —— 用 `llama-bench` 測，不要引用別人的數字。
3. **`--fit` 預設 on 在 4 GB 卡上的實際行為** —— 它自己算出來的 `-ngl` 跟 ctx 是否合理。
4. **模型切換延遲的實測值** —— 3–10 秒是外部說法，未驗證。
5. **`taskkill /T` 在真的有 model 子進程時是否仍清得乾淨**。

---

## 來源

- 本機實測（2026-09-05，ASUS Vivobook K6502ZC）：硬體規格、`llama --list-devices`、`install.ps1` 原始碼、`llama-server --help`、CUDA build 驗證、router mode 進程測試
- [ggml-org/llama-install.sh](https://github.com/ggml-org/llama-install.sh) —— 官方安裝腳本
- [llama.cpp releases](https://github.com/ggml-org/llama.cpp/releases) —— release 資產命名與 nightly tag b10819
- [New in llama.cpp: Model Management (ggml-org)](https://huggingface.co/blog/ggml-org/model-management-in-llamacpp) —— router mode 官方說明
- [llama.cpp Issue #11681](https://github.com/ggml-org/llama.cpp/issues/11681) —— `-c` 被 `--parallel` 除掉
- [llama.cpp PR #24124](https://github.com/ggml-org/llama.cpp/pull/24124) —— `--kv-unified-per-slot`
- [CUDA vs Vulkan benchmark (2026-04-23)](https://knightli.com/en/2026/04/23/llama-cpp-gpu-benchmark-cuda-rocm-vulkan-scoreboard/)
- [LMSA — 4GB VRAM 實務指南](https://lmsa.app/blog/running-local-ai-on-a-4gb-vram-gpu-in-2026-the-real-world-guide-that-actually-works/)
- [How to Run Multiple LLMs Locally Using Llama-Swap (KDnuggets)](https://www.kdnuggets.com/how-to-run-multiple-llms-locally-using-llama-swap-on-a-single-server)
- 既有知識庫：[[wiki/concepts/local-llm-deployment]]、[[wiki/concepts/agent-security-levels]]、[[wiki/sources/2026-09-01-local-llm-round-table-synthesis]]、[[work/current#W-2026-08-074]]、[[work/current#W-2026-08-080]]
