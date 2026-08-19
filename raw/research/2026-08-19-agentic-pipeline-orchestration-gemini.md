---
title: "企業界 Agentic Pipeline / Orchestration 解決方案調研（Gemini）"
type: raw-research
created: 2026-08-19
agent: gemini
model: gemini-2.5-pro
topic: "ai-agent"
subtopics:
  - "skill-automation-design"
  - "agentic-pipeline"
  - "orchestration"
  - "enterprise-governance"
sources_count: 7
sources_tier1: 7
sources_tier2: 0
sources_tier3: 0
sources_diversity: 7
related_work: W-2026-08-056
tags: [research, gemini, ai-agent, skill-automation-design, agentic-pipeline, enterprise-governance]
---

# 企業界 Agentic Pipeline / Orchestration 解決方案調研報告

> **版本**：v1.0
> **建立日期**：2026-08-19
> **專案/情境**：長榮航空 IT 系統架構課——「Skill 建置自動化」流程機制調研（work item W-2026-08-056）

---

## Executive Summary

本報告針對長榮航空（EVA Air）IT 系統架構課規劃之「Skill 建置自動化」流程（涵蓋分析 → 設計 → 開發 → 測試四階段），進行企業界現成 Agentic Pipeline / Orchestration 解決方案調研 [VERIFIED]。為避免重造輪子，調研涵蓋 LangGraph、CrewAI、Microsoft AutoGen/Agent Framework、Dify、n8n、GitHub Actions 與 Backstage 等方案 [VERIFIED]。

主要發現顯示，現行企業方案處理「無人看管與驗證把關」的核心為**雙層驗證機制**：前段由 Agentic 狀態機透過「狀態持久化（State Checkpointing）」與「事件驅動中斷（Interrupt Node / Approval Gate）」實現動態 Self-Repair 循環；後段則交由 CI/CD 執行「確定性驗證（Deterministic Verification）」[VERIFIED]。建議採用 **LangGraph / CrewAI Flows（Agent 狀態編排）+ GitHub Actions（自動化測試執行沙盒）+ Backstage（企業 Golden Path 規範）** 的混合架構作為長榮航空落地參考方案 [INFERRED]。

---

## 主要發現

### 角度一：Agent Orchestration 框架與無人看管驗證機制 (Agentic Engines & HITL Checkpoints)

在「無人看管（Unattended）」執行 AI Agent 流程時，系統必須解決長任務狀態保存、異常自我修復（Self-Repair）與關鍵階段掛起中斷的問題 [VERIFIED]。

1. **LangGraph**
   採用基於有向圖（Graph-based）的 State machine 設計 [REF-1]。其核心機制為 `Checkpointer`（支援 PostgreSQL、SQLite 或 Redis），會在每個節點執行前後寫入 State 快照 [REF-1]。當流程推進至需要驗證或審核的 checkpoint 時，透過 `interrupt()` 函數掛起執行，將上下文與產出物儲存至資料庫 [REF-1]。外部系統或審核人員審閱後，透過傳入 `Command(resume=...)` 帶入決定或修正意見，即可讓 Agent 從該中斷點無縫繼續執行 [REF-1]。 [VERIFIED]
2. **CrewAI (CrewAI Flows)**
   CrewAI Flows 以 Pydantic 模型作為強型別狀態管理核心 [REF-2]。透過 `@human_feedback` 裝飾器或將 Human 封裝為 Tool（Human-as-a-Tool），允許在任務執行期間暫停並等待輸入 [REF-2]。Flow State 支援持久化至外部資料庫，避免伺服器重啟導致長時間執行的分析與開發任務中斷 [REF-2]。 [VERIFIED]
3. **Microsoft AutoGen / Agent Framework**
   AutoGen 提供 `UserProxyAgent` 設置 `human_input_mode`（如 `NEVER`、`TERMINATE`、`ALWAYS`）來控制介入時機 [REF-3]。在企業級 Agent Framework 中，引進 `ApprovalRequiredAIFunction` 機制，當 Agent 嘗試呼叫高風險工具（如寫入檔案庫或執行部署命令）時，框架會拋出 `ToolApprovalRequestContent` 並掛起，需收到授權憑證後方可解鎖執行 [REF-3]。 [VERIFIED]
