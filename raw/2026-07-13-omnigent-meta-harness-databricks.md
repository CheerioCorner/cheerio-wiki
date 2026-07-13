---
title: "Introducing Omnigent: A Meta-Harness to Combine, Control and Share Your Agents"
source_type: blog
url: https://www.databricks.com/blog/introducing-omnigent-meta-harness-combine-control-and-share-your-agents
published: 2026-06 (approximate)
author: Databricks (Databricks Engineering Team)
---

# Introducing Omnigent: A Meta-Harness to Combine, Control and Share Your Agents

> Databricks 官方部落格文章，宣布開源 Omnigent — 一個位於現有 AI agents 之上的 meta-harness 層。

---

## 核心論點

Databricks 認為 agent engineering 的 frontier 正在向上移動一層：

- 單一 model + 單一 harness 已經不是最佳解
- Harvey、Anthropic、Databricks Genie 等成功案例都已改用 **多 agent / 多 harness 協作**
- 但現有每個 harness 都是自己的 silo，context、controls、執行方式都無法互通
- 需要一個 **meta-harness** 作為 abstraction layer

## Omnigent 三大支柱

### 1. Combine（組合）
- 在多個 agents 之間無縫切換（Claude Code、Codex、Pi、自訂 agents），只要一行 config 改變
- 多 harness 創作：用 YAML 定義 custom agent，port 到不同 harness
- 支援 terminal-based coding agents（Claude Code、Codex、Pi 等）和 SDKs（OpenAI Agents、Claude Agents SDK）

### 2. Control（控制）
- **Contextual security policies**：不只是 allow/deny，而是追蹤 session 動態狀態做智慧決策
  - 範例：下載 npm package 後→需要人類批准才能 git push
  - 範例：只能寫入自己建立的檔案，不能寫任意檔案
- **Cost policies**：動態追蹤 LLM 成本，達到 threshold 後暫停詢問
- **Strong OS sandbox**（OmniBox）：可靈活鎖定 OS 存取、攔截/轉換網路請求

### 3. Share（協作）
- 即時協作：透過 URL 分享 live agent session
- 隊友可以 review 檔案、留言、甚至發送 commands
- 多種介面：web、mobile、Mac OS native app、API

## 技術架構

- **Runner**：將任何 agent 包裝在 sandboxed session 中，提供統一 API
- **Server**：提供 policies 和 sharing 功能，透過 terminal、app、web APIs 暴露 session
- **Cloud execution**：可在本機或 hosted sandbox（Modal、Daytona）上執行

## 與現有 meta-harness 的關係

Omnigent 與 [[omnigent-entity|OmniAgent]] 同屬「定義 B（orchestration layer）」的實作，但：

- 比 OmniAgent 更強調 **security policies** 和 **collaboration**（即時共享 session）
- 開源（Apache 2.0），由 Databricks 支援
- roadmap 包含整合 GEPA（auto optimization at meta-harness level）、MemEx、RLM 等技術

## 連結

- GitHub: https://github.com/omnigent-ai/omnigent
- Docs: https://omnigent.ai/
- Quickstart: https://omnigent.ai/quickstart/install