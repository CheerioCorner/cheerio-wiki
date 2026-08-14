---
title: "anthropics/skills — 官方 GitHub Repo"
type: source
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [skill, anthropic, github, open-source]
topics: [skill]
provenance_raw: "raw/web/2026-08-14-anthropicsskills-public-repository-for-agent.md"
---

# anthropics/skills — 官方 GitHub Repo

> Source: [anthropics/skills](https://github.com/anthropics/skills)
> Clipped: 2026-08-14

## 重點摘要

Anthropic 官方公開的 Agent Skills 倉庫，展示 Claude Skills 系統的可能性與實作模式。

### 倉庫內容

四大類 Skill 範例：

- **Creative & Design**：藝術、音樂、設計相關
- **Development & Technical**：測試 Web App、MCP Server 生成等
- **Enterprise & Communication**：通訊、品牌等企業工作流
- **Document Skills**：docx/pdf/pptx/xlsx（**source-available**，非開源）

### 授權條款

- 大部分 Skills：**Apache 2.0**（開源）
- 文件處理 Skills（docx/pdf/pptx/xlsx）：**Source-available**（可觀摩，非開源），作為複雜 Skills 的參考實作

### 安裝方式

**Claude Code Plugin Marketplace：**
```
/plugin marketplace add anthropics/skills
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

**Claude.ai：** 已內建於付費方案。

**Claude API：** 透過 Skills API 上傳自訂 Skills。

### Partner Skills

- **Notion** — [Notion Skills for Claude](https://www.notion.so/notiondevs/Notion-Skills-for-Claude-28da4445d27180c7af1df7d8615723d0)

### 建立自訂 Skill

```yaml
---
name: my-skill-name
description: A clear description of what this skill does and when to use it
---

# My Skill Name
[Instructions here]
```

僅需 `name`（≤64 字元，小寫/數字/連字號）和 `description`（≤1024 字元）。

## 相關頁面

- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — 核心實體頁
- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills 概覽]]
- [[wiki/entities/agentskills-io-standard|agentskills.io 標準]]
