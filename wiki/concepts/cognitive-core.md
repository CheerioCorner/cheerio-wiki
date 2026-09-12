---
title: "Cognitive Core — Karpathy 的「小模型不是縮小版 Chatbot」概念"
type: concept
created: 2026-09-12
updated: 2026-09-12
sources: 1
tags: [small-model, on-device, agentic, philosophy, karpathy, cognitive-core]
topics: [agent-architecture]
canonical: concepts/cognitive-core
provenance:
  - kind: raw
    path: raw/youtube/mincpm-v2-intro.md
    url: https://www.youtube.com/watch?v=ox1mW2N9Z_Y
---

# Cognitive Core

> Andre Karpathy 提出的小模型設計哲學：理想的小模型不該是「縮小版 chatbot」，而應是一個剝除百科知識、只保留推理與工具調用能力的「認知核心」。

## 核心主張

理想的小模型（約 1B 參數，不超過幾 B）應具備以下特質 `[00:00]`：

1. **剝除百科式知識**：不嘗試把所有知識塞進模型權重
2. **保留推理能力**：邏輯推理、 chain of thought
3. **保留工具調用**：function calling、tool use
4. **保留查詢能力**：能查資料而非憑記憶回答

講者（Sam Witteveen）自 2018 年投入語言模型研究，認同這個方向——早期就認為「把所有知識塞進模型權重是很笨的做法」`[00:30]`。

## 為什麼重要

1. **大模型已全面支援工具調用**：所有大型閉源模型都支援 tool use `[00:45]`
2. **小模型的挑戰**：普遍不擅長工具調用與 agentic 應用 `[00:45]`
3. **手機厂商已開始實作**：透過 LoRA fine-tune 針對特定場景客製化小模型 `[01:13]`
4. **On-device 可行性**：1B 模型可運行在手機、瀏覽器、小型 CPU 應用上 `[04:35]`

## 代表性實作

**MiniCPM5** 是這個理念目前的代表性實作之一 `[01:43]`：

- 1B dense 模型，Llama 架構，128K context
- 2B 版本的「Defer to Tools」特性——不嘗試用自己的知識回答，主動委派給工具——可視為 cognitive core 概念的具體體現
- AA Omniscience 得分 -1（近乎零幻覺），代表模型知道自己不知道什麼

## 與相關概念的關係

### vs [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]]

兩者都主張「減少內建、依賴外部」，但層次不同：

| 維度 | Cognitive Core | Minimal Agent Philosophy |
|------|---------------|------------------------|
| 層次 | 模型權重/知識層級 | Harness/框架層級 |
| 減什麼 | 百科式知識 | 框架功能 |
| 保留什麼 | 推理、工具調用 | 核心循環 |
| 代表 | MiniCPM5 | Prime Agent 的 Continual Harness |

精神一致但操作層次不同，值得在兩頁互相標註但不合併。

### vs [[wiki/concepts/sub-agents-pattern|Sub-agents Pattern]]

Cognitive core 模型天然適合做 sub-agent——它不需要知道一切，只需要理解意圖、調用正確的工具。MiniCPM5-2B 被講者明確建議「用於 sub-agent」`[13:31]`。

## 參考來源

- [[wiki/sources/2026-09-12-minicpm5-1b-cognitive-core-review|MiniCPM5 1B 介紹影片]]
- [[wiki/entities/minicpm5|MiniCPM5]] — Cognitive core 的代表性實作
