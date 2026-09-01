---
title: herdr — Coding Agent 的 Terminal Runtime
type: entity
created: 2026-08-25
updated: 2026-08-25
sources: 2
tags: [herdr, terminal-multiplexer, agent-runtime, rust, orchestration]
canonical: entities/herdr
topics: [agent-runtime-implementations, coding-agent, agent-infrastructure]
---

> Herdr 是一個基於 Rust 寫成的**終端機代理人執行期（Agent Runtime）與多路複用器**，專為 AI coding agent 設計。它不是 agent 本身，而是 agent 運行的「基礎設施層」——確保 agent 進程不中斷、狀態可視、多 agent 可並行協作。

## 基本資訊

| 項目 | 內容 |
|---|---|
| 語言 | Rust |
| UI 框架 | Ratatui（TUI） |
| 授權 | Apache License 2.0 |
| GitHub | [herdrdev/herdr](https://github.com/herdrdev/herdr)（33k+ stars） |
| 官網 | [herdr.dev](https://herdr.dev) |
| 安裝 | `curl -fsSL https://herdr.dev/install.sh \| sh` / `brew install herdr` |

## 核心架構

### Client-Server 模型

```
┌─────────────────────────────────────┐
│         Herdr Server (Background)   │
│  - PTY 控制、進程管理、狀態偵測     │
│  - Session 持久化與恢復             │
│  - Socket API 暴露                 │
├─────────────────────────────────────┤
│         Herdr Client (Viewport)     │
│  - 薄客戶端，隨時 detach/reattach   │
│  - 關閉終端 agent 不中斷            │
└─────────────────────────────────────┘
```

### 資源管理層級

```
Workspace（專案）
  └── Tab（版面配置）
        └── Pane（虛擬終端 PTY）
              └── Agent（AI 進程）
```

### 四大語義狀態

| 狀態 | 意義 | 偵測方式 |
|---|---|---|
| 🔴 Blocked | 等待人工輸入/確認/授權 | Hook 主動報告 or 螢幕正則匹配 |
| 🟡 Working | 正在推理/生成/執行 | Hook or 螢幕偵測 |
| 🔵 Done | 任務完成，尚未檢視 | Hook or 螢幕偵測 |
| 🟢 Idle | 已檢視或等待下一個任務 | Hook or 螢幕偵測 |

## Socket API

通訊方式：NDJSON over Unix Socket（`~/.config/herdr/herdr.sock`）或 Windows Named Pipe

### 三大編排原語

| 原語 | 方法 | 用途 |
|---|---|---|
| **Spawn** | `pane.split` → `agent.start` | 開新 pane 啟動子 agent |
| **Inject** | `agent.prompt`（原子化） | 送出 prompt 同時開啟狀態監聽 |
| **Wait** | `agent.wait --until blocked/done` | 阻塞等到目標狀態 |

### 關鍵設計

- **原子化 Prompt**：`agent.prompt` 在單次 Socket 請求中完成「送出文字」+「開啟狀態監聽」，避免競態條件
- **防衝撞**：目標 agent 已 blocked 時，直接拒絕寫入
- **超時保護**：5 秒內目標沒反應就拋 `agent_prompt_stalled`
- **Session 恢復**：重啟後自動用 Session ID 呼叫 agent 的恢復指令（如 `claude --resume <id>`）

## Plugin 系統

Out-of-process 設計，任何語言都能寫：

```toml
# herdr-plugin.toml
[[build]]
command = "npm ci"

[[startup]]
command = "node init.js"

[[actions]]
name = "my-action"
command = "node action.js"

[[events]]
subscribe = "worktree.created"
command = "node on-worktree.js"
```

插件啟動時注入 `HERDR_PLUGIN_CONTEXT_JSON`，包含當前 UI 焦點狀態。

## 與 Pi/Tau 的關係

| 面向 | Herdr（下層 Substrate） | Pi/Tau（上層 Agent） |
|---|---|---|
| 職責 | 終端多路複用、進程持久化、狀態偵測 | LLM 推理、工具鏈執行、對話 |
| 本質 | 作業系統級容器 | 工程師級大腦 |
| 互補 | Pi 是 Herdr 官方認證的「Lifecycle Authority」 | Pi 可透過 Socket API 調度多子 agent |

**互補要點**：
- Pi 透過 Hook 主動向 Herdr 匯報狀態，消除畫面偵測的延遲
- Pi 可透過 Socket API 自行 spawn 子 pane，實現跨進程多 agent 調度
- Herdr 確保 Pi 進程在 SSH 斷線/關蓋後繼續運作，並支援自動恢復

## 對 Cheerio 助理的啟發

1. **背景常駐服務**：爬蟲從單次腳本升級為 daemon，支援 detach/reattach
2. **語義狀態感知**：遭遇 CAPTCHA/登入牆時轉 🔴 Blocked，通知人工介入
3. **多 agent 協作**：Spawn 多個子 agent 並行處理，避免 context 爆炸
4. **Session 持久化**：長時間爬取任務不怕中斷，自動恢復

## 來源

- [[wiki/sources/2026-08-25-herdr-deep-research|2026-08-25 Herdr 深度研究（Gemini Research）]]
- [[raw/youtube/herdr-runtime-for-coding-agents|YouTube: Herdr 影片逐字稿]]
- [[wiki/sources/2026-09-02-dhh-ai-workflow-practices|DHH：AI 工作流的實踐經驗]] — Herdr 作為 DHH 16 條並行工作流的可觀測性工具被提及

## 相關頁面

- [[wiki/entities/tau|tau]] — Pi 的 Python port，Herdr 的 Lifecycle Authority 之一
- [[wiki/entities/pi-mono|pi-mono]] — Pi 核心，可與 Herdr 整合
- [[wiki/concepts/agent-durability-patterns|agent-durability-patterns]] — 狀態持久化模式
- [[wiki/concepts/sub-agents-pattern|sub-agents-pattern]] — 子 agent 委派模式
- [[wiki/entities/dhh|DHH]] — 使用 Herdr 作為可觀測性工具的實踐者
