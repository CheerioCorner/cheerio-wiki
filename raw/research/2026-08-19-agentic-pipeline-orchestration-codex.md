---
title: "企業界 Agentic Pipeline / Orchestration 解決方案調研（Codex）"
type: raw-research
created: 2026-08-19
agent: codex
model: gpt-5.6-luna
topic: "ai-agent"
subtopics:
  - "skill-automation-design"
  - "agentic-pipeline"
  - "orchestration"
  - "durable-execution"
  - "enterprise-governance"
sources_count: 20
sources_tier1: 20
sources_tier2: 0
sources_tier3: 0
sources_diversity: 12
related_work: W-2026-08-056
tags: [research, codex, ai-agent, skill-automation-design, agentic-pipeline, enterprise-governance]
---

# 研究報告：企業界 Agentic Pipeline / Orchestration 解決方案調研

> **建立日期**：2026-08-19
> **專案/情境**：長榮航空 IT 系統架構課——「Skill 建置自動化」流程機制調研（work item W-2026-08-056）
> **產出方式**：OpenAI Codex CLI（`codex exec`），web_search 工具，前景執行

## Executive Summary

目前業界尚未出現一個能直接涵蓋「分析 → 設計 → 開發 → 測試 → Skill 發布」的標準產品。較成熟的做法是組合三層：以 LangGraph、Microsoft Agent Framework、AutoGen 等負責 Agent 流程；以 Temporal、Azure Durable Functions 等負責長時間執行、重試與恢復；以 GitHub Actions、Azure Pipelines、Backstage 等負責版本控管、測試、審核與發布。核心設計不是讓 Agent 直接修改正式環境，而是讓它產生可審核的 artifact、Pull Request 或 approval request，再由 deterministic pipeline 執行。建議長榮航空採「Agent orchestration + durable workflow + CI/CD gate」混合架構。

## 主要發現

### 角度一：Agent framework 的能力與適用範圍

[VERIFIED] LangGraph 適合建立具狀態、可分支、可暫停及可恢復的 Agent graph。它透過 `interrupt()` 暫停執行，並使用 checkpointer 保存完整 graph state；人員回覆後，可以從同一個 `thread_id` 繼續執行。[REF-1]

[VERIFIED] Microsoft Agent Framework 是 Microsoft 對 AutoGen 與 Semantic Kernel 後續整合方向的框架，提供 graph-based workflow、multi-agent orchestration，以及 request/response 型 Human-in-the-Loop 機制。[REF-2][REF-3]

[VERIFIED] AutoGen 的 `autogen_agentchat` 提供 `GraphFlow`，可用有向圖定義 Agent 間的執行順序與路由；它比較偏向 Agent 對話與協作層，長時間可靠執行仍需要額外的 durable workflow 平台。[REF-4]

[VERIFIED] CrewAI 以 Agents、Crews 與 Flows 組織多 Agent 工作流，並主打企業部署、測試與 observability；但公開資料多由 CrewAI 自身提供，對實際企業導入成效應採保守判讀。[REF-5]

[VERIFIED] Dify 將 Agent、LLM、知識檢索、工具、Code、分支、迴圈與 Human Review 放在視覺化 Workflow Canvas 中，適合快速建立可觀測的 AI workflow。[REF-6]

[VERIFIED] n8n 可在 AI Agent 呼叫高風險工具前插入 Human-in-the-Loop。Workflow 可以暫停，等待人員 Approve 或 Deny，例如寄信、修改資料或刪除資料。[REF-7]

[VERIFIED] Backstage Software Templates 並非完整 Agent framework，而是 Developer Portal 的 scaffold/orchestration 層。它可以定義多個 template steps、custom actions、dry-run testing、權限政策與 audit events，適合把「產出 Skill」包裝成標準化工程流程。[REF-8][REF-9]

[VERIFIED] Temporal 與 Azure Durable Functions 的定位不同於 LangGraph：前者重點是 durable execution、重試、timer、signal、狀態持久化與故障恢復；Azure Durable Functions 則提供等待外部事件與人員核准的標準模式。[REF-10][REF-11]

### 角度二：無人看管時的驗證、checkpoint 與風險控制

