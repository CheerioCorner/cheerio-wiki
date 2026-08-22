---
title: "Loop vs Graph Engineering 影片觀後想法：輕量小 agent 的架構設計"
type: raw-conversation
source_kind: thought
created: 2026-08-22
related_raw: "raw/youtube/loop-vs-graph-engineering.md"
---

# Loop vs Graph Engineering 觀後想法

[[raw/youtube/loop-vs-graph-engineering|Loop vs Graph Engineering — AI Agent Concepts Demystified]]

## 當下想法

重新看這部影片，對 AI Agent 工程階梯（Prompt Engineering → Context Engineering → Skill → Loop Engineering → Graph Engineering）的演進脈絡有更深一層的理解，尤其是 Sean 對 Loop 與 Graph 的定義非常清楚：

- **Loop**：強調讓 AI 自行決定步驟，適用於探索性或解決問題的任務（05:23-08:22）。
- **Graph**：指預先定義的執行流程，適合標準化作業，並能實現平行處理，提高效率（06:14-10:58）。

Sean 透過 Waku Agent 這個開源 harness，說明了 Loop Engineering 與 Graph Engineering 如何並存——這兩者各有各自的做法，對打造 Cheerio 很有幫助。

### 新想法：輕量小 agent 也能套用 Graph Engineering 的內部架構

影片展示的 Graph Engineering 特定架構有一個關鍵細節：整體可以並行處理；每一個被調用的單元（不管是 scan the tool 還是 scan the state）本身都是一個小 loop，最後彙整到 summarize 階段，之後才會繼續往後推進。

這套邏輯不只能用在整個 harness 上，我覺得也很適合拿來做「輕量小 agent」：

- 自己寫一個小 agent，不需要像一般 harness 那麼豐富或複雜，單純作為某個領域專家的 agent。
- 它不依賴於特定 harness，只要有權限的任何 harness 都可以調用它（換句話說，這個小 agent 是可攜式的專家單元，不綁定 Pi 或 Claude Code 這類特定執行環境）。
- 這個 agent 內部的架構設計，就非常適合套用 Graph Engineering：並行 scan 各個單元 → 每個單元自己是一個小 loop → 彙整到 summarize 階段 → 才繼續往後推進。

這跟 [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] 概念頁裡「Graph 內含 Loop」的觀點一致，但這裡的延伸點是：把這個 pattern 從「harness 等級」下放到「單一領域專家小 agent 等級」，作為 Cheerio 專案裡打造可攜式、可被任意 harness 調用的專家 agent 的設計方向。值得跟 [[wiki/entities/waku-agent|waku-agent]] 的 graph 實作方式（`graph.py` 定義 engine、`workflows/` 放具體 workflow、nodes + edges）對照，看看能不能抽出一個更輕量的版本用在 Cheerio 的小 agent 上。
