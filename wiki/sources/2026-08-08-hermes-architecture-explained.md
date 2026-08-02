---
title: "Hermes Architecture EXPLAINED: Memory, Context & Gateways"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [hermes, ai-agent, architecture, memory, context, gateway]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-08-hermes-architecture-explained
---

> 來源：[YouTube — Hermes Architecture EXPLAINED: Memory, Context & Gateways](https://youtu.be/n32qq7Kwzh0)
> 原始字幕：[[raw/youtube/hermes-architecture-explained|raw transcript]]

## 一句話

Hermes agent 的完整架構深度解析：agent loop、context 構建、context compression、gateway 多平台網關、三層記憶系統、cron 排程。

## 重點摘要

### Agent Loop
- **流程**：User message → Build context → Send to LLM → Tool calls（可重複）→ Response → Memory update
- 每次使用者發訊息都跑一次完整 loop
- Memory update：分析對話中是否有值得記憶的資訊，寫入記憶讓 agent 持續學習

### Context 構建
Context 由以下 markdown 檔案構成：
- **soul.md**：agent 個性（語調、靈感、目標、approach）；預設為空，可自訂或讓 agent 自己寫
- **user.md**（`memory/` 目錄下）：使用者資訊，agent 自動從對話中學習並更新
- **memory.md**（`memory/` 目錄下）：任意記憶（工具用法、workflow、有趣發現），agent 自動更新

額外 context 來源：
- 外部記憶的過去對話摘要（需設定 external memory）
- Skill 與 tool 描述
- 最近訊息歷史（超過閾值時自動 summarise）

### Context Compression
- 預設在 context window 50% 時觸發（可設定 70% 或 80%）
- **兩個觸發時機**：(1) 每次 turn 前 (2) LLM 回傳 context window error 時
- Token 估算：第一次用 `字元數 / 4`，之後用 LLM response 中的 `usage` 參數
- Compression prompt 位於 `context_compressor.py`，輸出包含：目標、約束、已完成動作、活躍狀態、歷史進度、阻塞、決策、已解決問題、相關檔案、關鍵 context、先前摘要等

### Gateway（多平台網關）
- **用途**：透過 Telegram、Discord、Slack、WhatsApp、Email、SMS 等平台連接 agent
- **架構**：Async.io loop 持續 poll 各 gateway
- **每個平台需獨立設定**（bot ID、user ID 等），非統一 gateway
- **Context 構建**：每次收到訊息都從零構建 context（不像 CLI 有完整對話歷史）
- **Session ID**：`<platform>_<session_id>` 格式，存於 SQLite
- **Session Manager**：處理 `/interrupt`（中斷）、`/steer`（引導）、併列（排隊）等指令

### Memory（三層記憶系統）

| 層級 | 儲存方式 | 內容 |
|---|---|---|
| **Markdown 檔案** | soul.md / user.md / memory.md | 個性、使用者資訊、任意事實 |
| **SQLite** | Session transcripts + bare text table | 完整對話紀錄 + 純文字供 similarity search |
| **External Memory** | Mem0 / SuperMemory / Honcho 等 | 跨 session 智慧記憶，需額外設定 |

- **External memory 查詢時機**：第一則訊息之後（而非之前），類似人類「先聽問題再回想」
- 各 provider 差異：Mem0 用 similarity search、SuperMemory 傳整段歷史由 LLM 擷取、Honcho 用 dialectic user modeling
- 預設未啟用 external memory，但建議開啟以改善 agent 學習

### Cron Jobs
- **非**綁定系統 cron，而是 Hermes 自己的 async loop
- **Tick function** 每分鐘執行，檢查 `~/.hermes/cron/jobs.json`
- **任務範例**：每天早上寄 AI 新聞 email、每週五寄工作報告
- **儲存**：`jobs.json`（plain JSON，非 SQLite）
- **輸出**：`cron/output/<job-id>/run.md`
- **通知**：自動發送到 home messaging platform（設定 gateway 時指定的 home user），非透過 send_message tool

## 來源

- [[raw/youtube/hermes-architecture-explained|Raw transcript — YouTube n32qq7Kwzh0]]

## 相關頁面

- [[wiki/entities/hermes-agent|hermes-agent]]
- [[wiki/sources/2026-08-05-hermes-agent-github-readme|2026-08-05 Hermes Agent: GitHub README]]
- [[wiki/entities/pi-mono|pi-mono]] — Pi 架構比較
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 設計哲學比較
