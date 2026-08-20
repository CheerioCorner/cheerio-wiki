---
title: "LLM Serving Architecture — 從 Token 到 KV Cache 的完整推理流程"
type: concept
created: 2026-08-21
updated: 2026-08-21
sources: 1
tags: [llm, inference, kv-cache, prefix-caching, batching, tokens]
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

## Prefix Caching `[26:54]`

保留 Cache 並按文字內容標記。模型偵測到重複的開頭文字時，跳過已處理的部分 `[27:16]`。Anthropic 的 Prompt Caching 讓快取輸入 Token 成本僅 1/10 `[27:23]`。

**實際應用：** 公司共用的系統提示（System Prompt）只處理一次，所有使用者共用同一份 Cache `[27:49]`。

## Batching：多用戶共用 GPU `[28:27]`

既然每次產 Token 都要讀取整個模型，不如讓多個用戶共用同一次讀取 `[28:58]`。50 個用戶的 Throughput 大幅提升 `[29:20]`。

**瓶頸是記憶體：** 每個用戶需要自己的 KV Cache，模型權重已佔據大部分 VRAM `[29:43]`。Cache 佔滿就滿了，新用戶要排隊 `[30:21]`。這就是 ChatGPT 顯示 "at capacity" 的原因。

## Agent 設計啟示

理解 Prefill/Decode 兩階段後，設計 AI Agent 時應思考：
- **如何給 AI 資訊**：Prompt 長度直接影響 TTFT，精簡輸入可加速首回應
- **哪些資訊該給 AI**：不是所有上下文都值得放入 Prompt，要權衡 Token 成本與回答品質
- **KV Cache 的利用**：系統提示可以被快取重用，設計好 System Prompt 能節省成本

## 來源

- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]]
