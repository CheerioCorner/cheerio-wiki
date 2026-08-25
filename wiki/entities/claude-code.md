---
title: "Claude Code — Anthropic AI Coding Agent"
type: entity
created: 2026-08-04
updated: 2026-08-14
sources: 3
tags: [claude-code, anthropic, mcp, ai-coding-agent, hooks, subagents]
topics: [ai-development-tools, agent-runtime-implementations]
canonical: entities/claude-code
---

> Claude Code 是 Anthropic 推出的 agentic coding tool，以終端機為起點，擴展到 Desktop App、Web、IDE、GitHub（`@claude` tag）。核心建立在 MCP（Model Context Protocol）之上，能自主觀察 codebase → 推理 → 執行工具 → 評估結果 → 重複循環。

## 核心機制

### 1. Agentic Loop（代理循環）
```
Observe → Reason → Action → Evaluate → Loop
```
每次收到指令後觀察 codebase（grep、read file、glob）、推理下一步、呼叫工具、評估結果、決定是否繼續。能處理多步驟複雜任務（重構整個模組、修復跨檔案 bug）。

### 2. Extended Thinking（擴展思考）
支援動態 thinking budget——根據任務複雜度自動調整思考預算，簡單任務少思考、複雜任務多思考。

### 3. MCP 整合（Model Context Protocol）
原生支援 MCP，透過 `.mcp.json` 或 `claude mcp add` 串接：
- **LSP Server**：語法分析、類型檢查（Pyright、TypeScript Language Server）
- **Database**：直接查詢資料庫
- **Custom API**：任何 MCP server
支援三種傳輸層：stdio（本地進程）、SSE（遠端 OAuth）、HTTP（REST）。

### 4. Skills（技能系統）
以 `SKILL.md` 檔案定義，包含 metadata、instructions、resources。支援 progressive disclosure（漸進式揭露）。概念與 Pi Agent 的 skill 系統一致。

### 5. Subagents（子代理）
可啟動 subagents 並行處理任務。例如 `/code-review` 同時啟動 4 個獨立 review agent：
1. CLAUDE.md 合規性檢查
2. CLAUDE.md 合規性檢查（第二視角）
3. Bug 掃描
4. Git 歷史分析

每個 subagent 獨立產出結果，最後由主 agent 綜合評分（confidence scoring ≥ 80 才通過）。

### 6. Hooks（鉤子系統）
**Deterministic 的事件驅動自動化機制**——在 AI 忘記或跳過規則時，由軟體層強制執行檢查、防呆與品質把關。三層架構：Event（什麼時候啟動）→ Matcher（攔截哪個操作）→ Handler（做什麼動作）。

10 個核心 Event（共 31 種）：`SessionStart`、`UserPromptSubmit`、`PreToolUse`（安全防呆）、`PostToolUse`（快速驗收）、`Stop`（深度檢查）、`Notification`、`SubagentStart`、`SubagentStop`、`PreCompact`。

5 種 Handler：`command`、`http`、`mcp_tool`、`prompt`、`agent`。

→ 詳見 [[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]]

#### 最新研究發現（2026-08-25）

根據 [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]]，Claude Code Hooks 有以下關鍵特性：

- **25+ 種事件**：比其他 Harness 更細緻，包括作業系統級檔案異動監控（`FileChanged`）、MCP 交互對話、Git Worktree 操作等（既有頁面另有依 YouTube 教學影片精算出的「31 種」精確計數，兩者不矛盾，本次研究報告用語較保守）
- **5 種 Handler 類型**：`command`（Shell 指令）、`http`（POST 請求）、`mcp_tool`（MCP 工具調用）、`prompt`（LLM 評估）、`agent`（子代理驗證）
- **非同步背景執行**：原生支援 `async: true`，適合讓耗時的測試套件或通知在背景執行
- **動態掛載與卸載**：可將 Hook 宣告在 Skill 或 Subagent 檔案的 Frontmatter 中，隨該組件執行而動態加載
- **環境變數持久化**：可利用 `CLAUDE_ENV_FILE` 寫入變數，並使其在後續的 Bash 工具調用中持續生效

### 7. Context Compaction（上下文壓縮）
對話過長時自動摘要化，釋放 context window 空間。

### 8. Permission Prompting（權限提示）
危險指令（刪除檔案、system 命令）暫停詢問使用者授權，確保人類保有最終控制權。

## 安裝與使用

```bash
# 安裝（原生安裝器為主推，npm deprecated）
curl -fsSL https://claude.ai/install.sh | sh
# 或
brew install anthropic/claude/claude-code

# 啟動
claude

# 設定 MCP Server
claude mcp add lsp-python npx -y @modelcontextprotocol/server-lsp \
  --command pyright-langserver --args --stdio
```

