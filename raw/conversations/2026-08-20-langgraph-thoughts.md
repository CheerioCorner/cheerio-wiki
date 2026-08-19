---
title: "LangGraph 影片觀後想法"
type: raw-conversation
source_kind: thought
created: 2026-08-20
related_raw: "raw/youtube/langgraph-in-10-minutes.md"
---

# LangGraph in 10 Minutes 觀後想法

[[raw/youtube/langgraph-in-10-minutes|LangGraph in 10 Minutes (Explained Clearly)]]

## 當下想法

看完這部影片，覺得 LangGraph 的 state machine 概念跟我们目前在做的 agent harness 很像——都是用 graph 來描述 agent 的執行流程，差別在於 LangGraph 是 Python library，而 Pi 是 TypeScript harness。值得深入比較兩者的 state management 方式。

特別是影片提到的 checkpoint / persistence 機制，跟 W-2026-08-057 在研究的 durable execution 概念直接相關。可以考慮把這部影片的重點整理進 wiki，作為 agent architecture 的參考資料。
