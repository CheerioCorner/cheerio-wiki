---
title: "Mem0 vs Decision-Ledger 對 W-074 架構的適用性"
type: discussion
created: 2026-08-22
updated: 2026-09-02
sources: 2
tags: [mem0, decision-ledger, memory-architecture, w-074]
topics: [agent-memory-context]
---

# Mem0 vs Decision-Ledger 對 W-074 架構的適用性

> W-074 規劃四類記憶架構（Episodic / Semantic / Procedural / Artifact）。本頁探討 Mem0 與 Decision-Ledger 兩種記憶機制各自適合哪一類，以及為何應該疊加而非二選一。

## 兩種記憶機制的本質差異

| 維度 | Mem0（向量式事實提煉） | Decision-Ledger（結構化決策節點） |
|---|---|---|
| 核心資料單元 | 獨立事實原子（"User prefers Python"） | 結構化決策節點（intent → rationale → action → outcome） |
| 擅長類別 | **Semantic Memory**（語意/事實偏好） | **Procedural Memory** / 決策軌跡 |
| 因果推理 | 弱——無法回答「當初為何選 A 不選 B」 | 強——完整推論鏈，可審計 |
| 時間追蹤 | 弱（依賴覆蓋，非原生時序邊） | 強（每筆決策有完整脈絡） |

## 結論：疊加而非二選一

**Mem0 的向量式設計適合 Semantic Memory，不適合直接取代 Procedural Memory 或決策軌跡記錄。**

原因：Mem0 的 LLM extraction 機制傾向把資訊「扁平化」成陳述句事實（例如把「我們討論了 A、B 兩個方案，考量成本後選 A」提煉成「選了 A 方案」），會丟失決策過程的推論脈絡。對需要回答「當初為何這樣做」的 Procedural Memory 來說，這種資訊丟失是致命的。

### 建議的架構組合

| 記憶類型 | 適合的機制 | 說明 |
|---|---|---|
| **Semantic Memory** | Mem0 | 事實偏好、使用者設定、已驗證知識 |
| **Procedural Memory** | Decision-Ledger | 每個決策的完整推論鏈（intent/rationale/action/outcome） |
| **Episodic Memory** | 事件流封存 ＋ 結案摘要（不用向量） | 2026-09-02 定案：查詢形狀是「哪個案子、什麼時候」，屬精確查詢，向量化回收價值低 |
| **Artifact Memory** | 檔案系統為真相 ＋ 索引表（不切塊） | 2026-09-02 定案：產出物多為結構完整的 markdown，切塊會毀掉結構，走 [[wiki/concepts/chunkless-rag|Chunkless RAG]] 路線 |

### 實證支持

Sakana AI（Stefania Druga）在 X-Bench long-horizon task 上的實驗發現：四階 recall policy ladder（No recall → Vector RAG → Decision-ledger → Oracle）中，**Decision-ledger 表現最好**，ranked recall 比單純 gate（要不要用記憶）更好 `[VERIFIED]`。

這表示：對於需要跨長時間跨度追溯決策脈絡的場景，結構化的 decision-ledger 比純向量搜尋更有效。

### 注意事項

- 本結論為推論（`[INFERRED]`），基於 Sakana AI 實驗 + Mem0 架構分析的交叉推導，非任一單一來源的直接陳述
- Mem0 本身並非「不好」，而是其設計哲學（事實原子化、扁平化提煉）跟 Procedural Memory 的需求（保留推論脈絡）不匹配
- 兩者疊加時，需要一個 orchestration 層決定查詢時該走哪條路徑

## 2026-09-02 更新：Episodic / Artifact 已定案

原本標記「待定」的兩層已在 [[work/designs/w074-memory-architecture|W-074 記憶架構設計]] 定案（見上表）。同時補上一個本頁原本沒點出的推論：

> **W-074 的一級紀錄（意圖紀錄 → 任務契約 → 事件流 → 驗收 verdict）本身就是 decision-ledger 的形狀**，`intent → rationale → action → outcome` 逐欄對得上。

因此 Procedural Memory 不需要另外導入一套 decision-ledger 系統——它是從既有一級紀錄投影出來的。本頁「疊加而非二選一」的結論不變，但成本評估要修正：**decision-ledger 這一半是免費的**（治理需求的副產品），要另外花力氣的只有 Semantic 那一半。

## 相關來源

- [[wiki/sources/2026-08-22-mem0-deep-research-comparison|Mem0 深度研究——比較、限制、Decision-Ledger 適用性、授權定價]]（角度三完整論證）
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|Memory Harnesses for Long-Running Research Agents（Stefania Druga, Sakana AI）]]（Decision-Ledger 實驗原始來源）

## 相關頁面

- [[wiki/entities/mem0|Mem0 — AI Agent 長期記憶框架]]
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder — 記憶檢索策略的效果階梯]]
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems — 記憶架構設計的三支柱與五種存儲方式]]
