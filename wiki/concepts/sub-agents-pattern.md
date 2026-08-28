---
title: "Sub-agents — 子 agent 委派模式"
type: concept
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [sub-agent, multi-agent, context-window, delegation, parallel-execution]
topics: [agent-architecture, agent-infrastructure]
canonical: concepts/sub-agents-pattern
provenance:
  - kind: raw
    path: raw/youtube/ibm-agentic-ai-five-terms.md
    url: https://www.youtube.com/watch?v=k5jYwyhDMxA
---

# Sub-agents — 子 agent 委派模式

> 主 agent 將複雜任務拆解，分派給多個子 agent 在獨立 context window 中執行，完成後回傳結果。

## 核心機制

```
主 Agent (Parent)
    ├── spawn → Sub-agent A (fresh context) → 完成 → 回傳結果
    ├── spawn → Sub-agent B (fresh context) → 完成 → 回傳結果
    └── spawn → Sub-agent C (fresh context) → 完成 → 回傳結果
         │
         └── 主 Agent 整合結果，context 保持乾淨
```

Sub-agent 的特性 `[08:58]`：
- **Fresh context window**：每個 sub-agent 在全新的 context 中運作 `[09:03]`
- **隔離性**：sub-agent 的工作不會污染主 agent 的 context `[09:15]`
- **可並行**：多個 sub-agent 可同時執行（例如 20 個）`[09:37]`
- **結果回傳**：完成後將摘要回傳給主 agent `[09:25]`

## 使用場景

| 場景 | 說明 | 為什麼需要 sub-agents |
|------|------|----------------------|
| 大型 codebase review | 幾千個檔案，一次載入會爆 context | 每個 sub-agent 讀一批檔案，回傳摘要 `[08:21]` |
| 並行獨立檢查 | 20 個函數各自獨立检查 | 20 個 sub-agent 同時跑，比依序快 20 倍 `[08:33]` |
| 跨領域任務 | 需要不同專業的複雜任務 | 每個 sub-agent 專注一個領域 |

## 與其他機制的區別

| 機制 | 作用範圍 | 標準化程度 | 例子 |
|------|---------|-----------|------|
| **Sub-agents** | Agent 內部，spawn 子 agent | ⚠️ 無正式標準文件 `[09:47]` | Pi 的 pi-subagents、Claude Code 的 subagent |
| **A2A** | Agent 間通訊 | 開放標準（Google 發起） | 採購 agent → 財務 agent |
| **MCP** | Agent ↔ 工具 | 開放標準（Anthropic 發起） | Agent → Notion Server |
| **Agent Skills** | Agent 能力封裝 | 開放標準 | Skill folder + skill.md |

> Sub-agents 是「模式」（pattern），不是「協定」（protocol）。它在各處幾乎相同，但沒有像 MCP 或 A2A 那樣的正式規範文件。

## 設計哲學的張力

⚠️ **Minimal Agent Philosophy 的反對立場**：

`[[wiki/concepts/agentic-ai|Agentic AI]]` 頁面引用 Mario Zechner 的 Minimal Agent Philosophy，明確把 sub-agents 列為「不需要的東西」——主張用 `bash` 自我呼叫替代。

| 立場 | 說明 | 適用場景 |
|------|------|---------|
| **IBM（本頁）** | Sub-agents 是現代 agent 系統的主流 pattern | 大型任務、需要 context 隔離 |
| **Minimal Philosophy** | 不需要 sub-agents，用 bash 自我呼叫 | 個人化極簡工具、追求可讀性 |

兩者不矛盾：前者是產業觀察（大型系統怎麼做），後者是設計哲學（個人工具怎麼做）。

## 相關頁面

- [[wiki/concepts/agentic-ai|Agentic AI]] — AI Agent 核心定義（含 Minimal Philosophy 對照）
- [[wiki/entities/a2a-protocol|A2A]] — Agent 間通訊（不同機制）
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]] — Skill → Hook → Extension
- [[wiki/sources/2026-09-04-ibm-agentic-ai-five-terms|IBM: 5 Terms for Agentic AI]] — 來源
- [[wiki/topics/agent-architecture|Agent Architecture]] — 架構導航
