---
title: "企業界 Agentic Pipeline / Orchestration 解決方案調研（Gemini）"
type: source
created: 2026-08-19
updated: 2026-08-19
sources: 7
tags: [ai-agent, agentic-pipeline, orchestration, enterprise-governance, skill-automation-design]
topics: [agent-architecture, skill-system]
provenance_raw: "raw/research/2026-08-19-agentic-pipeline-orchestration-gemini.md"
---

# 企業界 Agentic Pipeline / Orchestration 解決方案調研（Gemini）

> Gemini 2.5-pro 針對長榮航空 IT 系統架構課「Skill 建置自動化」流程（分析→設計→開發→測試四階段）進行的企業級 Agentic Pipeline / Orchestration 解決方案調研。

## ⚠️ 來源品質警告

本報告的 7 個來源 URL 皆為產品官方**根目錄／文檔首頁**（非深連結至特定文章），且各條「日期」欄位精確到日、但頁面本身通常不會標示這類日期——**這些日期未經獨立驗證，可能是模型推論或臆測產生**，不應引用為事實。來源列表詳見下方，僅作為「該官方文檔確實存在」的佐證。

## 核心發現

### 雙層驗證機制

報告提出的最關鍵架構模式是**雙層驗證機制**，用來解決「無人看管與驗證把關」的核心問題：

1. **前段：Agentic 狀態機**——透過「狀態持久化（State Checkpointing）」與「事件驅動中斷（Interrupt Node / Approval Gate）」實現動態 Self-Repair 循環
2. **後段：CI/CD 確定性驗證**——交由 CI/CD 執行 deterministic verification

### 各框架的 HITL 機制

| 框架 | 中斷機制 | 狀態持久化 |
|------|---------|-----------|
| LangGraph | `interrupt()` + `Command(resume=...)` | PostgreSQL/SQLite/Redis Checkpointer |
| CrewAI Flows | `@human_feedback` / Human-as-a-Tool | Pydantic State + DB Persistence |
| Microsoft AutoGen/AF | `ToolApprovalRequestContent` 掛起機制 | 工作流級持久化 |
| Dify (v1.13+) | Human Input Node（Email/WebApp 審核） | Celery Queue + Postgres |
| n8n | Native Tool Approval + Slack/n8n Chat 按鈕 | n8n Execution State Database |

### Agentic vs 傳統 CI/CD Checkpoint 差異

核心差異在三點：
1. **驗證性質**：Agentic 包含語意與品質判斷（LLM Judge），不只是 exit code
2. **失敗處理**：Agentic 有 Reflexion Loop（自我反思修復），傳統 CI/CD 失敗即停止
3. **狀態管理**：Agentic Checkpoint 保存完整 LLM 對話歷史與 context，非僅 Git SHA

### DevOps 整合

- **GitHub Actions + Agentic Workflows**：在 microVM sandbox 中運行 Agent，測試失敗時 traceback 自動作為 prompt 回傳給 Agent 進行 Auto-repair
- **Backstage MCP**：透過 MCP 插件將 Scaffolder Templates 暴露為 Agent 可呼叫的 Tools，確保自動產出符合企業 Golden Path

### 建議架構

LangGraph / CrewAI Flows（Agent 狀態編排）+ GitHub Actions（自動化測試執行沙盒）+ Backstage（企業 Golden Path 規範）。

## 與 Codex 版本的差異

本報告與 Codex 版本（見 [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研]]）交叉比對：
- Codex 版本引用更具體（深連結至個別文章）、來源數更多（20 vs 7）
- Codex 版本額外發現** Durable Execution 層**（Temporal / Azure Durable Functions），這是本報告完全沒提到的重要補充
- 兩邊核心結論高度一致（LangGraph checkpoint 機制、rollback 非內建能力等）
- **若兩份報告具體事實有出入，優先採信 Codex 版本**

## 來源

| # | URL | 備註 |
|---|-----|------|
| 1 | https://github.com/langchain-ai/langgraph | 根目錄，非深連結 |
| 2 | https://docs.crewai.com | 根目錄 |
| 3 | https://learn.microsoft.com/en-us/agent-framework/ | 根目錄 |
| 4 | https://docs.dify.ai | 根目錄 |
| 5 | https://docs.n8n.io | 根目錄 |
| 6 | https://github.com/features/actions | 根目錄 |
| 7 | https://backstage.io/docs | 根目錄 |

## 相關頁面

- [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研]] — 同主題交叉比對版本
- [[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]] — 核心架構概念
- [[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]] — Codex 額外發現的關鍵層
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 上游治理框架
- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 狀態持久化三路徑
