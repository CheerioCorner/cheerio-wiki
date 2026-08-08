---
title: agentskills.io — AI Agent Skill 開放標準
type: entity
created: 2026-08-07
updated: 2026-08-07
sources: 1
tags: [ai-agent, skill, standard, cross-platform]
topics: [agent-infrastructure, skill]
canonical: entities/agentskills-io-standard
provenance:
  - kind: external
    url: https://app.notion.com/p/3425979e3a8c8018834ad248b82208f0
---

# agentskills.io — AI Agent Skill 開放標準

> 一個包含**指令、腳本、資源**的可移植工作流模塊。

## 定義

Skill 是遵循 [agentskills.io](https://agentskills.io/) 開放標準的模組化工作流單元。不僅適用於單一 IDE，還可跨多個 AI 工具使用。

## 跨平台相容性

| 工具 | 類型 | 說明 |
|------|------|------|
| GitHub Copilot in VS Code | IDE | Chat 和 Agent 模式 |
| GitHub Copilot CLI | CLI | 終端命令行 |
| GitHub Copilot Coding Agent | Agent | 自動化任務執行 |
| Claude Code | CLI | Anthropic CLI agent |
| Claude Coworker | Agent | 協作模式 |
| Gemini Antigravity | CLI | Google Gemini agent |
| Gemini CLI | CLI | Google CLI |
| ... | — | 其他提供 Skill 注入的 Harness 工具 |

## Skill 資訊來源

### skills.sh（執行層）
- **營運者：** Vercel
- **性質：** 公開 Agent Skills Registry（動態套件登錄中心）
- **使用方式：** `npx skills` — 將程式碼下載並注入開發環境
- **生態系：** 跨多種 AI 編輯器與代理（Claude, Cursor, Copilot 等）
- ⚠️ **安全提醒：** 外部技能必須經過程式碼審查，建議轉存至企業內部私有庫

### Awesome GitHub Copilot（參考層）
- **營運者：** GitHub 官方 + 開源社群
- **性質：** 靜態知識庫與索引目錄（Curated Directory）
- **內容：** Agents、Skills、Instructions、MCP 工具整合方案
- **生態系：** 專注於 Microsoft / GitHub 生態系
- ⚠️ **注意：** 缺乏 SLA 保障，引入前需技術適用性驗證

## 與我現有做法的比較

| 概念 | agentskills.io 標準 | Pi Agent 做法 |
|------|---------------------|---------------|
| Skill 格式 | 開放標準（跨平台） | `~/.agents/skills/` 目錄結構 |
| 分發方式 | `npx skills` / Registry | 本地安裝 / npm |
| 安全模型 | 建議企業審查 | 信任本地檔案 |
| 相容性 | 多 Harness | Pi 專用 |

## 種子故事

這顆種子來自 Notion「任何當下 → AI相關 → Skill」頁面。記錄了 agentskills.io 開放標準如何讓 Skill 跨平台流通，以及 skills.sh 和 Awesome GitHub Copilot 兩大資訊來源的定位差異。

## 連結

- 🔗 [agentskills.io](https://agentskills.io/)
- 🔗 [skills.sh](https://skills.sh/)
- 🔗 [Awesome GitHub Copilot](https://awesome-copilot.github.com/)
- 📖 [[wiki/entities/mattpocock-skills|mattpocock/skills]]
- 📖 [[wiki/entities/github-copilot|GitHub Copilot]]
