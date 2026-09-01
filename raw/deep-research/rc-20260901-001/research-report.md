# Deep Research 報告 — rc-20260901-001

- 研究主題：Intel UHD 770 + 32GB RAM 環境下的 Local LLM 選擇：量化策略、TTFT 效能、模型篩選與 GitHub Copilot 整合——聚焦多步驟 DevOps skill 執行能力
- Notebook：https://notebooklm.google.com/notebook/b8e1d221-6784-454f-a20d-cb536253134f
- 產生時間：2026-09-01T13:55:07.443Z
- 品質過濾：63 → 61 筆（移除 2 筆）
- 蒸餾必要性：207 → 200 筆（移除 7 筆對研究問題無貢獻的來源）
- 來源分類/重新命名：67 / 67 筆成功

## 研究結果

### 1. 在 Intel UHD 770 + 32GB RAM + i5-14500T 的 Windows 11 環境下，量化（GGUF Q4/Q5/Q6/Q8）如何改變模型選擇範圍？哪些原本太大的模型量化後能在 32GB RAM 內運行且保持多步驟流程編排能力？

在 **Intel UHD 770 + 32GB RAM + i5-14500T** 的 Windows 11 環境下，量化技術（GGUF 格式）是決定本機 LLM 能否運作的關鍵鑰匙。在這種特定硬體下，存在著兩條關鍵的記憶體界線，它們深度地改變了模型的選擇範圍。

---

### 一、 系統記憶體架構與「16GB 共享記憶體」的絕對邊界

1. **Windows 11 的 50% 顯存分配硬性限制**：
   * **Intel UHD 770** 作為核芯顯示卡（iGPU），本身沒有獨立顯存（Dedicated VRAM）[1]。在 Windows 11 的 WDDM 驅動架構下，系統會預設將**系統記憶體的一半（50%）**作為「共享 GPU 記憶體（Shared GPU Memory）」[2-4]。
   * 意即在 32GB RAM 的系統中，**UHD 770 可調用的最高動態顯存上限為 16GB** [2, 3]。
   * 由於用戶的處理器為 **i5-14500T（14代核顯，非 Core Ultra 系列 2）**，並不支援最新的 "Shared GPU Memory Override" 驅動設定（該功能要求 Core Ultra Series 2 或更高硬體，可調高至 57%~93%）[5, 6]，因此 **16GB 的顯存上限在 Windows 11 下是無法突破的硬限制**。
2. **記憶體頻寬的物理瓶頸**：
   * 在本機 LLM 的運行中，**Token 生成（Token Generation）階段是典型的記憶體頻寬受限（Memory-bound）任務** [7, 8]。
   * 由於 iGPU 與 CPU 共享相同的系統主機記憶體（DDR5 或 DDR4 雙通道），iGPU 無法繞過系統記憶體頻寬的物理限制 [7]。UHD 770 只能在 **Prompt 預載（Prefill）階段**提供算力加速，而生成速度（Decode）本質上仍被系統記憶體頻寬所卡死 [7, 9]。

---

### 二、 量化如何改變模型選擇範圍？

GGUF 格式的記憶體映射技術（mmap）允許模型按需讀取 [10]，而量化（Q4/Q5/Q6/Q8）則將模型檔案體積進行大幅壓縮，從而將 32GB 的本機環境劃分為兩條截然不同的部署路線：

#### 1. GPU 全卸載（Fully Offloaded）路線：上限 16GB VRAM 
在此路線下，模型權重與 KV Cache 完全放入 UHD 770 的 16GB 共享記憶體中。
* **7B 級模型（例如 Qwen2.5-Coder-7B-Instruct）**：
  * **Q4_K_M**（約 4.13 GB）[11] 到 **Q8_0**（約 8.0 GB）[12] 均能 **100% 裝入 16GB 的 GPU 共享記憶體中**。這意味著您在運行 7B 模型時，還擁有極為充裕的顯存空間來設定超大的上下文視窗（Context Window），完全不用擔心溢出。
* **14B 級模型（例如 Qwen2.5-Coder-14B-Instruct）**：
  * **Q4_K_M** 量化模型檔案約 **9.0 GB** [13, 14]。在載入 llama.cpp/Ollama 後端基礎佔用（約 0.75 GB）後，約佔用 10-11 GB 顯存，**可完全裝入 16GB 顯存中運行** [12]。
  * **Q5_K_M**（約 10.2 GB）與 **Q6_K**（約 11.5 - 12.0 GB）在上下文視窗設定適中（例如 8K 以內）的情況下，也能完全裝入 16GB 的動態顯存中，並利用 iGPU 加速 [12]。
  * **Q8_0**（約 15 - 16 GB）會直接耗盡 16GB 的顯存上限，這時將完全沒有空間分配給 KV Cache，會導致執行時直接觸發記憶體溢出（OOM）或強制退回慢速的系統分頁交換（Swap） [15, 16]。

#### 2. CPU-only 推理 / 混合卸載路線：上限 32GB 系統 RAM
當模型體積超出 16GB VRAM 上限時，必須回到 32GB 的系統 RAM 中跑 CPU-only 或將部分層數卸載至 GPU [17, 18]。
* **32B / 35B 級模型（例如 Qwen2.5-Coder-32B-Instruct）**：
  * **Q4_K_M** 的模型檔案大小通常為 **20.5 GB 左右** [19, 20]。這意味著它**超出了 16GB GPU 共享記憶體的承載極限，無法完全交給核顯跑**。
  * 但因為 32GB RAM 的系統，扣除 Windows 11 系統與背景一般程序（約需 4GB-8GB 記憶體[21]）後，仍能提供約 24GB 的可用主機記憶體。因此，藉由 **Q4_K_M** 壓縮後的 20.5GB 模型，**可以完全裝入主系統記憶體中，以 CPU 推理（或部分層數混合卸載）的方式運行** [17, 22]。
  * **警告：Q5_K_M（約 24GB）或更高量化等級則不適合在此環境運行**，因為載入後剩餘的主機記憶體過少，在 Agent 工具載入上下文時會立即崩潰或發生嚴重的 Windows Swap 卡頓 [23, 24]。

---

### 三、 哪些原超大模型在量化後能在 32GB 內運行，且保持多步驟編排能力？

在 Aider、Cline、Roo Code 等本機編程 Agent 系統中，**多步驟流程編排能力（如工具調用、自動除錯與複雜邏輯編排）**是檢驗本機模型的黃金標準 [25, 26]。7B 級模型（即使是 Qwen2.5-Coder-7B）在面對複雜的多步驟 Agent 任務時，容易因為上下文拉長而丟失格式或陷入無限循環 [27]。

以下兩款模型在量化壓縮後，能在 32GB RAM 環境下運行，並保留完整的 Agentic 能力：

#### 1. 🌟 智能與速度的「甜點平衡」：Qwen2.5-Coder-14B-Instruct (`Q4_K_M` 或 `Q5_K_M`) [12, 14]
* **推薦量化**：`Q4_K_M`（檔案大小約 9.0GB）或 `Q5_K_M`（檔案大小約 10.2GB）[12, 14]。
* **本機運行表現**：
  * 可以 **100% 卸載（Fully Offloaded）至 UHD 770 的 16GB 共享記憶體中**，充分發揮 GPU 對 Prompt Prefill 的加速 [12, 28]。
  * 由於無需跨 CPU-GPU 總線進行頻繁的層數據調度，其運行穩定性最佳，回應延遲極低。
* **Agent 多步驟編排能力**：
  * Qwen2.5-Coder-14B 的邏輯推理與代碼修復能力十分強大，能夠順暢執行 Cline 的工具調用與 MCP 協議，不會因上下文增加而輕易格式崩潰 [13, 25]。

#### 2. 👑 智能的「極限天花板」：Qwen2.5-Coder-32B-Instruct (`Q4_K_M` 或 `IQ4_XS`) [13, 14]
* **推薦量化**：`Q4_K_M`（約 20.5GB）[19] 或 `IQ4_XS`（更輕量化，約 18GB 左右）。
* **本機運行表現**：
  * **無法全卸載至 GPU**，必須在 32GB 的主系統 RAM 中跑 **CPU 推理** [22]。
  * 受限於 i5-14500T（6P+8E 核心，20 執行緒）本機系統記憶體頻寬，其生成速度約為 **8 到 12 tokens/sec** [9, 29]，雖然慢，但已達本機實用門檻。
* **Agent 多步驟編排能力**：
  * 這是您在 32GB RAM 本機系統中能跑的 **最強 Agentic 核心**。該模型在代碼修復 benchmark (Aider 評分高達 73.7) 上的表現，已經與 GPT-4o 平起平坐 [13, 26]。
  * 雖然速度不如 14B 模型，但其工具調用的準確度、錯誤自我修正、長序列思考與複雜 codebase 的宏觀理解能力是 14B 所無法比擬的 [26]，能夠完美駕馭最複雜的跨檔案 Agent 自動化編程任務。

---

### 四、 本機部署的關鍵優化建議

為了讓 Qwen2.5-Coder 14B/32B 在此環境中運行得更好，建議手動配置以下參數：

1. **配置 KV Cache 量化 (`q8_0`)**：
   * 由於 14B/32B 的記憶體預算非常吃緊（尤其 32B 在主記憶體中），請務必開啟 KV Cache 量化（Ollama 環境變數設定 `OLLAMA_KV_CACHE_TYPE=q8_0`；llama.cpp 使用參數 `--cache-type-k q8_0 --cache-type-v q8_0`）[30, 31]。這能將 KV 緩衝區佔用的記憶體減半，直接降低長對話時 OOM 的機率 [32, 33]。
2. **強制開啟 Flash Attention**：
   * 設定環境變數 `OLLAMA_FLASH_ATTENTION=1`（或 llama-server 加入 `--flash-attn`），這能顯著降低注意力機制在長 context 下的 VRAM/RAM 佔用 [34, 35]。
3. **BIOS 調整 IGD Aperture Size**：
   * 請進入主機板 BIOS，將 **IGD Aperture Size** 調整到最大值（例如 1GB 或 2GB，而非預設的 128MB 或 256MB）[1, 36]。這可以擴大 PCIe BAR 的視窗大小，避免 iGPU 在調度 14B 模型的大張量數據時發生驅動崩潰 [37, 38]。

⚙️ 您是否需要為本機 Agent（如 Continue 或 Aider）對接 Ollama / llama.cpp 提供具體的設定檔（`config.json`）範例，以優化您的自動補全與對話表現？

