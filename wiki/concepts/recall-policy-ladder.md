---
title: "Recall Policy Ladder — 記憶檢索策略的效果階梯"
type: concept
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [memory, recall-policy, decision-ledger, rag, evaluation]
topics: [agent-memory-context]
canonical: concepts/recall-policy-ladder
---

# Recall Policy Ladder — 記憶檢索策略的效果階梯

> 四階記憶檢索策略（no recall → vector RAG → decision-ledger → oracle）在長時任務中的實測效果排名。核心發現：好的 recall policy 不只更準，還更省 token。

## 定義

Recall Policy Ladder 是 Stefania Druga（Sakana AI）在實驗長時間運行 research agent 時提出的記憶檢索策略分類框架。它按「策略在長時任務下的效果排名」組織，與 [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] 的「按技術類型分類」是**不同的分類軸**。

## 四個層級

### Layer 1：No recall（無檢索）
- **基線**：不使用任何記憶系統 `[04:34]`
- 適用：task 能完整塞進 context window 時
- 成本：最低

### Layer 2：Vector RAG（向量相似度檢索）
- 使用向量嵌入做相似度搜尋 `[04:40]`
- 適用：需要語意搜尋但不需要決策追蹤的場景
- 成本：中等

### Layer 3：Decision-ledger（決策帳本）
- 記錄每一步的決策，然後動態排序檢索 `[04:50]`
- **效果最好**的實用策略 `[08:17]`
- 比單純 gate（判斷「要不要用記憶」）更好 `[08:24]`
- 成本：較低（因為更精確，減少無用 token）`[09:36]`

### Layer 4：Oracle（預知答案）
- Ground truth：直接告訴 harness 正確記憶是什麼 `[05:00]`
- **不是满分**——模型可以拿到正確記憶但選擇忽略或被混淆 `[08:32]`
- 用於實驗對照，非實用策略

## 核心實驗結論

### Context 邊界法則
- **Task 能塞進 context** → 記憶不加分，只加成本（token + latency）`[06:14]`
- **Task 超出 context** → ranked recall policy 顯著提升準確率 `[07:49]`

### 成本觀點
「Bad memory is expensive」`[09:41]`——壞的記憶策略花更多 token，還可能把 agent 帶偏。好的 recall policy 不只更準，還更省。

### 跨模型通用性
- Qwen 27B (4-bit) ✅ `[09:22]`
- DeepSeek V4 Flash ✅ `[09:22]`
- 跨 benchmark：X-Bench ✅、Spider V2 ✅ `[09:30]`

## 與既有框架的關係

### 與 [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] 的互補
既有頁面的「四種檢索方式」（do nothing / keyword FTS5 / RAG / Graph RAG）按**技術類型**分類。Recall Policy Ladder 按**效果排名**分類，且多了 **decision-ledger**（逐步記錄決策並排序），這是既有頁面完全沒有的策略。

兩者不衝突，建議互相連結：
- 技術選型 → 參考四種檢索方式
- 效果排名 → 參考 Recall Policy Ladder

### 與 [[wiki/concepts/context-rot|Context Rot]] 的關聯
Context Rot 是觸發條件（長任務導致脈絡腐化），Recall Policy Ladder 是解方（如何在長任務中有效檢索記憶）。

## 設計啟示

1. **不要無腦加記憶**：先判斷 task 是否超出 context window
2. **Recall policy 是 first-class metric** `[09:59]`：不只是「要不要用 RAG」，而是「用什麼策略排序和檢索」
3. **Decision-ledger 值得嘗試**：比單純 vector RAG 更好，且成本更低
4. **壞記憶比沒有記憶更糟**：花更多 token，還可能誤導 agent

## 相關頁面

- [[wiki/sources/2026-08-20-memory-harnesses-long-running-research-agents|來源筆記]] — 完整實驗細節
- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 三支柱記憶架構（互補分類）
- [[wiki/concepts/context-rot|Context Rot]] — 觸發條件
- [[wiki/concepts/context-decay|Context Decay]] — 跨模型交接脈絡遺失
