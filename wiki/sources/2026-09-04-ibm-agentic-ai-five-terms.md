---
title: "5 Terms You Need to Know About Agentic AI — IBM Technology"
type: source
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [agentic-ai, agents-md, agent-skills, mcp, a2a, sub-agents, ibm]
topics: [agent-infrastructure, agent-architecture]
canonical: sources/2026-09-04-ibm-agentic-ai-five-terms
provenance:
  - kind: raw
    path: raw/youtube/ibm-agentic-ai-five-terms.md
    url: https://www.youtube.com/watch?v=k5jYwyhDMxA
---

# 5 Terms You Need to Know About Agentic AI — IBM Technology

> IBM Technology 出品，主講 Martin Keen。10 分鐘內拆解 front-end AI agent 底下的五個核心組件。

## 概要

影片將 agentic AI 拆解為五個你必須知道的術語，分為「agent 內部」與「agent 外部」兩大區塊：

| 區塊 | 術語 | 說明 |
|------|------|------|
| 內部 | [[wiki/concepts/agents-md-format\|AGENTS.md]] | 專案級 agent 配置檔 |
| 內部 | [[wiki/concepts/agent-skills\|Agent Skills]] | 按需載入的任務能力模組 |
| 外部 | [[wiki/entities/mcp-model-context-protocol\|MCP]] | Agent 連接工具/資料的開放協定 |
| 外部 | [[wiki/entities/a2a-protocol\|A2A]] | Agent 間通訊的開放協定 |
| 外部 | [[wiki/concepts/sub-agents-pattern\|Sub-agents]] | 主 agent 分派子任務給子 agent |

## 陳述級溯源

### Term 1: AGENTS.md `[00:46]`

- AGENTS.md 是一個 markdown 文字檔，放在專案根目錄 `[01:03]`
- 每次 agent 開始工作時都會讀取它 `[01:03]`
- 內容包括：測試指令、程式碼風格規範、PR 標題格式 `[01:15]`
- 可以巢狀存在——根目錄一個，子專案各一個 `[01:54]`
- 就近覆蓋原則：越靠近工作目錄的檔案優先級越高 `[02:06]`
- 起源於 OpenAI，後來捐給 Linux Foundation 底下的 Agentic AI Foundation `[02:12]`
- ⚠️ 治理組織「Agentic AI Foundation」為單一影片來源說法，待查證
- Claude 使用 `CLAUDE.md` 而非 `AGENTS.md`，概念相同但命名不同 `[02:24]`

### Term 2: Agent Skills `[03:14]`

- Agent Skill 是一個資料夾，裡面有一個 `skill.md` 檔案 `[03:14]`
- `skill.md` 包含 metadata 和 description，例如「invoke me when the user wants to X」`[03:45]`
- 用需求匹配（description matching）決定是否載入——不匹配就不佔用 context window `[03:50]`
- Agent Skills 也是一個開放標準，多個 agent 平台支援 `[04:12]`
- 對比：AGENTS.md = 專案怎麼運作，Agent Skills = 怎麼做特定任務 `[04:18]`

### Term 3: MCP — Model Context Protocol `[05:05]`

- MCP 是一個開放協定，用於連接 AI 應用程式到工具和資料來源 `[05:14]`
- MCP Server 把工具或資料來源包裝成標準介面 `[05:29]`
- Agent 用 MCP 語言跟 Server 溝通，Server 處理底層 API `[05:39]`
- 影片以 Notion 為例：Agent 講 MCP → Server → Notion API `[05:46]`
- 起源於 Anthropic，現由 Agentic AI Foundation（Linux Foundation）治理 `[06:01]`
- ⚠️ 治理沿革為單一影片來源說法，待查證
- MCP = agent ↔ 工具/資料；A2A = agent ↔ agent `[07:58]`

### Term 4: A2A — Agent to Agent `[06:25]`

- A2A 是一個開放協定，用於 agent 間通訊 `[06:31]`
- 影片以採購 agent（procurement）與財務 agent（finance）為例 `[06:39]`
- 每個 agent 發佈一個「Agent Card」——描述它做什麼、怎麼跟它溝通 `[07:29]`
- 其他 agent 讀取 Agent Card 後，決定如何委派工作 `[07:35]`
- 起源於 Google，現由 Agentic AI Foundation（Linux Foundation）治理 `[07:46]`
- ⚠️ 治理沿革為單一影片來源說法，待查證

### Term 5: Sub-agents `[08:48]`

- Sub-agent 是主 agent 子出來的子 agent，負責特定工作 `[08:58]`
- 每個 sub-agent 在自己的 fresh context window 中運作 `[09:03]`
- 完成後回傳結果給主 agent，主 agent 的 context 保持乾淨 `[09:15]`
- 可以並行執行多個 sub-agent（例如 20 個同時跑）`[09:37]`
- Sub-agents 不像其他四個有正式標準文件 `[09:47]`
- 但概念在各處幾乎相同：parent → spawn child → child 做事 → 回傳結果 `[10:03]`
- ⚠️ 注意：Minimal Agent Philosophy 將 sub-agents 列為「不需要」的東西（用 bash 自我呼叫替代），與 IBM 影片的「業界主流 pattern」描述形成對比

### 總結 `[10:25]`

- AGENTS.md + Agent Skills → agent 內部行為 `[10:28]`
- MCP + A2A → agent 外部連接（工具 + 其他 agent）`[10:36]`
- Sub-agents → 處理超出單一 context window 的工作 `[10:43]`

## 相關頁面

- [[wiki/concepts/agents-md-format|AGENTS.md]] — 專案級配置規範
- [[wiki/concepts/agent-skills|Agent Skills]] — Skill 系統根概念
- [[wiki/entities/mcp-model-context-protocol|MCP]] — Model Context Protocol
- [[wiki/entities/a2a-protocol|A2A]] — Agent2Agent Protocol
- [[wiki/concepts/sub-agents-pattern|Sub-agents Pattern]] — 子 agent 委派模式
- [[wiki/concepts/agentic-ai|Agentic AI]] — AI Agent 核心定義
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]] — 協定導航頁
