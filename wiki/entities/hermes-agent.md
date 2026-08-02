---
title: hermes-agent — 自我改進的 AI Agent
type: entity
created: 2026-08-05
updated: 2026-08-05
sources: 1
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

## 架構組件

| 組件 | 說明 |
|---|---|
| **CLI** | 互動式終端，multiline editing, slash-command autocomplete |
| **Gateway** | 多平台訊息網關，統一處理各平台輸入 |
| **Skills** | Procedural memory，可自動建立/改進，相容 agentskills.io |
| **Memory** | 持久化記憶 + user profiles，FTS5 搜尋 |
| **Tools** | 40+ tools，toolset system，可選 MCP |
| **Cron** | 自然語言排程，跨平台投遞 |

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

## 相關頁面

- [[wiki/entities/pi-mono|pi-mono]] — TypeScript agent toolkit
- [[wiki/entities/tau|tau]] — Pi 的 Python port
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/meta-harness|meta-harness]] — harness 再抽象研究
