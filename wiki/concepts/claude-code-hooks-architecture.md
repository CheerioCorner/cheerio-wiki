---
title: "Claude Code Hooks 架構 — Event / Matcher / Handler"
type: concept
created: 2026-08-18
updated: 2026-08-18
sources: 1
tags: [claude-code, hooks, deterministic-guardrails, event-driven-architecture, handler-types]
topics: [claude-code, hooks, ai-development-tools]
canonical: concepts/claude-code-hooks-architecture
provenance:
  - kind: raw
    path: "raw/youtube/rLNGSDYkK-w.md"
---

# Claude Code Hooks 架構 — Event / Matcher / Handler

> Claude Code Hooks 是一套 **deterministic 的事件驅動自動化機制**——在 AI 忘記或跳過規則時，由軟體層強制執行檢查、防呆與品質把關。三層架構：Event 決定時機、Matcher 篩選操作、Handler 決定動作。

## 核心主張

CLAUDE.md 是「提醒紙條」，Hook 是「自動門」。差別在於 **deterministic vs 依賴 AI 自律**。當某個動作在特定時機「絕對必須執行」且「一點都不想承擔被 AI 忘記的風險」時，就適合做成 Hook。

## 三層架構

```
Event（什麼時候啟動）
    ↓
Matcher（攔截哪個操作）
    ↓
Handler（做什麼動作）
```

### Event — 決定什麼時候發生

依工作階段分為四大階段、10 個核心 Event（總共 31 種）：

**啟動階段：**
- `SessionStart`：對話建立時（新開/恢復/clear）
- `UserPromptSubmit`：使用者按下 Enter 送出 prompt

**工具執行前（安全防呆）：**
- `PreToolUse`：Claude 決定好工具但還沒執行

**工具執行後（快速驗收）：**
- `PostToolUse`：工具成功執行後

**收尾階段：**
- `Stop`：這一輪工作結束時
- `Notification`：需要使用者確認時
- `SubagentStart`：主 Claude 叫 subagent 開始工作
- `SubagentStop`：subagent 做完交回結果
- `PreCompact`：對話太長要濃縮前

### Matcher — 決定具體要攔截哪個操作

從所有可能出現的動作中，挑出這個 Hook 真正需要處理的目標。

```json
{
  "matcher": {
    "tool": "Edit|Write",
    "if": { "file_path": "\\.ts$" }
  }
}
```

- 第一層篩選：鎖定工具類型（如只關心 Edit 和 Write）
- 第二層篩選：用 `if` 條件再縮小（如只檢查 `.ts` 副檔名）
- 其他操作不觸發 → 不浪費時間、不打斷工作

### Handler — 決定最後要做什麼動作

條件符合後，由 Handler 決定實際執行什麼。目前有 5 種類型：

| Handler | 本質 | 適合做什麼 | 限制 |
|---------|------|-----------|------|
| **command** | 執行電腦上的指令或腳本 | lint、格式化、擋危險操作、檢查腳本 | — |
| **http** | 傳資料到外部服務 | 失敗時送 Slack 通知、錯誤回報 | 需要網路 |
| **mcp_tool** | 使用已連線的 MCP 工具 | 從 Jira 抓任務、從 DB 查資料 | 需要 MCP server |
| **prompt** | 叫另一個 AI 判斷（只根據收到的資料） | 檢查 Commit 格式、審查內容 | 不能讀檔案/搜尋 |
| **agent** | 叫 subagent 先讀檔案/搜尋/測試，再回傳 | 完整驗收（讀需求→對照檔案→跑測試） | 成本較高 |

**prompt vs agent 的差異：**
- prompt：拿著現有資料直接回答，不自己打開檔案或搜尋
- agent：可以先讀檔案、搜尋程式碼、執行測試，查清楚之後再回答

**注意**：每個 Event 支援的 Handler 類型不同（如有些 Event 不支援 prompt/agent），設定前需查官方文件。

## 設計心法

### 1. 建立時：先講清楚兩件事

- **什麼時候啟動**（Event + Matcher）
- **啟動後做什麼**（Handler）

有這兩個資訊，AI 就能幫你建立第一版 Hook。

### 2. 穩定使用：確認兩件事

- **觸發範圍夠不夠精確**：Matcher 縮小範圍 → Handler 裡再檢查更細條件
- **Stop Hook 有沒有結束條件**：明確的通過條件 + 重試上限（避免無限迴圈）

### 3. Stop Hook 的無限迴圈防護

```
Hook 阻止結束 → Claude 修改 → 再次準備結束 → Hook 又啟動 → ...
```

解法：
- 記錄通過狀態（文章沒再修改就不重複檢查）
- 設重試上限（連續 3 輪沒通過就停止退回，交給人工）

## 實作案例

### Git Commit Secret Guard（PreToolUse + Command）

```
Event: PreToolUse
Matcher: Bash
Handler: command → git-commit-secret-guard 腳本
```

1. Claude 準備執行任何 Bash 指令 → Hook 叫起檢查腳本
2. 腳本先判斷是不是 `git commit` → 不是就安靜結束
3. 是 → 檢查要提交的內容有沒有 `.env`、私鑰、憑證、API 金鑰
4. 有 → 擋下並指出問題檔案；沒有 → 放行

### Humanizer Gate（Stop + Command 觸發 Agent）

```
Event: Stop
Matcher: 檢查有沒有修改過 Blog 文章
Handler: command → humanizer-gate 腳本 → 要求 Claude 開 Agent 呼叫 Humanizer Skill
```

1. Claude 準備結束工作 → Hook 攔截
2. 檢查過程中有没有修改 Blog 文章 → 沒有就放行
3. 有 → 開 Agent 呼叫 Humanizer Skill 審查 AI 味
4. 有問題 → 退回修改；通過 → 記錄通過狀態
5. 連續 3 輪沒通過 → 停止退回，交給人工

## 與其他 Hook 系統的關係

| 面向 | Claude Code Hooks | Copilot Hooks | Pi Agent Hooks |
|------|-------------------|---------------|----------------|
| Event 數量 | 31 種 | 7+ 種 | Extension 事件 |
| Handler 類型 | command/http/mcp_tool/prompt/agent | TypeScript callback | Extension script |
| 執行方式 | JSON 設定檔（`.claude/settings.json`） | TypeScript 註冊 | Extension 系統 |
| 核心理念 | Deterministic 強制執行 | Lifecycle callback | 事件驅動 |

## 來源

- [[wiki/sources/2026-08-18-claude-code-hooks-tutorial|Claude Code 必學設定 Hooks，完整教學一次搞懂]] — 主要來源

## 相關頁面

- [[wiki/entities/claude-code|Claude Code]] — Claude Code 完整介紹
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統]] — 同類比較（Copilot 的 lifecycle callback）
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]] — Skill/Hook/Extension 三層光譜
- [[wiki/entities/openai-codex|OpenAI Codex]] — 競品 Hooks 支援度比較
