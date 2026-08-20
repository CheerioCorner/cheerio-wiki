---
title: "OpenWiki GitHub Repository — 15.4k Stars, MIT License"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [langchain, openwiki, github, mit, cli]
topics: [okf, agent-runtime-implementations]
provenance_url: "https://github.com/langchain-ai/openwiki"
---

# OpenWiki GitHub Repository

> LangChain OpenWiki 的 GitHub repo：15.4k stars、1.1k forks、MIT license，Deep Agents 驅動的 CLI 工具。

## 基本規格

- **Stars**: 15.4k `https://github.com/langchain-ai/openwiki`
- **Forks**: 1.1k
- **License**: MIT
- **驅動**: Deep Agents（文件生成 agent）
- **Language**: TypeScript

## 兩種模式

| 模式 | 命令 | 輸出路徑 | 用途 |
|------|------|---------|------|
| Code（預設） | `openwiki --init` | `openwiki/` (in repo) | Repo 文件給 coding agents |
| Personal | `openwiki personal --init` | `~/.openwiki/wiki` | 個人知識庫 |

## 支援的 Model Providers（12 個）

OpenAI、Anthropic、Gemini、Bedrock、OpenRouter、GitHub Copilot，以及任何 OpenAI-compatible gateway。

## Connectors

Custom MCP、Notion、Slack、Gmail、X、Web Search、Hacker News、local git repositories。

## CI 整合

三種 CI 平台都支援：
- GitHub Actions: `openwiki-update.yml`
- GitLab CI: `openwiki-update.gitlab-ci.yml`
- Bitbucket Pipines: `openwiki-update.bitbucket-pipelines.yml`

## AGENTS.md / CLAUDE.md 注入機制

OpenWiki 只改寫 `<!-- OPENWIKI:START -->…<!-- OPENWIKI:END -->` 區塊，現有內容不受影響。`INSTRUCTIONS.md` 是使用者撰寫的 brief，永不被覆寫。

## 互動式 Visualizer

`openwiki visualize` — 本地啟動 node graph，支援 `--port`、`--export` 靜態匯出。

## 來源頁面

- [[wiki/sources/2026-08-20-langchain-openwiki-cli|YouTube 影片 source note]]
- [[wiki/entities/langchain-openwiki|OpenWiki entity]]
