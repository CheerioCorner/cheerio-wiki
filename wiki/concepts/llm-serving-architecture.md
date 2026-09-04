---
title: "LLM Serving Architecture — 從 Token 到 KV Cache 的完整推理流程"
type: concept
created: 2026-08-21
updated: 2026-09-04
sources: 2
tags: [llm, inference, kv-cache, prefix-caching, batching, tokens, sampling-parameters]
topics: [backend-systems, agent-infrastructure]
---

# LLM Serving Architecture

LLM 推理分為 **Prefill**（計算密集）與 **Decode**（記憶體頻寬密集）兩個階段，由 [[wiki/entities/vllm|vLLM]] 等模型伺服器執行。核心優化依賴 KV Cache、Prefix Caching 與 Batching。

## Token：模型的基本單位

語言模型不讀寫完整單詞，而是拆成 **Token**（約 3/4 個單詞）`[17:49]`。模型只有一個技巧：預測下一個 Token `[18:19]`。100 Token 的回答 = 模型跑 100 次循環 `[18:51]`。

Token 計價原因：每個 Token 都需要完整的模型讀取與計算，成本與 Token 數成正比。

## 推理的兩階段

### Prefill（暫停期）`[21:13]`
- 模型一次讀完整個 Prompt，所有 Token 同時通過模型 `[20:42]`
- **計算密集**（Compute-heavy）
- 產生第一個 Token 的時間 = **Time to First Token (TTFT)** `[21:42]`
- 輸入越長，暫停越久

### Decode（串流期）`[22:13]`
- 逐 Token 產生回答，每產一個 Token 就要**重新讀取整個模型** `[22:26]`
- **記憶體頻寬密集**（Memory Bandwidth-heavy）
- 速度上限 = GPU 讀取 VRAM 的速度 `[22:49]`
- **Time Per Output Token (TPoT)** ≈ 5 ms `[23:40]`

> 💡 這就是為什麼短問題回應快、長 Prompt 要等很久才蹦出第一個字——長 Prompt 的 Prefill 階段需要更多時間。

## KV Cache `[25:37]`

Prefill 完成後，計算結果存入 GPU 記憶體的 KV Cache `[25:53]`。後續 Decode 階段直接重用 Cache，不必重複 Prefill。暫停只付一次。

**問題：** 對話結束後 Cache 被清除，第二輪要從頭處理整個對話歷史 `[26:05]`。對話越長，等待越久 `[26:44]`。

## Prefix Caching / Prompt Caching `[26:54]`

保留 Cache 並按文字內容標記。模型偵測到重複的開頭文字時，跳過已處理的部分 `[27:16]`。Anthropic 的 Prompt Caching 讓快取輸入 Token 成本僅 1/10 `[27:23]`。

> 📖 **深入閱讀：** 完整的 Prompt Caching 機制（前綴匹配、可快取內容、Provider 差異、Agent 設計規則）請見 [[wiki/concepts/prompt-caching|Prompt Caching]]。

## Batching：多用戶共用 GPU `[28:27]`

既然每次產 Token 都要讀取整個模型，不如讓多個用戶共用同一次讀取 `[28:58]`。50 個用戶的 Throughput 大幅提升 `[29:20]`。

**瓶頸是記憶體：** 每個用戶需要自己的 KV Cache，模型權重已佔據大部分 VRAM `[29:43]`。Cache 佔滿就滿了，新用戶要排隊 `[30:21]`。這就是 ChatGPT 顯示 "at capacity" 的原因。

## 動態參數覆寫：Temperature 與 Sampling Parameters

推論引擎（如 vLLM、Ollama）應將 Temperature、Top-P 等採樣參數暴露為可動態設定的參數，供上層 Agent/Router 依任務即時注入最佳值，而非採用全域固定靜態值。

實務場景：
- **工具呼叫階段**：Router 傳入 T=0.0–0.2，確保 Schema 遵從
- **內容生成階段**：Router 動態切換為 T=0.7–1.0，允許創造力
- **地端小模型**：更需要低溫保護，避免量化誤差在高溫下放大為幻覺

> 詳見 [[wiki/concepts/llm-temperature|LLM Temperature — 推論參數調校]]

## Agent 設計啟示

理解 Prefill/Decode 兩階段後，設計 AI Agent 時應思考：
- **如何給 AI 資訊**：Prompt 長度直接影響 TTFT，精簡輸入可加速首回應
- **哪些資訊該給 AI**：不是所有上下文都值得放入 Prompt，要權衡 Token 成本與回答品質
- **KV Cache 的利用**：系統提示可以被快取重用，設計好 System Prompt 能節省成本
- **動態參數注入**：採樣參數（Temperature、Top-P）應由上層路由動態設定，而非全域固定

## 來源

- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]]
- [[wiki/sources/2026-09-04-llm-temperature-explained-kodekloud|KodeKloud LLM Temperature Explained]]
