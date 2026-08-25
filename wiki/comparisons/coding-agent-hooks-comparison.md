---
title: "五大 Coding Agent Harness Hook 機制比較"
type: comparison
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [hooks, ai-development-tools, coding-agent, lifecycle-hooks, event-driven, comparison]
topics: [hooks, ai-development-tools, coding-agent]
canonical: comparisons/coding-agent-hooks-comparison
provenance:
  - kind: raw
    path: "raw/deep-research/rc-20260825-001/research-report.md"
---

# 五大 Coding Agent Harness Hook 機制比較

> 2026 年 AI 編碼 Agent／IDE 開發工具 Harness 的生命週期擴充機制（Hook）深度比較，涵蓋五大工具的優劣勢、生命週期事件、配置方式與企業跨工具通用標準分析。

## 一、各工具的 Hook 機制概覽

| 工具 | 正式名稱 | 事件數量 | Handler 類型 | 配置方式 |
|------|---------|---------|-------------|---------|
| **GitHub Copilot** | Agent Hooks / Lifecycle Hooks | 7+（VS Code / CLI） | TypeScript callback | JSON 設定檔 + SDK 程式化 |
| **Claude Code** | Claude Code Hooks | 31 種 | command / http / mcp_tool / prompt / agent | JSON 設定檔 + Frontmatter 動態注入 |
| **OpenAI Codex CLI** | Codex Hook System | 10 個（實務僅支援 2 個） | 外部二進位攔截器 | JSON/TOML 設定檔（純宣告式） |
| **Pi Coding Agent** | Event hooks / Hostcall hooks | Extension 事件 | pi.on API / YAML hooks | JS/TS 程式碼 + YAML 設定 |
| **DeepSeek Harness** | Cordis events / Session events | Cordis 微核心事件 | ctx.on API | TypeScript 插件 + YAML 設定 |

## 二、優劣勢比較總表

| 面向 | GitHub Copilot | Claude Code | OpenAI Codex CLI | Pi Coding Agent | DeepSeek Harness |
|------|---------------|-------------|------------------|-----------------|------------------|
| **核心優勢** | 靈活配置、即時格式化、防死鎖安全鎖 | 極細緻觸發點、非同步背景處理、環境變數持久化 | 專為企業安全設計、Fail-closed 策略 | 零編譯動態載入、深層 LLM 上下文干預 | 時空可組合性、不可變事件流、跨生態通用適配 |
| **核心限制** | IDE 支援不均、同步阻塞、宣告式能力閹割 | 容易耗盡 Token、非同步 context 延遲、Session 恢復過期 | 生命週期極窄、流失 Ask 能力、高度實驗性 | 安全外洩邊緣、功能適配受限、Fail-open | 高度不穩定、學習曲線陡峭、第三方生態不成熟 |

## 三、生命週期事件對照表

| 生命週期階段 | GitHub Copilot | Claude Code | Codex CLI | Pi Coding Agent | DeepSeek Harness |
|-------------|---------------|-------------|-----------|-----------------|------------------|
| **工作階段啟動** | `sessionStart` | `SessionStart`, `Setup` | `SessionStart` | `session_start` | `session/turn/start` |
| **使用者提交提示詞** | `userPromptSubmitted` | `UserPromptSubmit`, `UserPromptExpansion` | `UserPromptSubmit` | `user.prompt.submit` | 事件流自動包含 |
| **工具執行前** | `preToolUse` | `PreToolUse`, `PermissionRequest` | `PreToolUse`, `PermissionRequest` | `tool_call` | `session/tool/before` |
| **工具執行後** | `postToolUse` | `PostToolUse`, `PostToolBatch` | `PostToolUse` | `tool_result` | `session/tool/result` |
| **工具執行失敗** | `postToolUseFailure` | `PostToolUseFailure` | 未實務處理 | `tool_result(isError)` | `session/tool/result(isError)` |
| **工作階段結束** | `agentStop` | `Stop` | `Stop` | `session_shutdown` | `session/turn/end` |
| **錯誤發生** | `errorOccurred` | `StopFailure`, `FileChanged`, `CwdChanged`, `ConfigChange` | — | `file.changed` | `session/permission/change` |

