---
title: "Memory Harnesses for Long-Running Research Agents（Stefania Druga, Sakana AI）"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [memory-harness, recall-policy, context-rot, sakana-ai, local-models]
topics: [agent-memory-context]
provenance_raw: "raw/youtube/2026-08-20-sakana-ai-memory-harnesses-for-long-running-research-agents.md"
provenance_url: "https://www.youtube.com/watch?v=R3-anFK1YM8"
---

# Memory Harnesses for Long-Running Research Agents

> Sakana AI 研究科學家 Stefania Druga 講長時間運行的 research agent 記憶系統實驗：write-manage-read 迴圈、recall policy ladder、decision-ledger、context rot。

## 講者

Stefania Druga，Sakana AI 東京研究科學家 `[00:12]`。曾是 AI engineering 社群成員，現居東京。

## Context Rot 問題

長 horizon tasks 中，模型會自相矛盾、重複工作、偏離問題 `[00:42]`。Druga 稱之為「context rot」`[01:29]`。

Meta 的趨勢預測：更多長 horizon tasks + 更少 model releases，兩者今年會收斂 `[01:02]`，使 context rot 成為優先議題。

## Coinbase 案例

Coinbase CEO 分享如何在增加 AI 用量的同時降低花費 `[01:42]`：
- 轉向更多 local/open-weight models
- Better routing、better caching、keeping context clean
- Better visibility（追蹤誰在用什麼、用於什麼任務）`[02:08]`

## 本地模型趨勢

- Qwen 27B、DeepSeek V4 Flash 可在 M3 Ultra (96GB) 上運行 `[02:17]`
- RAM 仍是瓶頸 `[02:33]`
- 本地模型開始對 agentic tasks 和 tool use 有用 `[02:37]`

## 實驗環境

- Mac M3 Ultra, 96GB RAM, 28-core CPU `[03:10]`
- 模型：Qwen 27B (4-bit quantized) + DeepSeek V4 Flash `[03:21]`
- 運行 evals 幾天，機器過熱需要風扇散熱 `[02:48]`

## Write-Manage-Read 迴圈

記憶不是單純的 database store，而是一個圍繞模型的 control loop `[03:48]`：
- **Write**：記錄資訊
- **Manage**：排序、過濾、維護
- **Read**：檢索給模型

## 實驗 Harness 設計

```
Research Agent（零 durable memory）
    ↓
Core（traces，始終顯示給 agent）`[04:08]`
    ↓
Recall Block（測試不同 modes）`[04:21]`
    ↓
Archival Block（跨 session 資訊追蹤）`[04:28]`
```

## Recall Policy Ladder

四個層級的檢索策略 `[04:34]`：

| 層級 | 策略 | 說明 |
|------|------|------|
| 1 | No recall | 基線：不使用記憶 |
| 2 | Vector RAG | 相似度搜尋 |
| 3 | Decision-ledger | 記錄每步決策，動態排序 `[04:50]` |
| 4 | Oracle | Ground truth（告訴 harness 正確記憶）`[05:00]` |

Oracle 不是满分——模型可以拿到正確記憶但選擇忽略或被混淆 `[08:32]`。

## 測試任務

### 任務 1：文獻回顧（含 retracted papers）
- 包含一篇 Nature 論文宣稱發現 742,000 種 promising materials（後被撤回）`[05:27]`
- 結論：所有資料能塞進 context 時，記憶不加分只加成本 `[06:14]`

### 任務 2：X-Bench（long-horizon memory benchmark）
- 問題在 step 500 問，答案在 step 124，完全超出 context window `[06:55]`
- 68 個問題，多個 seeds `[08:05]`
- **Decision-ledger 表現最好** `[08:17]`
- Ranked recall 比單純 gate（要不要用記憶）更好 `[08:24]`

## 關鍵發現

1. **Task 能塞進 context 時，記憶只加成本不加分** `[06:14]`
2. **Task 超出 context 時，ranked recall policy 效果最佳** `[07:49]`
3. **好的 recall policy 不只更準，還更省 token** —「bad memory is expensive」`[09:36]`
4. **跨模型通用**：在 Qwen 27B 和 DeepSeek V4 Flash 上都有效 `[09:22]`
5. **跨 benchmark 通用**：在 Spider V2 上也有效 `[09:30]`

## 建議

- 把 recall policy 當 first-class metric `[09:59]`
- 思考要存什麼記憶、如何排序、recall function 如何設計 `[10:15]`
- 記憶技術 landscape 很豐富：30+ cookbooks、從 file system retrieval 到 training memory models `[10:35]`

## Sovereign AI

本地模型讓你控制整個 pipeline 的每一步——資料、compute traces、evaluations `[11:20]`。這是 Sakana AI 在日本推動的重要方向 `[12:20]`。

## 相關頁面

- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 四階檢索策略框架
- [[wiki/concepts/context-rot|Context Rot]] — 長任務脈絡腐化
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 三支柱記憶架構
- [[wiki/concepts/context-decay|Context Decay]] — 跨模型交接脈絡遺失
