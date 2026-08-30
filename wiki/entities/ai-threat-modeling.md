---
title: AI System Threat Modeling — AI 系統威脅建模
type: entity
created: 2026-08-30
updated: 2026-08-30
sources: 2
tags: [threat-modeling, STRIDE, attack-surface, risk-assessment]
canonical: entities/ai-threat-modeling
topics: [agent-infrastructure, software-quality]
---

> AI 系統的威脅建模方法論：STRIDE 模型在 AI 系統的應用、Agent-specific Attack Surface 分析、威脅建模工具。這是 W-2026-08-085（AI 系統資安框架設計）的核心方法論。

## STRIDE × AI Agent

| STRIDE | AI Agent 威脅 | 防禦 |
|---|---|---|
| **Spoofing** | Agent impersonation | Authentication、attestation |
| **Tampering** | 修改 prompt/tool output | Integrity verification |
| **Repudiation** | 否認操作 | Audit trail、logging |
| **Info Disclosure** | 資料外洩 | DLP、data classification |
| **DoS** | 資源耗盡 | Rate limiting、quota |
| **EoP** | 權限提升 | Sandbox、least privilege |

## Attack Surface 分類

```
┌─────────────────────────────────────┐
│         AI Agent System            │
├─────────────────────────────────────┤
│  Input Layer:                       │
│    - User prompts                   │
│    - External data (RAG)            │
│    - Tool outputs                   │
├─────────────────────────────────────┤
│  Processing Layer:                  │
│    - LLM inference                  │
│    - Tool execution                 │
│    - Memory access                  │
├─────────────────────────────────────┤
│  Output Layer:                      │
│    - Responses                      │
│    - Tool calls                     │
│    - Side effects                   │
├─────────────────────────────────────┤
│  Infrastructure Layer:              │
│    - API endpoints                  │
│    - Databases                      │
│    - External services              │
└─────────────────────────────────────┘
```

## Multi-agent 特有威脅

- **Agent Impersonation**：偽造其他 agent 身分
- **Message Tampering**：篡改 agent 間通訊
- **Collusion**：多 agent 合謀惡意操作
- **Deception**：agent 騙騙人類/其他 agent

## 威脅建模工具

| 工具 | 適用場景 | 優點 |
|---|---|---|
| **Microsoft TMT** | 一般系統 | 圖形化、STRIDE |
| **OWASP AI Security** | AI 系統 | 專為 AI 設計 |
| **MITRE ATLAS** | AI 威脅 | 豐富案例庫 |

## 來源

- [[wiki/sources/2026-08-30-ai-threat-modeling|AI Threat Modeling 深度研究]]
- [[wiki/sources/2026-08-30-dev-security-vibe-coding|Vibe Coding 資安基本功]]

## 相關頁面

- [[wiki/entities/agent-security|agent-security]] — agent 安全基礎
- [[wiki/sources/2026-08-30-dev-security-vibe-coding|Vibe Coding 資安基本功]] — CIA 原則 + Threat Modeling 基礎
