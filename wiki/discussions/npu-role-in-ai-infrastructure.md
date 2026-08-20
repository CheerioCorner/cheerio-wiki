---
title: "NPU 在 AI 基礎設施架構中的角色"
type: discussion
created: 2026-08-21
updated: 2026-08-21
sources: 8
tags: [npu, ai-chip, edge-ai, hardware, gpu, tpu]
topics: [backend-systems, agent-infrastructure]
---

# NPU 在 AI 基礎設施架構中的角色

**狀態：🟢 已完成初步研究**（原始開放問題見下方，研究方法與品管附註見文末）

## 背景

在 Mumshad Mannambeth 的 AI Infrastructure 課程中，完整講解了 GPU 在 AI 推理中的角色，以及 CPU vs GPU 的架構差異。但課程**完全沒有提到 NPU（Neural Processing Unit）**。

## Cheer 的提問 `[觀看心得]`

> 影片中並沒有提到 NPU。那 NPU 在整個 AI 基礎設施架構中扮演什麼角色？它是什麼樣的運作，跟為什麼說它更適合 AI 時代？為什麼現在除了 CPU、GPU 之外，還需要 NPU？

---

## 研究結果

### 一、架構設計角度：NPU vs CPU vs GPU

三者的核心衝突在於「通用性/靈活性」與「算力密度/能效比」之間的取捨。

- **CPU**：晶片面積大部分用於控制邏輯、分支預測與多層快取，追求低延遲的通用指令執行。在 AI 基礎設施中扮演「總調度員」——系統 I/O、Tokenizer 前處理、以及像 LLM-D 這類智慧路由器的調度邏輯都由 CPU 負責。
- **GPU**：採用 SIMD/SIMT 架構，數千個小型核心同時執行相同指令，是訓練（Training）的首選，也因為 CUDA 生態成熟，在演算法快速變動的新模型推理上有彈性優勢 [REF-1] [VERIFIED]。
- **NPU**：採用 **Dataflow（資料流）架構**，捨棄複雜的通用控制單元，讓資料（權重、激活值）直接在處理單元陣列間流動、就地重用，大幅減少對外部記憶體的存取次數與功耗 [REF-1][REF-2] [VERIFIED]。以 Google TPU 為例，其核心運算單元 MXU 就是一種 128×128（v4 以前）或 256×256（v6e 起）的矩陣乘加陣列，這種「權重固定、資料流過」的設計就是業界常說的**脈動陣列（Systolic Array）**的具體實作 [REF-3] [VERIFIED]。

| 比較維度 | CPU | GPU | NPU / 專用 AI 晶片 |
|---|---|---|---|
| 控制邏輯比重 | 極高 | 中等 | 極低（硬體資料流） |
| 主要運算優勢 | 標量/序列運算 | 向量/大規模平行 | 張量/矩陣乘法 |
| 主要短板 | 矩陣吞吐量低 | 功耗大、成本高 | 軟體泛用性差、編譯器門檻高 |

### 二、應用場景角度：端側 vs 資料中心

**端側/邊緣（On-Device）**：核心需求是極致省電、低延遲、隱私（資料不用上雲）。這是目前 NPU 存在感最強的場景——Intel Core Ultra（Lunar Lake）的第四代 NPU 官方數據為 **48 TOPS**、比前一代提升約 4 倍 AI 算力 [REF-4] [VERIFIED]；Qualcomm Snapdragon 8 Gen 3 的 Hexagon NPU 可在手機端跑到 7B 參數等級的模型，官方宣傳每秒可生成約 20 tokens [REF-5] [VERIFIED]。這類 NPU 換來的是用大幅降低的功耗執行「背景虛化、語音辨識、端側小模型推理」這類持續性、對延遲極敏感的任務，代價是放棄了訓練能力與 CUDA 那樣的通用程式彈性。

