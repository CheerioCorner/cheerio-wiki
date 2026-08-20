---
title: "Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes"
type: source
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [ai-infrastructure, gpu, vllm, kubernetes, llm-serving, devops]
topics: [backend-systems, agent-infrastructure]
provenance_raw: "raw/youtube/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes.md"
provenance_url: "https://www.youtube.com/watch?v=hBzUokVYQkI"
---

# Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes

KodeKloud 的 Mumshad Mannambeth 講解 AI 運作背後的基礎設施：從單顆 GPU 到多 GPU 伺服器，再到 Kubernetes 上的智慧路由。面向 SRE / DevOps / 系統管理員，不需要機器學習背景。

> Duration: 00:51:00 | Segments: 219 | Language: en | Source: API (auto-generated)

---

## 影片重點摘要

### 1. 模型的本質：公式 + 權重 `[02:50]`

模型 = 一個公式（Transformer 架構）+ 數十億個權重（Weights）`[05:44]`。公式本身只有一頁程式碼，所有知識都在權重裡 `[06:04]`。權重以檔案形式存在於磁碟上，使用時載入記憶體。

**模型大小對照：**
- 小型模型：~2 GB `[06:15]`
- 中型模型：~16 GB `[06:18]`
- 大型模型（70B 參數）：~140 GB `[06:22]`
- 巨型模型（500B+ 參數）：數百 GB `[06:26]`

### 2. 為什麼需要 GPU？CPU vs GPU 架構差異 `[07:12]`

CPU 是少數強大核心，適合序列化複雜任務 `[08:27]`。但模型運算核心是數十億次互不依賴的小型乘法，可以完全平行處理 `[08:45]`。

GPU 有數千個小型核心，同時執行數學運算 `[09:36]`。以 Nvidia T4 為例，約 2,500 個核心，運算能力約為 CPU 的 100 倍 `[09:43]`。

**三個關鍵指標：**
- **Compute（算力）**：每秒能做多少數學運算 `[12:46]`
- **Capacity（容量）**：VRAM 能裝多少權重 `[12:52]`
- **Bandwidth（頻寬）**：GPU 讀取自身記憶體的速度 `[13:00]`

**GPU 記憶體（VRAM）是瓶頸：** 系統 RAM 頻寬僅 64 Gbps，比 GPU 核心需求慢約 50 倍 `[10:35]`。VRAM 直接建在 GPU 卡上，頻寬達 TB/s 級別 `[11:19]`。但 VRAM 很小（T4 只有 16 GB）`[12:05]`，空間非常珍貴。

**常見 GPU 規格比較 `[13:06]`：**
| GPU | TFLOPS | VRAM | Bandwidth |
|-----|--------|------|-----------|
| Desktop CPU | 1-3 | 32-64 GB RAM | 0.09 TB/s |
| Rack Server | 5-10 | 高 RAM | 0.5 TB/s |
| A100 | 312 | 80 GB | 2 TB/s |
| H100 | 990 | 80 GB | ~3.35 TB/s |
| H200 | 990 | 141 GB | ~4.8 TB/s |
| B200 | 2,250 | 192 GB | 8 TB/s |

### 3. 模型伺服器：vLLM `[15:34]`

腳本跑一次就結束，但真實使用者需要 always-on 的服務 → **模型伺服器**。vLLM 建在 PyTorch 之上，提供 OpenAI API 相容的 Web 介面 `[15:50]`。

一行指令啟動：`vllm serve <model>` `[16:17]`。啟動時將模型權重從磁碟載入 GPU 記憶體（約 16 GB，需 1-2 分鐘）`[16:31]`。

**關鍵特性：** 整個模型常駐 GPU 記憶體 `[17:01]`。一個伺服器 = 一份完整模型副本，複製成本極高 `[17:03]`。

### 4. Token 與推理循環 `[17:35]`

語言模型不讀寫完整單詞，而是拆成 **Token**（約 3/4 個單詞）`[17:49]`。模型只有一個技巧：給定目前所有文字，預測下一個 Token `[18:19]`。

100 Token 的回答 = 模型跑 100 次循環，每次產生一個 Token `[18:51]`。這就是為什麼 ChatGPT 的回答是逐字流出。

