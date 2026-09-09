---
title: "usestrix/strix GitHub Repo 研究"
source_kind: web
source_url: https://github.com/usestrix/strix
fetched: 2026-09-09
method: git clone + file inspection
---

# usestrix/strix — GitHub Repo 研究紀錄

研究日期：2026-09-09
研究方式：`git clone --depth 1` 後逐一檢視 README.md、pyproject.toml、AGENTS.md、Dockerfile、docker-entrypoint.sh、docs/、strix/runtime/、strix/config/codex.py、strix/interface/auth_cli.py 等檔案。

---

## 1. 專案定位

**Strix 是一個開源的 AI 自主滲透測試工具。**

> 來源：README.md 第一段
> 「Strix are autonomous AI penetration testing agents that act just like real hackers - they run your code dynamically, find vulnerabilities, and validate them through actual proofs-of-concept.」

功能涵蓋：
- 全套滲透測試工具（reconnaissance、exploitation、validation）
- 多 agent 編排（teams of AI pentesters）
- 真實 exploit 驗證（PoC，不是 false positive）
- 開發者優先 CLI
- 自動修復與報告生成（SARIF 2.1.0 格式）

> 來源：pyproject.toml keywords：`cybersecurity`, `security`, `vulnerability`, `scanner`, `pentest`, `agent`, `ai`, `cli`

---

## 2. LLM 使用方式

### 2.1 API Key（按量計費）— 預設方式

透過 LiteLLM 支援 100+ 供應商。

> 來源：docs/llm-providers/overview.mdx
> ```
> export STRIX_LLM="openrouter/z-ai/glm-5.3"
> export LLM_API_KEY="your-api-key"
> ```

支援的供應商包括：OpenAI、Anthropic、Google Vertex AI、AWS Bedrock、Azure OpenAI、OpenRouter、DeepSeek、Moonshot 等。

> 來源：docs/llm-providers/overview.mdx 表格與 Provider Guides 區塊

### 2.2 ChatGPT Plus/Pro 訂閱制帳號 — 明確支援

Strix 內建 OAuth 登入流程，可用 ChatGPT Plus/Pro 訂閱帳號執行，不需要 API key。

> 來源：README.md
> ```
> strix auth login chatgpt             # sign in with your ChatGPT account
> export STRIX_LLM="chatgpt/gpt-5.4"   # chatgpt/<model> runs on the subscription
> strix auth status                    # show the active sign-in, or logout to forget it
> ```

> 來源：strix/interface/auth_cli.py docstring
> 「Signing in only stores OAuth tokens (~/.strix/subscription-auth.json); model selection stays with STRIX_LLM. A `chatgpt/<model>` STRIX_LLM runs on the subscription.」

> 來源：strix/config/codex.py docstring
> 「Using a ChatGPT subscription outside OpenAI's own products is not officially supported by OpenAI; the user chooses this path knowingly. The OAuth constants are OpenAI's own Codex CLI values (the backend only accepts that client).」

實作方式：
- 使用 OpenAI Codex CLI 相同的 OAuth client ID (`app_EMoamEEZ73f0CkXaXp7hrann`)
- 走 `auth.openai.com` 的 OAuth 2.0 + PKCE 流程
- 取得 token 後把請求送到 `chatgpt.com/backend-api/codex`
- Token 存在 `~/.strix/subscription-auth.json`

### 2.3 本地模型

支援 Ollama、LM Studio 或任何 OpenAI-compatible server。

> 來源：docs/llm-providers/local.mdx
> ```
> export STRIX_LLM="ollama/qwen3-vl"
> export LLM_API_BASE="http://localhost:11434"
> ```

### 2.4 重要限制

只有 ChatGPT 訂閱被明確支援。Claude Pro/Max 訂閱登入在文件中**未被提及**。Anthropic 的 Claude 只能透過 API key 使用。

---

## 3. Agent 相容性

### 3.1 獨立 CLI — 不綁定任何 agent

Strix 本身是獨立的 CLI 工具：`strix --target <target>`，可直接在終端執行。

### 3.2 可搭配的外部 Agent

> 來源：README.md
> 「Strix is agent-ready. Give Claude Code, Cursor, Codex, or any SKILL.md-compatible agent the ability to run pentests, fix findings, and set up CI scanning:」
> ```
> npx skills add usestrix/strix
> ```

> 來源：AGENTS.md
> 列出 9 個 skill：penetration-testing-with-strix、managed-pentesting-with-strix、fix-security-vulnerabilities-with-strix、ci-security-scanning-with-strix 等。

### 3.3 GitHub Copilot — 未在支援列表中

文件中**沒有提到 GitHub Copilot CLI 或 Copilot Coding Agent 作為可搭配的 agent**。Copilot MCP URL (`https://api.githubcopilot.com/mcp/`) 只出現在 MCP server 範例中，那是 Strix 自己連 GitHub API 用的，不是 Copilot 作為 agent。

---

## 4. Docker 依賴

### 4.1 Self-hosted 模式 — 強制需要 Docker

> 來源：README.md Prerequisites：「Docker (running)」
> 來源：AGENTS.md：「Requires Docker running.」
> 來源：docs/advanced/configuration.mdx：`STRIX_RUNTIME_BACKEND` 預設值為 `"docker"`

### 4.2 Runtime Backend 架構

> 來源：strix/runtime/backends.py
> 只註冊了 `"docker"` 一個 backend。`_BACKENDS` 字典只有 docker。

> 來源：strix/runtime/docker_client.py
> 直接 `import docker`（Python 的 docker SDK / docker-py）

> 來源：pyproject.toml dependencies：`docker>=7.1.0`

### 4.3 Podman 可行性

搜尋整個 repo（.md、.mdx、.py、.toml、.yaml），`podman` 這個詞出現 **0 次**。文件中完全沒有提到 Podman。

可能的替代路徑（推測，文件未提及）：
1. Podman 的 Docker-compatible socket（`podman.sock`）+ `DOCKER_HOST` 環境變數
2. Podman 模擬 `docker` CLI（symlink）

但這些都沒有在文件中被說明或測試。

### 4.4 Dockerfile 內容

> 來源：containers/Dockerfile
> 基於 `kalilinux/kali-rolling:latest`，安裝了完整的滲透測試工具鏈：
> - nmap, sqlmap, nuclei, subfinder, naabu, ffuf
> - Caido (HTTP proxy)
> - chromium (browser automation)
> - trufflehog, gitleaks, trivy (secret/漏洞掃描)
> - semgrep, bandit (SAST)
> - 等等

---

## 5. Strix Cloud — 免 Docker 模式

> 來源：README.md Ways to Run Strix 表格
> 「Strix Cloud — no setup, validated findings, one-click autofix, and PR reviews.」

> 來源：AGENTS.md
> 「Managed cloud (app.strix.ai): no Docker, no LLM key, no local install; adds team dashboards, scheduling, PR reviews, and downloadable PDF/DOCX reports (Enterprise plan).」

Cloud 模式透過 `strix cloud` CLI 操作，掃描在 Strix Cloud 雲端執行。

---

## 6. 其他技術細節

### MCP 整合

> 來源：docs/integrations/mcp.mdx
> Strix 可連接 Model Context Protocol (MCP) servers，暴露其 tools 給 agent 使用。

### CI/CD 整合

> 來源：README.md
> 支援 GitHub Actions，可設定在 PR 時自動執行安全掃描。

### 授權

> 來源：pyproject.toml license: Apache-2.0
> 來源：README.md：「Authorized use only.」
