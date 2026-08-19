---
title: "ARC-AGI-3 Benchmark"
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 2
tags: [benchmark, evaluation, agi, reasoning]
topics: [agent-research-benchmarks, agent-research]
canonical: concepts/arc-agi-3-benchmark
---

> ARC-AGI-3——ARC Prize Foundation 於 2026 年推出的互動式推理 benchmark，測試 agent 在完全未知規則的模擬世界中探索、建立假設、修正錯誤與長期規劃的能力，用 **RHAE**（Relative Human Action Efficiency）評分。[[wiki/entities/prime-agent|Prime Agent]] 用它作為主要評測案例，也是「harness 差異可以讓同一個模型表現差 3 倍以上」的核心例證。

## 測試設計

- 完整 ARC-AGI-3 有 **135 個**人類設計的抽象環境
- 測試者不會拿到規則、目標或解法，只能觀察畫面、嘗試動作、推測世界如何運作，並把前一關學到的規則帶到後續關卡——同時測到探索、假設建立、錯誤修正、長期規劃
- 有**公開展示集**（供研究者反覆實驗）與 **ARC Prize 半私有資料**（Verified 認證用，內容不公開，用來檢查方法是否只對已知題目有效）兩種資料，兩者不可混用

## RHAE 計分方式

RHAE（Relative Human Action Efficiency）不只看 agent 有沒有過關，還比較 agent 用了多少次動作，相對於人類第一次玩同一關所需的動作數。100% 代表 agent 完成全部遊戲與關卡、整體行動效率達到或超過人類基準——**不代表 agent 在所有知識與工作上都等同人類**。

## Prime Agent 的成績：三種口徑不可混用

Prime Agent 這次跑的是**公開展示集**（25 個環境、183 個關卡），不是完整 135 個環境，也不是 ARC Prize Verified 用的半私有資料。三個常被混用的數字：

| 成績 | 執行者與環境 | 可以得出的結論 | 不能得出的結論 |
|---|---|---|---|
| **95.5%** | Prime Intellect 自行執行，Opus 5 + Prime Agent，Public Demo | 公司測試顯示 Prime Agent 在公開集表現很高（三次執行 95.0/95.2/95.5%，Best@3 達 99.97%，183/183 關卡） | 已通過 ARC Prize 半私有資料驗證 |
| **95.24%** | ARC Prize 網站目前可讀取的 Prime Agent 公開 scorecard（178/183 關、24/25 環境、11,245 動作，2026-08-06 讀取） | 至少有一個 95.2% 左右的 run 可逐關查看、可獨立核對 | 95.5% 這個最高 run 已被 ARC Prize 官方重現 |
| **30.16%** | ARC Prize Verified 頁面，Opus 5 在官方標準 harness 下的成績 | 同一模型在標準 harness 下的基準表現 | Prime Agent 在完全相同條件下、只靠換 harness 就能提升到 95.5% |

**核心訊號**：Opus 5 本身沒有重新訓練，只是換了一套 harness，RHAE 就從 30.16% 跳到 95%+ 區間——這支持「未來比較 AI 產品，不能只問用了哪個模型，也要問模型外面包了什麼 harness」的判斷。但 95.5% 本身仍停在「可研究的公司自測結果」，不是獨立驗證完成的紀錄；ARC Prize 的政策也要求公開資料上尚未驗證的獨立成績必須清楚標示。

## 來源
- [[wiki/sources/2026-08-13-prime-agent-official-blog|Prime Agent 官方部落格全文]]
- [[wiki/sources/2026-08-13-prime-agent-arc-agi-3-aiposthub|Prime Agent ARC-AGI-3 分析（AI郵報）]]

## 相關頁面
- [[wiki/entities/prime-agent|Prime Agent]]
- [[wiki/concepts/continual-harness|Continual Harness]]
- [[wiki/concepts/recursive-language-model|Recursive Language Model]]
