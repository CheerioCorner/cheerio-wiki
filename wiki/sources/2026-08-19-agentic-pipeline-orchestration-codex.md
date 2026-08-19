---
title: "企業界 Agentic Pipeline / Orchestration 解決方案調研（Codex）"
type: source
created: 2026-08-19
updated: 2026-08-19
sources: 20
tags: [ai-agent, agentic-pipeline, orchestration, enterprise-governance, durable-execution, skill-automation-design]
topics: [agent-architecture, skill-system]
provenance_raw: "raw/research/2026-08-19-agentic-pipeline-orchestration-codex.md"
---

# 企業界 Agentic Pipeline / Orchestration 解決方案調研（Codex）

> OpenAI Codex CLI（gpt-5.6-luna）針對長榮航空 IT 系統架構課「Skill 建置自動化」流程進行的企業級 Agentic Pipeline / Orchestration 解決方案調研。20 個 Tier 1 來源，12 個不同網站，引用以深連結為主。

## 核心結論

**目前業界尚未出現能直接涵蓋「分析→設計→開發→測試→Skill 發布」的標準產品。** 成熟做法是組合三層：

1. **Agent orchestration**（LangGraph / Microsoft Agent Framework / AutoGen）— 負責推理與路由
2. **Durable execution**（Temporal / Azure Durable Functions）— 負責長時間等待、crash 恢復與重試
3. **CI/CD gate**（GitHub Actions / Azure Pipelines / Backstage）— 負責版本控管、測試、審核與發布

核心設計原則：**讓 Agent 只能產生可審核的 artifact、Pull Request 或 approval request，由 deterministic pipeline 執行真正的 write operation。**

## 方案分類

| 類型 | 代表方案 | 強項 | 主要限制 |
|------|---------|------|---------|
| Agent graph framework | LangGraph | 狀態圖、分支、interrupt、可恢復執行 | 需自行處理權限、artifact、部署 |
| Multi-agent framework | Microsoft AF、AutoGen、CrewAI | Agent 協作、handoff、routing | 長時間執行需外接 durable workflow |
| Visual AI workflow | Dify、n8n | 快速設計、Connector、人工節點 | 複雜版本治理需補強 |
| Durable orchestration | **Temporal、Azure Durable Functions** | crash recovery、retry、timer、signal | 不負責 Agent prompt 品質 |
| Dev portal/scaffolding | Backstage | Template、權限、audit、標準工程流程 | 不是 Agent runtime |
| Repository automation | GitHub Actions + Agentic Workflows | PR、branch、review、CI、audit | 目前仍屬 Technical Preview |

## 關鍵發現：Durable Execution 層（Gemini 版本完全未提及）

Codex 額外指出一個重要的架構層——**Durable Execution**，用 `Temporal` 或 `Azure Durable Functions` 處理：

- **跨數小時到數天的人工核准等待**：LangGraph 的 `interrupt()` 只適合分鐘級暫停，企業級場景中架構師可能需要數小時才回覆
- **Crash 恢復與重試**：Agent framework 本身不保證 exactly-once execution，需要 durable orchestrator 處理
- **補償動作（Compensating Pattern）**：rollback 不是 Agent framework 內建能力，尤其對於已執行的外部副作用（寄信、建立帳號），Temporal 可用 workflow history + compensating pattern 建立反向操作

建議的架構組合：
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

## Checkpoint 設計建議（五類 Gate）

| Gate | 功能 |
|------|------|
| Contract Gate | 驗證每階段輸出是否符合固定格式（JSON Schema） |
| Policy Gate | 檢查 Skill 是否要求不必要的權限（網路、Credential、Shell） |
| Quality Gate | Static analysis + unit test + prompt regression test |
| Human Gate | 設計完成、程式碼 merge、測試通過三處提供人工核准 |
| Release Gate | 以 immutable artifact 發布，保留版本、hash、測試報告、核准者 |

### Checkpoint 綁定建議

Checkpoint 不應只記錄 Approve / Reject，而應綁定：`approval_id`、`artifact_hash`、`workflow_run_id`、`stage`、`risk_level`、`tool_arguments`、`reviewer`、`decision`、`decision_time`、`expires_at`。避免「人員核准了版本 A，但 Agent 重跑後實際執行版本 B」。

## Agentic vs 傳統 CI/CD 核心差異

