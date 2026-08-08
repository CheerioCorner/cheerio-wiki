---
title: pi-loop-scheduler — Pi 的 Cron Job 與排程系統
type: entity
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [pi, cron, scheduler, extension, loop]
topics: [pi-agent, extension-dev]
canonical: entities/pi-loop-scheduler
provenance:
  - kind: session
    path: raw/conversations/2026-08-10-pi-cron-job-explained.md
---

> `@pi-agents/loop` — Pi 的排程 extension，提供 cron job、動態自節奏、idle gating 等功能。非 core runtime 內建，而是透過 extension 機制載入。

## 架構概覽

```
┌─────────────────────────────────────────────────────────┐
│  User / LLM                                             │
│  ├─ /loop [interval] <prompt>  (slash command)          │
│  └─ cron_create / cron_delete / cron_list  (LLM tools)  │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│  LoopScheduler (1-second tick loop)                     │
│  ├─ 檢查所有 tasks 的 next fire time                    │
│  ├─ Idle gating：agent busy 時排隊等待                   │
│  ├─ Jitter：避免 API load spikes                        │
│  └─ Fire → pi.sendUserMessage(prompt)                   │
└──────────────────────┬──────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────┐
│  Task Store                                             │
│  ├─ Session tasks：記憶體中，session 結束即消失          │
│  └─ Durable tasks：持久化到 .pi-loop.json               │
└─────────────────────────────────────────────────────────┘
```

## 核心機制

### 1. Idle Gating（閒置閘道）

Cron job **只在 agent 閒置時觸發**，不會中斷正在進行的對話或工作。

```typescript
check() {
  for (const task of getAllTasks()) {
    if (this.shouldFire(task, now)) {
      if (this.isAgentBusy) {
        this.pendingFires.push(task.id);  // 排隊等待
      } else {
        this.fire(task);  // 立即觸發
      }
    }
  }
}
```

### 2. Anti-Thundering-Herd（防雷擊效應）

避免多個任務同時觸發造成 API 負載：

- Jitter = 50% of interval，上限 30 分鐘
- 例如：每小時的任務會在 30-60 分鐘間隨機觸發

```typescript
const gap = cronGapMs(task.cron, anchor);
const jitter = gap ? recurringJitterMs(task, gap, this.config) : 0;
fireTime = baseNext + jitter;
```

### 3. Durable vs Session Tasks

| 類型 | 儲存位置 | 生命週期 |
|---|---|---|
| Session | 記憶體 | Session 結束即消失，但 surviving compaction |
| Durable | `.pi-loop.json` | 跨 session 持久，missed one-shots 會補觸發 |

### 4. Multi-Instance Safety

- 使用 `.pi-loop.json.lock` 檔案鎖
- PID-based liveness checking
- 防止多個 Pi session 同時處理 durable tasks

## 工具與指令

### Slash Commands

| 指令 | 用途 |
|---|---|
| `/loop [interval] <prompt>` | 啟動 recurring loop |
| `/loop-list` | 列出所有 active loops |
| `/loop-kill <id\|label\|all>` | 取消 loop(s) |

### LLM Tools

| Tool | 用途 |
|---|---|
| `cron_create` | 建立 cron 排程 |
| `cron_delete` | 取消排程 |
| `cron_list` | 列出所有排程 |
| `schedule_wakeup` | 動態自節奏（60-3600s） |

## Cron Format

標準 5-field cron（local time）：

| Pattern | Meaning |
|---|---|
| `*/5 * * * *` | Every 5 minutes |
| `0 */2 * * *` | Every 2 hours |
| `30 14 * * *` | Daily at 2:30 PM |
| `0 9 * * 1-5` | Weekdays at 9 AM |

## 配置選項

```json
// .pi-loop.config.json
{
  "maxJobs": 50,                    // 最大同時任務數
  "recurringMaxAgeMs": 604800000,   // 7 天自動過期
  "checkIntervalMs": 1000,          // Scheduler tick 間隔
  "recurringJitterFrac": 0.5,       // Jitter 係數
  "recurringJitterCapMs": 1800000   // Jitter 上限 30 分鐘
}
```

## 與 Core Runtime 的關係

- **非 core runtime 內建**：透過 Pi 的 extension 機制載入
- **使用 `pi.sendUserMessage()`**：將 prompt 注入為使用者訊息
- **依賴 idle 狀態**：core runtime 提供 `setBusy()` / `setIdle()` 通知

## 來源

- [[wiki/sources/2026-08-10-pi-cron-job-explained]] — 本文整理
- npm: `@pi-agents/loop`
- repo: `https://github.com/badlogic/pi-mono/tree/main/packages/loop`

## 相關頁面

- Entities: [[wiki/entities/pi-agent-core]]、[[wiki/entities/pi-mono]]
- Concepts: [[wiki/concepts/minimal-agent-philosophy]]
