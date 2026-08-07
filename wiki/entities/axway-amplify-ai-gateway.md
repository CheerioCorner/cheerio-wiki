---
title: Axway Amplify AI Gateway — 企業 AI 治理層
type: entity
created: 2026-08-07
updated: 2026-08-07
sources: 1
tags: [ai-agent, gateway, enterprise, governance, mcp]
topics: [ai-agent]
canonical: entities/axway-amplify-ai-gateway
provenance:
  - kind: external
    url: https://app.notion.com/p/3515979e-3a8c-80fd-bf8e-ff5b7c49333b
---

# Axway Amplify AI Gateway — 企業 AI 治理層

> 企業級 AI 閘道，在前端應用程式、AI Agents 與企業後端 IT 生態系之間搭建安全、受控且靈活的橋樑。

## 核心定位

AI Gateway 是 Client Apps 與 AI Resources 之間的**治理層 (Governance Layer)**，提供 GUI 圖形化配置介面，解決企業 AI 蔓延（AI Sprawl）與影子 AI（Shadow AI）問題。

## 四大核心支柱

| 支柱 | 功能 | 說明 |
|------|------|------|
| **Connect** | 連結 | 預先配置各大 LLM 與 Guardrails 連接器 |
| **Build** | 建造 | 建置 MCP Server、AI Agent 或 RAG 架構 |
| **Govern** | 治理 | 集中資產儲存庫，套用 AuthN/AuthZ 與安全防護欄 |
| **Monitor** | 監視 | 監控與審核 AI 流量，管理使用成本 |

## 企業面臨的 AI 挑戰

### 四大風險
1. **敏感資料外洩** — 即時注入攻擊洩漏私人對話
2. **無法控制的行動** — 用戶以 1 美元訂購汽車（越權操作）
3. **成本爆炸性飆升** — 物流公司 AI 系統月花費 210 萬美元
4. **影子 AI** — 員工私用公開版 ChatGPT 處理商業機密

### 核心本質衝突
LLM 為**機率性 (Probabilistic)**，傳統 IT 技術為**確定性 (Deterministic)**，兩者結合產生安全、成本、合規與整合痛點。

## 架構設計

### 核心模組
- MCP Server & MCP Marketplace
- Prompt Templates & Prompt Guarding
- Data Sanitizer & Data Chunker
- Token Rate Limiter & Access Control
- LLM Orchestrator & Response Processing

### 整合架構（Hub and Spoke）
```
Models: OpenAI, Anthropic, Gemini, Mistral
         ↓
   AI Gateway (Hub)
   - RAG 架構支援
   - 向量資料庫分塊 Chunking
   - 中介軟體安全防護
         ↓
Enterprise IT: SAP, Salesforce, Legacy DBs, MFT, B2B
```

## 使用場景

### 1. 成本管理 (Cost Management)
- **Monitoring** — 即時監控使用趨勢
- **Token Quota** — 設定 Token 流量與上限限額
- **Budgets** — 為團隊或專案設定預算上限

### 2. MCP 管理 (MCP Management)
- **Access Control** — 認證與授權 Client/Agent 的呼叫
- **MCP Registry** — 支援手動註冊與自動註冊
- **MCP Proxy** — 將請求代理轉發至內部 Tools 或外部 MCP Server

### 3. Agent 管理 (Agent Management)
- **Agent Registry** — 集中呈現與部署可用 Agent
- **Agent Designer** — 圖形介面設計 Agent Prompt 與 Tool 配置
- **A2A Support** — 支持 Agent-to-Agent 協作與通訊協定
- **MCP Integration** — 將 Agent 與 MCP 生態系結合

### 4. RAG 整合 (Retrieval-Augmented Generation)
- **Data Retrieval** — 從 Vector Database 檢索關聯文本
- **Orchestration** — 組合 Prompt 與上下文
- **App Integration** — 整合 API、LLM 與向量資料庫

## 安全機制（7 大控制）

1. 將高風險 AI 系統流量導向合規區域的核准模型
2. 流量導向合規基礎設施
3. 端到端稽核日誌 (End-to-End Audit Logs)
4. 確保 AI 系統採用高品質資料
5. 執行防護欄 (Guardrails) 政策，防止敏感資料外洩
6. 新增安全機制防止未授權存取
7. 提供完整技術文件

## 進階特色

- **Cost Management** — Token/Monetary Quotas, Enforcement
- **MCP Support** — Traffic Monitoring, MCP Proxy, Rate Limiting
- **Gemini WIF Support** — Workload Identity Federation 認證支援
- **Agent Management** — A2A Proxy, AWS AgentCore Support
- **Advanced AI Flow** — Semantic Caching, Semantic Routing, LLM Load Balancing
- **User Experience** — AI 輔助 API 文件生成與欄位對應 Mapping

## 與我現有知識的關聯

| 概念 | AI Gateway 做法 | Pi Agent 做法 |
|------|-----------------|---------------|
| 工具整合 | MCP Protocol（集中管理） | MCP Protocol（本地） |
| 安全控管 | Gateway 層 RBAC + Guardrails | 信任本地檔案 |
| 成本控管 | Token Quota + Budgets | Token Budget（session 級） |
| Agent 管理 | Agent Registry + Designer | 本地 agent 配置 |
| RAG | 向量資料庫 + Chunking | 無內建 RAG |

## 種子故事

這顆種子來自 Notion「任何當下 → AI相關 → AI Gateway」頁面的 19 張截圖。透過 agy + Gemini 視覺分析，提取出完整的企業 AI 治理入門教材。內容涵蓋 LLM/MCP/Agent 基礎概念、企業風險案例、Axway AI Gateway 架構與四大使用場景。

## 連結

- 🔗 [Axway 官方網站](https://www.axway.com)
- 🔗 [MCP 官方規範](https://modelcontextprotocol.io/)
- 🔗 [Anthropic MCP 介紹](https://www.anthropic.com/news/model-context-protocol)
- 📖 [[wiki/entities/mcp-model-context-protocol|MCP]]
- 📖 [[wiki/entities/omnigent|Omnigent — Databricks meta-harness]]