**引用來源：**
- [1] [[Intel 官方] 技術指南 - BIOS 與系統設定調整內顯專用記憶體步驟](https://www.intel.com/content/www/us/en/support/articles/000041253/graphics.html)
- [2] [Frequently Asked Questions for Intel® Graphics Memory on Windows® 10](https://www.intel.com/content/www/us/en/support/articles/000020962/graphics.html)
- [3] [Urgent Request: Ability to Reduce Shared GPU Memory Allocation Beyond 50%](https://learn.microsoft.com/en-us/answers/questions/5812115/urgent-request-ability-to-reduce-shared-gpu-memory)
- [4] [[Intel 官方論壇] 技術討論 - 請求提供自訂降低共享顯示記憶體分配之反饋](https://learn.microsoft.com/en-us/answers/questions/5812115/urgent-request-ability-to-reduce-shared-gpu-memory)
- [5] [Shared GPU Memory Override Feature and Requirements - Intel](https://www.intel.com/content/www/us/en/support/articles/000101789/graphics.html)
- [6] [Shared GPU Memory Override Feature and Requirements - Intel](https://www.intel.com/content/www/us/en/support/articles/000101789/graphics.html)
- [7] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [8] [[Serverman 部落格] 排障手冊 - Ollama 推理速度緩慢的逐步排查與優化指引](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [9] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [10] [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [11] [[llama.cpp GitHub] 技術論壇 - Intel SYCL 後端架構效能回報與優化議題](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [12] [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [13] [qwen2.5-coder:14b-base - Ollama](https://ollama.com/library/qwen2.5-coder:14b-base)
- [14] [qwen2.5-coder:14b-instruct - Ollama](https://ollama.com/library/qwen2.5-coder:14b-instruct)
- [15] [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [16] [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [17] [[Easton Dev 部落格] 技術指南 - Ollama 批次處理、KV Cache 與 OOM 錯誤優化](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [18] [[Serverman 部落格] 排障手冊 - Ollama 推理速度緩慢的逐步排查與優化指引](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [19] [llama.cpp VRAM Requirements: Complete 2026 Guide to GPU Memory for Local LLMs](https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms)
- [20] [[OmniForge Blog] 技術指南 - 2026 最新 llama.cpp 的 GGUF 量化規格與記憶體需求表](https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms)
- [21] [Allocating RAM for GPU performance on self hosted LLM systems with integrated System & GPU RAM - PatrickMcCanna.net](https://patrickmccanna.net/allocating-ram-for-gpu-performance-on-self-hosted-llm-systems-with-integrated-system-gpu-ram/)
- [22] [Best Local LLMs for Continue.dev (2026 Tier List) - ModelFit](https://modelfit.io/tools/continue-dev/)
- [23] [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [24] [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [25] [Best Local LLMs for Continue.dev (2026 Tier List) - ModelFit](https://modelfit.io/tools/continue-dev/)
- [26] [qwen2.5-coder:14b-base - Ollama](https://ollama.com/library/qwen2.5-coder:14b-base)
- [27] [How to setup qwen coder 2.5 LLM for local dev on Windows in four steps](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [28] [How to Adjust Dedicated Video Memory for Intel Graphics](https://www.intel.com/content/www/us/en/support/articles/000041253/graphics.html)
- [29] [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [30] [[OmniForge Blog] 技術指南 - 解決本地 LLM 推理緩慢的 llama.cpp 最佳設定指引](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [31] [Why Your Local LLM Is Slow — llama.cpp Config Guide | OmniForge Blog](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [32] [[Hexdocs 官方] 技術指南 - LlamaCppEx 推理加速與參數調整優化指南](https://hexdocs.pm/llama_cpp_ex/performance.html)
- [33] [[OmniForge Blog] 技術指南 - 解決本地 LLM 推理緩慢的 llama.cpp 最佳設定指引](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [34] [[Serverman 部落格] 排障手冊 - Ollama 推理速度緩慢的逐步排查與優化指引](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [35] [[Serverman 部落格] 排障手冊 - Ollama 推理速度緩慢的逐步排查與優化指引](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [36] [[Hacker News 社群] 案例研究 - 利用 BIOS 鎖與 DVMT 調整內顯共享記憶體](https://news.ycombinator.com/item?id=37082906)
- [37] [What is IGD Aperture Size? - Intel](https://www.intel.com/content/www/us/en/support/articles/000028294/graphics.html)
- [38] [What is IGD Aperture Size? - Intel](https://www.intel.com/content/www/us/en/support/articles/000028294/graphics.html)

### 2. 不同量化等級的 TTFT（Time To First Token）在 CPU-only 推理下表現如何？互動式多輪對話（每次等待回應）的可接受 TTFT 閾值在哪？i5-14500T（14 核 20 緒）有哪些 CPU 加速手段（AVX-512、DLL 等）能改善 TTFT？

### 不同量化等級的 TTFT 在 CPU-only 推理下的表現

在 **CPU-only（純 CPU）** 推理環境下，**TTFT（Time To First Token，首字延遲）** 主要是由 **Prefill（預填充，亦即 Prompt 處理）** 階段的計算速度來決定 [1, 2]。此階段屬於**計算密集型（Compute-Bound）**任務 [2, 3]，模型必須將輸入的 Prompt 一次性全部處理完畢，才能輸出第一個 Token [2]。因此，量化等級對其 TTFT 的影響表現在以下幾個核心維度：

1. **模型容量與記憶體載入開銷**：
   量化等級越低（例如從 FP16 壓縮至 Q8_0，再到 Q4_K_M、Q2_K），模型權重的體積會大幅縮小（以 7B 模型為例，FP16 約需 14GB，而 Q4_K_M 僅需 4.7GB） [4]。這能顯著降低冷啟動時的硬碟與記憶體載入延遲，避免因記憶體不足而迫使系統與虛擬記憶體進行頁面交換（Swap）進而拖慢 TTFT [5]。
2. **CPU 解量化計算（Dequantization）開銷**：
   雖然低量化模型（如 Q4_K_M）減少了必須從記憶體讀取的權重數據總量 [6]，但 CPU 在運算時必須進行即時的「解量化」將其還原為浮點數，這會為 CPU 帶來額外的計算負載 [7]。
   * 近期的 `llama.cpp` 版本針對 x86 CPU（支援 AVX2 平台）優化了低位元量化（如 Q2_0、Q4_0）的計算內核，使這些低位元模型在 CPU 推理下的運行效率顯著提高 [8, 9]。
3. **主流選擇的表現**：
   * **Q4_K_M（推薦「性價比最高」）**：在純 CPU 推理下，Q4_K_M 能將模型體積壓縮至約 30% [10]，且與 FP16 相比，其輸出品質在日常對話中幾乎察覺不到差異 [4]，是能在 CPU 運算資源與記憶體載入時間之間取得最佳平衡的配置 [4, 11]。
   * **中大型模型限制**：若在 CPU-only 平台運行 14B 或 27B 等 dense（密集）中大型模型，即便量化至 Q4 [3]，TTFT 在處理稍長 Prompt 時仍很容易拉長至數秒甚至十秒以上，這是受限於 CPU 向量運算單元能力與系統記憶體頻寬的物理瓶頸 [3, 12]。

---

### 互動式多輪對話的可接受 TTFT 閾值

**首字延遲（TTFT）**是用戶在螢幕前等待系統回應時，「 perceived responsiveness（感知響應速度）」最關鍵的指標 [2, 13]。對於多輪互動式對話（如 IDE 內的 AI 編碼助手、Chatbot 界面），業界對其 TTFT 的可接受閾值定義如下 [13]：

* **極佳（Excellent）：< 300 ms** [13]。在此範圍內，首字回傳幾乎是瞬時的，用戶體驗最流暢、最無斷點。
* **可接受（Acceptable）：300 ms ~ 600 ms** [13]。此區間是用戶能保持自然對話、不會感到中斷的理想閾值，也是多數互動式應用所要求的標準。
* **可察覺延遲（Noticeable Delay）：600 ms ~ 1000 ms** [13]。用戶會開始注意到生成有一瞬間的停頓，但仍能接受。
* **糟糕/體驗中斷（Broken）：> 1000 ms（超過 1 秒）** [13]。一旦首字延遲超過 1 到 2 秒，用戶就會直覺懷疑系統是否出錯、卡死或網路異常 [13]。

> **重要提示**：TTFT 隨 **Prompt 的輸入長度（Input length）** 呈現正比關係 [12, 14]。100 個 token 的 prompt TTFT 可能只有 200ms；但若不進行優化，當 prompt 達到 10K 甚至 100K token 時，即便在極快的伺服器上，預填充也可能需要數秒甚至十秒以上 [12, 15]。

---

### i5-14500T (14核 20緒) 可用的 CPU 加速與 TTFT 優化手段

Intel i5-14500T 屬於第 14 代 Raptor Lake Refresh 架構（採用 6 個 P-Core 與 8 個 E-Core 的混合設計），以下為其核心加速手段與優化建議：

#### 1. 硬體指令集說明：AVX2 與 AVX-512 狀況
* **i5-14500T 並不支援 AVX-512 指令集** [16]。Intel 已在 Alder Lake 與之後的消費級混合架構處理器上，將 AVX-512 指令集熔斷關閉 [16]。
* 幸好，該處理器**完全支援 AVX2（Advanced Vector Extensions 2）指令集** [17]。這是 `llama.cpp` 在 x86 CPU 上加速權重矩陣乘法的最主要硬體基礎 [8, 17]，能直接提供數倍於通用 CPU 計算的運算效能 [8, 17]。

#### 2. 合理配置執行緒與 P-Core 綁定（避開 E-Core 瓶頸）
在 i5-14500T（14 核 20 緒，包含 6P + 8E）上，**盲目將緒數設為最大執行緒數（如 `--threads 20`）通常會導致性能嚴重衰退** [18]。
* **優化建議**：通常應將主要生成執行緒 **`--threads`** 設定為**實體大核（P-Core）的數量（設為 6）** [19, 20]，最多設為 8 [18]。這可避免因慢速 E-Core 參與計算，或過多執行緒所帶來的調度與線程切換開銷（Scheduling overhead） [18]。
* **Prefill 專用執行緒優化**：`llama.cpp` 提供分開的配置引數 **`--threads-batch`**（專用於預填充/批次處理階段） [18]。在 Prompt 長度較長時，可以將此引數設定為較高核心數（如 8 或 12），這能有效加快 Prefill 運算，進一步縮短 TTFT [18]。

#### 3. 採用 Intel 官方 OpenVINO 推理後端與內顯分流
* 相比於通用後端，使用 **Intel OpenVINO** 能深度壓榨該 CPU 的 AVX2 指令集並優化記憶體調度 [21, 22]。
* **iGPU UHD 770 混合分流（Offload）**：雖然 UHD 770 效能有限，但仍可利用 OpenVINO 將 Prefill 的一部分矩陣運算（如 KQV attention 運算）卸載到這顆 UHD 770 內顯上執行 [21, 23]。內顯能提供比 CPU 更好的向量並行計算能力，這對縮短長 Context 下的 TTFT 尤其有幫助 [6]。

#### 4. 啟用「提示快取（Prompt Caching / Prefix Caching）」
在多輪互動式對話中，這是**改善 TTFT 最具決定性的一招** [24]：
* 通過啟用 **`--cache-prompt`**（或在 LlamaCppEx 中配置 `cache_prompt: true`） [23]，模型會直接快取並複用前幾輪對話的 **KV Cache** [23]。當用戶輸入新一輪對話時，系統不需重複計算整段對話歷史（跳過冗餘的預填充） [24]，能將後續對話的 TTFT 直接降至毫秒級，是用戶體驗的「神速救星」 [24]。

#### 5. 啟用 KV Cache 量化
* 在 `llama.cpp` / `Ollama` 中開啟 **`--cache-type-k q8_0` 與 `--cache-type-v q8_0`**（或設定環境變數 `OLLAMA_KV_CACHE_TYPE=q8_0`） [7]。
* 這能將 KV Cache 記憶體佔用直接減半 [24]，降低 CPU 推理過程中反覆讀寫記憶體頻寬的負擔，有效釋放頻寬瓶頸（純 CPU 推理最大的瓶頸通常在記憶體頻寬，而非核心計算本身） [25, 26]。

#### 6. 使用投機解碼（Speculative Decoding / MTP）
* 在 CPU 面臨物理頻寬壁壘（Memory Wall）時，可以使用 Speculative Decoding 技術 [6, 27]。
* 這能使系統加載一個微型草稿模型（Draft Model），並在每一步權重讀取中一次性驗證並確認多個 Token [6, 27]。在 CPU-only 下，這也是繞過系統記憶體頻寬瓶頸、極致提升輸出響應性能的物理級逃生口 [6, 27]。

#### 7. 確保雙通道 DDR5 記憶體頻寬
* 既然記憶體頻寬是 CPU 推理的第一瓶頸 [25, 26]，請務必確認你的 i5-14500T 平台已插滿**雙通道（Dual-Channel）DDR5 記憶體**，這相比單通道能提供物理性翻倍的頻寬加速，會直接投射在 Prefill 階段的 TTFT 提升上 [6, 28]。

---

💡 **想進一步探討？** 
您可以告訴我您目前計畫運行的模型大小（例如 7B、14B 還是 32B 等），我可以協助您估算該模型在您的 i5-14500T (6P大核) 平台與 32GB RAM 下，調整上述優化引數後大概能達到多少的 token 處理速率！

**引用來源：**
- [1] [[Intel 官方] 技術指南 - Xeon 與 OpenVINO 多模態 RAG 加速架構](https://lenovopress.lenovo.com/lp2350-accelerating-multimodal-rag-with-intel-xeon-and-openvino)
- [2] [LLM Inference Speed Explained: TTFT, Throughput, and What Actually Matters - Infercom](https://infercom.ai/blog/llm-inference-speed-explained/)
- [3] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [4] [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [5] [[OmniForge Blog] 技術指南 - 解決本地 LLM 推理緩慢的 llama.cpp 最佳設定指引](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [6] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [7] [[OmniForge Blog] 技術指南 - 解決本地 LLM 推理緩慢的 llama.cpp 最佳設定指引](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [8] [Intel B70: LLama.ccp SYCL vs LLama.cpp OpenVino vs LLM-Scaler : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [9] [Intel B70: LLama.ccp SYCL vs LLama.cpp OpenVino vs LLM-Scaler : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [10] [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [11] [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [12] [LLM Inference Speed Explained: TTFT, Throughput, and What Actually Matters - Infercom](https://infercom.ai/blog/llm-inference-speed-explained/)
- [13] [LLM Inference Speed Explained: TTFT, Throughput, and What Actually Matters - Infercom](https://infercom.ai/blog/llm-inference-speed-explained/)
- [14] [LLM Inference Speed Explained: TTFT, Throughput, and What Actually Matters - Infercom](https://infercom.ai/blog/llm-inference-speed-explained/)
- [15] [LLM Inference Speed Explained: TTFT, Throughput, and What Actually Matters - Infercom](https://infercom.ai/blog/llm-inference-speed-explained/)
- [16] [LocalAI OpenVINO inference on Intel iGPU UHD 770 of Starling LM Beta with int8 quantization. Fully offloaded. No CPUs nor dGPUs were harmed in the making of this film. - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1c0j338/localai_openvino_inference_on_intel_igpu_uhd_770/)
- [17] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [18] [What should i change to optimize local hosted AI - Beginners - Hugging Face Forums](https://discuss.huggingface.co/t/what-should-i-change-to-optimize-local-hosted-ai/176339)
- [19] [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [20] [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [21] [[Reddit 社群] 技術討論 - LLM 推理之 KV Cache 卸載至系統記憶體可行性](https://www.reddit.com/r/LocalLLaMA/comments/1txpqru/maybe_kv_cache_offload_to_ram_isnt_bad/)
- [22] [[SYCL][Intel GPU] Long Term Features & Issues Tracking · ggml-org llama.cpp · Discussion #5277 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/5277)
- [23] [[Hexdocs 官方] 技術指南 - LlamaCppEx 推理加速與參數調整優化指南](https://hexdocs.pm/llama_cpp_ex/performance.html)
- [24] [[Hexdocs 官方] 技術指南 - LlamaCppEx 推理加速與參數調整優化指南](https://hexdocs.pm/llama_cpp_ex/performance.html)
- [25] [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [26] [[Serverman 部落格] 排障手冊 - Ollama 推理速度緩慢的逐步排查與優化指引](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [27] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [28] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)

### 3. Local LLM 與 GitHub Copilot（App / VS Code extension / CLI）的整合方式？是否支援自訂 model endpoint？OpenAI-compatible API 串接可行性？

### VS Code Extension (編輯器插件) 的整合方式與自訂端點

自 **VS Code v1.99** 起，官方推出了 **Bring Your Own Key (BYOK) Preview** 功能，讓 GitHub Copilot 能夠直接連接本地地端的 **Ollama** 服務 [1]。

*   **整合與設定步驟**：
    1.  於本地安裝 Ollama，執行 `ollama serve` 啟動服務，並透過 `ollama pull` 下載適合代碼補全或對話的模型（例如 `phi4-mini` 或 `codellama`）[2-4]。
    2.  打開 VS Code，在 GitHub Copilot 的對話視窗下方，點選 **Manage Models…** [2, 4, 5]。
    3.  勾選 **Ollama**，系統便會自動偵測並列出本地端可用的 Ollama 模型 [2, 4, 5]。
    4.  回到 Copilot 對話框，即可自由切換至下載的地端模型，此時 AI 的運算與原始碼資料流將完全保留在本地或內網中，不會流向外部雲端 [3, 4]。
*   **自訂遠端端點**：如果企業或團隊擁有統一的 GPU 伺服器，可以透過 VS Code 設定搜尋 `Ollama Endpoint`（位於 `GitHub Copilot Chat -> Preview -> Ollama Endpoint` 中）來指定自訂的 Ollama 遠端伺服器 URL [3, 4]。
*   **局限**：目前 VS Code Extension 的本地端整合仍有安全過濾或功能限制，且**必須登入 GitHub 帳號並驗證 Copilot 訂閱身份**才能正常啟用選單與面板功能 [6]。

---

### GitHub Copilot CLI 的整合與離線/地端配置 (BYOK)

**GitHub Copilot CLI** 具備更靈活的 **BYOK (Bring Your Own Key)** 原生整合，允許開發者透過環境變數，直接覆寫其後端預設的雲端模型 [7, 8]：

*   **關鍵環境變數設定** [9]：
    *   **`COPILOT_PROVIDER_BASE_URL`**：設定自訂模型供應商的端點 URL（例如本地 Ollama 設為 `http://localhost:11434`，LM Studio 設為 `http://localhost:1234/v1`）[7, 10, 11]。
    *   **`COPILOT_PROVIDER_TYPE`**：設定為 **`openai`**（預設）、`azure` 或 `anthropic` [12, 13]。
    *   **`COPILOT_MODEL`**：填入欲呼叫的地端模型識別名稱（如 `llama3.2` 或 `gemma-3-1b`）[7, 10, 11]。
    *   **`COPILOT_PROVIDER_API_KEY`**：若使用本地端免驗證服務（如 Ollama）則不需填寫 [9, 14]；若使用遠端 OpenAI-compatible API，則填入對應密鑰。
    *   **`COPILOT_OFFLINE`**：設定為 **`true`**，可以開啟**離線模式 (Offline Mode)**，這能強制防止 CLI 回傳或聯繫 GitHub 伺服器，非常適合在完全物理隔離（Air-Gapped）的環境中開發 [7, 15-17]。
*   **地端模型要求** [14]：
    *   模型必須支援 **Tool Calling (工具/函數呼叫)** 與 **Streaming (串流)**，否則 CLI 運作時會回傳錯誤。
    *   為了在複雜的 CLI 代碼生成與多輪對話中取得良好效果，建議使用**上下文窗口 (Context Window) 至少在 128k tokens 以上**的模型 [14]。

---

### OpenAI-compatible API 串接可行性與代理方案

**OpenAI-compatible API 串接的可行性極高**。除了 CLI 預設的 `openai` 供應商類型外 [12, 13]，目前主要的進階代理及整合手段包含：

1.  **LiteLLM Proxy 統一轉接**：
    *   如果希望在 VS Code 中使用更豐富的自訂模型（如 Anthropic Claude、地端自建的 vLLM 等），可使用本機架設的 LiteLLM Proxy [18, 19]。
    *   設定時，只需在 VS Code 的 `settings.json` 中配置進階代理複寫：
        ```json
        {
          "github.copilot.advanced": {
            "debug.overrideProxyUrl": "http://localhost:4000",
            "debug.testOverrideProxyUrl": "http://localhost:4000"
          }
        }
        ``` [20]
    *   這可以將 Copilot 的請求全部導向本地 LiteLLM（預設 port 4000），再由 LiteLLM 透過 unified 介面轉發、進行負載平衡、額度控制與日誌追蹤 [19, 21, 22]。
2.  **VS Code 擴充套件「Copilot Custom Provider」**：
    *   這是一個利用 VS Code 的公用 `LanguageModelChatProvider` API 所開發的套件 [23]。
    *   它能在 Copilot Chat 的模型選擇器中加入一個自訂的 Custom OpenAI Responses 管道，讓你配置自訂的 `baseUrl` 與 API 金鑰，流暢地對接符合 OpenAI Responses API 標準的地端或雲端服務 [23-25]。
3.  **極簡的開源替代方案 (Continue.dev)**：
    *   如果需要一個完全零成本、無登入驗證、且完美相容 OpenAI-compatible API (如 Ollama, LM Studio, LocalAI) 的 Copilot 級代碼助手，**Continue.dev** 是目前社群公認最優秀的開源方案 [26, 27]。
    *   它提供完整的 Tab 自動補全（需 FIM 格式模型）與 Chat 側邊欄，所有設定都由本機的 YAML/JSON 檔完全掌控 [26, 28-30]，是確保完全隱私、不受雲端限制與連線卡關的首選 fallback [26, 31, 32]。

💻 想要我為你詳細寫一份在 Windows 或 Linux 下使用 Ollama 串接 Copilot CLI，並啟用離線模式的完整環境變數配置腳本嗎？

**引用來源：**
- [1] [GitHub Copilot 串接Ollama：地端自託管LLM，守護企業程式碼資安的AI 補全解決方案](https://www.gss.com.tw/blog/github-copilot-ollama-local-llm)
- [2] [GitHub Copilot 串接Ollama：地端自託管LLM，守護企業程式碼資安的AI 補全解決方案](https://www.gss.com.tw/blog/github-copilot-ollama-local-llm)
- [3] [GitHub Copilot 串接Ollama：地端自託管LLM，守護企業程式碼資安的AI 補全解決方案](https://www.gss.com.tw/blog/github-copilot-ollama-local-llm)
- [4] [[部落格 專欄] 教學指南 - 企業專屬 Ollama 與 Copilot 本地對接安全性部署](https://www.gss.com.tw/blog/github-copilot-ollama-local-llm)
- [5] [[部落格 專欄] 教學指南 - 企業專屬 Ollama 與 Copilot 本地對接安全性部署](https://www.gss.com.tw/blog/github-copilot-ollama-local-llm)
- [6] [Add ability to use local LLMs in Copilot · community · Discussion #190067 - GitHub](https://github.com/orgs/community/discussions/190067)
- [7] [Using GitHub Copilot CLI with Local Models (LM Studio) - DEV Community](https://dev.to/playfulprogramming/using-github-copilot-cli-with-local-models-lm-studio-5e3b)
- [8] [[部落格 專欄] 技術指南 - 於 GitHub Copilot CLI 串接自託管 LLM 的具體步驟](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [9] [[部落格 專欄] 技術指南 - 於 GitHub Copilot CLI 串接自託管 LLM 的具體步驟](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [10] [[部落格 專欄] 技術指南 - 於 GitHub Copilot CLI 串接自託管 LLM 的具體步驟](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [11] [Using your own LLM models in GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [12] [[部落格 專欄] 技術指南 - 於 GitHub Copilot CLI 串接自託管 LLM 的具體步驟](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [13] [Using your own LLM models in GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [14] [Using your own LLM models in GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [15] [[DEV Community 專欄] 技術指南 - Copilot CLI 本地串接 LM Studio 實戰指南](https://dev.to/playfulprogramming/using-github-copilot-cli-with-local-models-lm-studio-5e3b)
- [16] [[部落格 專欄] 技術指南 - 於 GitHub Copilot CLI 串接自託管 LLM 的具體步驟](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [17] [Using your own LLM models in GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [18] [[LiteLLM 官方] 開源專案 - LiteLLM 代理與 GitHub Copilot 自訂端點對接](https://docs.litellm.ai/docs/tutorials/github_copilot_integration)
- [19] [[LiteLLM 官方] 開源專案 - LiteLLM 代理與 GitHub Copilot 自訂端點對接](https://docs.litellm.ai/docs/tutorials/github_copilot_integration)
- [20] [[LiteLLM 官方] 開源專案 - LiteLLM 代理與 GitHub Copilot 自訂端點對接](https://docs.litellm.ai/docs/tutorials/github_copilot_integration)
- [21] [GitHub Copilot - LiteLLM](https://docs.litellm.ai/docs/tutorials/github_copilot_integration)
- [22] [[VS Marketplace 官方] 產品規格 - Copilot 擴充套件自訂 API 供應商介面](https://marketplace.visualstudio.com/items?itemName=tzraeq.vscode-copilot-custom-provider)
- [23] [Copilot Custom Provider - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=tzraeq.vscode-copilot-custom-provider)
- [24] [Copilot Custom Provider - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=tzraeq.vscode-copilot-custom-provider)
- [25] [10 Best GitHub Copilot Alternatives to Run Locally (2026) | Free & Private | LocalAlternative](https://www.localalternative.io/blog/github-copilot-local-alternatives)
- [26] [Add ability to use local LLMs in Copilot · community · Discussion #190067 - GitHub](https://github.com/orgs/community/discussions/190067)
- [27] [[Pristren 部落格] 比較分析 - Continue 地端整合功能與實用性測評](https://pristren.com/blog/continue-dev-open-source-copilot/)
- [28] [[Pristren 部落格] 比較分析 - Continue 地端整合功能與實用性測評](https://pristren.com/blog/continue-dev-open-source-copilot/)
- [29] [How to setup qwen coder 2.5 LLM for local dev on Windows in four steps](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [30] [Add ability to use local LLMs in Copilot · community · Discussion #190067 - GitHub](https://github.com/orgs/community/discussions/190067)
- [31] [[Pristren 部落格] 比較分析 - Continue 地端整合功能與實用性測評](https://pristren.com/blog/continue-dev-open-source-copilot/)

### 4. 除了 Ollama 之外，Windows 11 上還有哪些適合的方案（llama.cpp、LM Studio、Jan、GPT4All 等）？各自在 TTFT 與易用性上的差異？

在 Windows 11 系統，特別是針對您目前 **Intel UHD 770 內顯（擁有 32 EUs）與 32GB 記憶體（共用系統記憶體 UMA 架構）**的硬體環境下 [1-5]，除了 Ollama 之外，還有幾種非常適合的主流 local LLM 方案。

由於內顯的運算單元（EUs）數量較少，且需要與系統共用記憶體頻寬，選擇合適的推理引擎和硬體加速後端，會對 **TTFT（Time to First Token，首字延遲/ Prompt 處理速度）** 以及**易用性**產生極大影響 [4, 6-8]。以下為您深入對比各方案的差異：

---

### 一、 Windows 11 本地 LLM 方案詳細對比

#### 1. LM Studio
*   **易用性：極高（最適合模型探索）** [9, 10]
    提供極為精美的圖形化介面（GUI），用戶可以直接在軟體內搜尋、一鍵下載 Hug開 Hugging Face 上的 GGUF 模型，並自動啟用本地伺服器，提供 OpenAI 相容的 API 接口（預設為 `http://localhost:1234/v1`）[11, 12]。
*   **TTFT 與效能：中等**
    雖然底層基於 llama.cpp，但因為 GUI 外殼封裝以及開箱即用的預設參數，在 Intel 平台上的硬體優化程度較低。**在相同設定下，LM Studio 的推理速度經常比純 llama.cpp 慢 2 到 3 倍** [13-15]。在 UHD 770 上，它常會因為無法良好偵測或驅動 OpenVINO 性能而退回 CPU 運算，導致處理長 prompt 時的 TTFT 明顯變慢。

#### 2. Jan
*   **易用性：極高（最適合新手日常對話）** [9, 10]
    Jan 是一款開源、本地優先且介面極簡的 ChatGPT 替代品 [9, 10]。同樣具備 GUI，下載模型與載入對話的流程非常直覺，對硬體偵測的自動化做得很好。
*   **TTFT 與效能：中下**
    與 LM Studio 類似，Jan 的優勢在於精緻的 UI 體驗 [9, 10]，但由於缺乏對 Intel 專屬推理後端（如 OpenVINO）的深度客製化支援，在 UHD 770 上的 TTFT 表現較普通。若無法順利啟用 Vulkan 加速，系統會主要依賴 CPU（AVX2 指令集）進行 Prefill，在處理長 context 時會有明顯的卡頓感 [16, 17]。

#### 3. GPT4All
*   **易用性：高** [9, 10]
    由 Nomic AI 開發，同樣提供安裝即可用的 GUI [9, 10]。
*   **TTFT 與效能：普通（專門針對低配/純 CPU 優化）** [9, 10]
    GPT4All 的核心特色是針對**低階硬體與純 CPU（CPU-Only）環境進行了極度優化** [9, 10]。如果您的 UHD 770 驅動在其他平台遇到相容性問題，GPT4All 依靠 CPU 運算（AVX/AVX2）仍能提供極為穩定的解碼表現 [16, 17]。然而，因為它不強求調用 GPU 推理加速，其 **TTFT（Prompt Processing）上限較低**，當輸入文字較長時，等待模型反應的時間會拉長 [16-18]。

#### 4. 原生 llama.cpp（命令行/ `llama-server.exe`）
*   **易用性：極低（陡峭的學習曲線）** [17-19]
    沒有預設的 GUI。您需要自行到 GitHub 下載對應編譯版本的 zip 壓縮包，手動下載 GGUF 模型，並在 Windows PowerShell 中輸入複雜的指令與參數（例如 `--flash-attn`, `-ngl`, `-b` 等）來啟動伺服器 [20-25]。
*   **TTFT 與效能：無庸置疑的最強（硬體極限速度）** [13-15]
    由於沒有 GUI 額外的資源消耗，且您可以自由微調所有參數（如 Flash Attention 記憶體配置、連續批次處理大小 `-b`、KV Cache 量化等），**效能和 TTFT 表現都是所有方案中最頂尖的** [23-27]。更重要的是，它能讓您自由選擇最適合 Intel 硬體的加速後端。

---

### 二、 UHD 770 內顯下的關鍵效能瓶頸：後端選擇（Backend）

若您選擇使用原生 **llama.cpp**，在 Windows 11 下針對 Intel GPU 主要有以下三種硬體加速後端，這會直接決定您的 TTFT 體驗：

1.  **OpenVINO 後端（TTFT 的絕對王者）** [28, 29]
    *   **效能：** OpenVINO 是 Intel 官方主力開發的推理加速引擎，**在 Prompt 處理（Prefill/TTFT）速度上徹底碾壓 SYCL 與 Vulkan** [6-8]。
    *   **數據：** 根據官方與社群在 7B 模型（Q4_0 量化）的測試，**OpenVINO 後端能跑出高達 ~1497 t/s 的 Prompt 處理速度**，而同硬體下 Vulkan 僅有 ~259 t/s，SYCL 則僅有 ~220 t/s [30-32]。（在最新 Intel 處理器上，OpenVINO 的 Prefill 還能衝上將近 3000 t/s [33-35]）。
    *   **結論：** 如果您需要處理長文本、程式碼補全（需要讀取大量上下文），**llama.cpp + OpenVINO 是唯一能提供秒級 TTFT 回應的方案**。
2.  **Vulkan 後端（最推薦的折衷方案：好設定、效能穩）** [36-38]
    *   **效能：** 雖然 Prompt 處理（TTFT）速度落後於 OpenVINO [33-35]，但 Token 生成速度（TG/Decode）非常優秀且穩定 [36-38]。
    *   **易用性：** 這是 Windows 11 用戶最省心的硬體加速方式。llama.cpp 官方釋出的 Windows 預編譯版本中已整合了 Vulkan 執行環境 [39-42]。您**不需要安裝任何額外的 SDK**，解壓縮即可在 2 分鐘內直接調用內顯進行運算 [36-38, 40, 42]。
3.  **SYCL 後端（極高折騰成本，效能不穩定）** [17, 19, 36, 37, 43]
    *   要在 Windows 上編譯與執行 SYCL，您必須下載高達 3GB 的 Intel oneAPI SDK 工具包以及 Visual Studio C++ 編譯環境，過程極為痛苦 [19, 43, 44]。且在 consumer/舊型內顯上，許多編譯優化尚未到位，甚至在許多 release 版本中出現效能低下或不穩定的反饋，不建議在 UHD 770 上強行折騰 [45-47]。

---

### 三、 綜合評估與實選建議

| 方案 | 易用性評級 | 核心特色 | 在 UHD 770 上的 TTFT 表現 | 適合使用場景 |
| :--- | :--- | :--- | :--- | :--- |
| **LM Studio** [9, 10] | ⭐⭐⭐⭐⭐ | 精美 GUI、OpenAI 相容 API、一鍵下模型 [9-11] | 🐌 **慢** (通常預設 CPU 運算或無優化 Vulkan) | 純對話、探索測試各種新模型 [9, 10] |
| **Jan** [9, 10] | ⭐⭐⭐⭐⭐ | 本地優先、介面如 ChatGPT、開源乾淨 [9, 10] | 🐌 **慢** (易退回 CPU) | 日常助理、隱私對話 [9, 10] |
| **GPT4All** [9, 10] | ⭐⭐⭐⭐ | 針對中低配 CPU 優化、運作極度穩定 [9, 10] | ⏳ **中下** (穩定但純依賴 CPU AVX 限制了上限) | 舊電腦、無獨立顯示卡且驅動難搞的系統 [9, 10] |
| **原生 llama.cpp (OpenVINO 後端)** [28, 29] | ⭐ | 命令行運作、全手動配置參數 [17-19] | ⚡ **極快** (~1400+ t/s 的超速 prompt 吞吐) [30-32] | 開發整合、IDE 程式碼補全（最在乎 TTFT 延遲） |
| **原生 llama.cpp (Vulkan 後端)** [36-38] | ⭐⭐ | 預編譯 zip 開箱即用，免裝 3GB SDK [36-38, 40, 42] | 🚄 **中等** (TG 解碼速度優秀，Prefill 較慢) [33-38] | 想省去設定麻煩又想體驗 GPU 加速的玩家 [36-38] |

💡 **針對您的 UHD 770 (32 EUs) + 32GB RAM 環境的最終實踐建議：**
由於 UHD 770 的圖形運算單元較小 [1-3]，在模型選擇上請堅守 **7B 到 14B 之間且經過 Q4_K_M 等中度量化的模型**（如 `Qwen2.5-Coder-7B-Instruct-Q4_K_M` 或 14B 版本）[21, 22, 48-50]，這樣才能在 32GB 記憶體共享頻寬下得到兼顧品質與輸出流暢度的本地體驗 [4, 21, 49, 50]。

如果您希望深入了解如何快速利用 PowerShell 命令列載入預編譯的 llama.cpp (Vulkan) 來測試模型速度，或者想知道如何將其接口串接到 VS Code 的 Continue 插件中，請隨時告訴我！

**引用來源：**
- [1] [[Reddit 社群] 案例研究 - UHD 770 內顯以 OpenVINO 與 int8 量化完全卸載推理實測](https://www.reddit.com/r/LocalLLaMA/comments/1c0j338/localai_openvino_inference_on_intel_igpu_uhd_770/)
- [2] [LocalAI OpenVINO inference on Intel iGPU UHD 770 of Starling LM Beta with int8 quantization. Fully offloaded. No CPUs nor dGPUs were harmed in the making of this film. - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1c0j338/localai_openvino_inference_on_intel_igpu_uhd_770/)
- [3] [LocalAI OpenVINO inference on Intel iGPU UHD 770 of Starling LM Beta with int8 quantization. Fully offloaded. No CPUs nor dGPUs were harmed in the making of this film. - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1c0j338/localai_openvino_inference_on_intel_igpu_uhd_770/)
- [4] [[Intel 官方] 技術指南 - 基於 SYCL 在 Intel GPU 上運行 llama.cpp 部署指引](https://www.intel.com/content/www/us/en/developer/articles/technical/run-llms-on-gpus-using-llama-cpp.html)
- [5] [[llama.cpp GitHub] 技術論壇 - Intel SYCL 後端架構效能回報與優化議題](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [6] [Intel B70: LLama.ccp SYCL vs LLama.cpp OpenVino vs LLM-Scaler : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [7] [[Reddit 社群] 比較分析 - Intel GPU 平台上 SYCL、OpenVINO 與 Scaler 效能對比](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [8] [Intel B70: LLama.ccp SYCL vs LLama.cpp OpenVino vs LLM-Scaler : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [9] [[LocalAlternative 媒體] 比較分析 - 本地 AI 補全工具與 Copilot 替代方案](https://www.localalternative.io/blog/github-copilot-local-alternatives)
- [10] [10 Best GitHub Copilot Alternatives to Run Locally (2026) | Free & Private | LocalAlternative](https://www.localalternative.io/blog/github-copilot-local-alternatives)
- [11] [[DEV Community 專欄] 技術指南 - Copilot CLI 本地串接 LM Studio 實戰指南](https://dev.to/playfulprogramming/using-github-copilot-cli-with-local-models-lm-studio-5e3b)
- [12] [Using GitHub Copilot CLI with Local Models (LM Studio) - DEV Community](https://dev.to/playfulprogramming/using-github-copilot-cli-with-local-models-lm-studio-5e3b)
- [13] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [14] [[Reddit 社群] 技術討論 - Intel 平台 speculative decoding 投機解碼效能翻倍實測](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [15] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [16] [[Reddit 社群] 討論問答 - Arrow Lake 與 iGPU 在 llama.cpp 推理效能反饋](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [17] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [18] [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [19] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [20] [How to setup qwen coder 2.5 LLM for local dev on Windows in four steps](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [21] [[部落格 專欄] 教學指南 - Windows 11 下四步驟極簡部署 Qwen2.5-Coder](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [22] [How to setup qwen coder 2.5 LLM for local dev on Windows in four steps](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [23] [Why Your Local LLM Is Slow — llama.cpp Config Guide | OmniForge Blog](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [24] [[OmniForge Blog] 技術指南 - 解決本地 LLM 推理緩慢的 llama.cpp 最佳設定指引](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [25] [Why Your Local LLM Is Slow — llama.cpp Config Guide | OmniForge Blog](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [26] [[Reddit 社群] 比較分析 - Intel GPU 平台上 SYCL、OpenVINO 與 Scaler 效能對比](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [27] [[Reddit 社群] 技術討論 - Intel 平台 speculative decoding 投機解碼效能翻倍實測](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [28] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [29] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [30] [[llama.cpp GitHub] 技術論壇 - Intel SYCL 後端架構效能回報與優化議題](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [31] [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [32] [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [33] [[llama.cpp GitHub] 技術論壇 - Intel SYCL 後端架構效能回報與優化議題](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [34] [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [35] [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [36] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [37] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [38] [[Reddit 社群] 技術討論 - Intel 平台 speculative decoding 投機解碼效能翻倍實測](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [39] [llama.cpp for SYCL - benchmarks - GitHub](https://github.com/Liquid4All/benchmarks-llama.cpp/blob/benchmarks/docs/backend/SYCL.md)
- [40] [llama.cpp for SYCL - benchmarks - GitHub](https://github.com/Liquid4All/benchmarks-llama.cpp/blob/benchmarks/docs/backend/SYCL.md)
- [41] [llama.cpp for SYCL - benchmarks - GitHub](https://github.com/Liquid4All/benchmarks-llama.cpp/blob/benchmarks/docs/backend/SYCL.md)
- [42] [llama.cpp for SYCL - benchmarks - GitHub](https://github.com/Liquid4All/benchmarks-llama.cpp/blob/benchmarks/docs/backend/SYCL.md)
- [43] [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [44] [[Reddit 社群] 技術討論 - Intel 平台 speculative decoding 投機解碼效能翻倍實測](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [45] [[Reddit 社群] 效能評測 - Arc 顯示卡在 SYCL/Vulkan 推理架喚基準測試](https://www.reddit.com/r/IntelArc/comments/1tr9397/intel_arc_pro_b70_32gb_for_local_llms_llamacpp/)
- [46] [Intel Arc Pro B70 (32GB) for Local LLMs: llama.cpp (SYCL/Vulkan), vLLM (Intel LLM Scaler) Benchmarks : r/IntelArc - Reddit](https://www.reddit.com/r/IntelArc/comments/1tr9397/intel_arc_pro_b70_32gb_for_local_llms_llamacpp/)
- [47] [Intel Arc Pro B70 (32GB) for Local LLMs: llama.cpp (SYCL/Vulkan), vLLM (Intel LLM Scaler) Benchmarks : r/IntelArc - Reddit](https://www.reddit.com/r/IntelArc/comments/1tr9397/intel_arc_pro_b70_32gb_for_local_llms_llamacpp/)
- [48] [Qwen2.5_Coder_(14B)-Conversational.ipynb - Google Colab](https://colab.research.google.com/github/unslothai/notebooks/blob/main/nb/Qwen2.5_Coder_(14B)-Conversational.ipynb)
- [49] [[Serverman 部落格] 排障手冊 - Ollama 推理速度緩慢的逐步排查與優化指引](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [50] [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)

## 已移除的來源（品質過濾）

- Qwen2.5-Coder 14B Instruct API & Playground - Fireworks AI — 該來源為 Fireworks AI 的商業 API 服務與 Playground 產品推廣頁面，主要提供定價、託管服務與企業解決方案等行銷資訊，缺乏與 Local LLM 本地部署、量化或本機整合相關的具體技術實作細節。
- Run Qwen2.5-Coder-14B Instruct API (Easy Deployment & Flat-Rate Pricing) - Featherless — 該來源為 Featherless AI 的雲端 API 託管平台行銷與定價頁面，其內容聚焦於 SaaS 平台的付費方案、專用 GPU 成本計算及與競爭對手的價格對比，不包含任何本地運行、量化策略或開發工具整合的實質技術細節。

## 已移除的來源（蒸餾必要性）

- Accelerating Multimodal RAG with Intel Xeon and OpenVINO: When Vision Meets Language — 此來源主要探討伺服器級處理器（Intel Xeon）與多模態 RAG（視覺與語言模型結合）的加速方案。本次研究核心為消費級硬體（i5-14500T + UHD 770 內顯）在 Windows 11 下的本地 LLM 部署與 Copilot 整合，該來源之硬體架構與應用場景皆不相符，對回答上述四個問題無實質貢獻。
- Intel iGPU Using Both i915 and xe Kernel Modules, Is This an Issue? How to Resolve? — 此來源為 Linux 核心驅動模組（i915 與 xe）衝突的排障討論。本次研究明確限定於 Windows 11 環境，作業系統與驅動管理機制完全不同，此 Linux 驅動設定之內容對回答任何一題皆無幫助。
- Linux users with Intel graphics can now worry less about RAM restrictions, thanks to new patch - Reddit — 此來源討論的是 Linux 核心針對 Intel 顯示晶片記憶體限制的修正修補程式（patch）。由於研究目標環境為 Windows 11，其系統記憶體與共用顯示記憶體分配機制與 Linux 完全不同，因此該 Linux 專屬資訊無參考價值。
- Xe – Merge Acceptance Plan — The Linux Kernel documentation — 此來源為 Linux 核心官方文件中關於 Xe 驅動程式併入主線的技術審查與計畫文件。內容偏向作業系統核心開發規範，與 Windows 本地端運行 LLM、量化選擇、TTFT 優化或編輯器整合完全無關。
- Qwen2.5-Coder 14B Instruct API & Playground - Fireworks AI — 此來源為 Fireworks AI 雲端託管 API 與線上 Playground 產品的介紹與收費頁面。本次研究重點在於「地端自託管（Ollama, llama.cpp 等）」與「Windows 11 本地硬體限制（32GB RAM）」，雲端付費 API 服務無法提供任何本地推理效能、記憶體量化策略或 CPU 加速的實質技術細節。
- Run Qwen2.5-Coder-14B Instruct API (Easy Deployment & Flat-Rate Pricing) - Featherless — 該來源屬於 Featherless AI 雲端 API 託管平台的產品宣傳與定價方案，與在 Windows 本地端（UHD 770 + 32GB RAM）進行模型量化部署、TTFT 評估、硬體加速及 Copilot 本地端串接等核心技術議題完全無關。
- Qwen/Qwen2.5-Coder-7B-Instruct · 14B in the future? - Hugging Face — 此來源為 2024 年底探討 Qwen2.5-Coder 未來是否會推出 14B 版本的社群閒聊。目前 14B 模型早已正式發布，且此討論不包含任何技術實作、量化策略、硬體加速或編輯器整合的實質數據與配置指導。

## 參考來源清單

- [10 Best GitHub Copilot Alternatives to Run Locally (2026) | Free & Private | LocalAlternative](https://www.localalternative.io/blog/github-copilot-local-alternatives)
- [Accelerating Multimodal RAG with Intel Xeon and OpenVINO: When Vision Meets Language](https://lenovopress.lenovo.com/lp2350-accelerating-multimodal-rag-with-intel-xeon-and-openvino)
- [Add ability to use local LLMs in Copilot · community · Discussion #190067 - GitHub](https://github.com/orgs/community/discussions/190067)
- [Allocating RAM for GPU performance on self hosted LLM systems with integrated System & GPU RAM - PatrickMcCanna.net](https://patrickmccanna.net/allocating-ram-for-gpu-performance-on-self-hosted-llm-systems-with-integrated-system-gpu-ram/)
- [Allocating RAM for GPU performance on self hosted LLM systems with integrated System & GPU RAM - PatrickMcCanna.net](https://patrickmccanna.net/allocating-ram-for-gpu-performance-on-self-hosted-llm-systems-with-integrated-system-gpu-ram/)
- [Autocomplete Role in Continue Models](https://docs.continue.dev/customize/model-roles/autocomplete)
- [Autocomplete Role in Continue Models](https://docs.continue.dev/customize/model-roles/autocomplete)
- [Best Local LLMs for Continue.dev (2026 Tier List) - ModelFit](https://modelfit.io/tools/continue-dev/)
- [Best Local LLMs for Continue.dev (2026 Tier List) - ModelFit](https://modelfit.io/tools/continue-dev/)
- [Continue Autocomplete Setup and Configuration Guide](https://docs.continue.dev/customize/deep-dives/autocomplete)
- [Continue Autocomplete Setup and Configuration Guide](https://docs.continue.dev/customize/deep-dives/autocomplete)
- [Continue.dev Review 2026: Is the Open Source Copilot Worth It? - Pristren](https://pristren.com/blog/continue-dev-open-source-copilot/)
- [Continue.dev Review 2026: Is the Open Source Copilot Worth It? - Pristren](https://pristren.com/blog/continue-dev-open-source-copilot/)
- [Copilot Custom Provider - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=tzraeq.vscode-copilot-custom-provider)
- [FAQ - Ollama documentation](https://docs.ollama.com/faq)
- [FAQ - Ollama documentation](https://docs.ollama.com/faq)
- [For Intel, there's DVMT that can be set to different values; with setup_var I ca... | Hacker News](https://news.ycombinator.com/item?id=37082906)
- [For Intel, there's DVMT that can be set to different values; with setup_var I ca... | Hacker News](https://news.ycombinator.com/item?id=37082906)
- [Frequently Asked Questions for Intel® Graphics Memory on Windows® 10](https://www.intel.com/content/www/us/en/support/articles/000020962/graphics.html)
- [Frequently Asked Questions for Intel® Graphics Memory on Windows® 10](https://www.intel.com/content/www/us/en/support/articles/000020962/graphics.html)
- [Getting started with Qwen2.5-14B - SecondState.io](https://www.secondstate.io/articles/qwen25/)
- [Getting started with Qwen2.5-14B - SecondState.io](https://www.secondstate.io/articles/qwen25/)
- [GitHub - jjleng/copilot-proxy: Make GH Copilot extension work with any LLM models](https://github.com/jjleng/copilot-proxy)
- [GitHub Copilot - LiteLLM](https://docs.litellm.ai/docs/tutorials/github_copilot_integration)
- [GitHub Copilot with Ollama: Run Local AI Models in VS Code (Offline & Free) - YouTube](https://www.youtube.com/watch?v=SYmOVP9Z-NU)
- [GitHub Copilot 串接Ollama：地端自託管LLM，守護企業程式碼資安的AI 補全解決方案](https://www.gss.com.tw/blog/github-copilot-ollama-local-llm)
- [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [Has anyone gotten Llama.cpp (or other) working using Intel iGPU (arrowlake) where it actually improves anything? - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [How to Adjust Dedicated Video Memory for Intel Graphics](https://www.intel.com/content/www/us/en/support/articles/000041253/graphics.html)
- [How to Adjust Dedicated Video Memory for Intel Graphics](https://www.intel.com/content/www/us/en/support/articles/000041253/graphics.html)
- [How to setup qwen coder 2.5 LLM for local dev on Windows in four steps](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [How to setup qwen coder 2.5 LLM for local dev on Windows in four steps](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [Intel Arc Pro B70 (32GB) for Local LLMs: llama.cpp (SYCL/Vulkan), vLLM (Intel LLM Scaler) Benchmarks : r/IntelArc - Reddit](https://www.reddit.com/r/IntelArc/comments/1tr9397/intel_arc_pro_b70_32gb_for_local_llms_llamacpp/)
- [Intel Arc Pro B70 (32GB) for Local LLMs: llama.cpp (SYCL/Vulkan), vLLM (Intel LLM Scaler) Benchmarks : r/IntelArc - Reddit](https://www.reddit.com/r/IntelArc/comments/1tr9397/intel_arc_pro_b70_32gb_for_local_llms_llamacpp/)
- [Intel B70: LLama.ccp SYCL vs LLama.cpp OpenVino vs LLM-Scaler : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [Intel B70: LLama.ccp SYCL vs LLama.cpp OpenVino vs LLM-Scaler : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [Intel iGPU Using Both i915 and xe Kernel Modules, Is This an Issue? How to Resolve?](https://discourse.ubuntu.com/t/intel-igpu-using-both-i915-and-xe-kernel-modules-is-this-an-issue-how-to-resolve/77626)
- [Intel's Latest Drivers Let's Users Allocate Up To 93% of System Memory To Arc iGPUs For Wider AI LLM Support - Wccftech](https://wccftech.com/intel-drivers-lets-users-allocate-93-percent-system-memory-to-arc-igpus-wider-ai-llm-support/)
- [Intel's Latest Drivers Let's Users Allocate Up To 93% of System Memory To Arc iGPUs For Wider AI LLM Support - Wccftech](https://wccftech.com/intel-drivers-lets-users-allocate-93-percent-system-memory-to-arc-igpus-wider-ai-llm-support/)
- [LLM Inference Speed Explained: TTFT, Throughput, and What Actually Matters - Infercom](https://infercom.ai/blog/llm-inference-speed-explained/)
- [LLM Inference Speed Explained: TTFT, Throughput, and What Actually Matters - Infercom](https://infercom.ai/blog/llm-inference-speed-explained/)
- [Linux users with Intel graphics can now worry less about RAM restrictions, thanks to new patch - Reddit](https://www.reddit.com/r/linux_gaming/comments/1jvyyx1/linux_users_with_intel_graphics_can_now_worry/)
- [LocalAI OpenVINO inference on Intel iGPU UHD 770 of Starling LM Beta with int8 quantization. Fully offloaded. No CPUs nor dGPUs were harmed in the making of this film. - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1c0j338/localai_openvino_inference_on_intel_igpu_uhd_770/)
- [LocalAI OpenVINO inference on Intel iGPU UHD 770 of Starling LM Beta with int8 quantization. Fully offloaded. No CPUs nor dGPUs were harmed in the making of this film. - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1c0j338/localai_openvino_inference_on_intel_igpu_uhd_770/)
- [Maybe KV cache offload to RAM isn't bad : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1txpqru/maybe_kv_cache_offload_to_ram_isnt_bad/)
- [Maybe KV cache offload to RAM isn't bad : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1txpqru/maybe_kv_cache_offload_to_ram_isnt_bad/)
- [Models | Continue Docs](https://docs.continue.dev/customize/models)
- [Models | Continue Docs](https://docs.continue.dev/customize/models)
- [Ollama + qwen2.5-coder:14b for local development - Reddit](https://www.reddit.com/r/ollama/comments/1s2j9ct/ollama_qwen25coder14b_for_local_development/)
- [Ollama + qwen2.5-coder:14b for local development - Reddit](https://www.reddit.com/r/ollama/comments/1s2j9ct/ollama_qwen25coder14b_for_local_development/)
- [Ollama Installation - Documentation & FAQ - HOSTKEY](https://hostkey.com/documentation/technical/gpu/ollama/)
- [Ollama Installation - Documentation & FAQ - HOSTKEY](https://hostkey.com/documentation/technical/gpu/ollama/)
- [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [Ollama Performance Tuning: Batching, KV Cache, and OOM - Easton Dev](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [Ollama Running Slow? Every Fix, Step by Step - Serverman](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [Ollama vs. vLLM: A deep dive into performance benchmarking | Red Hat Developer](https://developers.redhat.com/articles/2025/08/08/ollama-vs-vllm-deep-dive-performance-benchmarking)
- [Ollama vs. vLLM: A deep dive into performance benchmarking | Red Hat Developer](https://developers.redhat.com/articles/2025/08/08/ollama-vs-vllm-deep-dive-performance-benchmarking)
- [Optimum Intel OpenVino Performance : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/18a87je/optimum_intel_openvino_performance/)
- [Optimum Intel OpenVino Performance : r/LocalLLaMA - Reddit](https://www.reddit.com/r/LocalLLaMA/comments/18a87je/optimum_intel_openvino_performance/)
- [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [PSA for Intel Arc llama.cpp users: speculative decoding is finally worth turning on (merged ~40–90% speedup) - Reddit](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [Performance Benchmarks — OpenVINO™ documentation — Version(2026)](https://docs.openvino.ai/latest/openvino_docs_performance_benchmarks.html)
- [Performance Benchmarks — OpenVINO™ documentation — Version(2026)](https://docs.openvino.ai/latest/openvino_docs_performance_benchmarks.html)
- [Performance Guide — LlamaCppEx v0.8.45 - Hexdocs](https://hexdocs.pm/llama_cpp_ex/performance.html)
- [Performance Guide — LlamaCppEx v0.8.45 - Hexdocs](https://hexdocs.pm/llama_cpp_ex/performance.html)
- [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [Performance of llama.cpp on Intel GPU with SYCL backend #23313 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [Qwen/Qwen2.5-14B-Instruct - Hugging Face](https://huggingface.co/Qwen/Qwen2.5-14B-Instruct)
- [Qwen/Qwen2.5-14B-Instruct - Hugging Face](https://huggingface.co/Qwen/Qwen2.5-14B-Instruct)
- [Qwen/Qwen2.5-Coder-14B-Instruct - Hugging Face](https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct)
- [Qwen/Qwen2.5-Coder-14B-Instruct - Hugging Face](https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct)
- [Qwen/Qwen2.5-Coder-7B-Instruct · 14B in the future? - Hugging Face](https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct/discussions/13)
- [Qwen2.5 14B Instruct vs Qwen2.5-Coder 7B Instruct: Benchmarks, Pricing & Which Is Better in 2026 - LLM Stats](https://llm-stats.com/models/compare/qwen-2.5-14b-instruct-vs-qwen-2.5-coder-7b-instruct)
- [Qwen2.5 14B Instruct vs Qwen2.5-Coder 7B Instruct: Benchmarks, Pricing & Which Is Better in 2026 - LLM Stats](https://llm-stats.com/models/compare/qwen-2.5-14b-instruct-vs-qwen-2.5-coder-7b-instruct)
- [Qwen2.5 Coder 14B Instruct · Benchmarks, Pricing & Performance | BenchGecko](https://benchgecko.ai/model/qwen-qwen25-coder-14b-instruct)
- [Qwen2.5 Coder 14B Instruct · Benchmarks, Pricing & Performance | BenchGecko](https://benchgecko.ai/model/qwen-qwen25-coder-14b-instruct)
- [Qwen2.5 Coder Instruct 7B Intelligence, Performance & Price Analysis](https://artificialanalysis.ai/models/qwen2-5-coder-7b-instruct)
- [Qwen2.5 Coder Instruct 7B Intelligence, Performance & Price Analysis](https://artificialanalysis.ai/models/qwen2-5-coder-7b-instruct)
- [Qwen2.5-Coder Technical Report - arXiv](https://arxiv.org/html/2409.12186v3)
- [Qwen2.5-Coder Technical Report - arXiv](https://arxiv.org/html/2409.12186v3)
- [Qwen2.5_Coder_(14B)-Conversational.ipynb - Google Colab](https://colab.research.google.com/github/unslothai/notebooks/blob/main/nb/Qwen2.5_Coder_(14B)-Conversational.ipynb)
- [Qwen2.5_Coder_(14B)-Conversational.ipynb - Google Colab](https://colab.research.google.com/github/unslothai/notebooks/blob/main/nb/Qwen2.5_Coder_(14B)-Conversational.ipynb)
- [Recommended Models for Autocomplete in Continue](https://continue-docs.mintlify.app/ide-extensions/autocomplete/model-setup)
- [Recommended Models for Autocomplete in Continue](https://continue-docs.mintlify.app/ide-extensions/autocomplete/model-setup)
- [Run LLMs on Intel® GPUs Using llama.cpp](https://www.intel.com/content/www/us/en/developer/articles/technical/run-llms-on-gpus-using-llama-cpp.html)
- [Run LLMs on Intel® GPUs Using llama.cpp](https://www.intel.com/content/www/us/en/developer/articles/technical/run-llms-on-gpus-using-llama-cpp.html)
- [Shared GPU Memory Override Feature and Requirements - Intel](https://www.intel.com/content/www/us/en/support/articles/000101789/graphics.html)
- [Shared GPU Memory Override Feature and Requirements - Intel](https://www.intel.com/content/www/us/en/support/articles/000101789/graphics.html)
- [Tune GitHub Copilot Settings in VS Code - DEV Community](https://dev.to/pwd9000/tune-github-copilot-settings-in-vs-code-32kp)
- [Urgent Request: Ability to Reduce Shared GPU Memory Allocation Beyond 50%](https://learn.microsoft.com/en-us/answers/questions/5812115/urgent-request-ability-to-reduce-shared-gpu-memory)
- [Urgent Request: Ability to Reduce Shared GPU Memory Allocation Beyond 50%](https://learn.microsoft.com/en-us/answers/questions/5812115/urgent-request-ability-to-reduce-shared-gpu-memory)
- [Using GitHub Copilot CLI with Local Models (LM Studio) - DEV Community](https://dev.to/playfulprogramming/using-github-copilot-cli-with-local-models-lm-studio-5e3b)
- [Using your own LLM models in GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [What is IGD Aperture Size? - Intel](https://www.intel.com/content/www/us/en/support/articles/000028294/graphics.html)
- [What is IGD Aperture Size? - Intel](https://www.intel.com/content/www/us/en/support/articles/000028294/graphics.html)
- [What should i change to optimize local hosted AI - Beginners - Hugging Face Forums](https://discuss.huggingface.co/t/what-should-i-change-to-optimize-local-hosted-ai/176339)
- [What should i change to optimize local hosted AI - Beginners - Hugging Face Forums](https://discuss.huggingface.co/t/what-should-i-change-to-optimize-local-hosted-ai/176339)
- [Why Your Local LLM Is Slow — llama.cpp Config Guide | OmniForge Blog](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [Why Your Local LLM Is Slow — llama.cpp Config Guide | OmniForge Blog](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [Xe – Merge Acceptance Plan — The Linux Kernel documentation](https://www.kernel.org/doc/html/v6.8/gpu/rfc/xe.html)
- [[Alibaba Qwen 團隊] 學術論文 - Qwen2.5-Coder 系列模型技術報告與能力評測](https://arxiv.org/html/2409.12186v3)
- [[BenchGecko 媒體] 效能評測 - Qwen2.5-Coder-14B-Instruct 程式能力與速度基準](https://benchgecko.ai/model/qwen-qwen25-coder-14b-instruct)
- [[Continue 官方] 技術指南 - 編輯器內自動補全的推薦模型 Tier List](https://continue-docs.mintlify.app/ide-extensions/autocomplete/model-setup)
- [[Continue 官方] 操作手冊 - 自動補全設定與模型初始化流程](https://docs.continue.dev/customize/deep-dives/autocomplete)
- [[Continue 官方] 架構文件 - 程式碼自動補全的模型定位與機制](https://docs.continue.dev/customize/model-roles/autocomplete)
- [[Continue 官方] 產品規格 - 開源程式碼助手支援的本地模型對照表](https://docs.continue.dev/customize/models)
- [[DEV Community 專欄] 技術指南 - Copilot CLI 本地串接 LM Studio 實戰指南](https://dev.to/playfulprogramming/using-github-copilot-cli-with-local-models-lm-studio-5e3b)
- [[DEV Community 專欄] 技術指南 - VS Code 內細部調整 Copilot 行為與自訂端點](https://dev.to/pwd9000/tune-github-copilot-settings-in-vs-code-32kp)
- [[Easton Dev 部落格] 技術指南 - Ollama 批次處理、KV Cache 與 OOM 錯誤優化](https://eastondev.com/blog/en/posts/ai/20260410-ollama-performance-optimization/)
- [[Featherless 官方] 產品規格 - 14B 模型雲端 API 行銷與託管收費頁面](https://featherless.ai/models/unsloth/Qwen2.5-Coder-14B-Instruct)
- [[Fireworks AI 官方] 產品規格 - Qwen2.5-Coder-14B 託管端點與線上測試環境](https://fireworks.ai/models/fireworks/qwen2p5-coder-14b-instruct)
- [[GitHub 官方論壇] 社群問答 - Copilot 支援本地模型終端點討論](https://github.com/orgs/community/discussions/190067)
- [[Google Colab 範例] 技術指南 - 使用 Jupyter Notebook 運行 Qwen2.5-Coder-14B](https://colab.research.google.com/github/unslothai/notebooks/blob/main/nb/Qwen2.5_Coder_(14B)-Conversational.ipynb)
- [[HOSTKEY 官方] 操作手冊 - 本地 Ollama 安裝配置與常見問題疑難解答](https://hostkey.com/documentation/technical/gpu/ollama/)
- [[Hacker News 社群] 案例研究 - 利用 BIOS 鎖與 DVMT 調整內顯共享記憶體](https://news.ycombinator.com/item?id=37082906)
- [[Hexdocs 官方] 技術指南 - LlamaCppEx 推理加速與參數調整優化指南](https://hexdocs.pm/llama_cpp_ex/performance.html)
- [[Hugging Face 官方] 模型規格 - Qwen2.5-14B 指令微調模型權重與部署資訊](https://huggingface.co/Qwen/Qwen2.5-14B-Instruct)
- [[Hugging Face 官方] 模型規格 - Qwen2.5-Coder-14B 專業代碼指導模型說明頁](https://huggingface.co/Qwen/Qwen2.5-Coder-14B-Instruct)
- [[Hugging Face 論壇] 社群問答 - 初學者地端運行 LLM 的硬體與軟體優化建議](https://discuss.huggingface.co/t/what-should-i-change-to-optimize-local-hosted-ai/176339)
- [[Hugging Face 論壇] 社群討論 - Qwen2.5-Coder 14B 發布規劃探討](https://huggingface.co/Qwen/Qwen2.5-Coder-7B-Instruct/discussions/13)
- [[Infercom 媒體] 技術指南 - 深入探討 TTFT、吞吐量與硬體瓶頸關鍵指標](https://infercom.ai/blog/llm-inference-speed-explained/)
- [[Intel 官方] 技術指南 - BIOS 與系統設定調整內顯專用記憶體步驟](https://www.intel.com/content/www/us/en/support/articles/000041253/graphics.html)
- [[Intel 官方] 技術指南 - Xeon 與 OpenVINO 多模態 RAG 加速架構](https://lenovopress.lenovo.com/lp2350-accelerating-multimodal-rag-with-intel-xeon-and-openvino)
- [[Intel 官方] 技術指南 - 基於 SYCL 在 Intel GPU 上運行 llama.cpp 部署指引](https://www.intel.com/content/www/us/en/developer/articles/technical/run-llms-on-gpus-using-llama-cpp.html)
- [[Intel 官方] 效能評測 - OpenVINO 2026 年版本推理效能官方基準測試](https://docs.openvino.ai/latest/openvino_docs_performance_benchmarks.html)
- [[Intel 官方] 架構文件 - BIOS 中 IGD 顯示開口大小 (Aperture Size) 機制說明](https://www.intel.com/content/www/us/en/support/articles/000028294/graphics.html)
- [[Intel 官方] 產品規格 - Windows 10/11 內顯共享顯示記憶體機制問答](https://www.intel.com/content/www/us/en/support/articles/000020962/graphics.html)
- [[Intel 官方] 產品規格 - Windows 驅動中共享顯示記憶體覆寫功能與限制](https://www.intel.com/content/www/us/en/support/articles/000101789/graphics.html)
- [[Intel 官方論壇] 技術討論 - Linux 下 i915 與 xe 核心模組衝突排障](https://discourse.ubuntu.com/t/intel-igpu-using-both-i915-and-xe-kernel-modules-is-this-an-issue-how-to-resolve/77626)
- [[Intel 官方論壇] 技術討論 - 請求提供自訂降低共享顯示記憶體分配之反饋](https://learn.microsoft.com/en-us/answers/questions/5812115/urgent-request-ability-to-reduce-shared-gpu-memory)
- [[LLM Stats 媒體] 比較分析 - 14B 指令模型與 7B 專業代碼模型效能與資源對比](https://llm-stats.com/models/compare/qwen-2.5-14b-instruct-vs-qwen-2.5-coder-7b-instruct)
- [[Linux Kernel 官方] 架構文件 - Intel Xe 顯示核心驅動程式併入主線技術規範](https://www.kernel.org/doc/html/v6.8/gpu/rfc/xe.html)
- [[LiteLLM 官方] 開源專案 - LiteLLM 代理與 GitHub Copilot 自訂端點對接](https://docs.litellm.ai/docs/tutorials/github_copilot_integration)
- [[LocalAlternative 媒體] 比較分析 - 本地 AI 補全工具與 Copilot 替代方案](https://www.localalternative.io/blog/github-copilot-local-alternatives)
- [[ModelFit 媒體] 比較分析 - Continue 本地程式碼模型評級推薦](https://modelfit.io/tools/continue-dev/)
- [[Ollama 官方] 常見問題 - Windows 記憶體限制與 GPU 加速疑難排解](https://docs.ollama.com/faq)
- [[Ollama 官方] 模型規格 - Qwen2.5-Coder 14B 基礎程式模型權重規格](https://ollama.com/library/qwen2.5-coder:14b-base)
- [[Ollama 官方] 模型規格 - Qwen2.5-Coder 14B 對話指令程式模型權重規格](https://ollama.com/library/qwen2.5-coder:14b-instruct)
- [[OmniForge Blog] 技術指南 - 2026 最新 llama.cpp 的 GGUF 量化規格與記憶體需求表](https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms)
- [[OmniForge Blog] 技術指南 - 解決本地 LLM 推理緩慢的 llama.cpp 最佳設定指引](https://omniforge.online/blog/your-local-llm-is-slow-because-of-five-config-flags)
- [[PatrickMcCanna 部落格] 技術指南 - 系統記憶體共享與內顯 VRAM 分配優化](https://patrickmccanna.net/allocating-ram-for-gpu-performance-on-self-hosted-llm-systems-with-integrated-system-gpu-ram/)
- [[Pristren 部落格] 比較分析 - Continue 地端整合功能與實用性測評](https://pristren.com/blog/continue-dev-open-source-copilot/)
- [[Red Hat 官方] 比較分析 - Ollama 與 vLLM 在大規模推理下的架構與效能對比](https://developers.redhat.com/articles/2025/08/08/ollama-vs-vllm-deep-dive-performance-benchmarking)
- [[Reddit 社群] 技術討論 - Intel 平台 speculative decoding 投機解碼效能翻倍實測](https://www.reddit.com/r/LocalLLM/comments/1txt6xy/psa_for_intel_arc_llamacpp_users_speculative/)
- [[Reddit 社群] 技術討論 - LLM 推理之 KV Cache 卸載至系統記憶體可行性](https://www.reddit.com/r/LocalLLaMA/comments/1txpqru/maybe_kv_cache_offload_to_ram_isnt_bad/)
- [[Reddit 社群] 技術討論 - 最佳化 Intel OpenVINO 推理引擎性能配置技巧](https://www.reddit.com/r/LocalLLaMA/comments/18a87je/optimum_intel_openvino_performance/)
- [[Reddit 社群] 效能評測 - Arc 顯示卡在 SYCL/Vulkan 推理架喚基準測試](https://www.reddit.com/r/IntelArc/comments/1tr9397/intel_arc_pro_b70_32gb_for_local_llms_llamacpp/)
- [[Reddit 社群] 新聞討論 - Linux 下 Intel 顯示晶片記憶體限制修正 patch](https://www.reddit.com/r/linux_gaming/comments/1jvyyx1/linux_users_with_intel_graphics_can_now_worry/)
- [[Reddit 社群] 案例研究 - UHD 770 內顯以 OpenVINO 與 int8 量化完全卸載推理實測](https://www.reddit.com/r/LocalLLaMA/comments/1c0j338/localai_openvino_inference_on_intel_igpu_uhd_770/)
- [[Reddit 社群] 案例研究 - 地端 Ollama 與 Qwen2.5-Coder 14B 開發實務感受](https://www.reddit.com/r/ollama/comments/1s2j9ct/ollama_qwen25coder14b_for_local_development/)
- [[Reddit 社群] 比較分析 - Intel GPU 平台上 SYCL、OpenVINO 與 Scaler 效能對比](https://www.reddit.com/r/LocalLLaMA/comments/1swk3wh/intel_b70_llamaccp_sycl_vs_llamacpp_openvino_vs/)
- [[Reddit 社群] 比較分析 - 深入對比 llama.cpp 與 vLLM 在實際併發下的效能表現](https://www.reddit.com/r/IntelArc/comments/1vzxzxu/llamacpp_vs_vllm_high_raw_tps_but_poor_realworld/)
- [[Reddit 社群] 討論問答 - Arrow Lake 與 iGPU 在 llama.cpp 推理效能反饋](https://www.reddit.com/r/LocalLLaMA/comments/1uur0ic/has_anyone_gotten_llamacpp_or_other_working_using/)
- [[SYCL][Intel GPU] Long Term Features & Issues Tracking · ggml-org llama.cpp · Discussion #5277 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/5277)
- [[SYCL][Intel GPU] Long Term Features & Issues Tracking · ggml-org llama.cpp · Discussion #5277 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/5277)
- [[SecondState 官方] 技術指南 - Qwen2.5-14B 輕量化地端運行與 API 部署](https://www.secondstate.io/articles/qwen25/)
- [[Serverman 部落格] 排障手冊 - Ollama 推理速度緩慢的逐步排查與優化指引](https://www.serverman.co.uk/server/ollama-running-slow-how-to-speed-up-local-llm-inference/)
- [[Tutorial] Mastering Host-Memory Prompt Caching in llama-server · ggml-org llama.cpp · Discussion #20574 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/20574)
- [[Tutorial] Mastering Host-Memory Prompt Caching in llama-server · ggml-org llama.cpp · Discussion #20574 - GitHub](https://github.com/ggml-org/llama.cpp/discussions/20574)
- [[VS Marketplace 官方] 產品規格 - Copilot 擴充套件自訂 API 供應商介面](https://marketplace.visualstudio.com/items?itemName=tzraeq.vscode-copilot-custom-provider)
- [[Wccftech 媒體] 新聞報導 - Intel 最新驅動開放 93% 記憶體分配給 iGPU 運行 LLM](https://wccftech.com/intel-drivers-lets-users-allocate-93-percent-system-memory-to-arc-igpus-wider-ai-llm-support/)
- [[YouTube 頻道] 教學影片 - 地端 Ollama 模型串接 GitHub Copilot 實作引導](https://www.youtube.com/watch?v=SYmOVP9Z-NU)
- [[jjleng GitHub] 開源專案 - 自訂 Copilot 反向代理伺服器實作](https://github.com/jjleng/copilot-proxy)
- [[llama.cpp GitHub] 技術論壇 - Intel SYCL 後端架構效能回報與優化議題](https://github.com/ggml-org/llama.cpp/discussions/23313)
- [[llama.cpp GitHub] 技術論壇 - Intel SYCL 後端硬體相容與效能優化長期追踪](https://github.com/ggml-org/llama.cpp/discussions/5277)
- [[llama.cpp GitHub] 效能評測 - SYCL 後端在 Intel 顯示晶片上的基準性能測試數據](https://github.com/Liquid4All/benchmarks-llama.cpp/blob/benchmarks/docs/backend/SYCL.md)
- [[llama.cpp GitHub] 教學指南 - 主機記憶體提示快取 (Prompt Caching) 設定技巧](https://github.com/ggml-org/llama.cpp/discussions/20574)
- [[llama.cpp vs vLLM] High raw TPS but poor real-world performance : r/IntelArc - Reddit](https://www.reddit.com/r/IntelArc/comments/1vzxzxu/llamacpp_vs_vllm_high_raw_tps_but_poor_realworld/)
- [[llama.cpp vs vLLM] High raw TPS but poor real-world performance : r/IntelArc - Reddit](https://www.reddit.com/r/IntelArc/comments/1vzxzxu/llamacpp_vs_vllm_high_raw_tps_but_poor_realworld/)
- [[媒體 專欄] 比較分析 - Qwen2.5-Coder-7B 資源佔用與推理效能評估](https://artificialanalysis.ai/models/qwen2-5-coder-7b-instruct)
- [[部落格 專欄] 技術指南 - 於 GitHub Copilot CLI 串接自託管 LLM 的具體步驟](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)
- [[部落格 專欄] 教學指南 - Windows 11 下四步驟極簡部署 Qwen2.5-Coder](https://ronniediaz.com/2026/02/19/how-to-setup-qwen-coder-2-5-llm-for-local-dev-on-windows-in-four-steps/)
- [[部落格 專欄] 教學指南 - 企業專屬 Ollama 與 Copilot 本地對接安全性部署](https://www.gss.com.tw/blog/github-copilot-ollama-local-llm)
- [https://askubuntu.com/questions/1473461/check-ram-usage-for-i915-intel-gpu](https://askubuntu.com/questions/1473461/check-ram-usage-for-i915-intel-gpu)
- [https://askubuntu.com/questions/1473461/check-ram-usage-for-i915-intel-gpu](https://askubuntu.com/questions/1473461/check-ram-usage-for-i915-intel-gpu)
- [https://askubuntu.com/questions/1473461/check-ram-usage-for-i915-intel-gpu](https://askubuntu.com/questions/1473461/check-ram-usage-for-i915-intel-gpu)
- [https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Efficient-AI-Inference-on-CPUs-with-OpenVINO/post/1748451](https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Efficient-AI-Inference-on-CPUs-with-OpenVINO/post/1748451)
- [https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Efficient-AI-Inference-on-CPUs-with-OpenVINO/post/1748451](https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Efficient-AI-Inference-on-CPUs-with-OpenVINO/post/1748451)
- [https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Efficient-AI-Inference-on-CPUs-with-OpenVINO/post/1748451](https://community.intel.com/t5/Blogs/Tech-Innovation/Artificial-Intelligence-AI/Efficient-AI-Inference-on-CPUs-with-OpenVINO/post/1748451)
- [https://community.intel.com/t5/Graphics/Intel-UHD-Graphics-770-had-memory-leak/m-p/1741840](https://community.intel.com/t5/Graphics/Intel-UHD-Graphics-770-had-memory-leak/m-p/1741840)
- [https://community.intel.com/t5/Graphics/Intel-UHD-Graphics-770-had-memory-leak/m-p/1741840](https://community.intel.com/t5/Graphics/Intel-UHD-Graphics-770-had-memory-leak/m-p/1741840)
- [https://community.intel.com/t5/Graphics/Intel-UHD-Graphics-770-had-memory-leak/m-p/1741840](https://community.intel.com/t5/Graphics/Intel-UHD-Graphics-770-had-memory-leak/m-p/1741840)
- [https://community.intel.com/t5/Graphics/Shoud-I-increase-DVMT-pre-allocated/m-p/559441](https://community.intel.com/t5/Graphics/Shoud-I-increase-DVMT-pre-allocated/m-p/559441)
- [https://community.intel.com/t5/Graphics/Shoud-I-increase-DVMT-pre-allocated/m-p/559441](https://community.intel.com/t5/Graphics/Shoud-I-increase-DVMT-pre-allocated/m-p/559441)
- [https://community.intel.com/t5/Graphics/Shoud-I-increase-DVMT-pre-allocated/m-p/559441](https://community.intel.com/t5/Graphics/Shoud-I-increase-DVMT-pre-allocated/m-p/559441)
- [https://community.intel.com/t5/Graphics/Ubuntu-LTS-iGPU-Hangs-Using-Both-i915-and-xe-Kernel-Modules-Is/m-p/1738608/highlight/true](https://community.intel.com/t5/Graphics/Ubuntu-LTS-iGPU-Hangs-Using-Both-i915-and-xe-Kernel-Modules-Is/m-p/1738608/highlight/true)
- [https://community.intel.com/t5/Graphics/Ubuntu-LTS-iGPU-Hangs-Using-Both-i915-and-xe-Kernel-Modules-Is/m-p/1738608/highlight/true](https://community.intel.com/t5/Graphics/Ubuntu-LTS-iGPU-Hangs-Using-Both-i915-and-xe-Kernel-Modules-Is/m-p/1738608/highlight/true)
- [https://community.intel.com/t5/Graphics/Ubuntu-LTS-iGPU-Hangs-Using-Both-i915-and-xe-Kernel-Modules-Is/m-p/1738608/highlight/true](https://community.intel.com/t5/Graphics/Ubuntu-LTS-iGPU-Hangs-Using-Both-i915-and-xe-Kernel-Modules-Is/m-p/1738608/highlight/true)
- [https://medium.com/@kapildevkhatik2/optimizing-ollama-performance-on-windows-hardware-quantization-parallelism-more-fac04802288e](https://medium.com/@kapildevkhatik2/optimizing-ollama-performance-on-windows-hardware-quantization-parallelism-more-fac04802288e)
- [https://medium.com/@kapildevkhatik2/optimizing-ollama-performance-on-windows-hardware-quantization-parallelism-more-fac04802288e](https://medium.com/@kapildevkhatik2/optimizing-ollama-performance-on-windows-hardware-quantization-parallelism-more-fac04802288e)
- [https://medium.com/@kapildevkhatik2/optimizing-ollama-performance-on-windows-hardware-quantization-parallelism-more-fac04802288e](https://medium.com/@kapildevkhatik2/optimizing-ollama-performance-on-windows-hardware-quantization-parallelism-more-fac04802288e)
- [llama.cpp VRAM Requirements: Complete 2026 Guide to GPU Memory for Local LLMs](https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms)
- [llama.cpp VRAM Requirements: Complete 2026 Guide to GPU Memory for Local LLMs](https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms)
- [llama.cpp for SYCL - benchmarks - GitHub](https://github.com/Liquid4All/benchmarks-llama.cpp/blob/benchmarks/docs/backend/SYCL.md)
- [llama.cpp for SYCL - benchmarks - GitHub](https://github.com/Liquid4All/benchmarks-llama.cpp/blob/benchmarks/docs/backend/SYCL.md)
- [qwen2.5-coder:14b-base - Ollama](https://ollama.com/library/qwen2.5-coder:14b-base)
- [qwen2.5-coder:14b-base - Ollama](https://ollama.com/library/qwen2.5-coder:14b-base)
- [qwen2.5-coder:14b-instruct - Ollama](https://ollama.com/library/qwen2.5-coder:14b-instruct)
- [qwen2.5-coder:14b-instruct - Ollama](https://ollama.com/library/qwen2.5-coder:14b-instruct)
