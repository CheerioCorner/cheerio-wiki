---
title: "五大 Coding Agent Harness Hook 機制比較"
type: comparison
created: 2026-08-25
updated: 2026-08-31
sources: 5
tags: [hooks, ai-development-tools, coding-agent, lifecycle-hooks, event-driven, comparison]
topics: [hooks, ai-development-tools, coding-agent]
canonical: comparisons/coding-agent-hooks-comparison
provenance:
  - kind: raw
    path: "raw/deep-research/rc-20260825-001/research-report.md"
  - kind: local
    path: "docs/extensions.md"
---

# Coding Agent Hook 機制比較 — 子專題內容（Codex 審查後修正版）

> 研究問題：不同 Coding Agent Harness（Copilot、Claude Code、Codex CLI、Pi、DeepSeek）的 Hook 生命周期事件設計有什麼差異？哪些設計值得移植到我們自己的 agent？

## 一、横向對照表：五大工具 Hook 機制總覽

### 1.1 基本規格對照

| 面向 | GitHub Copilot | Claude Code | OpenAI Codex CLI | Pi Coding Agent | DeepSeek Harness |
|------|---------------|-------------|------------------|-----------------|------------------|
| **正式名稱** | Copilot Hooks | Claude Code Hooks | Codex Hooks | Extension Event Hooks | ⚠️ 未驗證 |
| **已知事件數** | 8 種 | 多版本，已知 30+ 種 | 10+ 種 | 30+ 種（5 大類別） | ⚠️ 未驗證 |
| **Handler 類型** | Command hook（Bash/PowerShell） | command / http / mcp_tool / prompt / agent（5 種） | Command hook | Extension script（TypeScript） | ⚠️ 未驗證 |
| **配置方式** | JSON 設定檔（`.github/copilot-hooks.json`） | JSON 設定檔 + Frontmatter 動態注入 | `.codex/hooks.json` / `config.toml` | JS/TS 程式碼 + YAML hooks | ⚠️ 未驗證 |
| **成熟度** | GA | GA | 活躍開發中 | 活躍開發中 | ⚠️ 未驗證 |

### 1.2 生命週期事件對照

| 生命週期階段 | GitHub Copilot | Claude Code | Codex CLI | Pi Coding Agent | DeepSeek Harness |
|-------------|---------------|-------------|-----------|-----------------|------------------|
| **Session 啟動** | `sessionStart` | `SessionStart`, `Setup` | `SessionStart` | `session_start`, `project_trust`, `resources_discover` | ⚠️ 未驗證 |
| **Session 結束** | `sessionEnd` | — | — | `session_shutdown`, `session_before_switch` | ⚠️ 未驗證 |
| **使用者輸入** | `userPromptSubmitted` | `UserPromptSubmit`, `UserPromptExpansion` | `UserPromptSubmit` | `input`, `before_agent_start` | ⚠️ 未驗證 |
| **LLM 請求前** | — | — | — | `context`, `before_provider_headers`, `before_provider_request` | ⚠️ 未驗證 |
| **LLM 回應後** | — | — | — | `after_provider_response`, `message_start/update/end` | ⚠️ 未驗證 |
| **工具執行前** | `preToolUse` | `PreToolUse`, `PermissionRequest` | `PreToolUse`, `PermissionRequest` | `tool_execution_start`, `tool_call`（可阻斷） | ⚠️ 未驗證 |
| **工具執行後** | `postToolUse` | `PostToolUse`, `PostToolBatch` | `PostToolUse` | `tool_execution_update`, `tool_result`（可修改）, `tool_execution_end` | ⚠️ 未驗證 |
| **Turn 結束** | — | — | — | `turn_end` | ⚠️ 未驗證 |
| **Agent 結束** | `agentStop` | `Stop` | `Stop` | `agent_end`, `agent_settled` | ⚠️ 未驗證 |
| **Subagent** | `subagentStop` | `SubagentStart`, `SubagentStop` | `SubagentStart`, `SubagentStop` | — | ⚠️ 未驗證 |
| **Compaction** | — | `PreCompact` | `PreCompact`, `PostCompact` | `session_before_compact`, `session_compact`, `session_compact_failed` | ⚠️ 未驗證 |
| **錯誤** | `errorOccurred` | `StopFailure`, `FileChanged`, `CwdChanged`, `ConfigChange` | — | `file.changed`, `session_info_changed` | ⚠️ 未驗證 |

