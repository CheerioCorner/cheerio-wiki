---
title: "MiniCPM5-2B: The Best Sub-Agent Model Yet?"
type: source
created: 2026-09-12
updated: 2026-09-12
sources: 1
tags: [youtube, minicpm5, sub-agent, 2b-model, tool-calling, rl2]
topics: [agent-architecture, agent-infrastructure]
canonical: sources/2026-09-12-minicpm5-2b-sub-agent-review
provenance:
  - kind: raw
    path: raw/youtube/mincpm-v2-benchmark.md
    url: https://www.youtube.com/watch?v=CXvncR7v66o
---

# MiniCPM5-2B: The Best Sub-Agent Model Yet?

> Sam Witteveen 的 YouTube 影片，深入評測 MiniCPM5 從 1B 擴展到 2B（實際 2.5B）的訓練方法升級與工具調用能力。

## 影片資訊

- **作者**：Sam Witteveen
- **URL**：https://www.youtube.com/watch?v=CXvncR7v66o
- **時長**：00:14:31
- **字幕**：English（auto-generated）
- **Raw**：[[raw/youtube/mincpm-v2-benchmark|MiniCPM5 2B Transcript]]

## 重點摘要

### 從 1B 到 2B 的擴展

MiniCPM5 從 1B 擴展到 2B（實際 2.5B），宣稱性能相當於 4B 模型 `[00:21]`。核心問題：當 SFT 和 post-training 規模乘以 2 倍，agentic 能力是否也同步提升？

### 訓練方法升級

**SFT 變化**：
- 1B 版：200B deep-thinking + 200B hybrid tokens
- 2B 版：改為 400B tokens 全 deep-thinking `[01:15]`

**RL2（Critic-based RL）**：
- 取代傳統 GRPO，宣稱更穩定 `[01:48]`
- 將 critic 納入 RL 過程
- 有配套論文（pre-print 階段）`[01:48]`

**多 Domain RL**：
- reasoning task RL
- general task RL
- agentic RL
- 每個 domain 專家只需精通一件事 `[02:16]`

**On-policy Distillation**：
- 模型自己 sample trajectories，與 teacher model 比較差異後更新 `[03:14]`

### Benchmark 表現

| Benchmark | MiniCPM5-2B | Qwen3.5-4B | 勝出 |
|-----------|:-----------:|:----------:|:----:|
| SWE-Bench Verified | 領先 | — | MiniCPM5 |
| GPQA diamond | 落後 | 較高 | Qwen |
| SWE-Bench Pro | 較低 | ~2 倍 | Qwen |
| Terminal-Bench | 落後 | 較高 | Qwen |

`[01:00]` `[03:51]` `[06:30]`

GPQA diamond 分數雖落後 Qwen，但遠超其他 2B 模型 `[03:51]`。不久前這種分數被認為是 10-100 倍大模型才能達到的水準。

### 本地實測

- **硬體**：RTX Pro 6000
- **三種版本**：BFloat16、DeepSpark（speculative decoding）、GGUF 4-bit（llama.cpp）
- **速度**：GGUF 4-bit 達 400+ tokens/sec `[07:32]`
- **進階 Agentech 測試**：8/8 通過（80% 通過率，跑了至少 10 次）`[10:53]`

### 工具調用核心優勢

**「Defer to Tools」特性**：
- 模型不嘗試用自己的知識回答，而是主動委派給工具 `[12:32]`
- 這正是 sub-agent 所需要的特性 `[13:02]`
- 不給工具時，模型能正確判斷不應使用自己的知識 `[12:32]`

**Agentech 測試亮點**：
- 基礎工具呼叫測試：全過 `[09:29]`
- 進階測試（含 planning、prompt injection resistance、failure retry）：8/8 `[10:53]`
- 多工具並行呼叫：快速且準確 `[11:58]`
- 大型 page pre-fill：完全沒有問題 `[12:15]`

### 適用場景

講者明確建議：**用於 sub-agent**，需要快速但相對簡單的模型，能處理多種工具呼叫並提供正確的參數 `[13:31]`。

### 其他觀察

- 邏輯推理與數學表現良好 `[08:00]`
- SVG/HTML 生成不佳（但這不是模型的用途）`[08:53]`
- 知識類問題（如 5000 字文章）不強 `[08:00]`
- 多版本釋出：SFT、base、mid-training、GGUF、DeepSpark `[04:24]`

---

## Cheer 的觀點（Cheer 本人的評論，非影片原文或研究結論）

> 在各方面比較下來，B2 這個模型非常適合用在專家領域的 Agent 上，因為它更專注於分析與工具調用（tool calling），這正是目前想找的能力方向——不需要 coding agent 的能力（不需要它寫 code），但希望它在其他能力上（意圖理解、工具調用）表現突出。它自身的知識儲備不是必要的，更需要的是理解人類意圖並調用所需工具完成任務的能力；跟它負責業務領域無關的內容不需要回答或處理。

**⚠️ 來源標註**：此段為 Cheer 本人對 MiniCPM5-2B 的個人評論與選型觀點，與 Cheer-Agent 專案中「Agent 選型 / 小模型評估」方向相關。非影片逐字稿內容，非 benchmark 事實，不可當作客觀研究結論引用。

### 陳述級溯源

本 source note 的每個事實性陳述均已 inline 標註 `[MM:SS]` 時間戳，對應 `raw/youtube/mincpm-v2-benchmark.md` 中的段落位置。Cheer 的評論獨立標示來源，不混入影片陳述級溯源。
