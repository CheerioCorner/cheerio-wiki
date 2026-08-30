---
title: "Enterprise API Security — 深度研究"
type: source
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [enterprise-security, oauth, azure-ad, api-key, deep-research]
canonical: sources/2026-08-30-enterprise-api-security
topics: [agent-infrastructure, software-quality]
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260830-002/research-report.md
---

> 來源：Gemini Notebook 深度研究（rc-20260830-002），63 筆來源。涵蓋 OAuth 2.0 / OIDC、Azure AD / Entra ID、API Key 管理、DLP、企業 API Gateway 安全。

## 研究摘要

### 1. OAuth 2.0 在 AI Agent 場景

- **Token Scope 設計**：最小權限原則，只授予 agent 必要的 scope
- **Refresh Token 管理**：短命 access token + rotation，避免 token 洩漏
- **On-Behalf-Of Flow**：agent 代表用戶操作時，需要明確的委派鏈

### 2. Azure AD / Entra ID 安全

- **App Registration**：每個 agent 一個獨立的 app registration
- **Conditional Access**：基於風險的存取控制（IP、裝置、行為）
- **Managed Identity**：Azure 環境中避免硬編碼憑證
- **Service Principal 權限最小化**：只授予必要的 API 權限

### 3. API Key 管理

| 方法 | 優點 | 缺點 |
|---|---|---|
| **Environment Variable** | 簡單、廣泛支援 | 容易洩漏（log/錯誤訊息） |
| **Secret Manager** | 集中管理、自動輪替 | 需要額外基礎設施 |
| **Vault（如 HashiCorp）** | 最安全、審計軌跡 | 複雜度高、成本高 |

### 4. 企業 AI Agent 權限設計

- **最小權限原則**：Agent 只能存取完成任務所需的最少資源
- **Tool Policy**：明確定義每個 tool 的存取範圍
- **Audit Trail**：所有 API 呼叫都要有可追溯的紀錄
- **DLP（資料外洩防禦）**：敏感資料不能離開受控環境

## 關鍵引用

- Microsoft: Azure AD Security Best Practices
- OAuth 2.0 Security Best Current Practice (RFC 9700)
- OWASP: API Security Top 10

## 相關頁面

- [[wiki/entities/agent-security|agent-security]] — agent 安全基礎
- [[wiki/entities/azure-devops|azure-devops]] — W-080 的主要整合對象
