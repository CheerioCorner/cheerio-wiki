---
title: "Prompt Caching Explained — Alejandro AO — Agent 設計與成本實作"
type: source
created: 2026-08-27
updated: 2026-08-27
sources: 1
tags: [prompt-caching, cost-optimization, agent-harness, llm, inference]
topics: [backend-systems, agent-infrastructure]
provenance_raw: "raw/youtube/alejandro-ao-prompt-caching.md"
---

# Prompt Caching Explained — Alejandro AO

Alejandro AO 在 Hugging Face 頻道的實作導向影片，從 Agent 設計與成本角度深入解說 Prompt Caching 的使用方式與注意事項。

## 概要

影片從一個具體的 Agent 場景出發：當 coding agent 累積 50k tokens 的 context，每次新查詢都必須重發完整歷史。若沒有 Prompt Caching，成本會呈二次方增長 `[00:00]`。Prompt Caching 讓重複讀到的 token 只需支付 ~10% 的價格，將成本曲線從二次方壓低為線性 `[05:10]`。

## 成本問題的本質

### 多輪對話的 Token 膨脹

Agent 在多輪對話中，每次都要重發完整歷史。例如：50k → 51k → 54k → 55k tokens，總消耗不是 55k 而是所有輪次的累加 `[04:44]`。即使使用像 Assistants API 這樣的抽象包裝，底層仍然是重新處理整個 context `[03:48]`。

### 快取定價模型

幾乎所有 LLM API 都有雙重定價：首次看到的 token 收取完整輸入價格，後續重複出現的 token（cache hit）僅收取 ~10% 的價格 `[05:43]`。OpenAI 和 Hugging Face Inference Providers 自動快取；Anthropic 和 Gemini 則需要手動啟用 `[13:22]`。

### 跨模型成本比較

 Alejandro 模擬了一個 200K token 的 session 在不同提供商的花費 `[06:58]`：
- Claude Opus 5 和 GPT-5.6 Sol：無快取時約 $41，啟用快取後大幅降低
- DeepSeek V4 Pro：價格極低，被形容為「基本上免費」`[08:00]`

## 快取失效陷阱

### 動態系統提示

在 System Prompt 中加入動態內容（如時間戳記、目前工作目錄、動態工具列表）會導致該點之後的所有快取完全失效 `[13:51]`。這是 Agent 設計中最常見也最昂貴的錯誤。

### 對話壓縮（Compaction）

當 context 過長觸發壓縮時，壓縮後的摘要會改變 prompt 前綴，導致 cache 重置 `[15:10]`。這是正常的行為——Compaction 本來就是用來精簡 context 的手段。

### 對話歷史的唯增原則

對話歷史應保持 append-only 結構，避免回頭修改之前的內容而破壞 cache `[16:06]`。

## Provider 快取差異

| Provider | 自動快取 | 快取 TTL |
|----------|---------|---------|
| OpenAI | ✅ 自動 | ~1 小時 `[11:48]` |
| Anthropic API | ❌ 需手動 | 5 分鐘（Claude Code 可達 1 小時）`[11:48]` |
| Hugging Face | ✅ 自動 | 依路由的 provider 而異 `[11:48]` |
| Gemini | ❌ 需手動 | 未明確提及 |

## 實作建議

1. **靜態優先**：System Prompt、文件、examples 放前面，用戶問題放最後
2. **避免動態內容**：不在 System Prompt 放時間戳、工作目錄等會變的東西
3. **Append-only History**：對話歷史只增不改
4. **監控 Cache Hit Rate**：使用支援 cache 監控的 Agent Harness（如 Pi 或 Tau）`[16:06]`
5. **注意 TTL**：長時間中斷後 cache 會過期，回來時需重新支付完整價格 `[12:20]`

## Cheer 的心得

> 之前已經看過 GPU 作為 LLM Server 如何進行 AI 運算的基礎原理，看完這兩支影片後，更能理解 Prompt Caching 和傳統應用系統的 Caching 不同之處，也更明白這在節省 Token 上背後的原理是什麼。

## 相關頁面

- [[wiki/concepts/prompt-caching|Prompt Caching]] — 完整概念頁
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] — 從 Token 到 KV Cache 的完整推理流程
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] — GPU 推理基礎
- [[wiki/sources/2026-08-27-ibm-technology-prompt-caching|What is Prompt Caching? | IBM Technology]] — 概念面解說

## 來源

- [[raw/youtube/alejandro-ao-prompt-caching|Prompt Caching Explained — Alejandro AO]]