### 1.3 各工具核心特色與限制

#### GitHub Copilot

**優勢：**
- 8 種事件覆蓋主要生命週期階段
- Command hook 實作簡單——Bash/PowerShell 腳本即可
- JSON 設定檔配置，不需要寫程式碼

**限制：**
- Handler 類型單一——僅支援 command hook，不能做 LLM 層干預
- 同步執行——Hook 必須快速完成，否則拖慢整個 session
- 跨平台一致性待驗證——官方主要描述 CLI 與 Cloud Agent

#### Claude Code

**優勢：**
- 事件數量最多（30+ 種），覆蓋面最廣
- 5 種 Handler 類型——從簡單 command 到 AI 審查（prompt/agent）都有
- `prompt` 和 `agent` handler 讓 Hook 本身能呼叫 LLM 做判斷——在本次比較的五個工具中，只有 Claude Code 明確提供此能力
- Matcher 機制可精確控制觸發範圍

**限制：**
- `prompt`/`agent` handler 每次觸發消耗 LLM 調用，成本較高
- 設定複雜度高——30+ 事件 × 5 種 Handler 的排列組合
- Fail-Safe 行為依事件、輸出格式與 exit code 而定，非單純按 handler 類型二分

#### OpenAI Codex CLI

**優勢：**
- 10+ 種事件，包含 Session、Tool、Subagent、Compact 等階段
- Command hook 配置（`.codex/hooks.json` / `config.toml`），不需要寫程式碼
- `PreToolUse` schema 支援 `allow`/`deny`/`ask` 等明確的 permission decision

**限制：**
- 仍在活躍開發中，API 可能隨版本變更
- 外部 command hook 的能力邊界較窄——不能像 Pi 那樣修改 messages 或 provider payload
- 文件與社群資源相對較少

#### Pi Coding Agent

**優勢：**
- 30+ 種事件，分為 Resource / Session / Agent / Model / Tool 5 大類別
- Extension 能直接介入 LLM 請求層——`context` 可修改 messages 陣列、`before_provider_request` 可替換 provider payload、`before_provider_headers` 可改 HTTP headers。這在其他工具中只部分存在或不存在
- `tool_call` 可阻斷（block + terminate）且可修改 input 參數；`tool_result` 可修改結果——像 middleware 一樣鏈式處理
- `before_agent_start` 可同時注入 message 和修改 system prompt
- `input` 事件可在 prompt 進入 agent 前攔截、轉換、或直接處理
- 零編譯動態載入——TypeScript 寫完直接跑，不需要 build step

**限制：**
- Extension 擁有完整系統權限，安全邊界弱（官方有安全警告）
- Fail-Safe 行為依事件不同：一般 extension 錯誤多數不終止 agent；但 `tool_call` handler 出錯會阻止工具
- Handler 類型單一——只有 TypeScript script，不能像 Claude Code 那樣用 command 跑外部腳本或用 prompt 叫 AI 判斷
- provider payload 修改具有高度 provider-specific 相容性風險
- 非互動模式下 UI API 可能是 no-op

#### DeepSeek Harness

**⚠️ 資料不足，待補**

目前沒有找到足夠可靠的官方一手資料來確認 DeepSeek Harness 的具體 Hook 機制。以下為目前已知的有限資訊，**均未經官方來源驗證**：

- 有社群研究提到 Cordis 微核心框架與事件驅動架構
- 有提及 `oh-my-dsh` 散佈版可能支援跨工具 Hook 復用
- 具體事件清單、Handler 類型、配置方式均不明

建議：待 DeepSeek Harness 有更多官方文件或穩定版本後，重新研究。

## 二、Pi Hook 機制深度分析

> Pi 是我們每天在用、要拿來當 Cheerio Agent 藍本的工具。以下基於 Pi 官方文件（extensions.md）的分析。

### 2.1 事件分類架構

Pi 的 Hook 事件按職責分為 5 大類別：

