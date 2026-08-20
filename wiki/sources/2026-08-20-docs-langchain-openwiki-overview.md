---
title: "OpenWiki Official Docs — Overview"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [langchain, openwiki, documentation, deep-agents]
topics: [okf, agent-runtime-implementations]
provenance_url: "https://docs.langchain.com/oss/openwiki/overview"
---

# OpenWiki Official Docs — Overview

> OpenWiki 官方文件 overview 頁。Deep Agents 驅動、LangSmith tracing、兩種模式。

## 核心定位

OpenWiki is an open source CLI that writes and maintains a Markdown wiki about your codebase or personal knowledge. The wiki captures details such as architecture, integrations, evals, and workflows so coding agents can use it as durable context instead of rediscovering the repository on every task. `https://docs.langchain.com/oss/openwiki/overview`

## 兩種模式

| 模式 | 命令 | 輸出路徑 |
|------|------|---------|
| Code（預設） | `openwiki` / `openwiki code` | `openwiki/` (in repo) |
| Personal | `openwiki personal` | `~/.openwiki/wiki` |

## 關鍵能力

- **Repository wikis**: Markdown docs under `openwiki/`，自動寫入 `AGENTS.md` 和 `CLAUDE.md`
- **Personal brain**: 從 git repos、Gmail、Notion、Web Search、Hacker News、X/Twitter 建立
- **Automatic updates**: GitHub Actions / GitLab CI / Bitbucket Pipelines
- **12 model providers**: OpenAI、Anthropic、Gemini、Bedrock、OpenRouter、GitHub Copilot 等
- **OKF v0.1**: ⚠️ 官方文件寫 v0.1，但 GitHub README 和官方部落格寫 v0.2——官方來源間存在版本標示不一致
- **LangSmith tracing**: 文件執行過程可追踪

## ⚠️ 版本矛盾

此官方文件頁寫「OKF v0.1」，但 GitHub README、官方部落格（raw 2）、know.2nth.ai 都明確說 OpenWiki 0.2 採用的是 OKF v0.2。這是官方自己文件間的不一致（docs 站可能未同步更新）。

## 相關頁面

- [[wiki/entities/langchain-openwiki|OpenWiki]]
- [[wiki/concepts/okf-open-knowledge-format|OKF]]