## 四、設定檔 vs 程式碼注入分類

### 類別一：純設定檔宣告式配置

| 工具 | 說明 |
|------|------|
| **OpenAI Codex CLI** | 完全依賴 `~/.codex/hooks.json` 或 `~/.codex/config.toml`，無程式化 API。外部二進位攔截器如何與 Codex 連接，仍需在 JSON/TOML 設定檔中宣告 |

### 類別二：設定檔 + 程式化雙軌掛接

| 工具 | 設定檔配置 | 程式化掛接 |
|------|-----------|-----------|
| **GitHub Copilot** | `.github/hooks/*.json`（專案級）、`~/.copilot/hooks/*.json`（個人全域） | SDK 程式化 Hook，可動態篡改 Prompt（`modifiedPrompt`） |
| **Claude Code** | `.claude/settings.json`（專案/全域） | Skill/Subagent Frontmatter 動態注入，原生 `type: "prompt"` / `type: "agent"` |
| **Pi Coding Agent** | `pi-yaml-hooks`（`.pi/hook/hooks.yaml`） | JavaScript/TypeScript 擴充，直接在 QuickJS 虛擬機中執行 |
| **DeepSeek Harness** | `cordis.patch.yml`（YAML 設定） | TypeScript Cordis 插件，直接呼叫 `ctx.on()` 掛接事件 |

## 五、企業跨工具通用標準分析

### 核心結論

在 **Agent Plugins 1.0（Open Plugin Spec）** 開放標準中：

| 組件類型 | 可移植性 | 說明 |
|---------|---------|------|
| **Agent Skills** | ✅ 可移植 | 任何相容該規格的 AI 用戶端都能直接讀取並加載 |
| **MCP 伺服器配置** | ✅ 可移植 | 定義於根目錄的 `mcp.json` 中，跨平台通用 |
| **Hooks** | ❌ 不可移植 | 被明確歸類為「非通用（Client-specific）」的專屬組件 |
| **斜線指令** | ❌ 不可移植 | 特定用戶端專屬的能力 |

### 事實上相容（De Facto Compatibility）

儘管官方標準中 Hook 被歸類為專屬能力，但實務上正朝著以 **Claude Code 格式為基礎** 的跨平台適配發展：

1. **VS Code / GitHub Copilot**：已原生支援讀取並解析 Claude Code 格式的 Hook 宣告
2. **Pi Coding Agent**：透過 `pi-hooks` 擴充適配 Claude Code 格式
3. **DeepSeek Harness**：透過 `oh-my-dsh` 散佈版自動發現並複用其他工具的 Hooks
4. **OpenAI Codex CLI**：採用獨立的安全孤島設計，無法直接相容

### 企業治理建議

1. **統一配置層**：以 Claude Code / VS Code 雙相容 JSON Schema 作為 Hook 配置的主要宣告標準
2. **腳本與行為抽離**：不要將核心安全檢查邏輯綁定在 Harness 特有功能中
3. **單一外部腳本調用**：將業務檢驗實作在獨立的外部 CLI 腳本中，各平台的 Hook 設定檔僅作為觸發入口

## 來源

- [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]]

## 相關頁面

- [[wiki/topics/hooks|Hooks]] — AI agent 事件驅動自動化機制
- [[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]]
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統]]
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]
- [[wiki/concepts/harness|Harness — LLM 的驅動層]]
- [[wiki/entities/deepseek-harness|DeepSeek Harness]]
- [[wiki/entities/claude-code|Claude Code]]
- [[wiki/entities/github-copilot|GitHub Copilot]]
- [[wiki/entities/openai-codex|OpenAI Codex]]
- [[wiki/entities/pi-mono|Pi Coding Agent]]