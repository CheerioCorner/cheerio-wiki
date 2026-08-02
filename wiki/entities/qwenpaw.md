---
title: "QwenPaw — Agent OS 架構的本地 AI 助理"
type: entity
created: 2026-08-08
updated: 2026-08-08
sources: 2
tags: [qwenpaw, ai-agent, agent-os, security, local-deploy, agentscope]
collection: entities
topics: [ai-agent]
canonical: entities/qwenpaw
provenance_url: https://github.com/agentscope-ai/QwenPaw
---

> AgentScope 團隊開發的開源個人 AI 助理，以 Agent OS 架構為核心，強調本地部署、安全沙箱與多通道整合。

## 基本資訊

| 欄位 | 值 |
| --- | --- |
| 全名 | Qwen Personal Agent Workstation |
| 開發團隊 | agentscope-ai（阿里巴巴） |
| 授權 | Apache 2.0 |
| GitHub | [agentscope-ai/QwenPaw](https://github.com/agentscope-ai/QwenPaw) |
| Stars | ~29,700（2026-08-08） |
| 最新版本 | v2.0.1（2026-07-24） |
| 安裝方式 | pip / Docker / Desktop App (Tauri) / Alibaba Cloud ECS / AgentScope Platform |
| Python | ≥ 3.11, < 3.14 |
| 框架 | AgentScope 2.0 + Agent Communication Protocol (ACP) |

## 核心架構：Agent OS

每個 Agent 的工作空間由三大支柱組成：

1. **Resources** — 透明、獨立的檔案工作區，活動範圍清晰可控
2. **Governance** — Tool Guard + File Guard 存取規則引擎，攔截可疑行為
3. **Sandbox** — OS 核心層級隔離（macOS Seatbelt / Linux Bubblewrap+Landlock / Windows AppContainer）

### 安全機制（四層）

| 層級 | 功能 |
| --- | --- |
| **Sandbox** | 核心層級執行隔離，Shell 指令在受限環境運行 |
| **Tool Guard** | YAML 規則引擎 + ShellEvasionGuardian，偵測 command injection、path traversal、reverse shell |
| **File Guard** | 獨立於 Tool Guard，預設保護 `~/.qwenpaw.secret/`、`~/.ssh` 等 |
| **Skill Scanner** | 啟用前掃描，偵測 prompt injection、硬編碼密鑰、資料外洩 |

### 記憶系統

- **Scroll Context** — 所有 turn 持久化，evicted turn 可按需召回（不破壞性摘要）
- **ReMe** — 自我進化個人知識庫，對話與資源自動轉為可讀、可搜、可連結的 Markdown 記憶

## 功能特色

### Coding Mode
三欄式 Web IDE：file tree + diff preview + chat。Jump-to-definition、find-references、structural code search。

### 多通道整合
DingTalk / Lark / WeChat / Discord / Telegram / iMessage / QQ — 一個 instance，所有 channel。

### Skills & Plugins
- 內建 Skills：排程、文件處理、瀏覽器、新聞等
- Plugin 架構 + 市場
- MCP 整合外部工具

### 多 Agent
- 獨立 spawn sub-agent（各自記憶與 skills）
- Agent Communication Protocol (ACP) 跨系統編排

### TUI
`qwenpaw` 全螢幕終端聊天，與 Console / Channel 共用同一 agent。

## 部署選項

| 方式 | 說明 |
| --- | --- |
| `pip install qwenpaw` | Python 環境管理 |
| Script Install | 一鍵安裝（uv + venv），macOS/Linux/Windows |
| Docker | `agentscope/qwenpaw:latest` |
| Alibaba Cloud ECS | 一鍵部署 |
| AgentScope Platform | 免費雲端部署 |
| ModelScope Studio | 雲端部署 |
| Desktop App (Beta) | Tauri 打包，零配置 |

## 本機模型

- **QwenPaw Local**（llama.cpp）— 內建，Web UI 一鍵下載
- **Ollama** — 需安裝 Ollama 服務
- **LM Studio** — 需安裝 LM Studio
- **QwenPaw-Flash** — 2B / 4B / 9B 專為 agent 場景訓練，Q4/Q8 量化

## 社群評價

### 正面
- 易用性高，多通道整合強
- 本地部署 + 安全沙箱是差異化優勢
- 文件處理能力與主動記憶系統實用

### 顾虑
- 外掛生態系仍在早期
- 阿里巴巴色彩可能導致 Qwen 生態鎖定
- 多技術棧（Python/JS/Rust）提高維護成本
- 安全沙箱依賴 OS 底層特性，系統更新可能影響穩定性

## 相關頁面

- [[wiki/sources/2026-08-08-qwenpaw-youtube-intro|YouTube 影片介紹]]
- [[wiki/sources/2026-08-02-qwenpaw-github-readme|GitHub README]]
- [[wiki/entities/hermes-agent|hermes-agent]] — 另一個 AI agent 專案（自我改進 learning loop）
- [[wiki/entities/tau|tau]] — Pi 的 Python port（另一個本地 agent 選項）
- [[wiki/entities/pi-mono|pi-mono]] — Pi Agent Toolkit
