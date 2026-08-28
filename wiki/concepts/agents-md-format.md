---
title: "AGENTS.md — Agent 專案配置規範"
type: concept
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [agents-md, project-config, openai, agentic-ai-foundation, claude-md]
topics: [agent-infrastructure, ai-development-tools]
canonical: concepts/agents-md-format
provenance:
  - kind: raw
    path: raw/youtube/ibm-agentic-ai-five-terms.md
    url: https://www.youtube.com/watch?v=k5jYwyhDMxA
---

# AGENTS.md — Agent 專案配置規範

> 放在專案根目錄的 markdown 文字檔，每次 agent 開始工作時自動讀取，用來定義專案的行為規則。類似 README，但寫給 agent 看。

## 核心概念

```
專案根目錄/
├── AGENTS.md          ← agent 每次啟動都讀取
├── src/
│   ├── AGENTS.md      ← 子專案覆蓋規則（就近覆蓋）
│   └── ...
└── README.md          ← 給人看的（不同用途）
```

## 關鍵特性

### 1. 自動讀取 `[01:03]`

Agent 每次開始在某個專案工作時，會讀取該專案的 AGENTS.md。內容指導 agent 的行為，例如：
- 測試指令（`npm test`）`[01:45]`
- 程式碼風格規範 `[01:15]`
- PR 標題格式 `[01:29]`

### 2. 巢狀與就近覆蓋 `[01:54]`

可以有多個 AGENTS.md——根目錄一個，子專案各一個。**越靠近工作目錄的檔案優先級越高** `[02:06]`。

### 3. 同類工具的不同命名

| 工具 | 檔案名稱 | 備註 |
|------|---------|------|
| OpenAI agents | `AGENTS.md` | 原始規範 `[02:12]` |
| Claude | `CLAUDE.md` | 同概念不同命名 `[02:33]` |
| Pi Agent | `AGENTS.md` | 採用原始規範 |
| 其他 | `COPILOT.md` 等 | 各平台可能有自己的變體 |

## 起源與治理

⚠️ 影片聲稱 AGENTS.md 由 OpenAI 引入，後捐給 Linux Foundation 底下的 Agentic AI Foundation `[02:12]`。此為單一影片來源說法，「Agentic AI Foundation」待查證。

## 與 Agent Skills 的差異

| 面向 | AGENTS.md | Agent Skills |
|------|-----------|-------------|
| 載入時機 | 每次啟動都讀 | 按需匹配才載入 `[03:50]` |
| 內容性質 | 專案規則（怎麼運作） | 任務能力（怎麼做事） |
| Context 影響 | 始終佔用 context | 不匹配就不佔 `[04:05]` |
| 檔案結構 | 單一檔案 | 資料夾 + skill.md + 腳本 |

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills]] — 按需載入的任務能力（對比）
- [[wiki/entities/mcp-model-context-protocol|MCP]] — Agent ↔ 工具連接
- [[wiki/entities/a2a-protocol|A2A]] — Agent ↔ Agent 通訊
- [[wiki/concepts/sub-agents-pattern|Sub-agents]] — 子 agent 委派
- [[wiki/concepts/agentic-ai|Agentic AI]] — AI Agent 核心概念
- [[wiki/sources/2026-09-04-ibm-agentic-ai-five-terms|IBM: 5 Terms for Agentic AI]] — 來源
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — 協定導航
