---
title: "Claude Code 必學設定 Hooks，完整教學一次搞懂 (Gary Chen)"
type: source
created: 2026-08-18
updated: 2026-08-18
sources: 1
tags: [claude-code, hooks, event-driven-architecture, deterministic-guardrails, developer-tools]
topics: [claude-code, hooks, ai-development-tools]
canonical: sources/2026-08-18-claude-code-hooks-tutorial
provenance_raw: "raw/youtube/rLNGSDYkK-w.md"
provenance_url: "https://youtu.be/rLNGSDYkK-w"
---

# Claude Code 必學設定 Hooks，完整教學一次搞懂

> 影片講者：Gary Chen | 時長：19:56 | 語言：zh-TW | 來源：YouTube

## 一句話總結

Claude Code Hooks 是一套 **deterministic 的事件驅動自動化機制**，在 AI 忘記或跳過規則時，由軟體層強制執行檢查、防呆與品質把關。

## 核心概念

### Hook vs CLAUDE.md

| 面向 | CLAUDE.md | Hooks |
|------|-----------|-------|
| 本質 | 提醒紙條（建議） | 自動門（強制） `[00:01:08]` |
| 執行者 | AI 模型自己判斷 | Claude Code 軟體層 `[00:01:52]` |
| 可靠性 | 依賴 AI 自律，可能忘記 | Deterministic，絕對執行 `[00:01:52]` |
| 適用場景 | 專案通用規則、大方向 | 特定時機必須嚴格執行的動作 `[00:02:12]` |

### 三層架構：Event → Matcher → Handler

```
Event（什麼時候啟動）
    ↓
Matcher（攔截哪個操作）
    ↓
Handler（做什麼動作）
```

- **Event**：定義觸發時機（例如 Claude 準備執行工具前）`[00:02:28]`
- **Matcher**：從所有操作中篩選出真正要處理的目標（例如只鎖定 Bash 指令）`[00:03:24]`
- **Handler**：條件符合後實際執行的動作（例如跑檢查腳本）`[00:03:45]`

## 10 個核心 Event（從 31 種中精選）

### 第一階段：啟動

| Event | 觸發時機 | 典型用途 |
|-------|---------|---------|
| **SessionStart** | 對話建立時（新開/恢復/clear）`[00:04:20]` | 強制載入 Skill（如 Superpowers）`[00:04:39]` |
| **UserPromptSubmit** | 使用者按下 Enter 送出 prompt `[00:04:56]` | 攔截提問，從 DB 撈相關記憶（如 Claude-Mem）`[00:05:02]` |

### 第二階段：工具執行前（安全防呆）

| Event | 觸發時機 | 典型用途 |
|-------|---------|---------|
| **PreToolUse** | Claude 決定好工具但還沒執行 `[00:05:37]` | 擋掉危險 Git 指令（如 `git reset --hard`）`[00:05:48]` |

### 第三階段：工具執行後（快速驗收）

| Event | 觸發時機 | 典型用途 |
|-------|---------|---------|
| **PostToolUse** | 工具成功執行後 `[00:06:18]` | 檢查 UI 檔案（空圖片連結、字體對比度不足）`[00:06:27]` |

### 第四階段：收尾

| Event | 觸發時機 | 典型用途 |
|-------|---------|---------|
| **Stop** | 這一輪工作結束時 `[00:06:56]` | 深度美感檢查、cross-model review `[00:07:11]` |
| **Notification** | 需要使用者確認時 `[00:07:32]` | 桌面通知/提示音 `[00:07:36]` |
| **SubagentStart** | 主 Claude 叫 subagent 開始工作 `[00:07:44]` | 補上任務規則和品質要求 `[00:07:49]` |
| **SubagentStop** | subagent 做完交回結果 `[00:07:44]` | 檢查產出有沒有達標 `[00:07:53]` |
| **PreCompact** | 對話太長要濃縮前 `[00:08:00]` | 保存關鍵決策、進度、不能更動的規則 `[00:08:09]` |

## 5 種 Handler 類型

