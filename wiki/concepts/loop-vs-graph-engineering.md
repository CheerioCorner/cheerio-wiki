---
title: "Loop vs Graph Engineering — AI Agent 兩種核心工作流模式"
type: concept
created: 2026-08-03
updated: 2026-08-03
sources: 1
tags: [ai-agent, loop-engineering, graph-engineering, system-design, workflow]
collection: concepts
topics: [ai-agent]
canonical: concepts/loop-vs-graph-engineering
---

> Loop 與 Graph 是 AI Agent 系統中兩種互補的工作流模式：Loop 讓 agent 自主迭代探索，Graph 用預定義流程平行執行。兩者共存而非替代。

## 核心定義

### Loop Engineering（迴圈工程）

Agent 自主決定下一步行動，反覆呼叫工具直到目標達成。

```
User → LLM → [需要工具？] → 是 → 選工具 → 執行 → 回傳結果 → LLM → 循環
                                      ↓
                                     否 → 回覆使用者
```

- **特性**：模型自主決策、一步一步執行、適合探索性任務
- **典型場景**：deep research、bug fix、開放式問答
- **範例**：Claude Code 修 GitHub PR — 自主決定用 web search、GitHub CLI、Supabase 等工具

### Graph Engineering（圖工程）

預定義節點（工具/LLM/agent call）與邊（路由），可平行或多序列執行。

```
User → Graph Start → [平行] → Node A (GitHub PR)
                            → Node B (Web Search)
                            → Node C (Calendar)
                            → Node D (Memory)
                          → Synthesize → [需要更多？] → 是 → 繼續
                                              ↓
                                             否 → 回覆使用者
```

- **特性**：預定義流程、可平行執行、適合已知 SOP
- **典型場景**：晨間簡報、客服 SOP、定期報告
- **範例**：Waku Agent gather graph — 同時查 GitHub + 網路 + 行事曆 + 記憶，然後綜合回覆

## 關鍵差異

| 維度 | Loop | Graph |
|------|------|-------|
| 決策者 | Agent（模型）自主決定 | 開發者預先定義 |
| 執行方式 | 一步一步序列 | 可平行執行 |
| 適合場景 | 探索性、未知流程 | 已知 SOP、重複性 |
| 彈性 | 高（模型自由發揮） | 低（流程固定） |
| 效率 | 可能多次不必要的呼叫 | 精準呼叫必要工具 |
| 範例工具 | MCP tools、web search | 定義好的 nodes + edges |

## 共存關係（重要）

**Graph 不取代 Loop**，兩者是互補的：

1. **Graph 內含 Loop**：graph 的某個節點可以是一個 agent loop（如 web search 節點内部是一個 loop）
2. **Loop 可被 Graph 編排**：多個 loop 可以被 graph 以平行或序列方式組織
3. **動態選擇**：routing 節點可用 LLM 判斷要走哪個 graph 或 loop

### 實際混合範例

```
Graph: Triage Workflow
  ├── Node: Classify (LLM) ──→ 需要複雜推理？──→ Agent Loop (用更多工具)
  │                                      ↓
  │                                     不需要 → 簡單回覆
  └── Node: Check Calendar (平行) ──→ 回傳結果
```

## AI Agent Engineering Ladder（演進脈絡）

```
Prompt Engineering (角色扮演)
  → Context Engineering (喂入數據)
    → Skills (procedural memory，固定流程)
      → Loop (agent 自主迭代)
        → Graph (預定義流程 + 非確定性節點)
```

Graph 是這個階梯的最新階段，結合了：
- Skills 的流程意識
- Loop 的 agent 自主性
- Deterministic workflow 的可靠性
- 新增：非確定性節點（LLM call）、模型 routing、guards

## 與 2023 Deterministic Workflow 的差異

2023 年的 Airflow / Step Functions 是純確定性的。Graph Engineering 的新之處：

1. **非確定性節點**：節點可以是 LLM call，每次執行結果可能不同
2. **模型 Routing**：LLM 作為 judge 決定走哪條邊
3. **Guards**：需要安全守護（以往 DAG 沒有）
4. **混合架構**：graph 內可以包含 loop

## 實作框架

- **Waku Agent**：`waku/graph/` 目錄定義 graph engine + workflows
- **LangGraph**：LangChain 生態的 graph 實作
- **Custom**：plain Python 定義 nodes + edges（如 Waku 的做法）

## 來源
- [[wiki/sources/2026-08-03-loop-vs-graph-engineering|Loop vs Graph Engineering — YouTube video]]

## 相關頁面
- [[wiki/entities/waku-agent|waku-agent]] — 展示 loop + graph 的實際框架
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 簡約設計哲學
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼流程
- [[wiki/entities/hermes-agent|hermes-agent]] — 同類 local-first agent
