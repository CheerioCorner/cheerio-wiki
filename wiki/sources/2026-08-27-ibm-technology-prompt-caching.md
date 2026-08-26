---
title: "What is Prompt Caching? | IBM Technology — 概念解說"
type: source
created: 2026-08-27
updated: 2026-08-27
sources: 1
tags: [prompt-caching, kv-cache, llm, inference, ibm]
topics: [backend-systems, agent-infrastructure]
provenance_raw: "raw/youtube/ibm-technology-prompt-caching.md"
---

# What is Prompt Caching? | IBM Technology

IBM Technology 頻道的概念解說影片，解釋 Prompt Caching 的核心原理與實作方式。

## 概要

Prompt Caching 是一種只快取 LLM **輸入端**的技術，透過儲存預先計算的 KV pairs 來避免重複處理相同的輸入內容 `[01:40]`。與傳統的 Output Caching（快取完整回應）不同，Prompt Caching 的快取對象是輸入的 Key-Value pairs `[01:40]`。

## 核心機制

### 輸出快取 vs 輸入快取

傳統 Output Caching：相同的 prompt 進來 → 直接從 cache 回傳上次的回應，跳過 LLM `[00:33]`。Prompt Caching 則是快取輸入 prompt 計算出的 KV pairs，LLM 仍然需要處理新的部分 `[01:40]`。

### KV Pairs 的計算

模型在處理 prompt 時，會在每一個 Transformer layer 為每個 token 計算 key-value pairs `[02:13]`。這些 KV pairs 代表模型對 prompt 的內部理解——每個詞如何與其他詞關聯、什麼上下文重要、什麼資訊需要關注 `[02:52]`。

計算這些 KV pairs 發生在 Prefill 階段——這是 LLM 產生第一個輸出 token 之前的計算密集期 `[02:52]`。Prompt Caching 的核心就是快取這些已預先計算好的 KV pairs，避免重複計算 `[02:52]`。

### 前綴匹配（Prefix Matching）

Cache 系統使用前綴匹配技術來判斷哪些內容可以快取：從 prompt 開頭逐個 token 比對，遇到第一個不同的 token 時，快取比對中斷、後續內容進入一般處理流程 `[06:05]`。這使得 prompt 的結構對自動快取至關重要 `[06:05]`。

### 靜態優先的 Prompt 結構

最佳實踐是將靜態內容（系統提示、文件、few-shot examples）放在 prompt 前面，動態內容（用戶問題）放在最後 `[06:36]`。這樣下次請求進來時，cache 可以匹配通過所有靜態內容，只需處理新的問題部分 `[07:07]`。

若反過來將動態內容放在前面，cache 會在第一個不同的 token 處立即失效 `[07:33]`。

## 可快取的內容

1. **文件**：50 頁產品手冊、研究論文、法律合約等放入 context window 的大型文件 `[04:33]`
2. **System Prompt**：最常見的快取對象，因為所有對話都共用相同的指令 `[05:05]`
3. **Few-shot Examples**：用來示範回應格式的範例 `[05:36]`
4. **Tool/Function Definitions**：工具與函數的定義 `[05:36]`
5. **對話歷史**：之前的對話內容 `[05:36]`

## 實作細節

- **最低門檻**：至少需要 ~1024 tokens 才能觸發快取；低於此門檻時，管理 cache 的額外開銷會超過節省的成本 `[08:05]`
- **快取存活時間**：通常 5-10 分鐘後清除，部分可保留長達 24 小時 `[08:05]`
- **自動 vs 顯式快取**：部分提供商自動執行前綴匹配快取；其他則需要開發者在 API 呼叫中明確標記要快取的部分 `[08:37]`

## Cheer 的心得

> 之前已經看過 GPU 作為 LLM Server 如何進行 AI 運算的基礎原理，看完這兩支影片後，更能理解 Prompt Caching 和傳統應用系統的 Caching 不同之處，也更明白這在節省 Token 上背後的原理是什麼。

## 相關頁面

- [[wiki/concepts/prompt-caching|Prompt Caching]] — 完整概念頁
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] — 從 Token 到 KV Cache 的完整推理流程
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] — GPU 推理基礎
- [[wiki/sources/2026-08-27-alejandro-ao-prompt-caching|Prompt Caching Explained — Alejandro AO]] — 實作面深入解說

## 來源

- [[raw/youtube/ibm-technology-prompt-caching|What is Prompt Caching? | IBM Technology]]