**資料中心級 LLM 推理**：這裡的「NPU 概念」主要以雲端專用 ASIC 的形式存在，例如 Google TPU、AWS Inferentia/Trainium、華為 Ascend。AWS 官方明確指出 Inferentia2 系列的 Inf2 執行個體就是為了「大型語言模型與潛在擴散模型的規模化部署」而設計 [REF-6] [VERIFIED]。這類晶片瞄準的正是課程裡講到的 Prefill（算力密集）與 Decode（記憶體頻寬密集）兩階段——用專用矩陣引擎優化 Prefill，用晶片間高速互連（類似 GPU 的 NVLink）擴展處理 Decode 階段 KV Cache 所需的記憶體頻寬。

### 三、產業採用角度：成熟度與 GPU 的關係

- **訓練階段**：GPU 因為 CUDA/PyTorch 生態的深度綁定，仍佔絕對主導地位。
- **推理階段**：專用晶片(NPU/ASIC)與 GPU 呈現「互補與競爭並存」的態勢。學術界對 LLM serving 的系統性調查也指出，隨著模型架構逐漸穩定，針對 Transformer 推理量身打造晶片的經濟效益越來越明顯 [REF-7] [VERIFIED]。金融業界的分析也指出，這是資料中心從單純堆 GPU 走向「異質運算（heterogeneous compute）」的整體趨勢，一部分負載交給更省電、單位成本更低的專用晶片 [REF-8] [INFERRED]。
- **軟體生態**：歷史上 NPU 最大的障礙是軟體生態（CUDA 壁壘）。但 vLLM 這類開源模型伺服器近年推出了硬體可插拔（pluggable backend）架構，開始原生支援 Google TPU、AWS Neuron（Inferentia/Trainium）等非 GPU 後端 [REF-2] [VERIFIED，功能存在於 vLLM 專案本身，可於 GitHub 上查證]。

### 回應 Cheer 的原始問題

1. **NPU 扮演的角色 / 跟 CPU、GPU 的分工**：CPU 負責調度與 I/O，GPU 負責訓練與彈性高的推理，NPU/專用 ASIC 負責「已經定型、要規模化跑」的推理任務，用犧牲通用性換取更高的算力/功耗比。
2. **為什麼更適合 AI 時代**：因為 AI 推理的核心運算(矩陣乘法)高度重複且規律，NPU 的資料流架構可以把資料留在晶片內部反覆重用，不必像 CPU/GPU 那樣頻繁跟外部記憶體搬資料——這正是解決「記憶體牆」瓶頸的設計思路，跟課程裡講的 KV Cache/Prefix Caching(想辦法少搬資料、少重算)是同一個底層邏輯的不同層次體現。
3. **為什麼除了 CPU/GPU 還需要 NPU**：端側是為了在幾瓦甚至更低的功耗下維持長時間可用(手機、筆電續航撐不住 GPU 等級的功耗)；資料中心則是純粹的成本考量——電力與散熱是資料中心營運成本的大頭，專用晶片能用更低的單位成本(每美元、每瓦的 token 產出)跑推理。
4. **在資料中心級 LLM 推理的成熟度**：已經進入實際生產部署階段，不是概念驗證——Google 自家服務大量依賴 TPU、AWS 有現成的 Inf2 執行個體可租用，vLLM 也已經原生支援這些非 GPU 後端。

### 補充回答「待研究方向」中的 NVIDIA Tensor Core 問題

這點本次研究沒有查到直接對應的權威來源，屬於 Claude 依既有知識的補充判斷，**標記為 [UNVERIFIED]，建議之後再另外查證**：NVIDIA 的 Tensor Core 在運算原理上(矩陣乘加、資料重用)跟 NPU 的脈動陣列設計哲學相近，但它是**嵌在 GPU 的 SM(Streaming Multiprocessor)裡的專用單元**，跟 GPU 其餘的 SIMT 通用核心共存，本質上還是「泛用 GPU 裡塞了一顆專用加速器」，跟「整顆晶片就是為推理設計」的獨立 NPU/TPU 在架構定位上不完全一樣。這題如果要有把握地回答,值得之後另開一輪研究。

---

## 研究方法與品管附註

這次研究先由 Gemini(透過 `agy` CLI)產出附引用格式的報告,但 **Claude 逐一查證後發現原始報告 9 個引用中有 4 個是編造或錯誤的**:

