---
title: "Agent Security 基礎 — 深度研究"
type: source
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [agent-security, prompt-injection, sandbox, deep-research]
canonical: sources/2026-08-30-agent-security-basics
topics: [agent-infrastructure, software-quality]
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260830-001/research-report.md
---

> 來源：Gemini Notebook 深度研究（rc-20260830-001），64 筆來源。涵蓋 Prompt Injection 防禦、Agent 權限邊界、多 Agent 信任模型、Sandbox 隔離、AI Coding Agent 安全架構比較。

## 研究摘要

### 1. Prompt Injection 防禦

| 類型 | 定義 | 防禦機制 |
|---|---|---|
| **Direct** | 攻擊者直接在 input 中注入指令 | Input validation、output filtering、system prompt hardening |
| **Indirect** | 攻擊指令藏在外部資料（文件/網頁）中 | Content sanitization、dual LLM pattern、instruction hierarchy |

### 2. Agent 權限邊界

- **Tool Permission**：每個 tool 有明確的 permission schema，agent 只能呼叫被授權的 tool
- **Resource Access Control**：filesystem/network/database 的存取控制
- **Capability-based Security**：agent 只持有特定 capability token，不能超出範圍

### 3. 多 Agent 信任模型

- **Capability Delegation**：主 agent 將特定能力委派給子 agent，有明確的範圍與時限
- **Attestation**：agent 間透過簽章驗證身分與能力
- **Sandbox 隔離**：每個 agent 在獨立的執行環境中運作

### 4. AI Coding Agent 安全架構比較

| Agent | 安全機制 | 限制 |
|---|---|---|
| **Pi** | Extension permission、Tool policy、Session isolation | 依賴開發者正確設定 |
| **Claude Code** | Permission prompts、File locking、Sandbox | 安全提示可能被忽略 |
| **Codex** | Network restriction、Filesystem sandbox | 雲端執行有資料外洩風險 |
| **Copilot** | Enterprise policy、Audit log | 商業方案，透明度較低 |

## 關鍵引用

- OWASP Top 10 for LLM Applications
- Anthropic: AI Security Research
- Microsoft: Prompt Injection Defense
- NIST AI Risk Management Framework

## 相關頁面

- [[wiki/entities/agent-security|agent-security]] — entity 頁面
- [[wiki/entities/herdr|herdr]] — 常駐背景 + 狀態偵測
- [[wiki/entities/opentelemetry|opentelemetry]] — 觀測性標準
