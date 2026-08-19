---
title: "Pi Cron Job 實作原理 — @pi-agents/loop 架構解析"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
provenance:
  - kind: session
    path: raw/conversations/2026-08-10-pi-cron-job-explained.md
tags: [pi, cron, scheduler, extension, loop]
topics: [pi-agent-ecosystem, extension-dev]
collection: sources
---

> 從 `@pi-agents/loop` extension 的原始碼與文件，解析 Pi 的 cron job 系統如何實作。

## 關鍵發現

### 1. 核心架構

- **Extension-based**：非 core runtime 內建，而是獨立 extension
- **1-second tick loop**：`LoopScheduler` 每秒檢查所有 tasks
- **Idle gating**：只在 agent 閒置時觸發，不中斷工作

### 2. 兩種排程模式

| 模式 | 工具 | 控制者 | 適用場景 |
|---|---|---|---|
| Fixed cron | `cron_create` | 使用者 | 每週巡檢、定時提醒 |
| Dynamic self-pacing | `schedule_wakeup` | LLM 自己 | 持續監控、迭代任務 |

### 3. 防雷擊效應（Anti-Thundering-Herd）

- Jitter = 50% of interval，上限 30 分鐘
- 避免多個 durable tasks 同時觸發造成 API 負載

### 4. Durable vs Session

- **Session tasks**：記憶體中，surviving compaction，session 結束消失
- **Durable tasks**：持久化到 `.pi-loop.json`，跨 session 持久

### 5. Multi-Instance Safety

- `.pi-loop.json.lock` 檔案鎖
- PID-based liveness checking
- 防止多個 Pi session 衝突

## 實際案例

以「每週一 09:00 wiki lint」為例：

1. `cron_create { cron: "0 9 * * 1", prompt: "每週 wiki lint...", recurring: true, durable: true }`
2. Task 儲存到 `.pi-loop.json`
3. Scheduler 每秒檢查，計算 next fire time
4. 等待 agent 閒置
5. 觸發時 `pi.sendUserMessage(prompt)`
6. LLM 收到 prompt，開始執行 wiki lint 流程

## 與 Core Runtime 的關係

Core runtime（`packages/agent/`）提供：
- `pi.sendUserMessage()`：注入使用者訊息
- `setBusy()` / `setIdle()`：通知 scheduler agent 狀態
- Extension 載入機制

`@pi-agents/loop` 則實作：
- Cron expression 解析
- Task store（session + durable）
- Scheduler engine
- Slash commands + LLM tools

## 來源

- `@pi-agents/loop` README.md
- `dist/index.js` — 主要邏輯
- `dist/scheduler.js` — Scheduler engine
- npm: `@pi-agents/loop`
