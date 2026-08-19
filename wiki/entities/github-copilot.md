---
title: "GitHub Copilot — AI 程式碼輔助生態系"
type: entity
created: 2026-08-04
updated: 2026-08-14
sources: 2
tags: [github-copilot, ai-coding, extensions-sdk, copilot-workspace, agent-skills]
topics: [ai-development-tools, agent-runtime-implementations]
canonical: entities/github-copilot
---

> GitHub Copilot 是 GitHub（Microsoft 旗下）推出的 AI 程式碼輔助產品線，從 IDE 補全演化為完整 Agent 生態系：Extensions SDK、Agent Skills、Cloud Agent、Code Review、MCP 整合。

## 生態系剖析（11 個組件）

### 1. IDE 補全（Inline Suggestions）
最基礎的功能。支援 VS Code、JetBrains、Visual Studio、Xcode、Eclipse 等。2025 年新增 **Next Edit Suggestions**（NES），能預測下一步要編輯的位置，從「被動補全」進化到「主動預測」。

### 2. Copilot Edits（IDE 內多檔案編輯）
跨多個檔案同時修改，類似「IDE 內的 agent mode」。Copilot 自動辨識需要改動的檔案、提出方案，使用者 approve 後一次套用。最接近「AI pair programming」的體驗。

### 3. Copilot Chat
對話式介面，支援 IDE、GitHub 網站、Mobile、Windows Terminal。支援 **Agent Mode**——讓 Copilot 自主決定修改哪些檔案、執行哪些指令，迭代直到任務完成。

### 4. Copilot CLI
`gh copilot` 在終端機使用。支援 **Hooks** 機制（事件驅動擴展點）。我們已建立 `chat-with-copilot` skill 串接。

### 5. GitHub Copilot App（桌面應用程式）
2025 年推出的獨立桌面 App，專做 agent-driven development。可同時跑多個 agent session、管理 issues/PRs、設定排程自動執行。

### 6. Copilot Workspace
以 GitHub Issue 為起點的雲端開發環境。流程：Issue → Code Graph 分析 → 自動產出實作計畫 → LSP 驗證 → 執行測試 → 開 PR。

### 7. Copilot Cloud Agent
自主 AI agent——給它一個 issue 或指令，它會研究 repo、制定計畫、改 code、開 PR。支援 **Custom Agents**（自訂工具、指令、MCP servers）。

### 8. Copilot Code Review
AI 自動 review PR，產生改進建議。整合到 pull request lifecycle 中。

### 9. Extensions SDK & Third-party Agents
讓第三方建立 Copilot extension（Webhook / MCP）。2025 年更支援 **Third-party Coding Agents**——其他 AI agent（如 Codex）可接入 Copilot 平台。

### 10. Agent Skills
資料夾形式的指令集（instructions + scripts + resources），Copilot 自動載入。概念類似 Pi 的 skill 系統，但 Copilot 的 skill 是「Copilot 自己讀」，Pi 的 skill 是「讀進來指導 Pi 行為」。

### 11. 客製化層
- **Copilot Spaces**：把 code、docs、specs 集中管理
- **Custom Instructions**：`.github/copilot-instructions.md`
- **Prompt Files**：可分享的 prompt 模板（Markdown）
- **Copilot Memory**（Public Preview）：自動從 repo 學習並記住資訊

## 與 Pi Agent / Claude Code 的差異

| 面向 | GitHub Copilot | Pi Agent | Claude Code |
|------|---------------|----------|-------------|
| **核心定位** | 開發平台（IDE 嵌入 + 雲端 Agent） | 個人助理框架（多工具整合） | 終端機 AI coding agent |
| **執行環境** | IDE / Cloud / CLI / Desktop App | Pi TUI（本機 + 遠端） | 終端機（CLI） |
| **Agent 模式** | Cloud Agent（GitHub 上跑） | Pi Agent（本機 TUI）+ Subagents | Agentic Loop（本地執行） |
| **擴展機制** | Extensions SDK + Agent Skills | Skills + Extensions + Custom Tools | MCP Tools |
| **Context 管理** | Spaces、Custom Instructions、Memory | AGENTS.md、Wiki 知識庫、Notion 花園 | CLAUDE.md |
| **多 Agent** | Third-party Coding Agents | Subagents + Round-table | 單一 agent 為主 |

## 互補關係

1. **Copilot 擅長 IDE 內即時輔助**，Pi 擅長**跨工具任務编排**（Notion、Obsidian、多 AI 協作）
2. **Copilot Extensions SDK** 概念與 Pi 的 skill 系統異曲同工
3. **Copilot Cloud Agent Custom Agents** 與 Pi 的 subagent 概念類似，但 Copilot 是雲端的、跟 GitHub repo 深度整合
4. **Copilot Memory**（自動化短期記憶）與我們的 Wiki 知識庫（手動維護長期記憶）可互補

## 定價方案（截至 2026-08）

| 方案 | 月費 | AI Credits |
|------|------|-----------|
| Copilot Free | $0 | 2000 completions + 有限 credits |
| Copilot Pro | $10 USD | 1,000 base + 加購 |
| Copilot Pro+ | $39 USD | 3,900 base + 7,000 total |

## Pi Agent 整合

### Copilot Skill（2026-08-08 建立）

Pi 可透過 `gh copilot` CLI 直接與 Copilot 討論：

```bash
gh copilot -p "prompt" --allow-tool 'shell(echo)'
```

**Skill 位置：** `~/.agents/skills/chat-with-copilot/SKILL.md`
**觸發詞：** 「問 Copilot」、「用 Copilot」、「chat-with-copilot」、「/copilot」

## 來源

- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]
- Notion 種子：[[https://app.notion.com/p/3bb5979e3a8c81baa661e06f1d8a1edc|GitHub Copilot — AI 程式碼輔助生態系]]
- [Copilot Extensions SDK](https://docs.github.com/en/copilot/building-copilot-extensions)
- [Copilot 官方文件](https://docs.github.com/en/copilot)

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — Copilot 可整合的 Protocol
- [[wiki/entities/claude-code|Claude Code]] — 競品 AI Coding Agent
- [[wiki/entities/pi-mono|Pi Agent]] — 競品 AI Coding Agent
- [[wiki/entities/langgraph|LangGraph]] — Agent Runtime 框架
