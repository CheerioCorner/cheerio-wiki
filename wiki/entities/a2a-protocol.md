---
title: "A2A（Agent2Agent Protocol）"
type: entity
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [a2a, agent-to-agent, google, protocol, agent-card, linux-foundation]
topics: [agent-infrastructure]
canonical: entities/a2a-protocol
provenance:
  - kind: raw
    path: raw/youtube/ibm-agentic-ai-five-terms.md
    url: https://www.youtube.com/watch?v=k5jYwyhDMxA
---

# A2A（Agent2Agent Protocol）

> Agent 間通訊的開放協定，讓不同 agent 能互相發現、溝通與協作。

## 核心概念

A2A 解決的問題：**不同 agent 之間如何互相找到對方、理解對方能做什麼、並協調工作？**

```
Agent A (採購)
    │
    ├── 讀取 Agent Card → 找到 Agent B (財務)
    │
    ├── 理解 Agent B 的能力與溝通方式
    │
    └── 委派工作：請 Agent B 審核合約
         │
         └── Agent B 回傳結果
```

## 關鍵機制：Agent Card

每個 agent 發佈一個 **Agent Card**，內容包括 `[07:29]`：
- 這個 agent 做什麼（capabilities）
- 怎麼跟它溝通（communication interface）
- 其他 agent 可以讀取这张卡來決定如何委派工作 `[07:35]`

## 與 MCP 的對比

| 面向 | MCP | A2A |
|------|-----|-----|
| 連接對象 | Agent ↔ 工具/資料 | Agent ↔ Agent |
| 起源 | Anthropic | Google |
| 標準組織 | Agentic AI Foundation | Agentic AI Foundation |
| 核心機制 | MCP Server（包裝工具介面） | Agent Card（描述能力） |
| 適用場景 | 串接 Notion、Stripe 等外部服務 | 跨團隊/跨組織的 agent 協作 |

> MCP 是 agent 的「USB-C 接口」（連接工具），A2A 是 agent 的「社交協定」（認識其他 agent）。

## 治理沿革

⚠️ 影片聲稱 A2A 現由 Agentic AI Foundation（Linux Foundation 底下）治理。此為單一影片來源說法，待進一步查證。

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — Agent ↔ 工具連接協定（對比）
- [[wiki/concepts/sub-agents-pattern|Sub-agents]] — Agent 內部的子任務委派（不同機制）
- [[wiki/concepts/agents-md-format|AGENTS.md]] — Agent 行為配置
- [[wiki/concepts/agentic-ai|Agentic AI]] — AI Agent 核心概念
- [[wiki/sources/2026-09-04-ibm-agentic-ai-five-terms|IBM: 5 Terms for Agentic AI]] — 來源
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — 協定導航
