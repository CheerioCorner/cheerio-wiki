---
title: "LLM Temperature Explained — KodeKloud (YouTube)"
type: source
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [llm, temperature, sampling-parameters, agent-flow, kodekloud, youtube]
topics: [agent-infrastructure, ai-agent]
canonical: sources/2026-09-04-llm-temperature-explained-kodekloud
provenance:
  - kind: raw
    path: raw/youtube/kodkloud-llm-temperature-explained.md
    url: https://www.youtube.com/watch?v=IWSutLJbURU
---

# LLM Temperature Explained — KodeKloud (YouTube)

> Duration: 00:05:05 | Segments: 23 | Language: en | Source: API (auto-generated)

## 一句話摘要

KodeKloud 白板解說影片，講者以 flight scanner 代理程式為例，定義 LLM Temperature 如何控制輸出的隨機性與創造力，並以航班查詢（低溫 0.1–0.3）與圖像生成（高溫 0.9–1.0）兩個反差案例，論證 agent flow 應針對不同任務階段實驗調整溫度，而非套用固定值。

## 應用脈絡

> 使用者用途：在選擇地端（本地部署）LLM 時，根據目標任務對應的溫度特性來輔助選型。

這支影片的核心觀點直接服務於地端 LLM 選型決策——不同任務型態（結構化查詢 vs 創意生成）對溫度的容忍度不同，而地端小模型在高溫下的幻覺率更高，因此選型時必須把「模型參數量 + 量化位元 + 溫度容忍度」一起評估。

## 陳述級溯源

### 架構基礎 `[00:00]–[00:53]`

- 講者定義 agent 工作流：input → agent → model → output `[00:25]`–`[00:48]`
- 舉例 agent 為 flight scanner（航班掃描器）`[00:53]`

### Temperature 定義 `[00:53]–[01:25]`

- 「The temperature parameter basically defines how creativity or randomness the model can have」`[01:00]`
- 雙核心特性：randomness（隨機性）與 creativity（創造力）`[01:25]`

### 數值範圍 `[01:25]–[01:49]`

- 標準範圍 0 到 1，部分場景可達 1.5 `[01:25]`–`[01:39]`
- 低溫（趨近 0）→ 模型果斷、聚焦任務、token 生成路徑穩定 `[01:39]`–`[01:49]`
- 高溫（1.0–1.5）→ 創造力大幅提升，詞彙組合多樣但增加不確定性 `[01:49]`

### 社群基準 `[02:18]`

- 「If you open Hugging Face and look into top 10 models randomly, a standard here is 0.7」`[02:18]`

### 確定性任務案例：Flight Scanner `[02:29]–[03:04]`

- 輸入範例：「I would like to fly to Paris」from Dubai `[02:29]`
- 反問：為什麼 Dubai 飛 Paris 需要高隨機性？「Would you like to go from Dubai to Sydney and fly back to Paris? No, right?」`[02:46]`
- 建議溫度：0.1–0.3 `[02:58]`

### 創造性任務案例：Image Generation `[03:06]–[03:34]`

- 為旅程生成圖片，需要高創造力 `[03:06]`
- 建議溫度：0.9–1.0 `[03:06]`
- 「Every token generation will need to be smarter, will need to have some creativity or randomness to it」`[03:26]`

### 反模式與實務建議 `[03:44]–[04:27]`

- Flight scanner 在溫度光譜上落在低溫區域 `[03:44]`
- 常見反模式：「People don't really think a lot about it... they try to set this to 0.5 randomly and just leave it」`[03:54]`
- 正確做法：「You need to question it. Why is it not 0.3? You need to experiment all of this in your agent flow」`[04:00]`

### 結語 `[04:31]`

- 這類參數讓工程師更需要深入理解系統細節 `[04:31]`
- 置入 KodeKloud 課程目錄宣傳 `[04:48]`

## 來源

- YouTube: [LLM Temperature Explained](https://www.youtube.com/watch?v=IWSutLJbURU)
- 頻道：KodeKloud
- Duration: 00:05:05

## 相關頁面

- [[wiki/concepts/llm-temperature|LLM Temperature — 推論參數調校]]（概念頁）
- [[wiki/concepts/local-llm-deployment|Local LLM 部署]]（地端選型實踐）
- [[wiki/concepts/hybrid-intent-router|混合式意圖路由器]]（動態參數注入）