[VERIFIED] LangGraph 的 checkpoint 是「可恢復狀態」，不是單純的人工審核畫面。流程可以在工具呼叫前中斷，將待執行的工具名稱與參數交給 reviewer，reviewer 可以選擇 approve、edit 或 reject。[REF-1]

[VERIFIED] n8n 的 HITL 主要放在「工具執行前」：Agent 可以提出動作，但工具在取得人員核准前不會真正執行。[REF-7]

[VERIFIED] Microsoft Agent Framework 使用 workflow request/response 機制，Executor 可以發出 approval request，工作流停止等待外部回覆，收到回覆後再繼續。[REF-2]

[VERIFIED] Dify 的 Human Input node 可收集 approval、修改、comment、forward 或 timeout handling，並記錄 node outputs、variables、execution paths 與 logs。[REF-6]

[VERIFIED] GitHub Agentic Workflows 的關鍵設計是將「Agent 的推理」與「實際寫入 GitHub」分離。Agent 預設採 read-only，輸出受限的 intent，再由另一個 safe-output handler 建立 Issue、Comment 或 Pull Request。[REF-12][REF-13]

[INFERRD] 這些方案的共同安全模式可以抽象成：

```text
Agent 產生計畫或 artifact
        ↓
自動化驗證：schema、policy、測試、security scan
        ↓
依風險決定：自動通過 / 人工審核 / 拒絕
        ↓
在受限環境執行
        ↓
產生版本化結果與 audit trail
```

但「rollback」並不是多數 Agent framework 的內建能力。LangGraph、n8n、Dify 通常提供 pause、resume、approve、reject，卻不一定能撤銷已完成的外部副作用。[REF-1][REF-6][REF-7]

[VERIFIED] Temporal 可以用 workflow history、signals、timers 與 compensating pattern 建立長時間 approval 流程；實際 rollback 仍需由應用程式設計補償動作，例如撤銷資源、還原設定或建立反向交易。[REF-10]

### 角度三：企業落地與成熟度

[VERIFIED] GitHub 已將 Agentic Workflows 建立在 GitHub Actions 之上，讓 Agent 執行於既有的 logs、permissions、sandbox、audit 與 repository context 中。GitHub 明確指出，Agentic Workflows 是補充 CI/CD，而不是取代 build、test、release pipeline。[REF-12]

[VERIFIED] GitHub Aspire 團隊公開展示了跨 Repository 文件自動化流程：產品 Pull Request merge 後，Agent 判斷是否需要文件更新，產生 draft Pull Request，最後交由 Subject Matter Expert Review。[REF-14]

該案例在一個公開統計期間內，流程執行 396 次、產生 82 個文件 Pull Request，82 個均被 merge；這是 GitHub 官方團隊自行揭露的案例，不能直接推論所有組織都能達到相同結果。[REF-14]

[VERIFIED] GitHub 也公開描述了 Accessibility feedback workflow：Issue 建立後由 Copilot 分析，再經過 Submitter Review、Accessibility Team Review、Link Audits 與 Close Loop；人工可以在流程中途重新觸發前一階段。[REF-15]

[VERIFIED] Chicago Trading Company 使用 Backstage Software Templates，將 Terraform module、Git Repository、Jenkins CI 與 Flux CD 設定模板化。官方案例宣稱截至 2023 年已節省約 18 個月 Developer effort，填完表單後約 10 分鐘即可建立並部署服務。[REF-16]

[VERIFIED] Formula Bot 使用 n8n 建立 AI Agent，依照使用者需求與 schema 動態路由至 BigQuery、Snowflake、Microsoft SQL 或 Google Analytics 等 connector workflow。[REF-17]

[UNVERIFIED] CrewAI 官方材料提到 DocuSign、PepsiCo、AB InBev 等企業使用其平台或框架，但公開資訊沒有充分揭露各企業的流程拓撲、失敗率、人工審核比例與 rollback 設計，因此只能視為 vendor-reported adoption，不宜當成獨立驗證的成功案例。[REF-18]

## 深入分析

### 1. 方案分類與比較

