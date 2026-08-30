---
title: "AI System Threat Modeling — 深度研究"
type: source
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [threat-modeling, STRIDE, attack-surface, deep-research]
canonical: sources/2026-08-30-ai-threat-modeling
topics: [agent-infrastructure, software-quality]
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260830-003/research-report.md
---

> 來源：Gemini Notebook 深度研究（rc-20260830-003），53 筆來源。涵蓋 STRIDE 模型在 AI 系統的應用、Agent-specific Attack Surface、威脅建模工具。

## 研究摘要

### 1. STRIDE × AI Agent

| STRIDE 面向 | AI Agent 場景的威脅 | 防禦 |
|---|---|---|
| **Spoofing** | Agent impersonation、prompt injection 偽造指令 | Authentication、attestation |
| **Tampering** | 修改 agent 的 system prompt 或 tool output | Integrity verification、immutable prompt |
| **Repudiation** | Agent 否認執行過某操作 | Audit trail、logging |
| **Info Disclosure** | 資料外洩（training data、user data） | Data classification、DLP |
| **DoS** | 資源耗盡（token、API quota） | Rate limiting、quota management |
| **Elevation of Privilege** | Agent 獲取超出權限的能力 | Capability-based security、sandbox |

### 2. AI Agent Attack Surface

| 攻擊面 | 威脅 | 防禦 |
|---|---|---|
| **Prompt Injection** | 操控 agent 行為 | Input validation、instruction hierarchy |
| **Tool Abuse** | 濫用 tool 呼叫 | Tool permission、rate limiting |
| **Data Exfiltration** | 竊取敏感資料 | Output filtering、DLP |
| **Privilege Escalation** | 獲取更高權限 | Least privilege、sandbox |
| **Supply Chain** | 惡意依賴/skill | Dependency scanning、audit |

### 3. Multi-agent 特有威脅

- **Agent Impersonation**：偽造其他 agent 的身分
- **Message Tampering**：篡改 agent 間的通訊內容
- **Collusion**：多個 agent 合謀進行惡意操作
- **Deception**：agent 騙騙人類或其他 agent

### 4. 威脅建模工具

| 工具 | 適用場景 | 優點 |
|---|---|---|
| **Microsoft Threat Modeling Tool** | 一般系統 | 圖形化、STRIDE 對應 |
| **OWASP AI Security** | AI 系統 | 專為 AI 設計 |
| **MITRE ATLAS** | AI 威脅 | 豐富的攻擊案例庫 |

## 關鍵引用

- STRIDE Threat Model (Microsoft)
- OWASP Top 10 for LLM Applications
- MITRE ATLAS (Adversarial Threat Landscape for AI Systems)
- NIST AI 100-2: AI Threat Modeling

## 相關頁面

- [[wiki/entities/agent-security|agent-security]] — agent 安全基礎
- [[wiki/sources/2026-08-30-dev-security-vibe-coding|Vibe Coding 資安基本功]] — CIA 原則 + Threat Modeling 基礎
