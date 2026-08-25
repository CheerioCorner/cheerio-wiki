---
title: "五大 Coding Agent Harness Hook 機制比較研究"
type: source
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [hooks, ai-development-tools, coding-agent, lifecycle-hooks, event-driven]
topics: [hooks, ai-development-tools, coding-agent]
canonical: sources/2026-08-25-coding-agent-hooks-comparison
provenance:
  - kind: raw
    path: "raw/deep-research/rc-20260825-001/research-report.md"
---

# 五大 Coding Agent Harness Hook 機制比較研究

> 2026 年 AI 編碼 Agent／IDE 開發工具 Harness 的生命週期擴充機制（Hook）盤點與比較，涵蓋 GitHub Copilot、Claude Code、OpenAI Codex CLI、Pi Coding Agent、DeepSeek Harness 五大工具。

## 核心發現

### 1. 各工具的 Hook 機制與正式名稱

| 工具 | 正式名稱 | 事件數量 | Handler 類型 |
|------|---------|---------|-------------|
| **GitHub Copilot** | Agent Hooks / Lifecycle Hooks | 7+（VS Code / CLI） | TypeScript callback |
| **Claude Code** | Claude Code Hooks | 31 種 | command / http / mcp_tool / prompt / agent |
| **OpenAI Codex CLI** | Codex Hook System | 10 個（實務僅支援 2 個） | 外部二進位攔截器 |
| **Pi Coding Agent** | Event hooks / Hostcall hooks | Extension 事件 | pi.on API / YAML hooks |
| **DeepSeek Harness** | Cordis events / Session events | Cordis 微核心事件 | ctx.on API |

### 2. 優劣勢比較總表

| 面向 | GitHub Copilot | Claude Code | OpenAI Codex CLI | Pi Coding Agent | DeepSeek Harness |
|------|---------------|-------------|------------------|-----------------|------------------|
| **優勢** | 靈活配置、即時格式化、防死鎖安全鎖 | 極細緻觸發點、非同步背景處理、環境變數持久化 | 專為企業安全設計、Fail-closed 策略 | 零編譯動態載入、深層 LLM 上下文干預 | 時空可組合性、不可變事件流、跨生態通用適配 |
| **限制** | IDE 支援不均、同步阻塞、宣告式能力閹割 | 容易耗盡 Token、非同步 context 延遲、Session 恢復過期 | 生命週期極窄、流失 Ask 能力、高度實驗性 | 安全外洩邊緣、功能適配受限、Fail-open | 高度不穩定、學習曲線陡峭、第三方生態不成熟 |

### 3. 生命週期事件對照表

| 生命週期階段 | GitHub Copilot | Claude Code | Codex CLI | Pi Coding Agent | DeepSeek Harness |
|-------------|---------------|-------------|-----------|-----------------|------------------|
| 工作階段啟動 | `sessionStart` | `SessionStart`, `Setup` | `SessionStart` | `session_start` | `session/turn/start` |
| 使用者提交提示詞 | `userPromptSubmitted` | `UserPromptSubmit`, `UserPromptExpansion` | `UserPromptSubmit` | `user.prompt.submit` | 事件流自動包含 |
| 工具執行前 | `preToolUse` | `PreToolUse`, `PermissionRequest` | `PreToolUse`, `PermissionRequest` | `tool_call` | `session/tool/before` |
| 工具執行後 | `postToolUse` | `PostToolUse`, `PostToolBatch` | `PostToolUse` | `tool_result` | `session/tool/result` |
| 工具執行失敗 | `postToolUseFailure` | `PostToolUseFailure` | 未實務處理 | `tool_result(isError)` | `session/tool/result(isError)` |
| 工作階段結束 | `agentStop` | `Stop` | `Stop` | `session_shutdown` | `session/turn/end` |
| 錯誤發生 | `errorOccurred` | `StopFailure`, `FileChanged`, `CwdChanged`, `ConfigChange` | — | `file.changed` | `session/permission/change` |

### 4. 設定檔 vs 程式碼注入分類

| 類型 | 工具 | 說明 |
|------|------|------|
| **純設定檔宣告式** | OpenAI Codex CLI | 完全依賴 `~/.codex/hooks.json`，無程式化 API |
| **設定檔 + 程式化雙軌** | GitHub Copilot | JSON 設定檔 + SDK 程式化 Hook |
| | Claude Code | settings.json + Skill/Subagent Frontmatter 動態注入 |
| | Pi Coding Agent | JavaScript/TypeScript 擴充 + YAML hooks 適配 |
| | DeepSeek Harness | TypeScript Cordis 插件 + YAML cordis.patch.yml 控制 |

### 5. 企業跨工具通用標準分析

**核心結論**：Hook 在 Agent Plugins 1.0 標準中被歸類為「非通用（Client-specific）」的專屬組件，與可移植的 Agent Skills / MCP 不同。

**事實上相容（De Facto Compatibility）**：
- VS Code 已原生支援讀取 Claude Code 格式的 Hook 宣告
- Pi 透過 `pi-hooks` 擴充適配 Claude Code 格式
- DeepSeek Harness 透過 `oh-my-dsh` 散佈版自動發現並複用其他工具的 Hooks
- OpenAI Codex CLI 採用獨立的安全孤島設計，無法直接相容

**企業治理建議**：
1. 統一配置層：以 Claude Code / VS Code 雙相容 JSON Schema 作為 Hook 配置標準
2. 腳本與行為抽離：不要將安全檢查邏輯綁定在 Harness 特有功能中
3. 單一外部腳本調用：將業務檢驗實作在獨立的外部 CLI 腳本中

## 引用來源

本報告引用 60 個來源，完整清單見原始研究報告：

- [[wiki/raw/deep-research/rc-20260825-001/research-report.md|完整研究報告]]

## 相關頁面

- [[wiki/topics/hooks|Hooks]] — AI agent 事件驅動自動化機制
- [[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]] — Event / Matcher / Handler 三層架構
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統]] — Lifecycle Callback
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]] — Skill / Hook / Extension 能力邊界
- [[wiki/concepts/harness|Harness — LLM 的驅動層]] — Harness 概念定義
- [[wiki/entities/deepseek-harness|DeepSeek Harness]] — Cordis 微核心架構