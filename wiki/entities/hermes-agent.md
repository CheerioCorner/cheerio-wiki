---
title: hermes-agent — 自我改進的 AI Agent
type: entity
created: 2026-08-05
updated: 2026-08-08
sources: 2
tags: [hermes, ai-agent, python, self-improving, skills, memory]
collection: entities
topics: [ai-agent, skill]
canonical: entities/hermes-agent
---

> [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) 是 Nous Research 開發的自我改進 AI agent。與 Pi / Tau 最大的差異：**有內建學習迴圈**——從經驗自動建立 skill、使用中自動改進、主動 persist 知識、跨 session 回憶。

## 核心特色

| 特色 | 說明 |
|---|---|
| **自我學習迴圈** | 完成複雜任務後自動建立 skill；使用中自動改進；定期 nudge 持久化記憶 |
| **記憶系統** | FTS5 session search + LLM summarization；Honcho dialectic user modeling |
| **多平台** | Telegram, Discord, Slack, WhatsApp, Signal, CLI — 單一 gateway process |
| **排程** | 內建 cron scheduler，自然語言定義，可投遞到任意平台 |
| **Subagents** | 隔離子 agent 做平行工作；Python script 透過 RPC 呼叫 tools |
| **7 種 Terminal Backend** | local, Docker, SSH, Singularity, Modal, Daytona, Vercel Sandbox |
| **Research-ready** | Batch trajectory generation, trajectory compression for training |

## 與 Pi / Tau 的比較

| 面向 | Pi | Tau | Hermes |
|---|---|---|---|
| 語言 | TypeScript | Python | Python |
| 自我學習 | ✗（靜態 skills） | ✗（靜態 skills） | ✓（自動建立 + 改進 skill） |
| 記憶 | Session JSONL | Session JSONL | Honcho user modeling + FTS5 cross-session |
| 多平台 | CLI only | CLI only | Telegram/Discord/Slack/WhatsApp/Signal |
| 排程 | 需 extension | 需 extension | 內建 cron |
| Terminal Backend | 本地 | 本地 | 7 種（含 serverless） |
| MCP | 不支援（刻意） | 未提及 | ✓ 支援 |
| Extension 相容 | Pi 生態 | 與 Pi 相容 | 獨立生態 |
| Skills Hub | 無 | 無 | [agentskills.io](https://agentskills.io/) |

## 安裝

```bash
# Linux / macOS / WSL2
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash

# Windows PowerShell（原生支援，不需要 WSL）
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

- 需要 Python 3.11+
- Windows 安裝包含 MinGit（portable，不影響系統 Git）
- 安裝路徑：`%LOCALAPPDATA%\hermes`（Windows）/ `~/.hermes`（Linux）

## Agent Loop

```
User message → Build context → Send to LLM → Tool calls（可重複）→ Response → Memory update
```

每次使用者發訊息都跑一次完整 loop。Memory update 分析對話中是否有值得記憶的資訊，寫入記憶讓 agent 持續學習。

## Context 構建

每次 turn 都從零構建 context，由以下組件構成：

| 檔案 | 用途 | 更新方式 |
|---|---|---|
| **soul.md** | Agent 個性（語調、靈感、目標） | 預設為空，可自訂或讓 agent 寫 |
| **user.md** | 使用者資訊 | Agent 自動從對話中學習 |
| **memory.md** | 任意記憶（工具用法、workflow） | Agent 自動更新 |

額外 context：外部記憶摘要（需設定 external memory）、skill/tool 描述、最近訊息歷史。

## Context Compression

- 預設在 context window **50%** 時觸發（可設定 70% 或 80%）
- **觸發時機**：(1) 每次 turn 前 (2) LLM 回傳 context error 時
- Token 估算：第一次用 `字元數 / 4`，之後用 LLM response 的 `usage` 參數
- Compression prompt（`context_compressor.py`）輸出：目標、約束、已完成動作、活躍狀態、歷史進度、阻塞、決策、已解決問題、相關檔案、關鍵 context、先前摘要

## Gateway（多平台網關）

- **平台**：Telegram、Discord、Slack、WhatsApp、Email、SMS
- **架構**：Async.io loop 持續 poll 各 gateway
- **每個平台需獨立設定**（bot ID、user ID），非統一 gateway
- **Session ID**：`<platform>_<session_id>` 格式，存於 SQLite
- **Session Manager**：處理 `/interrupt`、`/steer`、併列等指令
- **Context 構建**：每次收到訊息從零構建（不像 CLI 有完整對話歷史）

## Memory（三層記憶系統）

| 層級 | 儲存方式 | 內容 |
|---|---|---|
| **Markdown 檔案** | soul.md / user.md / memory.md | 個性、使用者資訊、任意事實 |
| **SQLite** | Session transcripts + bare text table | 完整對話 + 純文字供 similarity search |
| **External Memory** | Mem0 / SuperMemory / Honcho 等 | 跨 session 智慧記憶，需額外設定 |

- External memory 查詢時機：第一則訊息**之後**（類似人類「先聽問題再回想」）
- 各 provider 差異：Mem0 用 similarity search、SuperMemory 傳整段歷史由 LLM 擷取、Honcho 用 dialectic user modeling

## Cron Jobs

- 非綁定系統 cron，Hermes 自己的 async loop
- **Tick function** 每分鐘執行，檢查 `~/.hermes/cron/jobs.json`
- 輸出：`cron/output/<job-id>/run.md`
- 通知自動發送到 home messaging platform（設定 gateway 時指定），非透過 send_message tool

## 架構組件

| 組件 | 說明 |
|---|---|
| **CLI** | 互動式終端，multiline editing, slash-command autocomplete |
| **Gateway** | 多平台訊息網關，Async.io loop + session manager |
| **Skills** | Procedural memory，可自動建立/改進，相容 agentskills.io |
| **Memory** | 三層：Markdown files + SQLite + External providers |
| **Tools** | 40+ tools，toolset system，可選 MCP |
| **Cron** | 自然語言排程，tick function 每分鐘檢查 jobs.json |

## Nous Portal

統一訂閱服務，一個 key 搞定：
- 300+ 模型
- Tool Gateway：web search (Firecrawl), image gen (FAL), TTS (OpenAI), cloud browser (Browser Use)

```bash
hermes setup --portal
```

## Skills Hub

[agentskills.io](https://agentskills.io/) — 開放標準的 skill 市場，Hermes 相容。

## 來源

- [[wiki/sources/2026-08-05-hermes-agent-github-readme|2026-08-05 Hermes Agent: GitHub README]]
- [[wiki/sources/2026-08-08-hermes-architecture-explained|2026-08-08 Hermes Architecture EXPLAINED]]

## 相關頁面

- [[wiki/entities/pi-mono|pi-mono]] — TypeScript agent toolkit
- [[wiki/entities/tau|tau]] — Pi 的 Python port
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/meta-harness|meta-harness]] — harness 再抽象研究
