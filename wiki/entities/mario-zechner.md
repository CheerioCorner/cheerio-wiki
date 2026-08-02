---
title: Mario Zechner
type: entity
created: 2026-07-11
updated: 2026-07-11
sources: 2
tags: [pi, author, zechner]
collection: entities
topics: [ai-agent]
canonical: entities/mario-zechner
---

> 維護 `badlogic/pi-mono` 整套 monorepo 的作者。他的立場:**「自主 agent 就是 LLM + tools + 一個迴圈」**。

## 來源
- GitHub:`badlogic/pi-mono`
- 原文「What I learned building an opinionated and minimal coding agent」(mariozechner.at,2025-11-30)—— A 篇多次引用,**目前未直接 ingest**,可以視為第三層上游來源。
- Wiki 內:[[wiki/sources/2026-02-10-pi-agent-core-design]]、[[wiki/sources/2026-05-02-pi-mono-framework-tw]]

## 核心論點(彙整,目前所有來源都指向同一立場)
1. **前沿模型已被 RL 訓練得足夠理解「coding agent 是什麼」**——不需要 10000-token 系統提示詞。
   - Pi 的系統提示 + 工具定義加起來 **< 1000 token**、只有 **4 個內建工具**(read / write / edit / bash)。
2. **「不內建」是立場,不是缺點**:
   - 不做 MCP——占上下文 7-9%
   - 不做 sub-agents——失去可觀測性
   - 不做 plan mode——用文件代替
   - 不做權限檢查——安全劇場
   - 不做 maxSteps——迴圈自然結束
3. **極簡的實證**:Pi Agent Core 整套 runtime **5 個檔、約 1,500 行**;Terminal-Bench 2.0 與 Codex / Cursor / Windsurf 同列排行榜。

## 引用過的句子(來自 A 篇轉引)
- *"An autonomous agent is just an LLM + tools + a loop."*
- *"前沿模型已經被 RL 訓練得足夠理解「編碼 Agent」是什麼。你不需要 10,000 token 的系統提示詞。"*
- *"Claude Code 的 Plan Mode 會 spawn 一個子 Agent,你對子 Agent 內部的運作零可見性。這是黑盒中的黑盒。"*
- *"安全措施大多是安全劇場。一旦 Agent 能寫代碼和運行代碼,就 game over。"*
- *"我從來沒找到需要 maxSteps 的用例,所以為什麼要加?"*

## 待研究(`NOTED`)
> 他原 blog「What I learned building an opinionated and minimal coding agent」(2025-11-30)在本知識庫尚未直接 ingest,且是多個第二手來源(A)的主源。短期內視為上游來源,**可在下次 ingest 時建立獨立 source 頁**。

## 相關頁面
- Entities:[[wiki/entities/pi-mono]]、[[wiki/entities/pi-agent-core]]
- Synthesis:[[wiki/concepts/minimal-agent-philosophy]]
- Sources:[[wiki/sources/2026-02-10-pi-agent-core-design]] / [[wiki/sources/2026-05-02-pi-mono-framework-tw]]