| 類型 | 代表方案 | 強項 | checkpoint 能力 | 主要限制 |
|---|---|---|---|---|
| Agent graph framework | LangGraph | 狀態圖、分支、interrupt、可恢復執行 | 強，支援狀態 checkpoint 與 approve/edit/reject | 需自行處理權限、artifact、部署與 rollback |
| Multi-agent framework | Microsoft Agent Framework、AutoGen、CrewAI | Agent 協作、handoff、group chat、workflow routing | 中等，通常提供 request/response 或 human input | 長時間執行、交易一致性與 audit 需外接 |
| Visual AI workflow | Dify、n8n | 快速設計、Connector、視覺化與人工節點 | 中等，適合工具呼叫前 approval | 複雜版本治理、強一致性與 enterprise SDLC 需補強 |
| Durable orchestration | Temporal、Azure Durable Functions | crash recovery、retry、timer、signal、長流程 | 強，適合等待數小時或數天的 approval | 不負責 Agent prompt、品質評估或 Skill 設計本身 |
| Dev portal/scaffolding | Backstage | Template、權限、dry-run、audit、標準工程流程 | 以任務與權限 gate 為主 | 不是 Agent runtime |
| Repository automation | GitHub Actions + Agentic Workflows | PR、branch、review、CI、audit、安全輸出 | 強，天然適合 Pull Request approval | 目前 Agentic Workflows 仍屬 Technical Preview。[REF-12] |

### 2. 建議的 Skill 建置流程映射

可將四個階段拆成「Agent 產出」與「Deterministic gate」兩部分：

| 階段 | Agent 工作 | 自動化驗證 | 人工 checkpoint |
|---|---|---|---|
| 分析 | 讀取需求、盤點工具、辨識風險 | 輸出 JSON Schema、需求完整性、敏感資料掃描 | 高風險需求或權限模型審核 |
| 設計 | 產生 Skill contract、輸入輸出格式、錯誤處理 | Schema validation、policy lint、威脅模型檢查 | 架構與安全設計核准 |
| 開發 | 產生 SKILL.md、scripts、templates、測試 | Static analysis、dependency scan、sandbox 執行 | Merge Request / Pull Request review |
| 測試 | 產生測試案例、執行 golden tests、負向測試 | deterministic tests、LLM judge、regression test | 測試報告與發布核准 |
| 發布 | 建立版本、更新 registry、產生文件 | package validation、簽章、版本衝突檢查 | 正式環境或共享目錄發布核准 |

[INFERRD] 最適合的組合不是四個 Agent 互相聊天，而是：

```text
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

理由是 Agent framework 擅長「推理與路由」，durable orchestrator 擅長「等待、恢復與重試」，CI/CD 則擅長「可重複驗證與正式發布」。這三者的責任邊界清楚，較不容易把所有可靠性問題塞進 Prompt。

### 3. 與傳統 CI/CD checkpoint 的異同

| 面向 | 傳統 CI/CD | Agentic pipeline |
|---|---|---|
| 輸入 | Git commit、artifact、固定參數 | Git、文件、使用者需求、外部資料與非結構化內容 |
| 執行行為 | 通常 deterministic | 可能因模型、context、tool 結果而變動 |
| Checkpoint | build、test、security scan、manual approval | 除上述項目外，還需驗證 plan、tool call、模型輸出與權限 |
| 審核單位 | 人員或 policy engine 審核固定 artifact | 人員可能要審核 Agent 意圖、推理結果與預計副作用 |
| Rollback | 版本切換、artifact redeploy、資料庫 migration rollback | 除版本回復外，還要處理 Agent 已執行的外部 side effect |
| 失敗重試 | 多數可安全重跑 | 可能造成重複建立、重複寄信或重複修改 |
| 可觀測性 | logs、metrics、traces | 另需保存 prompt、model、tool call、context、decision 與 policy 結果 |
| 安全模型 | 主要防止 pipeline 權限誤用 | 還要防止 prompt injection、工具濫用與未預期資料外傳 |

[VERIFIED] GitHub Actions 的傳統 Environment protection rules 可以要求 reviewer、限制 branch、加入 wait timer，以及以 GitHub App 實作 custom protection rule。[REF-19]

[VERIFIED] Azure Pipelines 的 approval/checks 可在 deployment stage 執行前要求指定人員核准，並保留 policy check logs。[REF-20]

[INFERRD] 因此，Agentic pipeline 與 CI/CD 最大的不同不是「有沒有人工核准」，而是「核准對象不同」：

- CI/CD 核准的是已經產出的 artifact 或 deployment。
- Agentic pipeline 還必須核准 Agent 準備做什麼、準備呼叫哪些工具，以及它的輸出是否符合 contract。
- 最安全的架構是讓 Agent 只能產生 proposal，真正的 write operation 必須穿過 deterministic policy 與既有 CI/CD gate。

### 4. 針對長榮航空的建議 checkpoint 設計

建議至少設計以下五類 gate：

1. `Contract Gate`
   驗證每一階段輸出是否符合固定格式，例如 `analysis.json`、`design.yaml`、`skill-manifest.json`、`test-report.json`。

2. `Policy Gate`
   檢查 Skill 是否要求不必要的網路、檔案、Credential、Shell 或外部 API 權限。

3. `Quality Gate`
   執行 schema validation、static analysis、unit test、integration test、prompt regression test 與安全測試。

4. `Human Gate`
   在設計完成、程式碼準備 merge、測試通過準備發布三個位置提供人工核准。低風險 Skill 可以自動通過，高風險 Skill 必須指定 reviewer。

5. `Release Gate`
   將 Skill 以 immutable artifact 發布，保留版本、hash、產生模型、prompt 版本、測試報告、核准者與時間戳記。

[INFERRD] Checkpoint 不應只記錄「Approve / Reject」，而應綁定：

```text
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

