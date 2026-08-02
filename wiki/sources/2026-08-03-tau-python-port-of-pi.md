---
title: "Tau: A Python Port of Pi — Coding Harness Deep Dive"
type: source
created: 2026-08-03
updated: 2026-08-03
sources: 1
tags: [pi, tau, python, coding-harness, textual]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-03-tau-python-port-of-pi
---

> 來源：[YouTube — Tau: A Python Port of Pi](https://www.youtube.com/watch?v=qo1QNxWcm28)（25:03）
> 原始字幕：[[raw/youtube/tau-python-port-of-pi|raw transcript]]

## 一句話

Tau 是 Pi 的 Python 移植版，架構完全相同，差異只在 TUI 層使用 Textual 框架而非 TypeScript 從零打造。

## 重點摘要

### Tau 是什麼
- Pi 的 **Python port**，由 Pi 團隊內部開發
- 架構與 Pi **完全相同**：session 管理、skills、extensions、system prompt 都一樣
- 跨平台：Mac / Linux / Windows
- 安裝方式：一行 script

### TUI 差異（Textual vs Pi 原生 TypeScript）
- Pi 的 TUI 是 TypeScript 從零打造；Tau 用 **Textual**（Python terminal UI framework）
- 指令大致相同，但有些 Tau 獨有功能：
  - `/skills` — 列出所有已載入 skills（Pi 在 session header 顯示）
  - `/prompts` — 列出自訂 prompts
  - `/tools` — 列出所有工具及其來源（built-in vs extension）
  - **通知**：背景工作完成時跳出通知（Pi 需 extension 補）

### Session 管理（與 Pi 相同）
- Session 以 **tree**（非 list）儲存，每個 message 有 `parent` 指向前列
- 支援 **fork**：從任意 message 分支出新對話
- 儲存格式：**JSONL**（每行一個 JSON 物件）
- 路徑：`.tau/sessions/<working-directory>/`
- `/tree` 檢視與導覽整棵 session tree
- `/resume` 列出並恢復過去 sessions

### Session 匯出
- `/export` — 匯出為 HTML（互動式審閱）或 JSONL
- 可讓另一個 agent 分析匯出的 session（用 session ID 即可）
- 適合測試 skill / tool / MCP server 後做 feedback loop

### Skills 系統（與 Pi 相同）
- Skills = procedural instructions in markdown，可附帶 scripts / templates
- 載入位置：`~/.agents/skills/`、`~/.tau/skills/`、`~/.pi/skills/` + project root
- 明確呼叫：`/skill:<name>`
- Agent 可自動判斷並呼叫 skill（description 在 system prompt 中）
- `/skills` 命令可即時檢視、讀 skill 內容、加入 context

### Custom Prompts（Slash Commands）
- 與 skills 的差異：
  - Skills = procedural，agent 知道它的存在（in system prompt）
  - Prompts = 純文字替換，agent 不知道有 prompts（前端 level 替換）
- 範例：`/issue` → 替換為完整 GitHub issue 開立 prompt

### Extensions
- 與 Pi **完全相容**，使用相同 events
- 需 port（語言不同）但 API 相同
- 影片中示範：Ryan 的 sub-agent extension
- `/reload` 重新載入 skills / prompts / extensions / themes

### Themes
- 可自訂 TUI 主題
- tau-light、high contrast、Catppuccin Mocha 等
- JSON 檔案格式，可請 Tau 自動產生

### Provider 支援
- Subscription：Codex、Anthropic、GitHub Copilot
- API key：OpenAI、Anthropic、Gemini 等
- Hugging Face inference providers（單一路由）
- Custom provider

## 與我們的關聯

- 我們目前使用 **Pi**（TypeScript 版），Tau 證明了架構的可移植性
- Tau 的 `/skills`、`/tools` 列出功能值得 Pi 借鏡
- Session tree + JSONL 儲存格式是 harness 設計的核心模式
- Extension 相容性確認：Pi 生態的 extensions 可跨語言複用

## 來源

- [[raw/youtube/tau-python-port-of-pi|Raw transcript — YouTube qo1QNxWcm28]]

## 相關頁面

- [[wiki/entities/pi-mono|pi-mono]] — Tau 的上游原型
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi runtime 核心
- [[wiki/concepts/meta-harness|meta-harness]] — harness 再抽象研究
