---
title: "LLM Temperature — 推論參數調校"
type: concept
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [llm, temperature, sampling-parameters, inference-tuning, agent-flow, local-llm]
topics: [agent-infrastructure, ai-agent]
canonical: concepts/llm-temperature
---

# LLM Temperature — 推論參數調校

> **成長階段：** 🌱 種子期

## 一句話定義

**Temperature**是控制 LLM 每次 token 生成隨機性與創造力的推論參數，數值越低輸出越穩定/果斷，數值越高輸出越發散/有創意；實務上應依 agent 的任務型態分別設定，而非套用固定值。

## 核心機制

Temperature 參數 T 透過縮放 Logits（`z_i / T`）影響 Softmax 機率分佈 `[01:25]`：

| 溫度值 | 數學行為 | 輸出特性 | 適用場景 |
|--------|---------|---------|---------|
| **T → 0** | 趨近貪婪解碼（Greedy Decoding） | 極度果斷、路徑一致 | 邏輯推理、程式碼生成、結構化 JSON 輸出 |
| **T = 0.1–0.3** | 機率分佈尖銳 | 穩定、低隨機性 | 工具呼叫（Tool Calling）、航班檢索、路由決策 |
| **T = 0.7** | 社群標準基線 | 平衡 | 一般對話（Hugging Face 熱門模型預設）`[02:18]` |
| **T = 0.9–1.0** | 機率分佈平坦 | 高多樣性、創造力強 | 創意寫作、圖像提示詞生成、發想 |
| **T > 1.0** | 極端拉平（部分模型可達 1.5） | 高度不可控 | 實驗性探索（罕見） |

## 地端 LLM 選型的溫度考量

地端部署 LLM 時，溫度與模型特性有交互效應：

1. **小模型 + 高溫 = 高幻覺率**：7B/8B Q4_K_M 等量化模型在高溫下幻覺（Hallucination）發生率顯著高於雲端超大模型，因此地端執行嚴謹任務時溫度通常需更保守（0.0–0.2）
2. **低溫抑制量化誤差**：低溫度（0.1–0.3）能有效抑制量化誤差引起的輸出發散，提升工具呼叫與結構化檢索的穩定性
3. **選型三維評估**：地端模型選型應同時考慮「模型參數量 + 量化位元 + 溫度容忍度」

> **使用者脈絡：** 在選擇地端 LLM 時，根據目標任務對應的溫度特性來輔助選型。若任務為確定性查詢（如 DevOps 指令執行），優先選低溫穩定的模型；若任務含創意生成，則需評估模型在高溫下的品質衰減。

## 與其他採樣參數的協同

Temperature 通常與以下參數協同運作：

- **Top-P（核採樣 Nucleus Sampling）**：限制採樣範圍為累積機率前 P% 的 token
- **Top-K**：限制採樣範圍為機率最高的 K 個 token
- **Min-P**：過濾機率低於最小值的 token

> 協同機制：高溫時若不搭配 Top-P/Top-K，容易採樣到機率極低的噪音 token。實務上建議「低溫 + 高 Top-P」或「高溫 + 低 Top-P」的組合策略。

## Agent 多階工作流調參

在複合型 Agent 中，不同工作階段應動態切換溫度：

```
使用者輸入
    ↓
[意圖路由] T=0.1（確定性分流）
    ↓
[工具呼叫 / Schema 呼叫] T=0.0–0.2（嚴格遵從 JSON Schema）
    ↓
[內容潤飾 / 發想] T=0.7–1.0（允許創造力）
    ↓
[結構化輸出] T=0.0–0.1（精確格式）
```

> 這就是 [[wiki/concepts/hybrid-intent-router|混合式意圖路由器]] 的「參數級分流」維度——意圖路由器除了分流模型本體外，也可依意圖性質自動附加對應的 Temperature 參數。

## 常見反模式

- ❌ **盲設 0.5**：「People try to set this to 0.5 randomly and just leave it. Why is it 0.5, you need to question it.」`[03:54]`
- ❌ **全域固定值**：不同任務用同一溫度，忽略了確定性任務與創造性任務的差異
- ❌ **不做實驗**：「You need to experiment all of this in your agent flow to make sure your agent is always proper」`[04:00]`

## 正確做法

- ✅ 針對每個 agent 用途實驗調整 `[04:00]`
- ✅ 按任務型態分派：確定性 → 低溫，創造性 → 高溫
- ✅ 地端小模型更保守：寧可低溫確保穩定，不盲目追求多樣性

## 與其他概念的關係

- 為 [[wiki/concepts/local-llm-deployment|Local LLM 部署]] 補充推論參數維度
- 為 [[wiki/concepts/hybrid-intent-router|混合式意圖路由器]] 提供「參數級分流」機制
- 為 [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] 補充動態參數覆寫概念
- 主要來源：[[wiki/sources/2026-09-04-llm-temperature-explained-kodekloud|KodeKloud LLM Temperature Explained]]
