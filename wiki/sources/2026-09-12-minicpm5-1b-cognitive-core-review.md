---
title: "MiniCPM5 - Just How Good Can a 1B Model Be?"
type: source
created: 2026-09-12
updated: 2026-09-12
sources: 1
tags: [youtube, minicpm5, on-device, 1b-model, cognitive-core, agentic]
topics: [agent-architecture, agent-infrastructure]
canonical: sources/2026-09-12-minicpm5-1b-cognitive-core-review
provenance:
  - kind: raw
    path: raw/youtube/mincpm-v2-intro.md
    url: https://www.youtube.com/watch?v=ox1mW2N9Z_Y
---

# MiniCPM5 - Just How Good Can a 1B Model Be?

> Sam Witteveen 的 YouTube 影片，介紹 OpenBMB 推出的 MiniCPM-5 1B dense 模型，探讨 Karpathy 的 cognitive core 概念與 on-device agentic 應用。

## 影片資訊

- **作者**：Sam Witteveen
- **URL**：https://www.youtube.com/watch?v=ox1mW2N9Z_Y
- **時長**：00:20:54
- **字幕**：English（auto-generated）
- **Raw**：[[raw/youtube/mincpm-v2-intro|MiniCPM5 1B Transcript]]

## 重點摘要

### Karpathy 的 Cognitive Core 概念

Andre Karpathy 長期主張，理想的小模型不該是「縮小版 chatbot」，而應是一個約 1B 參數的「認知核心」——剝除大部分百科式知識，只保留推理、工具調用、查詢能力 `[00:00]`。核心觀點：模型不需要把所有知識塞進權重，應優先使用外部工具 `[00:30]`。

講者自 2018 年投入語言模型研究，認同這個方向——早期就認為把所有知識塞進模型權重是很笨的做法 `[00:30]`。目前挑戰在於小模型普遍不擅長工具調用與 agentic 應用 `[00:45]`。

### OpenBMB 與 MiniCPM-5 規格

OpenBMB 是清華大學 NLP Lab `[02:58]`。MiniCPM-5 1B 規格：

| 屬性 | 數值 |
|------|------|
| 參數量 | 1B dense |
| 架構 | Llama style |
| Context Window | 128K |
| 授權 | Apache 2 |

`[04:47]`

### 三版本訓練結構

1. **Base model**：純預訓練，訓練資料公開（ultra-fine web dataset、math pre-training data）`[05:09]`
2. **SFT**：400B tokens（1B 版為 200B deep-thinking + 200B hybrid）`[05:39]`
3. **Full version**：SFT + RL + on-policy distillation `[06:10]`

訓練資料完全公開，這在開放模型中很少見 `[05:09]`。On-policy distillation 的概念值得專門影片深入探討 `[06:10]`。

### Benchmark 表現

- **Token 效率**：比 Qwen 3.52B reasoning 少用 31 倍 tokens，比 non-reasoning 版少 8 倍 `[07:47]`
- **AA Omniscience benchmark**：得分 -1（近乎零幻覺），遠優於 Qwen 0.8B 等模型的大量負分 `[08:10]`
- 在同量級模型中表現突出 `[07:16]`

### 工具調用能力實測

- 單工具呼叫（get weather、calculator）：完全正確 `[14:54]`
- 重複工具呼叫：表現良好 `[15:26]`
- 多步推理（multi-step chain reasoning）：表現良好 `[15:54]`
- 長任務（long-running jobs）：有時通過、有時失敗，第二個長任務特別容易失敗 `[16:15]`
- No tool restraint 測試：表現良好，能分辨何時不需工具 `[17:24]`

### 已知限制

- **長 Chain of Thought 容易 loop**：GSM8K 等測試中，模型常因 16K token 限制而陷入思考迴圈，無法得出答案 `[17:24]`
- **指令遵循不夠精確**：講者嘗試給模型命名 "Jennifer"，模型反覆忽略 `[13:59]`
- **內容生成偏弱**：無法產出 5000 字文章，SVG/HTML 生成品質基礎 `[11:53]`
- 限制長 CoT 是困難的問題，連 OpenAI 從 GPT-5.0 到 5.5 都在專注解決 `[19:00]`

### On-Device 應用

- 可運行在手機、瀏覽器、小型 CPU 應用上 `[04:35]`
- OpenBMB 已展示 MiniCPM desk pet（Electron app）`[10:18]`
- 已有開發者用 Rust 建構 edge home harness `[09:15]`
- 預期未來更多硬體會搭載這類小型模型 `[09:46]`

### 陳述級溯源

本 source note 的每個事實性陳述均已 inline 標註 `[MM:SS]` 時間戳，對應 `raw/youtube/mincpm-v2-intro.md` 中的段落位置。跨段落歸納陳述標註最主要來源段落的時間戳，前綴 `≈`。