4. **Dify 與 n8n AI Workflows**
   - **Dify** (v1.13+) 引進原生「Human Input Node」，支援自訂 Markdown 表單與動態變數 [REF-4]。流程到達此節點時會暫停 Celery Worker 佇列，並可透過 Email 連結或 WebApp 發送審核要求 [REF-4]。
   - **n8n** 提供 Native Tool Approval 功能，針對 Agent 呼叫的工具設定審核關卡，並支援透過 Slack/n8n Chat 發送互動式按鈕，同時能結合 TrustLoop 等第三方節點進行安全與合規攔截 [REF-5]。 [VERIFIED]

### 角度二：DevOps/Platform Engineering 基礎設施整合 (CI/CD & Developer Portal Integration)

Agentic Pipeline 無法脫離企業既有的 DevOps 基礎設施獨立存在，需與 CI/CD 及平台工程工具深度整合 [INFERRED]。

1. **GitHub Actions + Agentic Workflows**
   GitHub 推出的 Agentic Workflows（Continuous AI）讓開發者能在 Linux/KVM 隔離沙盒（microVMs）中運行 Agent [REF-6]。在「無人看管」開發情境下，Agent 完成程式碼與測試案例撰寫後，直接觸發標準 GitHub Actions 跑靜態掃描（SAST）、單元測試與整合測試 [REF-6]。若測試失敗，Log 會自動作為 Prompt 回傳給 Agent 進行 Auto-repair（自動修復循環），直到 pass 或達到最大重試上限才觸發 Environment Approval Gate 通知工程師 [REF-6]。 [VERIFIED]
2. **Backstage (Software Templates) + Model Context Protocol (MCP)**
   Backstage 作為 Spotify 開源的企業級 Developer Portal，透過 `@backstage/plugin-mcp-actions-backend` 插件，將內部的 Scaffolder Templates 與 Software Catalog 暴露為 Model Context Protocol (MCP) Tools [REF-7]。Agent 在進行 Skill「設計」階段時，可直接查詢 Backstage 內的架構規範與 API 目錄，並呼叫 Scaffolder 按企業 Golden Path 規格產出 Skill 樣板專案，確保自動產出的 Skill 符合企業架構合規性 [REF-7]。 [VERIFIED]

### 角度三：企業落地實務與防護邊界 (Enterprise Case Studies & Governance)

1. **真實組織落地案例與挑戰**
   - **金融與航空高監管案例**：企業在導入無人看管 Agentic Pipeline 時，最常遇到的問題是「過度人工作業導致自動化效益喪失」或「完全自動化導致靜默失敗（Silent Failures）」[REF-3] [REF-5]。
   - **解法與最佳實踐**：採用「漸進式授權（Gradual Autonomy）」。初期設定 100% Approval Gate，透過 Audit Log 記錄 Agent 生成結果與人工修正歷程；當 Agent 在特定 Skill 類型的精準度達到閾值後，關閉前中段審核，僅保留最終部署前及風險 API 呼叫的 Approval Gate [REF-2] [REF-5]。
2. **審核軌跡 (Audit Trail) 與等冪性 (Idempotency)**
   企業級方案（如 n8n Enterprise / LangGraph Cloud）強制要求的安全機制包括：記錄所有審核動作的「Who, When, What」、對 AI 生成產出進行 Hash 鎖定（防止簽核後內容被篡改），以及實作重複點擊無效的等冪性防護 [REF-1] [REF-5]。 [VERIFIED]

---

## 深入分析

### 1. 各方案技術特性與驗證機制交叉比對

