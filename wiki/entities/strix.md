---
title: "Strix — 開源 AI 自主滲透測試工具"
type: entity
created: 2026-09-09
updated: 2026-09-09
sources: 1
tags: [pentest, ai-security, vulnerability-scanner, open-source, llm, docker]
topics: [software-quality, container-infrastructure, skill-system]
canonical: entities/strix
---

# Strix — 開源 AI 自主滲透測試工具

> Strix 是一個開源的 AI 自主滲透測試工具（Apache 2.0），用 AI Agent 模擬真實黑客行為，動態執行目標程式碼、找出漏洞、並透過實際 PoC 驗證。

## 核心能力

- **全套滲透測試工具**：reconnaissance、exploitation、validation
- **多 agent 編排**：teams of AI pentesters 協作與平行執行
- **真實 exploit 驗證**：working PoC，不是傳統掃描器的 false positive
- **開發者優先 CLI**：headless 模式支援 CI/CD 整合
- **自動修復與報告**：AI 生成 security patches + compliance-ready pentest reports（SARIF 2.1.0）

## LLM 使用方式

### API Key（按量計費）

透過 LiteLLM 支援 100+ 供應商：OpenAI、Anthropic、Google Vertex AI、AWS Bedrock、Azure、OpenRouter、DeepSeek、Moonshot 等。

### ChatGPT Plus/Pro 訂閱制 — 明確支援

Strix 內建 OAuth 流程，可用 ChatGPT Plus/Pro 訂閱帳號執行，不需要 API key。實作上使用 OpenAI Codex CLI 相同的 OAuth client ID，走 `auth.openai.com` 的 OAuth 2.0 + PKCE 流程。文件明確標註此用法「not officially supported by OpenAI」。

**限制：** 只有 ChatGPT 訂閱被明確支援。Claude Pro/Max 訂閱登入未在文件中被提及；Anthropic Claude 只能透過 API key 使用。

### 本地模型

支援 Ollama、LM Studio 或任何 OpenAI-compatible server，但文件警告本地模型在 agentic task 上表現較差。

## Agent 相容性

### 獨立 CLI

`strix --target <target>` 可直接在終端執行，不依賴任何外部 agent。

### 可搭配的外部 Agent

文件明確列出：**Claude Code、Cursor、Codex**，或任何 **SKILL.md-compatible** agent。透過 `npx skills add usestrix/strix` 安裝 9 個 skills。

### GitHub Copilot — 未提及

文件中沒有提到 GitHub Copilot CLI 或 Copilot Coding Agent 作為可搭配的 agent。

## Docker 依賴

### Self-hosted 模式 — 強制需要 Docker

`STRIX_RUNTIME_BACKEND` 預設值為 `"docker"`，runtime 只註冊了 docker 一個 backend。Dockerfile 基於 `kalilinux/kali-rolling:latest`，安裝完整滲透測試工具鏈。

### Podman 可行性

搜尋整個 repo，`podman` 出現 0 次。文件完全未提及 Podman。推測可能路徑（未被文件支持）：
1. Podman 的 Docker-compatible socket + `DOCKER_HOST` 環境變數
2. Podman symlink 模擬 `docker` CLI

## Strix Cloud — 免 Docker 模式

`strix cloud` 模式掃描在 Strix Cloud 雲端執行，不需要 Docker、LLM key 或本地安裝。Enterprise 方案提供 SSO、compliance reports、VPC/self-hosted deployment。

## MCP 整合

可連接 Model Context Protocol (MCP) servers，支援 stdio 和 http 兩種 transport。

## 來源

- [[wiki/sources/2026-09-09-usestrix-strix-github-research|usestrix/strix — 開源 AI 自主滲透測試工具研究]]

## 相關頁面

- [[wiki/entities/agent-security|Agent Security — AI Agent 安全基礎]]
- [[wiki/entities/docker|Docker]]
- [[wiki/entities/podman|Podman]]
- [[wiki/comparisons/docker-vs-podman|Docker vs Podman]]
- [[wiki/concepts/agent-skills|Agent Skills — Skill 系統根概念]]