| Handler | 適合做什麼 | 範例 |
|---------|-----------|------|
| **command** | 執行電腦上的指令或腳本 `[00:09:25]` | lint 檢查、Prettier 格式化、擋危險操作 |
| **http** | 傳資料到外部服務 `[00:09:55]` | 工具失敗時送 Slack 通知 |
| **mcp_tool** | 使用已連線的 MCP 工具 `[00:10:18]` | 從 Jira 抓今日任務作為背景資訊 |
| **prompt** | 叫另一個 AI 模型判斷（只根據收到的資料）`[00:10:33]` | 檢查 Commit 訊息是否符合團隊格式 |
| **agent** | 叫一個 subagent 先讀檔案/搜尋/測試，再回傳結果 `[00:10:58]` | 檢查修改過的檔案對照需求、執行測試 |

**注意**：每個 Event 支援的 Handler 類型不同，設定前需查官方文件。

## 實際案例

### 案例 1：Git Commit Secret Guard `[00:12:27]`

- **Event**：PreToolUse `[00:12:54]`
- **Matcher**：Bash `[00:12:58]`
- **Handler**：command（執行 `git-commit-secret-guard` 腳本）`[00:13:05]`
- **功能**：Claude 準備 `git commit` 時，先檢查要提交的內容有沒有 `.env` 檔案、私鑰、憑證、疑似 API 金鑰。有就擋下並指出問題檔案；沒有就放行 `[00:13:25]`。
- **設定位置**：`~/.claude/settings.json`（全域，所有專案通用）`[00:13:01]`

### 案例 2：Humanizer Gate（去除 AI 味）`[00:14:31]`

- **Event**：Stop `[00:14:47]`
- **Matcher**：檢查有沒有修改過 Blog 文章 `[00:14:52]`
- **Handler**：command（執行 `humanizer-gate` 腳本 → 要求 Claude 開 Agent 呼叫 Humanizer Skill）`[00:14:56]`
- **功能**：Claude 寫完文章準備結束時，攔截並用 Agent 審查 AI 寫作痕跡。有問題就退回修改；通過才放行 `[00:15:10]`。
- **關鍵設計**：記錄通過狀態，文章沒再修改就不重複檢查；連續 3 輪沒通過就停止退回，交給人工確認（避免無限迴圈）`[00:16:34]`。

## 建立 Hook 的心法

1. **先講清楚兩件事**：什麼時候啟動 + 啟動後做什麼
2. **確認觸發範圍**：Matcher 縮小範圍 → Handler 裡再檢查更細條件（避免在無關操作中被叫起）
3. **Stop Hook 要設結束條件**：明確的通過條件 + 重試上限，避免工作卡在重複檢查

## Claude Code vs Codex 差異

| 面向 | Claude Code | Codex |
|------|-------------|-------|
| Event 數量 | 31 種 `[00:04:14]` | 11 種 `[00:17:57]` |
| Handler 類型 | command, http, mcp_tool, prompt, agent `[00:09:12]` | 僅 command `[00:18:12]` |
| 共通支援 | PreToolUse、Stop 兩邊都有 `[00:18:04]` | — |
| 建立方式 | 講清楚需求，AI 幫你建立 | 同，但不要直接複製 Claude Code 設定 |

## 第三方專案引用

影片中提到的第三方專案（僅作為 Event 用途的佐證，不另開 entity 頁）：

- **Superpowers**：利用 SessionStart Hook 強制載入 Skill，對付 AI 載入 Skill 的隨機性
- **Claude-Mem**：利用 UserPromptSubmit Hook 攔截提問，從 DB 撈相關記憶塞給 Claude
- **Impeccable**：PostToolUse 檢查 UI（空圖片連結、字體對比度）+ Stop 做深度美感檢查
- **Matt Pocock Skills**：PreToolUse 擋掉危險 Git 指令

## 來源

- [[raw/youtube/rLNGSDYkK-w|Claude Code 必學設定 Hooks，完整教學一次搞懂 — 原始逐字稿]]

## 相關頁面

- [[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]] — 三層架構詳細說明
- [[wiki/entities/claude-code|Claude Code]] — Claude Code 完整介紹
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統]] — 同類比較
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]] — Skill/Hook/Extension 三層
