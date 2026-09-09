---
title: "usestrix/strix — 開源 AI 自主滲透測試工具研究"
type: source
created: 2026-09-09
updated: 2026-09-09
sources: 1
tags: [pentest, ai-security, vulnerability-scanner, docker, llm, open-source]
topics: [software-quality, container-infrastructure, skill-system]
canonical: sources/2026-09-09-usestrix-strix-github-research
provenance_raw: "raw/web/2026-09-09-usestrix-strix-github-research.md"
---

# usestrix/strix — 開源 AI 自主滲透測試工具研究

> Strix 是一個開源的 AI 自主滲透測試工具，用 AI Agent 模擬真實黑客行為：動態執行目標程式碼、找出漏洞、並透過實際 PoC 驗證。Apache 2.0 授權。

## 專案定位

Strix 是自動化滲透測試 + 漏洞掃描 + 利用 + 驗證工具。核心差異在於「真實 exploit 驗證」——它不只是掃描器，而是會實際嘗試利用漏洞並產出 working PoC，減少誤報。

> 「Strix are autonomous AI penetration testing agents that act just like real hackers - they run your code dynamically, find vulnerabilities, and validate them through actual proofs-of-concept.」— README.md

覆蓋 OWASP Top 10：SQLi、XSS、SSRF、RCE、IDOR、privilege escalation、JWT attacks、business logic flaws 等。

## LLM 使用方式

### API Key（按量計費）

預設方式，透過 LiteLLM 支援 100+ 供應商（OpenAI、Anthropic、Google Vertex AI、AWS Bedrock、Azure、OpenRouter、DeepSeek、Moonshot 等）。

```bash
export STRIX_LLM="openrouter/z-ai/glm-5.3"
export LLM_API_KEY="your-api-key"
```

### ChatGPT Plus/Pro 訂閱制 — 明確支援

Strix 內建 OAuth 流程，可用 ChatGPT Plus/Pro 訂閱帳號執行，**不需要 API key**。

```bash
strix auth login chatgpt
export STRIX_LLM="chatgpt/gpt-5.4"
```

實作上使用 OpenAI Codex CLI 相同的 OAuth client ID，走 `auth.openai.com` 的 OAuth 2.0 + PKCE 流程，token 送到 `chatgpt.com/backend-api/codex`。文件明確標註：「Using a ChatGPT subscription outside OpenAI's own products is not officially supported by OpenAI; the user chooses this path knowingly.」

**重要限制：** 只有 ChatGPT 訂閱被明確支援。Claude Pro/Max 訂閱登入**未在文件中被提及**；Anthropic Claude 只能透過 API key 使用。

### 本地模型

支援 Ollama、LM Studio 或任何 OpenAI-compatible server，但文件警告本地模型在 agentic task 上表現較差，建議重要評估仍用 frontier cloud model。

## Agent 相容性

### 獨立 CLI

Strix 本身是獨立 CLI 工具（`strix --target <target>`），不依賴任何外部 agent。

### 可搭配的外部 Agent

文件明確列出：**Claude Code、Cursor、Codex**，或任何 **SKILL.md-compatible** agent。透過 `npx skills add usestrix/strix` 安裝 9 個 skills。

### GitHub Copilot — 未提及

文件中**沒有提到 GitHub Copilot CLI 或 Copilot Coding Agent 作為可搭配的 agent**。Copilot MCP URL 只出現在 MCP server 範例中（Strix 連 GitHub API 用），不是 Copilot 作為 agent。

## Docker 依賴

### Self-hosted 模式 — 強制需要 Docker

README Prerequisites 明確列出「Docker (running)」。`STRIX_RUNTIME_BACKEND` 預設值為 `"docker"`，runtime 只註冊了 docker 一個 backend，直接 import `docker` SDK（docker-py）。

### Podman 可行性

搜尋整個 repo，`podman` 出現 **0 次**。文件完全未提及 Podman。推測可能路徑（但未被文件支持）：
1. Podman 的 Docker-compatible socket + `DOCKER_HOST` 環境變數
2. Podman symlink 模擬 `docker` CLI

### Dockerfile

基於 `kalilinux/kali-rolling:latest`，安裝完整滲透測試工具鏈（nmap、sqlmap、nuclei、Caido、chromium、trufflehog、gitleaks、trivy、semgrep 等）。

## Strix Cloud — 免 Docker 模式

不想碰 Docker 時可用 `strix cloud` 模式，掃描在 Strix Cloud 雲端執行。不需要 Docker、不需要 LLM key、不需要本地安裝。Enterprise 方案提供 SSO、compliance reports、VPC/self-hosted deployment。

## MCP 整合

可連接 Model Context Protocol (MCP) servers，暴露其 tools 給 agent 使用。支援 stdio（本地 subprocess）和 http（遠端）兩種 transport。

## 相關頁面

- [[wiki/entities/agent-security|Agent Security — AI Agent 安全基礎]]
- [[wiki/entities/docker|Docker]]
- [[wiki/entities/podman|Podman]]
- [[wiki/comparisons/docker-vs-podman|Docker vs Podman]]
- [[wiki/concepts/agent-skills|Agent Skills — Skill 系統根概念]]
