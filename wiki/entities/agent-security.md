---
title: Agent Security — AI Agent 安全基礎
type: entity
created: 2026-08-30
updated: 2026-08-30
sources: 2
tags: [agent-security, prompt-injection, sandbox, permission]
canonical: entities/agent-security
topics: [agent-infrastructure, software-quality]
---

> AI Agent 安全的核心議題：Prompt Injection 防禦、Agent 權限邊界、多 Agent 信任模型、Sandbox 隔離。這是 W-2026-08-085（AI 系統資安框架設計）的基礎知識。

## 核心威脅

### 1. Prompt Injection

| 類型 | 攻擊方式 | 防禦 |
|---|---|---|
| **Direct** | 直接在 input 注入指令 | Input validation、output filtering |
| **Indirect** | 指令藏在外部資料中 | Content sanitization、dual LLM |

### 2. Tool Abuse

- Agent 可能被操控呼叫不該呼叫的 tool
- 防禦：Tool permission schema、rate limiting、audit trail

### 3. Data Exfiltration

- Agent 可能被操控洩漏敏感資料
- 防禦：Output filtering、DLP、data classification

### 4. Privilege Escalation

- Agent 可能獲取超出權限的能力
- 防禦：Capability-based security、sandbox、least privilege

## 權限邊界設計

### Tool Permission

每個 tool 有明確的 permission schema：
```json
{
  "tool": "file_read",
  "permissions": ["read"],
  "scope": "workspace/",
  "requires_approval": false
}
```

### Resource Access Control

- **Filesystem**：只允許存取特定目錄
- **Network**：只允許特定 API endpoint
- **Database**：Row-Level Security（RLS）

### Capability-based Security

Agent 只持有特定 capability token，不能超出範圍。

## 多 Agent 信任模型

| 機制 | 原理 | 適用場景 |
|---|---|---|
| **Capability Delegation** | 主 agent 委派能力給子 agent | Sub-agent 系統 |
| **Attestation** | 透過簽章驗證身分 | 高安全需求 |
| **Sandbox Isolation** | 獨立執行環境 | 不信任的 agent |

## AI Coding Agent 安全比較

| Agent | 安全機制 | 信任模型 |
|---|---|---|
| **Pi** | Extension permission、Tool policy | 開發者設定 |
| **Claude Code** | Permission prompts、File locking | 用戶確認 |
| **Codex** | Network restriction、Sandbox | 雲端隔離 |
| **Copilot** | Enterprise policy、Audit | 企業管理 |

## 來源

- [[wiki/sources/2026-08-30-agent-security-basics|Agent Security 基礎深度研究]]
- [[wiki/sources/2026-08-30-skill-supply-chain-security|Skill Supply Chain Security 深度研究]]

## 相關頁面

- [[wiki/entities/opentelemetry|opentelemetry]] — 觀測性標準
- [[wiki/entities/herdr|herdr]] — 常駐背景 + 狀態偵測
- [[wiki/sources/2026-08-30-dev-security-vibe-coding|Vibe Coding 資安基本功]] — CIA 原則
