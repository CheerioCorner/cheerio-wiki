---
title: "Herdr 深度研究 — Coding Agent 的 Terminal Runtime"
type: source
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [herdr, deep-research, agent-runtime, orchestration]
canonical: sources/2026-08-25-herdr-deep-research
topics: [agent-runtime-implementations, coding-agent]
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260825-002/research-report.md
  - kind: raw
    path: raw/youtube/herdr-runtime-for-coding-agents.md
---

> 來源：Gemini Notebook 深度研究（rc-20260825-002）+ YouTube 影片逐字稿。研究範圍涵蓋 Herdr 架構設計、與 Pi/Tau 比較、Agent Orchestration 實現、對 Cheerio 助理的啟發。

## 研究摘要

### 1. 核心架構

Herdr 是基於 Rust + Ratatui 的**終端機代理人執行期與多路複用器**，採用 Client-Server 模型：背景伺服器常駐託管所有 PTY 與 agent 進程，薄客戶端隨時可 detach/reattach。

**資源管理層級**：Workspace（專案）→ Tab（版面）→ Pane（PTY）→ Agent（AI 進程）

**四大語義狀態**：🔴 Blocked（等人工）、🟡 Working、🔵 Done、🟢 Idle

**狀態偵測雙軌制**：
- **Lifecycle Hook（精準）**：agent 透過事件主動向 Herdr Socket 匯報狀態，無延遲
- **螢幕特徵匹配（fallback）**：對無 Hook 的 agent（如 Claude Code），讀取 PTY 底部緩衝區快照，用 TOML 正則規則匹配

### 2. Socket API

通訊：NDJSON over Unix Socket 或 Windows Named Pipe

**三大編排原語**：
- **Spawn**：`pane.split` 開新 pane → `agent.start` 啟動 agent
- **Inject**：`agent.prompt`（原子化：送出 + 監聽，避免競態）
- **Wait**：`agent.wait --until blocked/done`（同步阻塞）或 `events.subscribe`（非同步監聽）

**關鍵設計**：原子化 prompt 防競態、5 秒超時防卡死、blocked 時拒絕寫入防衝撞

### 3. Plugin 系統

Out-of-process，任何語言都能寫。TOML manifest 定義 build/startup/actions/events/panes/link_handlers。插件啟動時注入 `HERDR_PLUGIN_CONTEXT_JSON`。

### 4. Herdr vs Pi/Tau

| 面向 | Herdr（下層） | Pi/Tau（上層） |
|---|---|---|
| 職責 | 終端多路複用、進程持久化、狀態偵測 | LLM 推理、工具鏈執行 |
| 互補 | Pi 是 Herdr 官方認證的 Lifecycle Authority | Pi 可透過 Socket API 調度多子 agent |

### 5. 對 Cheerio 的啟發

1. 背景常駐服務（Scraping Daemon）
2. 語義狀態感知（CAPTCHA/登入牆 → 🔴 Blocked → 通知人工）
3. 多 agent 協作（Spawn 子 agent 並行）
4. Session 持久化（長時間爬取不怕中斷）

## 關鍵引用

- Herdr 官方文件：[herdr.dev/docs](https://herdr.dev/docs/)
- Socket API：[herdr.dev/docs/socket-api](https://herdr.dev/docs/socket-api/)
- Plugin 系統：[herdr.dev/docs/plugins](https://herdr.dev/docs/plugins/)
- Agent Orchestration：[herdr.dev/docs/agent-automation](https://herdr.dev/docs/agent-automation/)
- Pi 整合：[herdr.dev/docs/integrations](https://herdr.dev/docs/integrations/)

## 相關頁面

- [[wiki/entities/herdr|herdr]] — entity 頁面
- [[wiki/entities/tau|tau]] — Pi 的 Python port
- [[wiki/entities/pi-mono|pi-mono]] — Pi 核心
- [[wiki/concepts/agent-durability-patterns|agent-durability-patterns]]