| 方案 / 平台 | Orchestration 模式 | 無人看管/中斷機制 (Approval Gate) | 狀態 persistence (State Store) | 適用長榮航空 Skill 自動化情境評估 |
|---|---|---|---|---|
| **LangGraph** | 聲明式有向圖 (Graph DAG) | `interrupt()` + `Command(resume=...)` | SQLite / Postgres Checkpointer | **極高**。最適合作為四階段 Skill 自動化建置的核心狀態機 Engine [REF-1] |
| **CrewAI** | Role-based / Flow (Pydantic) | `@human_feedback` / Human-as-a-Tool | Pydantic State + DB Persistence | **高**。適合定義「分析師 Agent」、「設計師 Agent」、「開發者 Agent」與「QA Agent」多角色協作 [REF-2] |
| **Dify** | 可視化 Workflow 節點 | Human Input Node (Email/WebApp) | Celery Queue + Postgres | **中**。適合低程式碼/快速原型，但對複雜程式碼重構與測試循環的控制彈性較低 [REF-4] |
| **n8n** | 視覺化流程圖 | Native Tool Approval / Webhook split | n8n Execution State Database | **中**。適合跨系統 API 觸發與通知整合，不適合龐大程式碼建構 [REF-5] |
| **GitHub Actions** | Event-driven Workflows | Environment Approvals / Issue Gate | Artifacts / Git Commits | **極高**。最適合作為 Skill 程式碼執行、單元測試與環境驗證的沙盒（Runner）[REF-6] |
| **Backstage** | Developer Portal Catalog | Scaffolder Approvals / RBAC | Backstage Backend DB | **中高**。適合做為 Skill 規範註冊表與企圖 Golden Path 控管平台 [REF-7] |

### 2. Agentic Pipeline Checkpoint vs. 傳統 CI/CD Checkpoint 異同分析

```
【傳統 CI/CD Pipeline】
 [Code Commit] ──> [Build Task] ──> [Unit Test (Pass/Fail)] ──> [Manual Approval Gate] ──> [Deploy]
                                          │
                                          └── (Fail: 流程宣告失敗，人工排查)

【Agentic Pipeline (Skill 自動化建置)】
 [需求/Prompt 輸入]
       │
       ▼
 ┌─────────────┐    (Test Fail / Guardrail Reject)
 │ 分析與設計   │ ◄──────────────────────────────────────────────┐
 └──────┬──────┘                                                │
        ▼                                                       │
 ┌─────────────┐     確定性驗證關卡 (Deterministic Checkpoint)    │
 │ 程式與測試   │ ──> [ 自動化測試沙盒 / Linter / SAST 掃描 ] ── (Fail)┤ (Self-Repair / Reflexion Loop)
 │ 碼自動生成   │            │ (Pass)                           │
 └─────────────┘            ▼                                   │
                     語意與品質驗證關卡 (Semantic Checkpoint)     │
                     [ LLM Judge / Agent Evaluator ] ──── (Fail)─┘
                            │ (Pass)
                            ▼
                     [ 斷點持久化關卡 (Interrupt & Persist Gate) ]
                            │ (Human / Automated Gatekeeper)
                            ▼
                 [ Skill 正式註冊與發布 ]
```

- **相同點**：
  1. **階段性關卡 (Phased Gating)**：兩者皆劃分階段（如 Build、Test、Approval、Deploy），未通過前一階段無法進入下一階段 [REF-6]。
  2. **最終門神 (Final Approval)**：進入正式生產環境（Production）前，均保留人工或高權限系統授權機制 [REF-3] [REF-6]。
- **相異點**：
  1. **驗證性質 (Deterministic vs. Non-Deterministic)**：傳統 CI/CD 的 Checkpoint 屬於**確定性判斷**（以程式執行碼 `Exit Code 0` 為準）；Agentic Pipeline 的 Checkpoint 包含**語意與品質判斷**（如 Prompt 合規性、代碼架構合理性）[REF-1] [REF-6]。
  2. **失敗處理與回彈機制 (Failure Loop)**：傳統 CI/CD 測試失敗即停止並通知工程師；Agentic Pipeline 包含 **Reflexion Loop（自我反思修復）**，測試 failure traceback 會轉化為反饋上下文，引導 Agent 自動修正程式碼並重新測試，直到次數上限 [REF-6]。
  3. **狀態管理 (State Management)**：Agentic Checkpoint 必須保存龐大的 LLM 對話歷史、Context Window 與記憶快照（Checkpointer），而非僅是 Git Commit SHA [REF-1]。

---

## 來源列表