## 來源列表

| # | Tier | URL | 標題 | 日期 | 類型 |
|---|---|---|---|---|---|
| 1 | T1 | https://docs.langchain.com/oss/python/langchain/human-in-the-loop | Human-in-the-Loop | 未標示；2026-08-19 查閱 | 官方文件 |
| 2 | T1 | https://learn.microsoft.com/en-us/agent-framework/workflows/human-in-the-loop | Microsoft Agent Framework Workflows - Human-in-the-loop | 2026-07-17 更新 | 官方文件 |
| 3 | T1 | https://microsoft.github.io/agent-resources/develop-agents/ | Develop Agents | 未標示；2026-08-19 查閱 | 官方文件 |
| 4 | T1 | https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/graph-flow.html | GraphFlow | 未標示；2026-08-19 查閱 | 官方文件 |
| 5 | T1 | https://insights.crewai.com/ | AI Agent in Production | 未標示；2026-08-19 查閱 | 官方案例/調查 |
| 6 | T1 | https://dify.ai/workflows | Design Agentic Workflows for Production | 未標示；2026-08-19 查閱 | 官方產品文件 |
| 7 | T1 | https://docs.n8n.io/build/integrate-ai/ai-examples/human-in-the-loop-for-tools | Human-in-the-loop for AI tool calls | 未標示；2026-08-19 查閱 | 官方文件 |
| 8 | T1 | https://backstage.io/docs/next/software-templates/generated-index/ | Software Templates | 未標示；2026-08-19 查閱 | 官方文件 |
| 9 | T1 | https://backstage.io/docs/permissions/overview/ | Permissions Overview | 未標示；2026-08-19 查閱 | 官方文件 |
| 10 | T1 | https://temporal.io/ | Durable Execution Solutions | 未標示；2026-08-19 查閱 | 官方產品文件 |
| 11 | T1 | https://learn.microsoft.com/en-ca/azure/durable-task/common/durable-task-human-interaction | Human Interaction Pattern | 未標示；2026-08-19 查閱 | 官方文件 |
| 12 | T1 | https://github.blog/ai-and-ml/github-copilot/automate-repository-tasks-with-github-agentic-workflows/ | Automate repository tasks with GitHub Agentic Workflows | 2026-02-13 | 官方部落格 |
| 13 | T1 | https://github.blog/ai-and-ml/generative-ai/under-the-hood-security-architecture-of-github-agentic-workflows/ | Under the hood: Security architecture | 2026-03-09 | 官方部落格 |
| 14 | T1 | https://github.blog/ai-and-ml/github-copilot/automating-cross-repo-documentation-with-github-agentic-workflows/ | Automating cross-repo documentation | 2026-07-08 | 官方案例 |
| 15 | T1 | https://github.blog/ai-and-ml/github-copilot/continuous-ai-for-accessibility-how-github-transforms-feedback-into-inclusion/ | Continuous AI for accessibility | 2026-03-18 左右；頁面日期需再確認 | 官方案例 |
| 16 | T1 | https://backstage.io/blog/2023/09/29/chicago-traiding-company-adopter-spotlight/ | Chicago Trading Company Adopter Spotlight | 2023-09-29 | 官方案例 |
| 17 | T1 | https://n8n.io/case-studies/formula-bot/ | How Formula Bot uses n8n | 未標示；2026-08-19 查閱 | 官方案例 |
| 18 | T1 | https://campus.crewai.com/courses/crewai-production-engineer | CrewAI Production Engineer | 未標示；2026-08-19 查閱 | 官方產品材料 |
| 19 | T1 | https://docs.github.com/en/actions/reference/workflows-and-actions/deployments-and-environments | Deployments and environments | 未標示；2026-08-19 查閱 | 官方文件 |
| 20 | T1 | https://learn.microsoft.com/mt-mt/azure/devops/pipelines/process/approvals?view=azure-devops | Pipeline deployment approvals | 2025-11 左右；頁面日期需再確認 | 官方文件 |

