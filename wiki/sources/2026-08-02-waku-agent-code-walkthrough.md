---
title: "Waku Waku! Waku agent — code walkthrough"
type: source
created: 2026-08-02
updated: 2026-08-02
sources: 1
tags: [youtube, ai-agent, local-first, memory-system]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-02-waku-agent-code-walkthrough
provenance_raw: "raw/youtube/2026-08-02-waku-agent-code-walkthrough.md"
provenance_url: "https://youtu.be/rvRyBhILrls"
---

> 來源：[YouTube — Waku Waku! Waku agent is your personal AI agent](https://youtu.be/rvRyBhILrls)
> Channel：[Sean's AI Stories](https://www.youtube.com/@SeanAIStories)
> 原始字幕：[[raw/youtube/2026-08-02-waku-agent-code-walkthrough|raw transcript]]
> Duration：20:49 / 601 segments / English

## 一句話

Sean Chen 完整走讀 waku-agent 程式碼，展示 harness、loop、memory、eval 四大支柱的實作。

## 重點摘要

### 四大支柱
影片以 waku-agent 為實例，完整展示 AI agent 系統的四大支柱：
1. **Harness** — 系統架構：Gateway → Memory → Loop → Tools → Reply → Ops
2. **Loop** — 約 95 行 plain Python 的 agentic loop（reason → act → observe）
3. **Memory** — 三層記憶：Semantic（durable facts）、Episodic（dated events）、Procedural（skills）
4. **Eval / LLM-Ops** — Deterministic tests + LLM-as-judge，tracing，release gate

### Demo 亮點
- **World Cup 多工具迴圈**：search_web × N → create_event × N，8 次迭代，一次完成
- **Retrieval gate**：cheap model 先判斷「這回合需不需要記憶？」，skip vs retrieve
- **Semantic memory**：存取朋友資訊（Sergey、Raj、Vincent）、社交帳號
- **Procedural memory（Skills）**：schedule-meeting skill，可即時修改並立即生效
- **Voice mode**：wake word "waku waku"，Whisper 扫描 → 大模型接管 → 語音回覆
- **Telegram gateway**：手機發訊息，筆電跑 turn，long-polling

### 程式碼走讀
- `waku/loop/agent.py` — ~95 行主迴圈，max_iterations=10
- `waku/memory/` — semantic/episodic/procedural 三層，consolidation 定期 digest
- `waku/memory/retrieval_gate.py` — 決定是否 retrieve
- `evals/deterministic/` vs `evals/judge/` — 兩套 eval 分離
- `waku/ops/tracing.py` — 每 turn append `.waku/traces/<date>.jsonl`
- `.waku/state.db` — SQLite + FTS5，一個檔案存所有記憶
- `SOUL.md` — 本地 system prompt，可即時修改

### 關鍵設計
- **Local-first**：所有資料在 `.waku/state.db`，無雲端依賴
- **BYOK**：Anthropic / OpenAI / Gemini / DeepSeek 等，一個 adapter handle 所有 provider
- **Gateway 多通道**：CLI、Telegram、Voice、Dashboard 共用同一個 agent brain
- **Memory is the hero**：semantic + episodic + procedural，consolidation 自動 digest

## 來源
- [[raw/youtube/2026-08-02-waku-agent-code-walkthrough|Raw transcript — YouTube rvRyBhILrls]]

## 相關頁面
- [[wiki/entities/waku-agent|waku-agent]] — 專案 entity page
- [[wiki/sources/2026-08-02-waku-agent-github-readme|Waku agent GitHub README]] — 配套 GitHub 文件
- [[wiki/entities/hermes-agent|hermes-agent]] — 同類 local-first AI agent
- [[wiki/entities/pi-agent-core|pi-agent-core]] — waku 使用的 sub-agent
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 簡約 agent 設計哲學
