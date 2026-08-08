---
title: pi-agent-dashboard — Real-time Web Dashboard for Pi Sessions
type: entity
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [pi, dashboard, web-ui, coding-agent]
collection: entities
topics: [pi-agent, extension-dev]
canonical: entities/pi-agent-dashboard
provenance_raw: "raw/web/2026-08-02-pi-agent-dashboard.md"
---

> BlackBeltTechnology 開發的 Pi coding-agent 即時 Web Dashboard。一個瀏覽器 tab 管理多個 pi session——**與 TUI 並存，不取代它**。

## 核心定位

- 多 session 同時監控 + 即時 chat mirroring
- 從手機或 laptop 遠端控制 pi agent
- 雙向互動：從瀏覽器發送 prompt、指令
- 支援中文 UI（簡體）

## 架構

```
Bridge Extension（每個 pi session）
    ↕ WebSocket :9999
Dashboard Server（Node.js）
    ↕ WebSocket :8000
Web Client（React + Tailwind）
```

| 組件 | 位置 | 角色 |
|---|---|---|
| Bridge Extension | `packages/extension/` | 在 pi session 內運行，轉發 events、relay commands、自動啟動 server |
| Dashboard Server | `packages/server/` | 聚合 events、管理 terminals、HTTP + 雙 WebSocket gateway |
| Web Client | `packages/client/` | React UI，80+ 組件 |
| Shared | `packages/shared/` | 跨 package 的 TypeScript types、protocols |
| Plugin Runtime | `packages/dashboard-plugin-runtime/` | Plugin loader、slot registry |

## 安裝方式

### A. Electron 桌面 App（零前提條件）
- 從 GitHub Releases 下載對應平台安裝檔
- Standalone mode 內建 Node.js，自動安裝 pi + dashboard + openspec 到 `~/.pi-dashboard/`
- 首次啟動有 setup wizard

### B. npm install
```bash
npm install -g @blackbelt-technology/pi-agent-dashboard
pi-dashboard
# 開啟 http://localhost:8000
```

### C. 從原始碼
```bash
git clone → corepack enable → pnpm install → pnpm run build
```

### D. Docker
```bash
cd docker → cp .env.example .env → docker compose up -d --build
```
Server、pi agent、code-server、zrok、tmux、terminals 全部在一個 container。

## 主要功能

### Sessions & Chat
- 即時 session mirroring（所有活躍 pi session + live streaming messages）
- 雙向互動（從瀏覽器發 prompt）
- Session 統計（token counts、costs、model info、context usage bar）
- Session spawning（headless 或 tmux）
- 歷史 session 瀏覽
- Force kill escalation（soft abort → SIGKILL）

### Workspace & UI
- Workspace 管理（按 project folder 分組、pinned directories、drag-to-reorder）
- Command autocomplete（`/` prefix）
- Mobile-friendly（responsive layout + swipe drawer）
- Markdown preview（Mermaid、syntax highlighting、KaTeX math）
- 本地圖片內聯（`pi-asset:<hash>` streaming channel）

### 整合
- **PromptBus**：統一路由 TUI / dashboard / custom adapters
- **Extension UI System**：extension 可宣告 slash-command modal UIs（不需 React）
- **pi-flows**：即時 flow execution dashboard、Flow Architect
- **OpenSpec**：browse specs、manage changes、create new changes
- **Browser-based provider auth**：Anthropic、OpenAI Codex、GitHub Copilot、Gemini
- **Custom LLM providers**：OpenAI-compatible / Anthropic-compatible / Google Generative AI
- **Package management**：browse、install、update、remove pi packages

### Dev Tools
- 整合 terminal（xterm.js + node-pty）
- Diff viewer（side-by-side + unified）
- Editor integration（VS Code、Cursor）

### Networking
- mDNS 自動發現
- zrok tunnel（持久 public URL，手機遠端控制）

## Model Proxy

Dashboard 暴露 OpenAI-compatible HTTP proxy（`/v1/...`）：
- `GET /v1/models`、`POST /v1/chat/completions`、`POST /v1/messages`
- 認證：proxy API keys only（`pi-proxy-*` prefix）

## 配置

- Config：`~/.pi/dashboard/config.json`（自動建立）
- Tool overrides：`~/.pi/dashboard/tool-overrides.json`
- Settings UI：⚙ gear icon

| 預設 | 說明 |
|---|---|
| port 8000 | HTTP + WebSocket |
| piPort 9999 | Pi extension WebSocket |
| autoStart true | Bridge 自動啟動 server |
| spawnStrategy "headless" | 或 "tmux" |
| tunnel.enabled true | zrok tunnel |

## 認證

OAuth2 guards external access，localhost 無需認證。支援 GitHub、Google、Keycloak、OIDC。

## 推薦 Extensions

| Extension | 必要性 | 功能 |
|---|---|---|
| pi-anthropic-messages | **required** | Claude model tool calls |
| pi-flows | strongly suggested | Flow dashboard + role aliases |
| pi-web-access | strongly suggested | web_search、code_search、fetch_content |
| pi-dashboard-subagents | optional | Agent tool card UI |
| pi-agent-browser | optional | browser tool |

## Plugin 系統

Dashboard UI 由 named slots 組成，plugins 用 React components claim slots。10 個可用 slot：`session-card-badge`、`content-view`、`settings-section`、`tool-renderer` 等。

## 與 Pi 的關係

- **不是** Oh My Pi
- 與 pi TUI 並存，不取代
- 透過 Bridge Extension 連接 pi session
- 自動啟動：pi 啟動時若 server 未運行，bridge 自動 spawn

## 來源

- [[raw/web/2026-08-02-pi-agent-dashboard|原始剪藏]]
- GitHub: [BlackBeltTechnology/pi-agent-dashboard](https://github.com/BlackBeltTechnology/pi-agent-dashboard)

## 相關頁面

- [[wiki/entities/pi-mono|pi-mono]] — Pi Agent Toolkit monorepo（dashboard 的上游）
- [[wiki/entities/pi-agent-core|pi-agent-core]] — agent runtime
- [[wiki/entities/plannotator|plannotator]] — 另一個 Pi 視覺化工具（定位不同：code review vs. session management）