**兩個關鍵指標：**
- **請求時間長**：正常 Web 請求幾毫秒，但數百 Token 意味著數百次循環 `[19:04]`
- **請求大小差異巨大**：「法國首都」幾個 Token vs「摘要 100 頁合約」數萬 Token `[19:20]`

### 5. Prefill 與 Decode：推理的兩階段 `[20:08]`

**Prefill 階段（暫停期）：** `[21:13]`
- 模型一次讀完整個 Prompt，所有 Token 同時通過模型 `[20:42]`
- 計算密集（Compute-heavy），產生第一個 Token `[21:02]`
- 暫停時間 = Time to First Token (TTFT) `[21:42]`
- 輸入越長，暫停越久

**Decode 階段（串流期）：** `[22:13]`
- 逐 Token 產生回答，每產一個 Token 就要重新讀取整個模型 `[22:26]`
- 記憶體頻寬密集（Memory Bandwidth-heavy）`[22:44]`
- 速度上限 = GPU 讀取 VRAM 的速度 `[22:49]`
- 3 TB/s 頻寬 vs 16 GB 模型 = 每秒最多讀取 ~200 次 = 200 tokens/s `[23:25]`
- Time Per Output Token (TPoT) ≈ 5 ms `[23:40]`

### 6. KV Cache 與 Prefix Caching `[25:24]`

**KV Cache：** Prefill 完成後，計算結果存入 GPU 記憶體的 KV Cache `[25:37]`。後續 Decode 階段直接重用 Cache，不必重複 Prefill `[25:53]`。暫停只付一次，不是每個字都停。

**問題：** 對話結束後 Cache 被清除。第二輪對話要從頭重新處理整個對話歷史 `[26:05]`。對話越長，等待越久 `[26:44]`。

**Prefix Caching：** 保留 Cache 並按文字內容標記 `[26:54]`。模型偵測到重複的開頭文字時，跳過已處理的部分 `[27:16]`。Anthropic Console 有 "Prompt Caching" 功能，快取輸入 Token 成本僅 1/10 `[27:23]`。

### 7. Batching：多用戶共用 GPU `[28:27]`

既然每次產 Token 都要讀取整個模型，不如讓多個用戶共用同一次讀取 `[28:58]`。50 個用戶 = 同一次讀取，總輸出（Throughput）大幅提升 `[29:20]`。

**瓶頸是記憶體，不是算力：** 每個用戶需要自己的 KV Cache，而模型權重已佔據大部分 VRAM `[29:43]`。Cache 佔滿就滿了，新用戶要排隊或被踢出 `[30:21]`。這就是 ChatGPT 顯示 "at capacity" 的原因 `[30:32]`。

### 8. Model Sharding：多 GPU 切分模型 `[31:24]`

巨型模型（140 GB+）裝不進單一 GPU（80 GB）`[31:55]`。解法：把模型切成多片，分配到多個 GPU `[32:10]`。

**兩種切分方式：**
- **Pipeline Parallelism（按層切分）：** GPU 1 負責前幾層，GPU 2 負責後幾層 `[33:11]`。適合跨機器（NVLink 速度不夠快時）`[34:07]`
- **Tensor Parallelism（層內切分）：** 單層的運算分給多個 GPU `[33:28]`。適合同機器內（NVLink 超高速互連）`[33:36]`

**規則：** Chatty talk 放盒子裡，Light talk 放盒子間 `[34:07]`。

### 9. LLM-D：Kubernetes 上的智慧路由器 `[37:52]`

傳統 Load Balancer 不適合 LLM：每台伺服器的 Cache 不同（會丟棄已存的工作）`[35:37]`，且每個請求大小差異巨大（LB 無法區分）`[36:19]`。

**LLM-D** 是 Red Hat、Google、IBM、Nvidia 共同開發的開源專案 `[37:52]`。它是一個智慧路由器，根據三個條件決定路由 `[38:09]`：
1. 各伺服器已有的 Cache（Cache-aware routing）→ 吞吐量提升 3 倍，首回應時間減半 `[39:03]`
2. 各伺服器的記憶體使用量與佇列長度 `[39:09]`
3. Prefill/Decode 分離（Disaggregated serving）→ 70% 更多 tokens/s `[40:00]`