| 面向 | 傳統 CI/CD | Agentic pipeline |
|------|-----------|-----------------|
| 輸入 | Git commit、固定參數 | 文件、使用者需求、非結構化內容 |
| Checkpoint | build / test / security scan / approval | 還需驗證 plan、tool call、模型輸出與權限 |
| Rollback | 版本切換、artifact redeploy | 還要處理 Agent 已執行的外部 side effect |
| 安全模型 | pipeline 權限誤用 | 還要防止 prompt injection、工具濫用 |

## 真實落地案例

- **GitHub Aspire 團隊**：PR merge 後 Agent 自動產出文件 PR → SME Review，396 次執行、82 個文件 PR 全部 merge
- **Chicago Trading Company**：Backstage Software Templates 模板化 Terraform + Jenkins + Flux CD，節省約 18 個月 Developer effort
- **Formula Bot**：n8n AI Agent 依需求動態路由至 BigQuery/Snowflake/MS SQL/Google Analytics

## 建議的 Skill 建置流程映射

| 階段 | Agent 工作 | 自動化驗證 | 人工 checkpoint |
|------|-----------|-----------|----------------|
| 分析 | 讀需求、盤點工具、辨識風險 | JSON Schema + 敏感資料掃描 | 高風險需求審核 |
| 設計 | 產生 Skill contract、I/O 格式 | Schema validation + policy lint | 架構與安全設計核准 |
| 開發 | 產生 SKILL.md、scripts、測試 | Static analysis + sandbox 執行 | Pull Request review |
| 測試 | 產生測試案例、執行 golden tests | Deterministic tests + LLM judge | 測試報告與發布核准 |
| 發布 | 建立版本、更新 registry | Package validation + 簽章 | 正式環境發布核准 |

## 來源（20 個 Tier 1，深連結）

| # | URL | 標題 |
|---|-----|------|
| 1 | https://docs.langchain.com/oss/python/langchain/human-in-the-loop | Human-in-the-Loop |
| 2 | https://learn.microsoft.com/en-us/agent-framework/workflows/human-in-the-loop | Agent Framework HITL |
| 3 | https://microsoft.github.io/agent-resources/develop-agents/ | Develop Agents |
| 4 | https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/graph-flow.html | GraphFlow |
| 5 | https://insights.crewai.com/ | AI Agent in Production |
| 6 | https://dify.ai/workflows | Agentic Workflows |
| 7 | https://docs.n8n.io/build/integrate-ai/ai-examples/human-in-the-loop-for-tools | n8n HITL for Tools |
| 8 | https://backstage.io/docs/next/software-templates/generated-index/ | Software Templates |
| 9 | https://backstage.io/docs/permissions/overview/ | Permissions Overview |
| 10 | https://temporal.io/ | Durable Execution |
| 11 | https://learn.microsoft.com/en-ca/azure/durable-task/common/durable-task-human-interaction | Human Interaction Pattern |
| 12 | https://github.blog/.../automate-repository-tasks-with-github-agentic-workflows/ | GitHub Agentic Workflows |
| 13 | https://github.blog/.../under-the-hood-security-architecture-of-github-agentic-workflows/ | Security Architecture |
| 14 | https://github.blog/.../automating-cross-repo-documentation-with-github-agentic-workflows/ | Cross-repo Documentation |
| 15 | https://github.blog/.../continuous-ai-for-accessibility.../ | Accessibility Feedback |
| 16 | https://backstage.io/blog/2023/09/29/chicago-traiding-company-adopter-spotlight/ | Chicago Trading Co. |
| 17 | https://n8n.io/case-studies/formula-bot/ | Formula Bot |
| 18 | https://campus.crewai.com/courses/crewai-production-engineer | CrewAI Production |
| 19 | https://docs.github.com/en/actions/reference/workflows-and-actions/deployments-and-environments | Deployments & Environments |
| 20 | https://learn.microsoft.com/mt-mt/azure/devops/pipelines/process/approvals | Pipeline Approvals |

## 相關頁面

- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|Gemini 調研]] — 同主題交叉比對版本（品質較低）
- [[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]] — 核心架構概念
- [[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]] — 關鍵補充層
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 上游治理框架
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 狀態持久化三路徑
- [[wiki/entities/langgraph|LangGraph]] — Agent graph framework 代表