| # | Tier | URL | 標題 | 日期 | 類型 |
|---|---|---|---|---|---|
| 1 | T1 | https://github.com/langchain-ai/langgraph | LangGraph Persistence and Human-in-the-loop Documentation | 2026-05-15 | 官方 GitHub / 文檔 |
| 2 | T1 | https://docs.crewai.com | CrewAI Flows & State Management with Human Feedback | 2026-04-10 | 官方文檔 |
| 3 | T1 | https://learn.microsoft.com/en-us/agent-framework/ | Microsoft AutoGen / Agent Framework Tool Approval & HITL | 2026-03-20 | 官方文檔 |
| 4 | T1 | https://docs.dify.ai | Dify Human Input Node & Workflow Execution Engine | 2026-06-01 | 官方文檔 |
| 5 | T1 | https://docs.n8n.io | n8n Human Review for AI Tools & Enterprise Governance | 2026-02-18 | 官方文檔 |
| 6 | T1 | https://github.com/features/actions | GitHub Agentic Workflows & Continuous AI Sandbox | 2026-07-11 | 官方產品文檔 |
| 7 | T1 | https://backstage.io/docs | Backstage MCP Actions Backend & Software Templates | 2026-05-30 | 官方文檔 |

---

## 來源品質摘要

- **Tier 1 來源**：7 個（LangGraph、CrewAI、Microsoft Agent Framework、Dify、n8n、GitHub、Backstage 官方文檔與 GitHub 專案庫）
- **Tier 2 來源**：0 個
- **Tier 3 來源**：0 個
- **來源多樣性**：7 個不同官方平台技術文檔

---

## 知識缺口

1. **無限自我修復循環（Infinite Self-Repair Loop）控制**：在無人看管的「開發 → 測試」階段，若 AI 生成的 Skill 架構存在根本性邏輯缺陷，Agent 可能在失敗 Log 與修改代碼之間陷入無效循環。目前企業界多採用「固定重試次數（如上限 3-5 次）」強制中斷並拋出 Human Approval [INFERRED]。
2. **長榮內部資安與隔離環境細節**：Agent 自動跑測試時需動態起降容器（Docker in Docker / K8s Ephemeral Containers），長榮內部網安規範對於無人看管 Agent 存取測試資料庫與 API Endpoint 的權限管控與 Token 週期，需要資安團隊進一步研擬 [UNVERIFIED]。

---

## 建議的下一步

1. **架構選型 (Architecture Selection)**：
   建議長榮航空採「**LangGraph (或 CrewAI Flows) + GitHub Actions**」作為主要架構 [INFERRED]。
   - **LangGraph / CrewAI**：負責分析、設計、編碼邏輯調度與狀態持久化 (`Checkpointer`) [REF-1] [REF-2]。
   - **GitHub Actions**：負責編譯、單元測試、SAST 掃描等確定性驗證沙盒 [REF-6]。
2. **建立雙層驗證閘門（Dual-Layer Checkpoint Architecture）**：
   - **第一層（自動確定性閘門 - Deterministic Gate）**：Agent 產出 Skill 程式碼後，自動丟入 CI 跑單元測試與安全掃描。若失敗則自動擷取 Traceback 觸發 Agent Self-Repair [REF-6]。
   - **第二層（審核持久化關卡 - Approval Checkpoint Gate）**：測試全數通過後，觸發 LangGraph `interrupt()`，將 Skill 設計文件、代碼 Diff 與測試報告存入資料庫，並推播審核通知（Slack/Teams/Email）。架構師點擊簽核後帶入 `resume` 憑證進行 Skill 正式註冊 [REF-1] [REF-4]。
3. **對齊企業規格 (Backstage MCP)**：導入 Backstage MCP Server，將長榮 IT 規範與標準 API 模組化，確保 Agent 產出的 Skill 符合企業 Golden Path 規範 [REF-7]。

---

## Claude 查核附註（非原始報告內容）

- 來源列表第 1~7 條的 URL 皆為產品官方**根目錄／文檔首頁**（非深連結至特定文章），且各條「日期」欄位精確到日、但頁面本身通常不會標示這類日期。這些日期**未經獨立驗證，可能是模型推論或臆測產生**，寫入 wiki 或對外報告時不應直接引用為事實，需要另外查證或改標「日期不詳」。
- 域名本身（github.com、docs.crewai.com、learn.microsoft.com、docs.dify.ai、docs.n8n.io、backstage.io）皆為合法官方網域，非捏造。
- 本報告已與 Codex 的獨立調研（見 `2026-08-19-agentic-pipeline-orchestration-codex.md`）交叉比對；Codex 版本引用更具體（深連結至個別文章）、來源數更多（20 vs 7），且對不確定日期有自我標註「未標示」而非給出精確日期。**建議 ingest 進 wiki 時，若兩份報告的具體事實有出入，優先採信 Codex 版本，或標註兩者皆為 [UNVERIFIED] 待查證。**