| 類別 | 事件 | 觸發時機 |
|------|------|---------|
| **Resource** | `project_trust`, `resources_discover` | 啟動時載入信任與資源 |
| **Session** | `session_start`, `session_shutdown`, `session_before_switch`, `session_before_fork`, `session_before_compact`, `session_compact`, `session_compact_failed`, `session_before_tree`, `session_tree`, `session_info_changed` | Session 生命週期 |
| **Agent** | `before_agent_start`, `agent_start`, `agent_end`, `agent_settled`, `input`, `turn_start`, `turn_end`, `message_start/update/end`, `ui_prompt_start/end` | Agent 迴圈與 UI |
| **Model** | `model_select`, `thinking_level_select`, `context`, `before_provider_headers`, `before_provider_request`, `after_provider_response` | LLM 調用層 |
| **Tool** | `tool_execution_start/update/end`, `tool_call`, `tool_result`, `user_bash` | 工具執行層 |

Pi 文件採用這種分類方式來組織事件；其他工具可能有不同的分類法。

### 2.2 LLM 層深度干預

Pi 的特色不是事件數最多，而是 extension 能直接介入 context、provider payload、headers 與 tool call。以下是 Pi 中較突出的三個事件：

1. **`context`（修改 messages 陣列）**：在每次 LLM 調用前，Extension 可以拿到 messages 陣列的 deep copy 並修改回傳。這提供了成本、上下文長度與相關性優化的能力——但上下文修剪也可能刪掉必要資訊，需謹慎實作。

2. **`before_provider_request`（替換 provider payload）**：在 HTTP 請求送出前，Extension 可以 inspect 甚至 replace 整個 provider-specific payload。這讓你可以針對不同 provider 做差異化處理——但也意味著修改可能造成 provider-specific 的相容性問題。

3. **`before_agent_start`（修改 system prompt + 注入 message）**：一個事件可以同時做兩件事——修改 system prompt（chained，多個 extension 依序修改）以及回傳 message 給本次 agent loop。

### 2.3 與 Cheerio Agent 的關聯

Pi 的 Hook 系統直接影響 Cheerio Agent 的設計：

- **`tool_call` + `block/terminate`** → 可實作「危險操作攔截」（如 `rm -rf`、`sudo`）
- **`before_agent_start` + `systemPrompt`** → 可實作「動態 system prompt 注入」（如根據 session 狀態加入不同指引）
- **`context`** → 可實作「上下文修剪」（如移除 debug-only 訊息、壓縮過長的工具結果）——但需注意不要誤刪必要資訊
- **`input`** → 可實作「輸入前處理」（如自動展开 skill 指令、轉換 prompt 模板）——但需保留原始輸入供稽核
- **`session_start` + `session_shutdown`** → 可實作「session 級別的資源管理」（如資料庫連線池、WebSocket 連線）

## 三、哪些設計值得移植到 Cheerio Agent

> 這是本研究的核心價值——從工具的 Hook 設計中，提煉出 Cheerio Agent 應該採用的設計。

### 3.1 有明確根據，建議移植

| 設計來自 | 設計概念 | 為什麼值得移植 | Cheerio 實作方式 |
|---------|---------|--------------|----------------|
| **Claude Code** | Matcher 機制（event + 條件過濾） | Matcher 讓你可以精確控制「哪些操作觸發 Hook」，避免全域攔截拖慢效能 | Cheerio Agent 的 Hook 設計應支援 event + matcher（tool name + 條件過濾） |
| **Pi** | `tool_call` 可阻斷 + 可修改 input | 直接在工具執行前攔截並修改參數，是最強的安全把關點 | Cheerio 必須實作 tool_call 的 block/terminate 能力，並搭配審計紀錄 |
| **Pi** | `context` 可修改 messages | 提供上下文長度與成本優化的能力 | Cheerio Agent 應支援 pre-LLM-call 的 messages 修改，但需有版本化與驗證機制 |
| **Pi** | `input` 事件（prompt 前處理） | 在 prompt 進入 agent 前攔截、轉換、或直接處理 | Cheerio Agent 的 input 處理應可被 Extension 擴充，並保留原始輸入供稽核 |
| **各工具共同** | tool 前 hook 必須支援明確的 allow/deny/modify 語意 | 這是所有工具的共同設計方向，不是單一工具的獨家能力 | Cheerio 的 tool_call handler 應回傳明確的阻斷/放行/修改結果 |

