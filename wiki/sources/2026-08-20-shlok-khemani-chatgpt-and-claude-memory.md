---
title: "Lessons from Studying Every Memory System — Shlok Khemani"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [chatgpt, claude, memory, personalization, consumer-ai]
topics: [agent-memory-context]
provenance_url: "https://finance.biggo.com/podcast/21c6a495ef5a2b19"
---

# Lessons from Studying Every Memory System — Shlok Khemani

> ⚠️ 本文探討 ChatGPT/Claude 消費級產品的個人化記憶系統，**與 Stefania Druga 的 memory harnesses 研究無關**。不要與 recall-policy-ladder 或 context-rot 混淆。

## 講者

Shlok Khemani，獨立研究員，花一年 reverse-engineering ChatGPT、Claude、Gemini、Poke 的記憶系統。

## 核心發現：三年收斂

ChatGPT 和 Claude 從相反設計出發，2026 年中收斂到相同模式：**running profile + on-demand retrieval** `https://finance.biggo.com/podcast/21c6a495ef5a2b19`。

### 演化時間線

**ChatGPT:**
- 2023: 無記憶（amnesia）
- 2024-02: Memory v1 — fact list（使用者手動管理）
- 2025-04: Memory v2 — running profile（background dreaming）
- 2026: 加入 on-demand retrieval + profile 可見

**Claude:**
- 2025-08: v1 — 無 profile，只有 on-demand retrieval tools
- 2025-09: v2 — 加入 running profile（刻意不同設計）

### 兩者差異

| 維度 | ChatGPT | Claude |
|------|---------|--------|
| Profile 大小 | ~4,000 tokens, 16 sections | ~1,000 tokens |
| 記憶風格 | Dense, keyword-like | Complete sentences |
| 更新頻率 | 每幾天 | 每 24 小時 |
| 使用者可見性 | 2026-06 才可見（LLM summary） | 2025-09 起可見（raw profile） |
| 編輯方式 | 2026-06 起支援 | Edits trigger re-synthesis |

## 三個教訓

### 1. 沒有 canonical memory architecture

「There is no single way to do memory.」各產品都自己建，不外包。Gemini 也用 running profile（per-memory timing logs）。Agent 生態系（Claude Code、OpenClaw、Hermes）用 markdown files、heartbeat processes、knowledge bases。

### 2. Memory is a function of compute

記憶設計是經濟問題。Profile 有兩種成本：
- **Maintenance cost**: 更新頻率 × 每次更新的 compute
- **Serving cost**: profile 在 context window 中的 token 成本

ChatGPT 選高 serving cost（4k tokens, 每幾天更新），Claude 選高 maintenance cost（1k tokens, 每 24 小時更新）。

### 3. Continual learning 已經在外面了

Running profile 就是 continual learning system：模型信念 → 套用到對話 → 產生新資訊 → dreaming 合成回 profile → 影響下一輪對話。learning 發生在 model weights 之外。

## 瓶頸轉移

最關鍵的結論：**瓶頸已從 memory architecture 轉移到 context gathering**。Khemani 的 ChatGPT profile 有一個矛盾（Thailand vs Turkey），但系統既不會偵測也不好奇——因為證據在面對面對話和 email 裡，模型永遠讀不到。「None of this feels like 2026.」

## ⚠️ 與其他頁面的關係

本篇探討的是 **消費級 AI 產品的使用者個人化記憶**，與以下頁面是**不同領域**：
- `wiki/concepts/ai-agent-memory-systems.md` → 工程師如何幫 agent/harness 設計記憶系統
- `wiki/concepts/recall-policy-ladder.md` → Stefania Druga 的 coding/research agent 記憶策略排名

兩者字面上都叫 "memory"，但應用場景完全不同。已在相關頁面加交叉連結但明確劃界。

## 相關頁面

- [[wiki/concepts/ai-agent-memory-systems|AI Agent Memory Systems]] — 不同領域（agent harness 記憶 vs 消費級個人化）
- [[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]] — 不同領域（research agent 記憶策略）
- [[wiki/concepts/context-rot|Context Rot]] — 本篇提到的 context gathering 瓶頸是另一種 context 問題
