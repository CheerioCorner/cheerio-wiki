---
title: "Prompt Caching — LLM 輸入端 KV Cache 重用機制"
type: concept
created: 2026-08-27
updated: 2026-08-27
sources: 2
tags: [prompt-caching, kv-cache, llm, inference, cost-optimization, prefix-matching]
topics: [backend-systems, agent-infrastructure]
---

# Prompt Caching

Prompt Caching 是一種只快取 LLM **輸入端**的優化技術，透過儲存預先計算的 KV pairs 來避免重複處理相同的輸入內容。與傳統應用系統的 Output Caching（快取完整回應）根本不同：Prompt Caching 快取的是輸入的 Key-Value pairs，LLM 仍然需要處理新的部分。

## 與傳統 Caching 的根本差異

傳統 Output Caching：相同 query 進來 → 直接從 cache 回傳上次的結果，完全跳過運算 `[IBM 00:33]`。Prompt Caching 則是快取輸入 prompt 在 Prefill 階段計算出的 KV pairs，後續請求只需重新處理新增的部分 `[IBM 01:40]`。

簡單來說：Output Caching 是「跳過整個運算」；Prompt Caching 是「只重算增量」。

## 核心機制：KV Pairs 與 Prefill

### KV Pairs 的計算

語言模型在處理 prompt 時，會在每一個 Transformer layer 為每個 token 計算 key-value pairs `[IBM 02:13]`。這些 KV pairs 代表模型對 prompt 的內部理解——每個詞如何與其他詞關聯、什麼上下文重要、什麼資訊需要關注 `[IBM 02:52]`。

計算這些 KV pairs 發生在 **Prefill 階段**——這是 LLM 產生第一個輸出 token 之前的計算密集期 `[IBM 02:52]`。對應到 [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] 中的 Prefill 階段（暫停期），是整個推理流程中延遲最高的部分。

Prompt Caching 的核心就是快取這些已預先計算好的 KV pairs，避免重複計算 `[IBM 02:52]`。

### 前綴匹配（Prefix Matching）

Cache 系統使用前綴匹配技術來判斷哪些內容可以快取：從 prompt 開頭逐個 token 比對，遇到第一個不同的 token 時，快取比對中斷、後續內容進入一般處理流程 `[IBM 06:05]`。

這就是為什麼 **prompt 結構對自動快取至關重要**：

- ✅ **靜態優先**：系統提示 → 文件 → few-shot examples → 用戶問題 `[IBM 06:36]`
- ❌ **動態優先**：用戶問題 → 系統提示 → 文件（快取立即失效）`[IBM 07:33]`

## 可快取的內容

按實用性排序 `[IBM 04:33]`：

| 內容類型 | 說明 | 快取效益 |
|----------|------|---------|
| **System Prompt** | 所有對話共用的指令、個性、規則 | 最高（最常重複） |
| **文件/Context** | 50 頁手冊、研究論文、法律合約 | 高（大型靜態內容） |
| **Few-shot Examples** | 示範回應格式的範例 | 中 |
| **Tool/Function Definitions** | 工具與函數的定義 | 中 |
| **對話歷史** | 之前的對話內容 | 中（隨對話增長） |

## 成本影響

### 二次方 vs 線性成本

沒有 Prompt Coding 時，多輪對話的 token 消耗呈二次方增長：50k → 51k → 54k → 55k，總消耗是所有輪次的累加 `[Alejandro 04:44]`。啟用 Prompt Caching 後，重複的 token 只需支付 ~10% 的價格，成本曲線從二次方壓低為線性 `[Alejandro 05:10]`。

### 跨模型比較

Alejandro 模擬了一個 200K token session 的花費 `[Alejandro 06:58]`：

| Provider | 無快取 | 有快取 | 備註 |
|----------|--------|--------|------|
| Claude Opus 5 | ~$41 | 大幅降低 | Anthropic 需手動啟用快取 |
| GPT-5.6 Sol | ~$41 | 大幅降低 | OpenAI 自動快取 |
| DeepSeek V4 Pro | 極低 | 幾乎免費 | 目前最具競爭力的定價 |

## Provider 快取差異

| Provider | 自動快取 | 快取 TTL | 備註 |
|----------|---------|---------|------|
| OpenAI | ✅ 自動 | ~1 小時 `[Alejandro 11:48]` | — |
| Anthropic API | ❌ 需手動 | 5 分鐘 `[Alejandro 11:48]` | Claude Code 可達 1 小時 |
| Hugging Face | ✅ 自動 | 依路由 provider 而異 | — |
| Gemini | ❌ 需手動 | 未明確提及 | — |

**最低門檻**：至少需要 ~1024 tokens 才能觸發快取；低於此門檻時，管理 cache 的額外開銷會超過節省的成本 `[IBM 08:05]`。

## Agent 設計規則

從 Alejandro AO 的實作經驗整理出的關鍵規則：

1. **靜態優先的 Prompt 結構**：System Prompt、文件、examples 放前面，用戶問題放最後 `[IBM 06:36]`
2. **避免動態 System Prompt**：不在 System Prompt 放時間戳、工作目錄等會變的東西，否則 cache 會在該點之後全部失效 `[Alejandro 13:51]`
3. **Append-only History**：對話歷史只增不改，避免回頭修改破壞 cache `[Alejandro 16:06]`
4. **注意 Compaction 重置**：Context 壓縮會改變 prompt 前綴，導致 cache 重置——這是正常行為 `[Alejandro 15:10]`
5. **監控 Cache Hit Rate**：使用支援 cache 監控的 Agent Harness（如 Pi 或 Tau）`[Alejandro 16:06]`
6. **注意 TTL**：長時間中斷後 cache 會過期，回來時需重新支付完整價格 `[Alejandro 12:20]`

## 與其他概念的關係

- 推理的兩階段瓶頸 → [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]]
- GPU 記憶體與頻寬瓶頸 → [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]]
- 模型切分策略 → [[wiki/concepts/model-sharding|Model Sharding]]
- 量化推理引擎 → [[wiki/entities/vllm|vLLM]]

## 來源

- [[wiki/sources/2026-08-27-ibm-technology-prompt-caching|What is Prompt Caching? | IBM Technology]] — 概念解說
- [[wiki/sources/2026-08-27-alejandro-ao-prompt-caching|Prompt Caching Explained — Alejandro AO]] — Agent 設計與成本實作
