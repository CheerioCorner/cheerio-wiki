---
title: GitHub Copilot
type: entity
created: 2026-08-04
updated: 2026-08-04
sources: 1
tags: [github-copilot, ai-coding, extensions-sdk, copilot-workspace]
topics: [ai-development-tools]
canonical: entities/github-copilot
---

# GitHub Copilot

> GitHub 的 AI 程式碼輔助工具，從自動補全演化為完整的 AI Coding Agent 生態系。

## 架構演進

```
2021 — Copilot (Code Completion)
2023 — Copilot Chat (Conversational)
2024 — Copilot Extensions SDK (Third-party)
2024 — Copilot Workspace (Agentic)
2025 — Copilot Coding Agent (Autonomous)
```

## 核心組件

### 1. Copilot Extensions SDK
- **用途**：第三方開發者接入私有工具
- **介面**：Webhook / MCP
- **案例**：接入 Sourcegraph Code Graph、私有 LSP

### 2. Copilot Apps
- **用途**：建立自訂 Copilot 擴充
- **功能**：自訂指令、知識庫掛載
- **配置**：`.github/copilot-instructions.md`

### 3. Copilot Workspace
- **用途**：Task-driven Agent 環境
- **流程**：Code Graph → Plan → LSP 驗證 → 執行測試
- **特色**：完整的開發工作流程

### 4. Copilot Coding Agent
- **用途**：自主執行多步驟任務
- **能力**：Issue → PR、Code Review、Bug Fix

## LSP / Code Graph 整合

### 內建工具
- **TypeScript LSP** — 內建探針
- **Python LSP** — 內建探針
- **GitHub Code Search** — Tree-sitter/SCIP 索引

### Extension 整合
```yaml
# .github/copilot-instructions.md
# 自訂 Copilot 指令

## 專案架構
- 使用 OKF 格式的 knowledge wiki
- 參考 docs/ 目錄下的架構文件

## 程式碼規範
- 遵循 ESLint 規則
- 使用 TypeScript strict mode
```

## 安全性

1. **Code Suggestions** — 只建議不自動執行
2. **Chat Permissions** — 控制檔案存取
3. **Workspace Trust** — 限制未信任專案

## GitHub 熱門相關專案

| 專案 | 說明 |
|---|---|
| `github/copilot-docs` | 官方文件 |
| `github/copilot-vscode` | VS Code 擴充 |
| `github/copilot-cli` | CLI 工具 |

## 來源

- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]
- [Copilot Extensions SDK](https://docs.github.com/en/copilot/building-copilot-extensions)
- [Copilot 官方文件](https://docs.github.com/en/copilot)

## Pi Agent 整合

### Copilot Skill（2026-08-08 建立）

Pi 可透過 `gh copilot` CLI 直接與 Copilot 討論：

```bash
# 使用方式
gh copilot -p "prompt" --allow-tool 'shell(echo)'

# 範例
gh copilot -p "What is recursion?" --allow-tool 'shell(echo)'
```

**Skill 位置：** `~/.agents/skills/chat-with-copilot/SKILL.md`
**觸發詞：** 「問 Copilot」、「用 Copilot」、「chat-with-copilot」、「/copilot」
**執行目錄：** `~/.pi/copilot-runs/`

**與 chat-with-gemini skill 比較：**
| | chat-with-gemini (Gemini) | chat-with-copilot |
|---|---|---|
| CLI | agy (Antigravity) | gh copilot |
| 執行方式 | tmux/Windows Terminal pane | 背景執行 |
| 認證 | Google OAuth | GitHub auth login |
| Token 消耗 | 免費額度 | AI Credits |

**相關 Skill：**
- `chat-with-gemini` — 單獨跟 Gemini 聊天
- `chat-with-gemini-research` — 叫 Gemini 做深度研究
- `round-table` — 多 AI 圓桌會議（Pi 主持，subagent + Gemini + Copilot 共同討論）

## 相關頁面

- [[wiki/entities/mcp-model-context-protocol|MCP]] — Copilot 可整合的 Protocol
- [[wiki/entities/claude-code|Claude Code]] — 競品 AI Coding Agent
- [[wiki/entities/pi-mono|Pi Agent]] — 競品 AI Coding Agent