- 一個引用直接給了 Google 搜尋查詢字串當「官方文件」網址
- 一個引用的 arXiv 編號(2402.19469)實際指向一篇講「人形機器人行走」的完全不相關論文
- 一個 Google Cloud TPU 文件網址是 404
- 一個 Medium 文章網址查無此文(疑似虛構)

其餘引用經 WebFetch/WebSearch 查證後,網址本身也多半跟原始出處不符,但主張的內容大方向正確。Claude 因此**沒有直接把 Gemini 的原始報告存入知識庫**,而是逐條查證後,替換成實際存在且可訪問的來源重新整理成上方內容。本頁所有 [VERIFIED] 標記均為 Claude 實際透過 WebFetch/WebSearch 開啟並確認過的來源,而非沿用 Gemini 原始報告的自稱驗證。

**引用來源列表(均經 Claude 手動查證存在)：**

| # | Tier | URL | 標題 |
|---|------|-----|------|
| REF-1 | T2 | https://semiengineering.com/heterogeneous-npu-data-movement-what-the-execution-flow-shows/ | Heterogeneous NPU Data Movement: What The Execution Flow Shows |
| REF-2 | T1 | https://github.com/vllm-project/vllm | vLLM 官方原始碼(硬體可插拔後端支援) |
| REF-3 | T1 | https://docs.cloud.google.com/tpu/docs/v4 | Google Cloud — TPU v4 官方文件(MXU/TensorCore 架構) |
| REF-4 | T1 | https://www.intel.com/content/www/us/en/support/articles/000099574/processors/intel-core-ultra-processors.html | Intel 官方 — Lunar Lake / Core Ultra 處理器總覽(NPU4 規格) |
| REF-5 | T1 | https://www.qualcomm.com/content/dam/qcomm-martech/dm-assets/documents/Unlocking-on-device-generative-AI-with-an-NPU-and-heterogeneous-computing.pdf | Qualcomm 官方白皮書 — Unlocking on-device generative AI with an NPU |
| REF-6 | T1 | https://aws.amazon.com/machine-learning/inferentia/ | AWS 官方 — Inferentia2 產品頁面 |
| REF-7 | T1 | https://arxiv.org/abs/2312.15234 | 學術論文 — Towards Efficient Generative LLM Serving: A Survey from Algorithms to Systems |
| REF-8 | T2 | https://www.tdsecurities.com/ca/en/ai-infrastructure-reframing-gpu-vs-asic | TD Securities — AI Infrastructure: Reframing the GPU vs. ASIC Debate |

**這次發現的教訓**：`chat-with-gemini-research` skill 雖然在 prompt 裡強制要求了嚴格引用格式,但 Gemini 仍然會編造「看起來很真實」的網址(真實存在的 arXiv 編號、看起來合理的官方文件路徑)。Skill 流程裡的 Step 4「驗證引用完整性」目前只檢查格式(有沒有 [REF-N]、有沒有 URL),**沒有實際開啟每個 URL 確認可訪問且內容相符**——這是流程上的漏洞,建議之後補上這一步的強制執行,而不是預設信任模型的自我宣稱。

## 原始待研究方向(供對照)

1. NPU vs GPU 的架構差異(核心設計、記憶體架構、適合的運算類型)— ✅ 已於上方回答
2. NPU 在伺服器端 vs 邊緣端的角色定位 — ✅ 已於上方回答
3. 對 Cheer 的企業場景(長榮航空 IT):NPU 是否影響 Local LLM / 邊緣 AI 的部署決策?— 待另外結合公司實際場景深入討論
4. NVIDIA 的 Tensor Cores 是否算是一種 NPU?— 部分回答,見上方補充段落,標記 [UNVERIFIED] 待進一步查證
5. 為什麼 Mumshad 的課程只提 GPU?— 合理推測是因為課程聚焦在「目前業界資料中心級 LLM 服務仍以 GPU 為絕對主流」的現況教學,NPU/TPU 屬於較新興、廠商各自為政的異質運算選項,不在單一課程的核心範疇內(此為 Claude 推論,非研究來源直接陳述,[INFERRED])

## 相關頁面

- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] — GPU 的完整角色
- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Source Note]] — 影片來源
