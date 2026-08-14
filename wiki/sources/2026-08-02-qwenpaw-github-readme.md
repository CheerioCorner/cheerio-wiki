---
title: "QwenPaw: GitHub README — 完整功能與安裝指南"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-02-agentscope-aiqwenpaw-your-personal-ai-assistant.md
    url: https://github.com/agentscope-ai/QwenPaw
tags: [qwenpaw, ai-agent, agent-os, security, local-deploy, agentscope, github]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-02-qwenpaw-github-readme
provenance_raw: raw/web/2026-08-02-agentscope-aiqwenpaw-your-personal-ai-assistant.md
provenance_url: https://github.com/agentscope-ai/QwenPaw
provenance_url: https://github.com/agentscope-ai/QwenPaw
---

> 來源：[agentscope-ai/QwenPaw GitHub README](https://github.com/agentscope-ai/QwenPaw)
> Clipped: 2026-08-02

## 一句話

QwenPaw 是 AgentScope 的開源個人 AI 助理，支援本地/雲端部署、多通道聊天、安全沙箱、Coding Mode、Skills/Plugin 擴充。

## 重點摘要

### 核心特色

| 特色 | 說明 |
| --- | --- |
| **三層記憶** | Live working context + 完整 verbatim history + ReMe 自我進化知識庫 |
| **本地/雲端** | QwenPaw-Flash (2B/4B/9B) 無需 API key；也支援 Ollama、LM Studio、14+ 雲端 |
| **安全內建** | Kernel-level Sandbox + Tool Guard + File Guard + Skill Scanner + Access Policy |
| **多 Agent** | 獨立 spawn sub-agent，ACP 跨系統編排 |
| **Coding Mode** | 三欄 Web IDE（file tree + diff + chat），jump-to-definition、find-references |
| **Extensible** | Skills + Plugin marketplace + MCP 整合 |
| **多通道** | DingTalk / Lark / WeChat / Discord / Telegram / iMessage / QQ |
| **資料主權** | 本地部署，資料不離開機器 |

### 安裝方式（7 種）

1. **Pip Install** — `pip install qwenpaw`
2. **Script Install** — 一鍵（uv + venv），macOS/Linux/Windows
3. **Docker** — `agentscope/qwenpaw:latest`
4. **Alibaba Cloud ECS** — 一鍵部署
5. **AgentScope Platform** — 免費雲端
6. **ModelScope Studio** — 雲端
7. **Desktop App (Beta)** — Tauri，零配置

### Agent OS 2.0 架構（v2.0.0）

- **Agent OS — Workspace**：Resources（透明檔案）+ Governance（allow/deny/ask/sandbox）+ Sandbox（OS 核心隔離）
- **Agent OS — Drivers**：MCP / A2A / ACP 連接層，加密憑證 + per-call policy gate
- **Loop Engineering**：Coding Mode、Mission Mode 等進階 agent loop 模板
- **Scroll Context**：每 turn 持久化，evicted turn 可按需召回
- **ReMe v0.4**：對話→可讀/可搜/可連結 Markdown 記憶
- **Terminal UI**：全螢幕終端聊天

### 安全四層防護

- **Sandbox**：Seatbelt (macOS) / Bubblewrap+Landlock (Linux) / AppContainer (Windows)
- **Tool Guard**：YAML 規則引擎 + ShellEvasionGuardian，偵測 injection/traversal/reverse shell
- **File Guard**：獨立防護，預設保護 `~/.qwenpaw.secret/`、`~/.ssh`
- **Skill Scanner**：啟用前掃描 prompt injection、硬編碼密鑰、資料外洩

### 本機模型

- QwenPaw Local（llama.cpp）— 內建
- Ollama / LM Studio — 需安裝服務
- QwenPaw-Flash — 2B/4B/9B agent 專用模型，Q4/Q8 量化

### 路線圖

- 多模型切換（進行中）
- Browser-use Chrome 擴充（進行中）
- QwenPaw Creator / Insight（進行中）
- 兼容現有 agents（如 Claude Code）（規劃中）
- Group chat（規劃中）

## 來源

- [[../../raw/web/2026-08-02-agentscope-aiqwenpaw-your-personal-ai-assistant|Raw GitHub README]]

## 相關頁面

- [[wiki/entities/qwenpaw|QwenPaw]]
- [[wiki/sources/2026-08-08-qwenpaw-youtube-intro|YouTube 影片介紹]]
