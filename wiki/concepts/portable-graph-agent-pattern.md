---
title: "Portable Graph Agent Pattern — 可攜式 Graph 專家 Agent 模式"
type: concept
created: 2026-08-22
updated: 2026-08-22
sources: 0
tags: [agent-design, graph-engineering, portable-agent, lightweight]
collection: concepts
topics: [agent-architecture]
canonical: concepts/portable-graph-agent-pattern
related_raw: "raw/conversations/2026-08-22-loop-graph-engineering-thoughts.md"
---

> 將 Graph Engineering 的「並行 scan → 小 loop → 彙整」架構從 harness 層級下放到「輕量領域專家小 agent」，打造不綁定特定 harness、可被任意執行環境調用的可攜式專家單元。

## 核心概念

[[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] 展示的 Graph pattern 在 harness 等級運作良好：整體並行處理，每一個被調用的單元（不管是 scan the tool 還是 scan the state）本身都是一個小 loop，最後彙整到 summarize 階段，之後才繼續往後推進。

本頁探討的延伸方向是：**把這套 pattern 下放到「單一領域專家小 agent」等級**。

### 從 Harness 層級到 Agent 層級

| 維度 | Harness 層級（既有） | Agent 層級（本 pattern） |
|------|---------------------|------------------------|
| 範圍 | 整個 agent 系統的頂層編排 | 單一領域專家小 agent 的內部架構 |
| 節點類型 | tool call、LLM call、agent call、router | 更小粒度的 scan / process / summarize 單元 |
| 目標 | 平行協調多個不同 agent 或工具 | 在一個小 agent 內部平行處理多個子任務 |
| 繫結 | 通常與特定 harness 耦合 | 可攜式，不綁定特定 harness |

### 可攜式專家 Agent 的三個特徵

1. **輕量專注**：不需像一般 harness 那麼豐富或複雜，單純作為某個領域專家的 agent。
2. **Harness 獨立**：不依賴於特定 harness，只要有權限的任何 harness 都可以調用它。換句話說，這個小 agent 是**可攜式的專家單元**，不綁定 Pi、Claude Code 這類特定執行環境。
3. **內部 Graph 化**：內部架構套用 Graph Engineering pattern——並行 scan 各個單元 → 每個單元自己是一個小 loop → 彙整到 summarize 階段 → 才繼續往後推進。

### 為什麼「Graph 內含 Loop」在小 agent 層級同樣成立

既有 [[wiki/concepts/loop-vs-graph-engineering|loop-vs-graph-engineering]] 概念頁已經指出「Graph 內含 Loop」——graph 的某個節點可以是一個 agent loop。本 pattern 進一步說明：即使 agent 本身就是一個小單元，它的內部仍然可以（且適合）用 Graph pattern 來組織，因為：

- 領域專家的工作往往有**可預先定義的 SOP**（對應 Graph 的預定義流程）。
- SOP 中每個步驟可能需要**自主決策**（對應 Loop 的模型自主性）。
- 並行執行能**提升效率**——多個 scan 單元同時跑，比序列執行快。

### 潛在實作方向

值得與 [[wiki/entities/waku-agent|waku-agent]] 的 graph 實作方式（`graph.py` 定義 engine、`workflows/` 放具體 workflow、nodes + edges）對照，看看能不能抽出一個更輕量的版本，用在 Cheerio 專案的小 agent 上。

關鍵問題：如何把 waku-agent 的 `waku/graph/` 目錄結構（engine + workflows + nodes/edges）精簡成一個不依賴 waku harness 的獨立單元？

## 設計假設

1. **領域專家的小 agent 不需要完整的 harness 功能**——它只需要接受輸入、內部用 Graph pattern 處理、回傳結果。
2. **可攜性來自介面標準化**——只要輸入/輸出格式固定（如 MCP tool schema 或 function calling），任何 harness 都能調用。
3. **Graph pattern 在小規模下仍然值得**——即使只有 3-5 個節點，並行 scan + summarize 的架構仍比純序列 loop 更清晰、更有效率。

## 來源

本頁為 Cheer 的原創延伸想法，基於對 [[wiki/sources/2026-08-03-loop-vs-graph-engineering|Loop vs Graph Engineering]] 影片的二次觀看心得。

想法原文：[[raw/conversations/2026-08-22-loop-graph-engineering-thoughts|loop-vs-graph-engineering 觀後想法]]

## 相關頁面

- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 延伸自本頁的上位概念（影片原始內容）
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」的簡約設計哲學，與本 pattern 的輕量理念一致
- [[wiki/concepts/harness|harness]] — Harness 定義；本 pattern 強調小 agent 與 harness 解耦
- [[wiki/entities/waku-agent|waku-agent]] — Graph Engineering 的實際框架對照（`graph.py` + `workflows/`）
- [[wiki/entities/omnigent|omnigent]] — Meta-harness 概念，潛在的可攜式 agent 調用者
- [[wiki/entities/pi-mono|pi-mono]] — Pi monorepo，另一個潛在的 harness 調用者
