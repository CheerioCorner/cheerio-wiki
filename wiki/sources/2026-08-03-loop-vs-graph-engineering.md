---
title: "Loop vs Graph Engineering — AI Agent Concepts Demystified"
type: source
created: 2026-08-03
updated: 2026-08-03
sources: 1
tags: [ai-agent, loop-engineering, graph-engineering, system-design, waku-agent]
collection: sources
topics: [agent-architecture]
provenance:
  - kind: youtube
    path: raw/youtube/loop-vs-graph-engineering.md
canonical: sources/2026-08-03-loop-vs-graph-engineering
---

> 來源：[YouTube — Loop vs Graph Engineering — AI Agent Concepts Demystified](https://youtu.be/IMLwvK08JVc)
> 原始字幕：[[raw/youtube/loop-vs-graph-engineering|raw transcript]]

## 一句話

Sean Chen 解釋 AI Agent 工程的演進階梯（Prompt → Context → Skills → Loop → Graph），並用 Waku Agent 展示 loop 與 graph 的實際差異與共存關係。

## 重點摘要

### AI Agent Engineering Ladder（工程階梯）

| 階段 | 核心概念 | 類比 |
|------|----------|------|
| Prompt Engineering | 角色扮演，控制 LLM 回應方式 | 告訴 LLM「你是李白」 |
| Context Engineering | 喂入數據（CRM、客戶資料）讓 agent 能實際工作 | 不只角色，还要給資料 |
| Skills | Procedural memory，教 agent 固定流程 | 告訴小孩「在美國走右邊」 |
| Loop | Agent 自主迭代，用可用工具達成目標 | 「去修 bug，修完告訴我」 |
| Graph | 預定義工作流，節點 + 邊，可平行執行 | 已知 SOP，平行跑多個工具 |

### Loop vs Graph 核心差異

**Loop（迴圈）**
- Agent 決定下一步做什麼，一步一步來
- 適合：探索性任務（deep research、bug fix）
- 範例：Claude Code 修 GitHub PR bug，自主決定用哪些工具

**Graph（圖）**
- 預定義流程，節點（工具/LLM/agent call）+ 邊（路由）
- 可平行執行多個工具
- 適合：已知 SOP、重複性工作流
- 範例：晨間簡報（同時查 GitHub PR + 網路搜尋 + 行事曆 + 記憶）

### 關鍵觀點

1. **不是替代關係**：Graph 不取代 Loop，兩者共存
2. **Graph 內含 Loop**：graph 的某個節點可以是一個 agent loop（如 web search 節點）
3. **Loop 適合探索**：模型決定一步做什麼；Graph 適合已知流程
4. **與 2023 deterministic workflow 的差異**：
   - 節點可以是非確定性的（LLM call）
   - 模型可以選擇邊（routing）
   - 需要 guards（以往 DAG 調度沒有的）

### Waku Agent 實作展示

- Dashboard 展示 triage graph（分類 + 行事曆平行）和 gather graph（GitHub + 網路 + 行事曆 + 記憶平行）
- Graph 定義在 `waku/graph/` 目錄，`graph.py` 定義 engine，`workflows/` 放具體 workflow
- 節點類型：tool call、LLM call、agent call、router
- 使用者可貢獻自己的 graph workflows

## 來源
- [[raw/youtube/loop-vs-graph-engineering|Raw transcript — YouTube IMLwvK08JVc]]

## 相關頁面
- [[wiki/entities/waku-agent|waku-agent]] — 影片中展示的 AI agent 框架
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 概念頁
- [[wiki/entities/hermes-agent|hermes-agent]] — 同類 local-first agent
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 簡約設計哲學