**架構：** 運行在 Kubernetes 上 `[40:22]`，66% 的 GenAI 組織使用 K8s 管理推理工作負載 `[40:48]`。用 Helm 部署，`values.yaml` 定義整個叢架構 `[45:57]`。

**Well-lit Paths：** 預調好的配置模板 `[42:06]`：
- Optimized baseline：單池 + Cache-aware routing
- Disaggregated：Prefill 池 + Decode 池分離
- Sharded：巨型模型跨多 GPU

**Leader Worker Set：** K8s 新物件，用於管理跨多機器的巨型模型 pod 群 `[45:33]`。

### 10. 這就是基礎設施的工作 `[48:37]`

整堂課幾乎都是基礎設施工作（Pod、Deployment、路由、快取、Auto Scaling、記憶體、網路），不是機器學習 `[48:52]`。Linux、Containers、Kubernetes、Monitoring 就是 AI 平台的基礎 `[49:03]`。

---

## Cheer 的想法

> 以下為使用者 Cheer 看完影片後的個人心得與延伸思考，非影片逐字稿。

我從這影片了解到為什麼 GPU 比 CPU 更適合運行模型，也了解為什麼模型的運算方式原來和一般應用程式不同，也了解到為什麼會用 Token 來計價模型的運算金額，以前不知道多少 B 的 Model 代表的是多少空間大小，現在稍微有了些概念，尤其是在 Transformer 架構、參數（Weights）與模型檔案的關係中多了一層了解。

對我來說，最有趣的是模擬模型的推理過程。原來在每個 Token 裡面，它是運作有所謂的 Prefill and Decode 的階段，這也讓我理解了，為什麼有時候跟 AI 說話，如果內容很短，它可以很快回答；如果給它一大堆東西，它有時候需要很久才蹦出第一個字。

我覺得這有助於幫助我在設計自己的 AI Agent 時，會去思考怎麼樣給 AI 資訊，好讓整個回應速度更快、也更加清楚什麼樣的資訊應該要給 AI。後面，他又講到如何透過 KV 快取（KV Cache）與前綴快取（Prefix Caching）優化效能，以及批次處理（Batching）與模型分片（Sharding）技術如何解決記憶體瓶頸，這部分我覺得跟我昨天看到 Memory Harness 的部分有互相呼應，讓我清楚知道記憶體（Memory）的瓶頸是什麼。

我們可以用什麼樣的方式，在我們的技術上知道底層是怎麼運作的？而在我們軟體的技術上，又要怎麼樣去配合？

最後講到介紹了 LLM-D 作為 Kubernetes 上的智慧路由器，如何有效率的分配負載並管理整個伺服器集群。這個部分我覺得對於企業來說是非常重要的資訊。因為我們確實有考量是不是要引進 Local LLM，究竟是要自己採購硬體設備（像 H200 或 B200 之類的輝達硬體設備）作為 AI model 的伺服器，或者是我們要租賃雲端的 GPU 伺服器。我覺得這也包括我們要做一些 Kubernetes 或是 Container 的規劃，在這上面都蠻有幫助的。

**🔍 開放問題（已研究，見 [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]]）：** 影片中並沒有提到 NPU（Neural Processing Unit）。那 NPU 在整個 AI 基礎設施架構中扮演什麼角色？它是什麼樣的運作，跟為什麼說它更適合 AI 時代？為什麼現在除了 CPU、GPU 之外，還需要 NPU？

---

## 相關頁面

- [[wiki/concepts/multi-model-cost-routing|Multi-Model Cost Routing]] — 多模型基礎設施的成本工程
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 記憶架構設計
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents]] — Cheer 提到「跟昨天看到 Memory Harness 互相呼應」
- [[wiki/sources/2026-08-20-thenewstack-multi-model-ai-infrastructure|Coinbase runs 1,200 agents]] — 企業多模型基礎設施實例
- [[wiki/entities/coinbase-ai-engineering|Coinbase AI Engineering]] — 多模型成本工程實踐
