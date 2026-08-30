---
title: "Multi-Agent Orchestration 設計模式深度研究"
type: source
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [multi-agent, orchestration, design-patterns, deep-research]
canonical: sources/2026-08-25-multi-agent-orchestration-patterns
topics: [agent-runtime-implementations, coding-agent]
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260825-003/research-report.md
---

> 來源：Gemini Notebook 深度研究（rc-20260825-003），65 筆來源，涵蓋常駐背景、語義狀態偵測、原子化 Prompt 三大模式在 Herdr / Pi / Claude Code / Copilot SDK / LangGraph 中的實現比較，及對 Cheerio 助理的應用建議。

## 研究摘要

### 1. 常駐背景（Persistent Background）

解決 LLM 無狀態、長工期任務中斷的問題。Herdr 實現程度最高（Rust 背景伺服器託管 PTY），Pi 透過 pi-herdr-agents 整合，Claude Code 支援 `context: fork` + `background: true`。

### 2. 語義狀態偵測（Semantic State Detection）

四種機制：TSR（任務狀態表示法）、PTY 螢幕匹配、Lifecycle Hook 自我宣告、圖形狀態機 + Checkpoint。Herdr 採用雙軌制（Hook 精準 + TOML 匹配 fallback），Pi 透過 Hook 主動申報，LangGraph 用 interrupt 中斷點。

### 3. 原子化 Prompt（Atomic Prompt）

四種防競態機制：任務認領鎖、分散式檔案鎖、Git Worktrees、原子化 API 呼叫。Herdr 的 `agent prompt --wait` 是原子呼叫，Claude Code 用檔案鎖保護平行寫入，LangGraph 用 Pending Writes 保留成功結果。

### 4. Cheerio 助理應用

五個微型 Agent 角色（Orchestrator / Fetcher / Distiller / Extractor / Self-Healing），自癒混合模式（Syntactic Fast Path + Inference Core），TSR 狀態追蹤 + Checkpoint 斷點續爬。

## 關鍵引用

- Herdr 官方文件：[herdr.dev/docs](https://herdr.dev/docs/)
- LangGraph Persistence：[docs.langchain.com](https://docs.langchain.com/oss/python/langgraph/persistence)
- Claude Code Agent Teams：[code.claude.com/docs/en/agent-teams](https://code.claude.com/docs/en/agent-teams)
- Pi herdr agents：[pi.dev/packages/pi-herdr-agents](https://pi.dev/packages/pi-herdr-agents)

## 相關頁面

- [[wiki/entities/multi-agent-orchestration-patterns|multi-agent-orchestration-patterns]] — entity 頁面
- [[wiki/entities/herdr|herdr]] — 實現常駐背景與原子化 Prompt 的代表工具
- [[wiki/entities/langgraph|LangGraph]] — 圖形狀態機 + Checkpoint
