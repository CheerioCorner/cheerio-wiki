---
title: "Skill Supply Chain Security — 深度研究"
type: source
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [skill-security, supply-chain, sandbox, deep-research]
canonical: sources/2026-08-30-skill-supply-chain-security
topics: [agent-infrastructure, software-quality, skill]
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260830-004/research-report.md
---

> 來源：Gemini Notebook 深度研究（rc-20260830-004），95 筆來源。涵蓋 Skill 供應鏈風險、權限控管、Sandbox 隔離、現有 Skill 生態系安全機制比較。

## 研究摘要

### 1. Skill 供應鏈風險

| 風險 | 定義 | 防禦 |
|---|---|---|
| **Malicious Skill** | 故意植入惡意邏輯的 skill | Code review、signature verification |
| **Dependency Confusion** | 替套件名稱混淆，安裝惡意版本 | Private registry、lockfile |
| **Typosquatting** | 利用拼寫錯誤誘導安裝 | Name validation、scanning |
| **Data Exfiltration** | Skill 竊取敏感資料 | Permission control、sandbox |

### 2. Skill 權限控管

- **Tool Permission**：每個 tool 有明確的 permission schema
- **Resource Access**：filesystem/network/database 存取控制
- **Capability Restriction**：skill 只能使用被授權的能力

### 3. Skill Sandbox 隔離

| 策略 | 實作方式 | 適用場景 |
|---|---|---|
| **Process Isolation** | 獨立進程、容器 | 高隔離需求 |
| **Filesystem Restriction** | chroot、namespace | 檔案存取控制 |
| **Network Control** | firewall、proxy | 網路存取控制 |
| **Resource Limits** | cgroup、quota | 資源使用限制 |

### 4. 現有 Skill 生態系比較

| 生態系 | 安全機制 | 審查流程 | 信任模型 |
|---|---|---|---|
| **Claude Code Skills** | Permission prompts、sandbox | 人工審查 | 用戶確認 |
| **Copilot Extensions** | Enterprise policy、audit | 微軟審查 | 企業管理員 |
| **Pi Extensions** | Extension permission、tool policy | 開發者自律 | 用戶信任 |

## 關鍵引用

- OWASP: Software Component Verification Standard
- NIST: Secure Software Development Framework
- SLSA: Supply-chain Levels for Software Artifacts

## 相關頁面

- [[wiki/entities/agent-security|agent-security]] — agent 安全基礎
- [[wiki/concepts/agent-skills|agent-skills]] — Skill 系統根概念
- [[wiki/concepts/agent-extensibility-hierarchy|agent-extensibility-hierarchy]] — 擴充架構層級