## 來源品質摘要

- Tier 1 來源：20 個
- Tier 2 來源：0 個
- Tier 3 來源：0 個
- 來源多樣性：12 個不同網站
- 主要來源類型：官方文件、官方部落格、官方企業案例、官方 GitHub Repository
- 學術論文：本報告未將尚未充分驗證的近期論文作為核心依據

## 知識缺口

- [UNVERIFIED] LangGraph、Dify、n8n、CrewAI 在大型航空、金融或高監管企業內的完整 production architecture、SLA、失敗率與成本資料，公開資訊不足。
- [UNVERIFIED] 多數框架沒有明確保證 Agent workflow 的 exactly-once execution；重試可能造成外部工具重複執行，仍需自行設計 idempotency key。
- [UNVERIFIED] 「rollback」通常不是 Agent framework 內建能力，尤其是寄信、建立帳號、修改外部系統等不可逆副作用。
- [UNVERIFIED] GitHub Agentic Workflows 目前仍是 Technical Preview，不宜直接作為航空正式生產流程的唯一核心。
- [INFERRD] 人工審核若沒有 timeout、代理審核者、拒絕後修正路徑與 approval expiration，流程可能長時間卡住；各產品官方文件未必完整描述 enterprise operating model。

## 建議的下一步

1. 先做一個小型 PoC：以「分析 → Skill contract → 自動測試 → Pull Request → 人工核准 → 發布」為最小流程。
2. Agent orchestration 優先評估 LangGraph 與 Microsoft Agent Framework。
3. Durable execution 優先評估 Temporal；若既有 Azure 生態較深，則同步評估 Azure Durable Functions。
4. 以 GitHub Actions 或 Azure Pipelines 執行 deterministic validation、security scan 與正式發布。
5. 把 Agent 輸出限定為 branch、artifact 或 Pull Request，不允許 Agent 直接寫入正式 Skill Registry。
6. 建立獨立的 `Approval Service` 或 approval record，讓核准綁定 artifact hash、版本、工具參數與有效期限。
7. 第一階段採高比例人工審核；累積足夠測試與 audit data 後，再依風險等級逐步放寬自動化。
8. 若要快速驗證視覺流程，可用 Dify 或 n8n；若目標是長期企業級 Skill Factory，建議核心仍採程式化 workflow + durable execution + CI/CD gate。

## Claude 查核附註（非原始報告內容）

- 本報告的來源列表以深連結（特定文章／頁面）為主，多數條目對不確定的發布日期誠實標註「未標示；查閱日期」而非臆測，可信度優於同批 Gemini 版本（見 `2026-08-19-agentic-pipeline-orchestration-gemini.md`）。
- 兩份報告在核心結論高度一致（LangGraph 的 checkpoint/interrupt 機制、rollback 非內建能力、Agent 只能產出可審核 artifact 而非直接寫正式環境）；Codex 版本額外指出 Gemini 版本沒有的一層——Durable Execution（Temporal / Azure Durable Functions），用於處理跨數小時到數天的人工核准等待、crash 恢復與重試，這是設計 W-2026-08-057 驗證機制時建議納入的關鍵補充。
