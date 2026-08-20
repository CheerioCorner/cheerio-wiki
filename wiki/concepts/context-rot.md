---
title: "Context Rot（脈絡惡化）— 長任務中的記憶腐化"
type: concept
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [context-rot, long-horizon-tasks, memory, agent-reliability]
topics: [agent-memory-context]
canonical: concepts/context-rot
---

# Context Rot（脈絡惡化）— 長任務中的記憶腐化

> 單一 agent 在長時間單一 session 內，因 context 累積導致模型自相矛盾、重複工作、偏離原問題的現象。與 [[wiki/concepts/context-decay|Context Decay]] 觸發條件不同但後果類似。

## 定義

Context Rot 是 Stefania Druga（Sakana AI）在研究長時間運行 research agent 時提出的概念 `[01:29]`。指在 long-horizon tasks 中，隨著 context 不斷累積，模型會：

1. **自相矛盾**：前後說法不一致 `[00:42]`
2. **重複工作**：忘記已經做過某個任務，重新再做 `[00:47]`
3. **偏離問題**：忘記原始問題，開始回答不相關的東西 `[00:50]`

## 與 Context Decay 的區別

| 維度 | Context Decay | Context Rot |
|------|--------------|-------------|
| **觸發條件** | 跨模型交接（Gemini→Claude→Codex） | 單一 agent 長時任務內 |
| **成因** | 中介格式無法承載完整決策過程 | Context 累積超出模型處理能力 |
| **場景** | 多 AI 協作流程 | 單一 agent 的長 session |
| **解方** | 契約驅動、rejected_alternatives | [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]]、記憶系統 |

兩者**後果類似**（脈絡失真），但**觸發條件不同**，需要不同的解方。

## 為什麼現在更重要

Meta 的趨勢預測 `[01:02]`：
- 更多長 horizon tasks
- 更少 model releases
- 兩者今年會收斂 → context rot 成為優先議題

## 解方方向

- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 四階記憶檢索策略
- Decision-ledger — 記錄每步決策，動態排序檢索
- 本地模型 + sovereign AI — 控制整個 pipeline `[11:20]`

## 相關頁面

- [[wiki/concepts/context-decay|Context Decay]] — 跨模型交接脈絡遺失（互補概念）
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 記憶檢索策略
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 記憶架構設計
- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|來源筆記]]