## MCP 配置範例

```json
{
  "mcpServers": {
    "lsp-python": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-lsp", "--command", "pyright-langserver", "--args", "--stdio"]
    },
    "lsp-typescript": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-lsp", "--command", "typescript-language-server", "--args", "--stdio"]
    }
  }
}
```

## OKF 整合

Claude Code 完全相容 OKF 格式——Agent 可直接讀取 OKF Markdown 理解專案知識：

```
.knowledge-wiki/
├── index.md
├── architecture.md
├── modules/
│   ├── auth.md
│   └── database.md
└── decisions/
    └── 2026-08-04-orm-choice.md
```

## Claude Code vs Pi Agent — 互補關係

| 面向 | Claude Code | Pi Agent |
|------|-------------|----------|
| **定位** | Coding Agent（專注程式碼） | LifeOS Agent（跨域 orchestrator） |
| **介面** | Terminal / Desktop App / Web / IDE / GitHub | TUI（Terminal UI） |
| **Protocol** | MCP（開放標準） | Extension System |
| **擴充方式** | Plugins（commands + agents + skills + hooks） | Skills + Extensions |
| **LSP 整合** | MCP Server 原生支援 | Extension（如 Pi LSP Bridge） |
| **Subagent** | 並行 multi-agent review | `pi-subagents` skill |
| **呼叫方式** | 直接使用 | 透過 `chat-with-claude` skill CLI 呼叫 |

**關鍵互補點：**
- Claude Code 做**深度 coding**（重構、debug、測試），Pi 做**廣度 orchestration**（工作追蹤、知識管理、跨工具協作）
- 我們透過 `chat-with-claude` skill 把 Claude Code 當作「coding subagent」來呼叫
- MCP 是 Claude Code 核心優勢，Pi 的 extension system 在跨域整合上更靈活

## 客觀事實

| 概念 | 發現 | 來源 |
|------|------|------|
| 維護者 | Anthropic | GitHub repo |
| 驅動模型 | Claude 5 家族（Opus 5、Sonnet 5）、Haiku 4.5 | 2026-08 用戶裁定 |
| 安裝方式 | 原生安裝器（curl / brew / winget）為主推；npm deprecated | GitHub README |
| Plugin 系統 | commands + agents + skills + hooks + MCP | plugins/README.md |
| 官方 Plugins | 14 個（code-review、feature-dev、hookify、security-guidance 等） | plugins/ 目錄 |
| MCP 傳輸層 | stdio、SSE、HTTP | plugin-dev skill |
| Hook 事件 | PreToolUse、PostToolUse、Stop、SubagentStop、SessionStart、SessionEnd、UserPromptSubmit、PreCompact、Notification | hookify README |
| Extended Thinking | 動態 thinking budget | Anthropic docs |
| Context Compaction | 自動摘要化長對話 | Anthropic docs |
| 授權 | 商業授權（Anthropic Commercial ToS） | Anthropic ToS |

## Claude Design MCP Handoff（設計交付）

Claude Code 可透過 MCP 接收 [[wiki/entities/claude-design|Claude Design]] 的設計輸出：

1. **設計匯出**：在 Claude Design 中匯出，取得 MCP prompt
2. **Metadata 讀取**：Claude Code 讀取設計 metadata、Design System、所有元素
3. **應用建構**：將設計轉為真實前端程式碼
4. **Deep Review**：驗證設計與 [[wiki/concepts/design-md-format|design.md]] 一致性
5. **後端整合**：透過 MCP 連接 Supabase 等後端服務

## 來源

- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]
- [[wiki/sources/2026-08-13-claude-design-youtube|How To Use Claude Design To Build Beautiful Sites]]
- Notion 種子：[[https://app.notion.com/p/3bb5979e3a8c81ce93fdfd8be0c7ab49|Claude Code — Anthropic AI Coding Agent]]
- [Claude Code 文件](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code)
- [MCP 官方](https://modelcontextprotocol.io/)

## 相關頁面

- [[wiki/entities/claude-design|Claude Design]] — 設計交付的來源工具
- [[wiki/entities/mcp-model-context-protocol|MCP]] — Claude Code 的核心 Protocol
- [[wiki/entities/github-copilot|GitHub Copilot]] — 競品 AI Coding Agent
- [[wiki/entities/pi-mono|Pi Agent]] — 競品 AI Coding Agent
- [[wiki/entities/langgraph|LangGraph]] — Agent Runtime 框架
