---
title: "Hermes Agent: GitHub README — 自我改進 AI Agent"
type: source
created: 2026-08-05
updated: 2026-08-05
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-02-nousresearchhermes-agent-the-agent-that-grows-with-you.md
tags: [hermes, ai-agent, python, self-improving, skills, memory, nous-research]
collection: sources
topics: [ai-agent, skill-cases-and-comparisons]
canonical: sources/2026-08-05-hermes-agent-github-readme
provenance_url: https://github.com/NousResearch/hermes-agent
---

> 來源：[NousResearch/hermes-agent GitHub README](https://github.com/NousResearch/hermes-agent)
> Clipped：2026-08-02

## 一句話

Nous Research 的自我改進 AI agent，有內建學習迴圈、多平台 gateway、7 種 terminal backend，可部署在 $5 VPS 到 GPU cluster。

## 重點摘要

### 定位

**「The agent that grows with you」**——唯一有內建學習迴圈的 agent：
- 完成複雜任務後**自動建立 skill**
- 使用中**自動改進 skill**
- **定期 nudge** 持久化記憶
- **FTS5 session search** + LLM summarization 做 cross-session recall
- **Honcho** dialectic user modeling（越來越了解你）

### 多平台部署

- **CLI**：互動式終端 TUI
- **Messaging**：Telegram, Discord, Slack, WhatsApp, Signal
- **Voice**：語音備忘轉錄
- **Cross-platform**：單一 conversation 跨平台延續

### 7 Terminal Backends

| Backend | 說明 |
|---|---|
| local | 本地終端 |
| Docker | 容器化 |
| SSH | 遠端主機 |
| Singularity | HPC 容器 |
| Modal | Serverless GPU |
| Daytona | Serverless workspace |
| Vercel Sandbox | 雲端沙箱 |

Daytona 和 Modal 支援 **serverless persistence**——agent 環境閒置時 hibernates，需要時喚醒，成本趨近於零。

### 記憶系統

- **Agent-curated memory**：agent 自己決定記什麼
- **Periodic nudges**：定期提醒 persist 重要知識
- **FTS5 session search**：全文搜尋過去 sessions
- **Honcho**：dialectic user modeling，建立 user profile
- **USER.md / MEMORY.md**：結構化記憶檔案

### Skills 系統

- **自動建立**：完成複雜任務後自主建立 skill
- **自動改進**：使用中自我優化
- **Skills Hub**：[agentskills.io](https://agentskills.io/) 開放標準
- **相容 OpenClaw**：可遷移 OpenClaw skills

### 內建排程

自然語言 cron scheduler：
- Daily reports
- Nightly backups
- Weekly audits
- 跨平台投遞（CLI / Telegram / Discord 等）

### Subagents

- 隔離子 agent 做平行工作
- Python script 透過 RPC 呼叫 tools
- 將多步驟 pipeline 折疊成零 context cost turns

### Nous Portal

統一訂閱：
- 300+ models
- Tool Gateway：web search (Firecrawl), image gen (FAL), TTS (OpenAI), cloud browser (Browser Use)
- `hermes setup --portal` 一行設定

### MCP Integration

支援連接任意 MCP server 延伸能力。

### Windows 原生支援

PowerShell 安裝包含：
- uv (Python package manager)
- Python 3.11
- Node.js
- ripgrep
- ffmpeg
- Portable Git Bash (MinGit)

安裝路徑：`%LOCALAPPDATA%\hermes`

### OpenClaw Migration

```bash
hermes claw migrate              # 互動式遷移
hermes claw migrate --dry-run    # 預覽
```

遷移內容：SOUL.md, memories, skills, API keys, messaging settings, workspace instructions。

## 與我們研究的關聯

### Harness 架構研究要點

1. **自我學習迴圈**——Pi/Tau 沒有的核心差異，值得深入研究
2. **Honcho user modeling**——如何建立「越來越了解你」的 user profile
3. **7 Terminal Backends**——部署彈性遠超 Pi/Tau
4. **Skills Hub (agentskills.io)**——開放標準的 skill 生態
5. **Multi-platform gateway**——單一 agent 跨平台服務

### 待深入研究

- Architecture 頁面：`hermes-agent.nousresearch.com/docs/developer-guide/architecture`
- Skills 系統：自動建立/改進的機制
- Memory 系統：Honcho dialectic model 的運作方式
- Trajectory compression：用於訓練下一代 tool-calling model

## 來源

- [[raw/web/2026-08-02-nousresearchhermes-agent-the-agent-that-grows-with-you|Raw — GitHub README]]

## 相關頁面

- [[wiki/entities/hermes-agent|hermes-agent]] — entity 頁面
- [[wiki/entities/pi-mono|pi-mono]] — TypeScript agent toolkit
- [[wiki/entities/tau|tau]] — Pi 的 Python port
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/meta-harness|meta-harness]] — harness 再抽象研究