### 3.2 有根據但需調整定位

| 設計來自 | 設計概念 | 參考價值 | 需要調整的地方 |
|---------|---------|---------|--------------|
| **Pi** | Extension 零編譯動態載入 | 開發體驗極好——寫完 TypeScript 直接跑 | Cheerio Agent 需要更嚴格的權限控制（Pi 的 extension 有完整系統權限） |
| **Claude Code** | `prompt`/`agent` handler（用 AI 審查） | 在本次比較中，只有 Claude Code 明確提供此能力 | 應先定位成非同步 advisory / 可選 policy，而非核心 handler——它會增加延遲、成本、遞迴觸發與 prompt injection 風險 |
| **Pi** | 不可變事件 log（概念） | Cheerio 的可追溯性需求——每次 Hook 觸發都應記錄供事後追溯 | 這是 Cheerio 自己的架構需求，應基於可追溯性需求設計，而非標成「參考某工具」 |
| **Codex CLI** | 明確的 permission decision（allow/deny/ask） | `PreToolUse` schema 支援明確的 permission decision | Cheerio 的 tool 前 hook 應支援類似的明確語意 |

### 3.3 不建議移植

| 設計來自 | 設計概念 | 為什麼不建議 |
|---------|---------|-------------|
| **Copilot** | 同步阻塞 Hook | 拖慢整個 session——但這不是說同步本身錯，而是需要搭配 timeout 與並行策略 |
| **Pi** | Fail-Open 預設行為 | 安全性不足——Cheerio 的安全相關 hook 應預設 Fail-Closed，非安全 hook（如 logging）可 Fail-Open |

### 3.4 Cheerio Agent Hook 設計建議

基於上述分析，Cheerio Agent 的 Hook 系統應滿足：

1. **事件分類**：可參考 Pi 的 5 大類別（Resource / Session / Agent / Model / Tool），根據 Cheerio 的實際控制點決定初期事件數
2. **Matcher**：支援 tool name + 條件過濾（如副檔名、路徑模式）
3. **Handler 類型**：
   - `script`（TypeScript/JavaScript）— 基礎
   - `command`（外部 CLI 腳本）— 安全檢查、lint
   - `ai_advisory`（用 AI 審查，非同步 advisory）— 品質把關（初期不作為阻斷依據）
4. **Fail-Safe 分級**：
   - Security gate hook：預設 Fail-Closed
   - Observability hook：Fail-Open
   - Context mutation hook：明確版本化並拒絕無效輸出
5. **上下文干預**：支援 `context`（修改 messages）+ `before_agent_start`（修改 system prompt）
6. **不可變事件 log**：每次 Hook 觸發都記錄到 immutable log，供事後追溯

## 來源

- [[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]]
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統]]
- [[wiki/entities/openai-codex|OpenAI Codex]]（Hook 機制章節）
- [[wiki/entities/deepseek-harness|DeepSeek Harness]]
- [[wiki/entities/pi-mono|Pi Coding Agent]]（Hook 機制章節）
- Pi 官方文件：`docs/extensions.md`（Extension Event Hooks 完整清單）
- GitHub Copilot 文件：[Hooks 概覽](https://docs.github.com/en/copilot/concepts/agents/hooks)、[Hooks reference](https://docs.github.com/en/copilot/reference/hooks-reference)
- Claude Code 文件：[Hooks reference](https://code.claude.com/docs/en/hooks)
- Codex CLI：[config.md](https://github.com/openai/codex/blob/main/docs/config.md)、[PreToolUse schema](https://github.com/openai/codex/blob/main/codex-rs/hooks/schema/generated/pre-tool-use.command.output.schema.json)
- [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]]

## 相關頁面

- [[wiki/topics/hooks|Hooks]] — AI agent 事件驅動自動化機制
- [[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]]
- [[wiki/concepts/copilot-hooks-system|Copilot Hooks 系統]]
- [[wiki/entities/openai-codex|OpenAI Codex]]
- [[wiki/entities/deepseek-harness|DeepSeek Harness]]
- [[wiki/entities/pi-mono|Pi Coding Agent]]
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]
- [[wiki/concepts/harness|Harness — LLM 的驅動層]]
