---
title: "Context Engineering（脈絡工程）— 決定模型這一次看到什麼"
type: concept
created: 2026-09-02
updated: 2026-09-02
sources: 4
tags: [context-engineering, context-rot, context-decay, memory, harness, agent-reliability]
topics: [agent-memory-context, agent-architecture]
canonical: concepts/context-engineering
---

# Context Engineering（脈絡工程）— 決定模型這一次看到什麼

> Context Engineering 是「決定模型這一次呼叫看到哪些東西」的設計功夫。Prompt Engineering 管**怎麼說**，Context Engineering 管**給它看什麼**——後者在長時任務裡的影響通常大於前者。

## 為什麼要獨立一頁

wiki 裡原本有兩個**病症**頁——[[wiki/concepts/context-rot|Context Rot]]（單一 agent 長 session 內 context 累積導致腐化）與 [[wiki/concepts/context-decay|Context Decay]]（跨模型交接時決策脈絡遺失）——但沒有一頁把**對策**收在一起。本頁補這個缺口：把散在 [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]]、[[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]]、[[wiki/concepts/contract-driven-development|契約驅動開發]] 的解法，依「context 生命週期」重新編排。

## 定位：它是 Harness 的職責之一，不是獨立系統

[[wiki/concepts/harness|Harness]] 的五大職責裡就有一項是 Context Management。Context Engineering 是這項職責的設計面：

```
Prompt Engineering   → 這一段話怎麼寫（指令品質）
Context Engineering  → 這一次呼叫看到什麼（輸入選擇）
Memory Engineering   → 什麼東西被留下來、留多久（儲存策略）
Harness Engineering  → 以上三件事由誰執行、什麼時候執行（驅動層）
```

三者常被混談。實務上的分工是：**記憶決定「有什麼可以拿」，脈絡工程決定「這一次拿哪些」，prompt 決定「拿到之後怎麼用」。**

## 四個對策族（依 context 生命週期）

### ① Write — 控制寫入量

不是所有東西都該進 context。

- **Keep context lean**：切換任務時直接開新 session，不讓 context 累積（[[wiki/concepts/context-rot|Context Rot]] 記錄的 Coinbase 做法）
- **分階段閉環**：每階段內部完成驗證，不把驗證壓力堆到最後（[[wiki/concepts/context-decay|Context Decay]] 的第 4 解方）
- **摘要優先於原文**：每一層記憶配一份 summary 供快查，需要細節才翻原文

### ② Read — 控制取回量（最關鍵的一族）

[[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] 是目前唯一有實測排名的框架（Stefania Druga / Sakana AI，X-Bench long-horizon task）：

| 階 | 策略 | 實測結論 |
|---|---|---|
| 1 | No recall | 基線，只在 task 塞得進 context 時可行 `[04:34]` |
| 2 | Vector RAG | 語意搜尋，中等成本 `[04:40]` |
| 3 | **Decision-ledger** | 記錄每步決策再動態排序檢索 `[04:50]`，**實用策略中效果最好** `[08:17]` |
| 4 | Oracle | 理論上限，非實用選項 |

兩個容易被忽略的推論：

- **ranked recall 比 gate 更好** `[08:24]`——與其判斷「要不要用記憶」，不如永遠取回但排序
- **好的 recall policy 不只更準，還更省 token**——省成本與提品質在這裡不衝突，不需要取捨

### ③ Handoff — 控制交接格式

跨模型／跨 harness 交接是 [[wiki/concepts/context-decay|Context Decay]] 的觸發點，對策是讓交接物**帶著理由走**：

- **契約驅動**（[[wiki/concepts/contract-driven-development|Contract-Driven Development]]）：用結構化契約記錄完整決策過程，不只結果
- **`rejected_alternatives` 欄位**：明確記錄「這個方案試過、為什麼放棄、放棄的後果」，避免下一棒重複踩同一個坑
- **超長 context window**：減少交接次數（治標，不解決格式問題）

> ⚠️ 黑盒外部 agent（只拿得到最終輸出）本質上就是一次跨模型交接，所以 decay 風險最高的地方往往不是自己的 session，而是外包出去的那一段。

### ④ Maintain — 控制留存與淘汰

[[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] 的六種維護策略裡，與脈絡工程最相關的兩個：

- **Retire 而非 Delete**：標記失效但保留歷史。刪掉會讓「為什麼當初這樣做」永久消失，正好製造 decay
- **Attribute**：追蹤記憶來源（對話／搜尋／行事曆）。來源不明的記憶無法判斷可信度，也就無法決定要不要放進 context

## 病症 → 對策對照

| 症狀 | 觸發條件 | 主要對策族 |
|---|---|---|
| 自相矛盾、重複做同一件事、偏離原問題 `[00:42][00:47][00:50]` | 單 agent 長 session | ① Write（開新 session）＋② Read（decision-ledger） |
| 被否決的方案重複嘗試、設計意圖流失 | 跨模型／跨 harness 交接 | ③ Handoff（契約 ＋ rejected_alternatives） |
| 記憶越多反而越不準、成本上升 | 記憶庫長大 | ② Read（ranked recall）＋④ Maintain（retire） |

## 實務檢查清單

1. 這次呼叫塞進去的東西，有沒有一項是「以防萬一」而不是「這次需要」？
2. 取回策略停在第 2 階（vector RAG）了嗎？有沒有結構化的決策紀錄可以升到第 3 階？
3. 交接物有沒有帶「為什麼」，還是只帶「做了什麼」？
4. 有沒有 `rejected_alternatives` 這類欄位承接被否決的方案？
5. 記憶是 delete 還是 retire？
6. 摘要與原文是否分層，讓平常只讀摘要？
7. 長任務是靠「模型 context 夠大」硬撐，還是有明確的分段策略？

## 開放問題

- Decision-ledger 的實測優勢來自 research agent 場景，在**短任務高頻**場景是否仍成立，尚無資料
- 「ranked recall 優於 gate」的結論若成立，代表多數 agent 目前做的「要不要查記憶」判斷是白費工——這點值得在自己的系統上重測
- 四個對策族之間的優先順序（先做哪一族 CP 值最高）目前沒有實證排序

## 相關頁面

- [[wiki/concepts/context-rot|Context Rot（脈絡惡化）]] — 單 agent 長 session 的病症
- [[wiki/concepts/context-decay|Context Decay（脈絡衰減）]] — 跨模型交接的病症
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 取回策略的實測排名
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 存儲／檢索／維護三支柱
- [[wiki/concepts/coala-four-memory-types|CoALA 四種記憶類型]] — 記憶的功能分類
- [[wiki/concepts/harness|Harness — LLM 的驅動層]] — Context Management 是其五職責之一
- [[wiki/concepts/contract-driven-development|契約驅動開發]] — 交接格式對策
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 另一組常被並列的 Engineering 分類
- [[wiki/discussions/mem0-vs-decision-ledger-for-w074|Mem0 vs Decision-Ledger 對 W-074 架構的適用性]] — 本頁對策在自建系統上的落點

## 來源

本頁為既有 wiki 頁面的綜整，未新增外部原始資料。支撐論點的來源筆記：

- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents（Sakana AI）]] — ① Write、② Read 全部論點與時間戳
- [[wiki/sources/2026-08-17-frontend-ai-roundtable|前端 AI 圓桌]] — ③ Handoff 的 context decay 場景與契約解方
- [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|AI Agent Memory Systems — 5 種記憶架構設計方式]] — ④ Maintain 六策略
- [[wiki/sources/2026-08-25-four-types-memory-ai-agent|The Four Types of Memory Every AI Agent Needs]] — 記憶功能分類與脈絡工程的分工
