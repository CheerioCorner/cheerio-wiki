---
title: "Agentic Pipeline Checkpoint 設計 — 從 Agent 產出到正式發布的多層把關"
type: concept
created: 2026-08-19
updated: 2026-08-19
sources: 2
tags: [ai-agent, agentic-pipeline, checkpoint, hitl, enterprise-governance, skill-automation-design]
topics: [agent-architecture, skill-system]
canonical: concepts/agentic-pipeline-checkpoint-design
---

> Agentic Pipeline 的 Checkpoint 與傳統 CI/CD 的根本差異：不只是「build pass/fail」，還必須驗證 Agent 的推理意圖、工具呼叫與語意品質。本頁彙整 Gemini + Codex 兩份調研的架構共識。

## 核心原則

**讓 Agent 只能產生可審核的 artifact（proposal），真正的 write operation 必須穿過 deterministic policy 與既有 CI/CD gate。**

Agent 不直接修改正式環境。Agent 的輸出限定為：branch、Pull Request、artifact 或 approval request。

## 雙層驗證機制

Gemini 與 Codex 兩份調研的共同核心發現——**雙層驗證機制**：

### 第一層：自動確定性閘門（Deterministic Gate）

Agent 產出 Skill 程式碼後，自動丟入 CI 跑：

- Unit test
- Security scan（SAST）
- Schema validation
- Dependency scan

**若失敗**：自動擷取 traceback，作為 prompt 回傳給 Agent 進行 Self-Repair（Reflexion Loop），直到 pass 或達到最大重試上限。

### 第二層：語意與品質驗證閘門（Semantic Checkpoint）

確定性測試通過後，進行：

- LLM Judge / Agent Evaluator（語意品質判斷）
- Prompt regression test
- Policy lint（權限檢查）

### 第三層：人工審核關卡（Interrupt & Persist Gate）

測試全數通過後，觸發持久化：

- LangGraph `interrupt()` 或 Temporal signal
- 將設計文件、代碼 Diff、測試報告存入資料庫
- 推播審核通知（Slack / Teams / Email）
- 架構師點擊簽核後帶入 `resume` 憑證

## 五類 Gate 設計（Codex 建議）

| Gate | 功能 | 檢查內容 |
|------|------|---------|
| **Contract Gate** | 格式驗證 | 每階段輸出是否符合固定格式（`analysis.json`、`design.yaml`、`skill-manifest.json`、`test-report.json`） |
| **Policy Gate** | 權限檢查 | Skill 是否要求不必要的網路、檔案、Credential、Shell 或外部 API 權限 |
| **Quality Gate** | 品質驗證 | Schema validation + static analysis + unit test + integration test + prompt regression test |
| **Human Gate** | 人工審核 | 設計完成、程式碼 merge、測試通過三處提供人工核准 |
| **Release Gate** | 發布控管 | 以 immutable artifact 發布，保留版本、hash、測試報告、核准者、時間戳記 |

### Checkpoint 綁定建議

Checkpoint 不應只記錄 `Approve / Reject`，而應綁定：

```
approval_id
artifact_hash
workflow_run_id
stage
risk_level
requested_action
tool_arguments
reviewer
decision
decision_time
expires_at
```

這可以避免「人員核准了版本 A，但 Agent 重跑後實際執行版本 B」的問題。

## Agentic vs 傳統 CI/CD Checkpoint 對比

| 面向 | 傳統 CI/CD | Agentic pipeline |
|------|-----------|-----------------|
| 輸入 | Git commit、固定參數 | 文件、使用者需求、非結構化內容 |
| 執行行為 | Deterministic | 因模型、context、tool 結果而變動 |
| Checkpoint | build / test / approval | 還需驗證 plan、tool call、模型輸出與權限 |
| 審核單位 | 審核 artifact | 還要審核 Agent 意圖、推理結果與預計副作用 |
| 失敗處理 | 停止並通知 | Reflexion Loop 自動修復 |
| Rollback | 版本切換 | 還要處理 Agent 已執行的外部 side effect |
| 安全模型 | pipeline 權限誤用 | 還要防止 prompt injection、工具濫用 |
| 可觀測性 | logs / metrics / traces | 還需保存 prompt、model、tool call、context、decision |

## 漸進式授權（Gradual Autonomy）

企業落地的最佳實踐——**先嚴後鬆**：

1. **初期**：100% Approval Gate，透過 Audit Log 記錄 Agent 生成結果與人工修正歷程
2. **中期**：當 Agent 在特定 Skill 類型的精準度達到閾值後，關閉前中段審核
3. **成熟期**：僅保留最終部署前及風險 API 呼叫的 Approval Gate

低風險 Skill 可以自動通過，高風險 Skill 必須指定 reviewer。

## 自動化驗證流程映射

| 階段 | Agent 工作 | 自動化驗證 | 人工 checkpoint |
|------|-----------|-----------|----------------|
| 分析 | 讀需求、盤點工具、辨識風險 | JSON Schema + 敏感資料掃描 | 高風險需求審核 |
| 設計 | 產生 Skill contract、I/O 格式 | Schema validation + policy lint | 架構與安全設計核准 |
| 開發 | 產生 SKILL.md、scripts、測試 | Static analysis + sandbox 執行 | Pull Request review |
| 測試 | 產生測試案例、執行 golden tests | Deterministic tests + LLM judge | 測試報告與發布核准 |
| 發布 | 建立版本、更新 registry | Package validation + 簽章 | 正式環境發布核准 |

## 核心架構組合建議

```
Temporal / Azure Durable Functions
    └── LangGraph 或 Microsoft Agent Framework
            ├── Analysis Agent
            ├── Design Agent
            ├── Development Agent
            └── Test / Reviewer Agent
    └── GitHub Actions / Azure Pipelines
            ├── deterministic tests
            ├── security scan
            ├── approval gate
            └── release / rollback
```

三層分工：Agent framework 擅長推理路由，durable orchestrator 擅長等待恢復，CI/CD 擅長可重複驗證。

## 來源

- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研]] — 20 個 Tier 1 來源，深連結，五類 Gate 建議
- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|Gemini 調研]] — 7 個 Tier 1 來源，雙層驗證機制首提

## 相關頁面

- [[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]] — 長時間執行可靠性層
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — Agent framework 內部持久化路徑
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 上游治理框架
- [[wiki/concepts/agentic-ai|Agentic AI]] — Agent 核心定義
- [[wiki/entities/langgraph|LangGraph]] — Agent graph framework 代表
