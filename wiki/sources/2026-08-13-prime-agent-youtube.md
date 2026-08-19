---
title: "Prime Agent — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent"
type: source
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [agent, python, runtime, tool-calling, subagent]
topics: [agent-runtime-implementations]
provenance_raw: "raw/youtube/i-replaced-pi-and-opencode-with-this.md"
provenance_url: "https://www.youtube.com/watch?v=eQZQl_7-X40"
---

> Martí Blanes 介紹 Prime Agent：建構於 Pi 之上，用 Python runtime 取代傳統 tool calling 的開源 coding agent。
>
> ⚠️ **修正（2026-08-13）**：影片內容誤植開發者為「Prime Intelligence」，正確為「**Prime Intellect**」（見官方部落格 [[wiki/sources/2026-08-13-prime-agent-official-blog|Prime Agent 官方部落格全文]]，內容也更完整，含 ARC-AGI-3 評測與 Continual Harness 風險說明）。

## 影片資訊

| 屬性 | 值 |
|------|-----|
| 標題 | I Replaced Pi and OpenCode With This |
| 作者 | Martí Blanes |
| 時長 | 08:29 |
| URL | https://www.youtube.com/watch?v=eQZQl_7-X40 |
| 字幕 | auto-generated (en) |

## 核心重點

### 1. Prime Agent 是什麼
- 完全開源，建構在 [[wiki/entities/pi-agent-core|pi-agent-core]] 之上 `[00:15]`
- 開發者：Prime Intellect（影片原稱「Prime Intelligence」，已修正）`[04:06]`
- 作者原本使用 Pi 和 OpenCode，現在改用 Prime Agent `[00:00]`

### 2. 架構差異：Python Runtime vs Tool Calling
- **傳統模式**：模型拿到工具列表 → 逐次 tool call → 每次結果回傳 context `[00:54]`
- **Prime 模式**：給模型一個 Python runtime → 模型寫 Python 腳本 → 一次執行多個操作 `[01:08]`
- **優勢**：一個 Python 腳本可等同 50 次 tool call，大幅提速並節省 context window `[01:17]`
- **額外好處**：Python 環境是 persistent 的，變數可跨對話保存 `[02:54]`

### 3. RLM（Recursive Language Model）Function
- 用於在 Python 程式邏輯中觸發 [[wiki/concepts/recursive-language-model|subagent]] `[04:06]`
- 是**獨立的 agent session**，不是傳統 tool-call 式 subagent `[04:39]`
- 更類似 [[wiki/entities/openai-codex|Codex]] 的 orchestration 功能 `[04:46]`
- 可在 for 迴圈、條件判斷中動態觸發 `[05:21]`

### 4. Continual Harness
- Agent 可自動改進自身的 harness（類似 [[wiki/entities/hermes-agent|Hermes]] 自動學習，但更進一步）`[06:54]`
- 不只修改行為/系統提示/生成 skill，還修改 harness 本身 `[06:54]`
- **⚠️ 風險**：可能讓 agent 學到錯誤行為，需定期審查學習內容 `[07:21]`

## 來源
- [[wiki/raw/youtube/i-replaced-pi-and-opencode-with-this|Raw Transcript]]

## 相關頁面
- [[wiki/entities/prime-agent|Prime Agent]]
- [[wiki/entities/pi-agent-core|pi-agent-core]]
- [[wiki/entities/hermes-agent|hermes-agent]]
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]]
- [[wiki/concepts/recursive-language-model|Recursive Language Model]]
