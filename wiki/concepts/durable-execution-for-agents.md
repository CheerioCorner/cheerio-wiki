---
title: "Durable Execution for Agents — Agent 框架之外的可靠性層"
type: concept
created: 2026-08-19
updated: 2026-08-19
sources: 2
tags: [ai-agent, durable-execution, temporal, azure-durable-functions, crash-recovery, enterprise-governance]
topics: [agent-architecture, skill-system]
canonical: concepts/durable-execution-for-agents
---

> Agent framework（LangGraph、AutoGen 等）擅長推理與路由，但不擅長「等待數小時的人工核准」或「crash 後恢復到正確狀態」。Durable Execution 平台（Temporal、Azure Durable Functions）填補這段空白。

## 核心問題

Agent 跑到一半 crash 了，或是需要等架構師花 4 小時審核一份設計文件——這兩種場景都需要「跨時間的可靠性保證」，但這不是 Agent framework 的本職。

| 場景 | Agent Framework 能處理嗎？ | 為什麼 |
|------|--------------------------|-------|
| 分鐘級 tool call 失敗重試 | ✅ 可以 | 框架內建 retry |
| 等待數小時的人工核准 | ⚠️ 不理想 | `interrupt()` 只是暫停執行，不保證進程存活 |
| Agent 進程 crash 後恢復 | ⚠️ 有限 | Checkpointer 只存 state，不存「等在哪個 step」的執行上下文 |
| 跨數天的多階段審核流程 | ❌ 不行 | 需要 external signal + timer + compensating pattern |
| 已執行副作用的 rollback | ❌ 不行 | 需要應用層設計補償動作 |

## Durable Execution 是什麼

Durable Execution 平台的核心能力：**把程式的執行狀態持久化到外部儲存，讓進程 crash 後可以從最後一個 checkpoint 恢復，不丟失任何步驟。**

### Temporal

- **定位**：Durable execution 平台，核心是 workflow + activity + signal + timer
- **關鍵能力**：
  - Workflow history（完整執行軌跡）
  - External signal（等待外部事件，如人工核准）
  - Timer（等待逾時、排程觸發）
  - Compensating pattern（撤銷已執行的副作用）
  - Crash recovery（進程死亡後自動從 checkpoint 恢復）
- **與 Agent Framework 的關係**：Temporal 不負責 Agent 的 prompt、品質評估或 Skill 設計——它只負責「確保每個 step 最終都會完成，不管中間 crash 幾次」

### Azure Durable Functions

- **定位**：Microsoft 的 durable execution 實作，整合在 Azure 生態系中
- **關鍵能力**：
  - Human interaction pattern（等待外部事件的標準模式）
  - Orchestration replay（重播執行軌跡恢復狀態）
  - 與 Azure DevOps / GitHub Actions 整合
- **適用場景**：已在 Azure 生態系中的企業，可直接使用

## 建議的三層架構

Codex 調研提出的三層分工：

```
┌─────────────────────────────────────────┐
│  Layer 3: CI/CD Gate                     │
│  GitHub Actions / Azure Pipelines        │
│  → deterministic tests, security scan    │
│  → approval gate, release, rollback      │
├─────────────────────────────────────────┤
│  Layer 2: Durable Execution              │
│  Temporal / Azure Durable Functions      │
│  → crash recovery, retry, timer, signal  │
│  → 等待數小時的人工核准                    │
│  → compensating pattern (rollback)       │
├─────────────────────────────────────────┤
│  Layer 1: Agent Orchestration            │
│  LangGraph / Microsoft Agent Framework   │
│  → reasoning, routing, tool calls        │
│  → 分析、設計、編碼邏輯                    │
└─────────────────────────────────────────┘
```

**分工原則**：Agent framework 擅長「推理與路由」，durable orchestrator 擅長「等待、恢復與重試」，CI/CD 擅長「可重複驗證與正式發布」。三者責任邊界清楚，較不容易把所有可靠性問題塞進 Prompt。

## 與 Agent Durability Patterns 的關係

[[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] 描述的是 Agent framework **內部**的三種持久化路徑（While Loop / Session Tree / Checkpointer）。Durable Execution 是**外部**的第四種路徑——不在 Agent framework 內部，而是在它之下：

| 維度 | Checkpointer（路徑 3） | Durable Execution（路徑 4） |
|------|----------------------|---------------------------|
| 控制範圍 | Agent state（prompt、memory、tool calls） | Workflow execution（step ordering、retry、signal） |
| Crash 恢復 | 恢復 Agent 對話 context | 恢復整個執行流程（含等待中的 signal） |
| 人工介入 | `interrupt()` / `resume()` | External signal + timer |
| 等待時間上限 | 分鐘級（進程需存活） | 無上限（狀態在外部儲存） |
| Rollback | 不支援 | Compensating pattern |

## 在 Skill 建置自動化中的應用

以四階段 Skill 自動化為例：

| 階段 | Agent 做什麼 | Durable Execution 做什麼 |
|------|------------|------------------------|
| 分析 | 讀需求、盤點工具 | 持久化分析結果，確保 crash 後不需重跑 |
| 設計 | 產生 Skill contract | 等待架構師核准（可能數小時），不卡住進程 |
| 開發 | 產生程式碼、跑測試 | 失敗重試（最多 N 次），超過才 escalate |
| 測試 | 執行 golden tests | 等待測試結果，逾時自動通知 |

## 來源

- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研]] — 20 個 Tier 1 來源，Durable Execution 層是 Codex 額外發現
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|Gemini 調研]] — 同主題交叉比對版本（未提及 Durable Execution）

## 相關頁面

- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — Agent framework 內部的持久化路徑
- [[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]] — Checkpoint 整體架構
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 上游治理框架
- [[wiki/entities/langgraph|LangGraph]] — Agent graph framework 代表
