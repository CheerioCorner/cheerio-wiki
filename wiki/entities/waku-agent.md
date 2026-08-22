---
title: "waku-agent — Local-first personal AI agent with harness + loop + memory + eval"
type: entity
created: 2026-08-02
updated: 2026-08-17
sources: 4
tags: [ai-agent, local-first, memory-system, python, open-source]
collection: entities
topics: [agent-runtime-implementations]
canonical: entities/waku-agent
---

> Waku agent 是一個 local-first 個人 AI 助理，以 readable code 展示 agent 系統的四大支柱：harness、loop、memory、eval/LLM-Ops。

## 基本資訊

| 欄位 | 值 |
|------|-----|
| 名稱 | waku-agent |
| 作者 | Sean Chen ([@ShenSeanChen](https://github.com/ShenSeanChen)) |
| Repo | [ShenSeanChen/waku-agent](https://github.com/ShenSeanChen/waku-agent) |
| License | MIT |
| 語言 | Python |
| Channel | [Sean's AI Stories](https://www.youtube.com/@SeanAIStories) |
| 定位 | 教學用 readable blueprint（非 production 產品） |

## 架構

```
Gateway (CLI / Telegram / Voice / Dashboard)
  → Working Memory (SOUL.md + memory + history)
    → LLM ↔ Tools (reason → act → observe loop)
      → Reply
    ← Retrieval Gate (skip / retrieve)
    ← Memory (SQLite + FTS5: semantic · episodic · procedural)
  → Ops (trace → eval → gate → release)
```

### 四大支柱

**1. Harness — 系統架構**
- 完整 agent 系統：Gateway → Memory → Loop → Tools → Reply → Ops
- 每個 box 對應一個 module（`waku/gateway/`、`waku/loop/`、`waku/memory/`、`waku/tools/`、`waku/ops/`）

**2. Loop — Agentic 迴圈**
- ~95 行 plain Python（`waku/loop/agent.py`）
- 無框架依賴（no LangGraph）
- 兩個 exit conditions：model 自然停止 / max_iterations 硬限制（default 10）
- 多工具迴圈：search_web × N → create_event × N（World Cup demo：8 iterations）

**3. Memory — 三層記憶系統**
| 類型 | 內容 | 儲存 |
|------|------|------|
| Semantic | durable facts（朋友、帳號、偏好） | `state.db` facts + `MEMORY.md` |
| Episodic | dated events（行程、對話紀錄） | `state.db` episodes |
| Procedural | skills（可執行的工作流程） | `.waku/skills/` |

- **Retrieval gate**：cheap model 先判斷「需不需要 retrieve？」，避免每次全量查詢
- **Consolidation**：每 N 次對話後自動 digest，將對話摘要為 semantic facts
- 儲存：SQLite + FTS5（一個 `.waku/state.db`），同步產生 human-readable `MEMORY.md`

**4. Eval / LLM-Ops**
- Deterministic tests（`evals/deterministic/`）— pytest，0/1，不需 judge
- LLM-as-judge（`evals/judge/`）— DeepEval，scored %，需 API key
- Release gate（`make gate`）— deterministic 必須 100% + judge 必須過 threshold
- Tracing：每個 turn append `.waku/traces/<date>.jsonl`（zero setup）
- Usage tracking：`.waku/usage.jsonl`，append-only ledger

## 功能

| 功能 | 命令 | 說明 |
|------|------|------|
| Terminal chat | `waku` | 終端聊天 |
| Dashboard | `waku dashboard` | localhost:7777，7 個 tab（Overview/Gateway/Loop/Memory/Tools/Data/Ops） |
| Voice | `waku voice` | wake word "waku waku"，Whisper 扫描 + Kokoro TTS |
| Telegram | `waku telegram` | long-polling，無需 public URL |
| Brief | `waku brief` | Apple Calendar + Mail 整合晨間簡報 |
| MCP | `.waku/mcp.json` | 任何 MCP server 工具自動出現 |
| Skills | `python -m waku skill install <url>` | Procedural memory，可社群分享 |
| Sub-Agents | `delegate_task` | 委派 coding 任務給 Pi agent（experimental） |
| Google Calendar | `WAKU_GOOGLE_CALENDAR=1` | opt-in，Application Default Credentials |

## Graph Engineering（工作流圖）

Waku Agent 不只有 loop，還支援 graph 工作流：

- **Triage graph**：分類是否需要複雜 agent call + 平行查行事曆
- **Gather graph**：同時查 GitHub PR + 網路搜尋 + 行事曆 + 記憶，綜合回覆
- **定義位置**：`waku/graph/` 目錄
  - `graph.py`：定義 graph engine（nodes + edges）
  - `workflows/`：具體 workflow（triage.py, gather.py）
- **節點類型**：tool call、LLM call、agent call、router
- **核心觀點**：Graph 內含 Loop（如 web search 節點内部是 agent loop）

> 詳見 [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] 概念頁。

> 記憶存儲、檢索方法與維護策略的完整比較，詳見 [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|5 種記憶架構設計方式]]。

## 與 Pi 的關係

- waku-agent 使用 [Pi](https://github.com/earendil-works/pi) 作為 sub-agent（`delegate_task` tool）
- 透過 `pi -p "task"` headless mode 執行 coding 任務
- Waku 保持 orchestrator 角色（memory、context、evals），Pi 作為 specialist contractor
- Pi transcript 存在 `.waku/outbox/delegate-*.log`

## 亮點設計

- **Local-first**：所有資料在本地，無雲端依賴
- **BYOK**：支援 Anthropic / OpenAI / Gemini / DeepSeek 等多 provider
- **Gateway 多通道**：CLI、Telegram、Voice、Dashboard 共用同一個 agent brain
- **Memory is the hero**：三層記憶 + retrieval gate + consolidation
- **Readable code**：整個核心 ~95 行 Python，任何人都能一個下午讀完
- **Soul.md**：本地 system prompt，可即時修改並立即生效

## 來源
- [[wiki/sources/2026-08-02-waku-agent-code-walkthrough|YouTube code walkthrough]]
- [[wiki/sources/2026-08-02-waku-agent-github-readme|GitHub README]]
- [[wiki/sources/2026-08-03-loop-vs-graph-engineering|Loop vs Graph Engineering — YouTube video]]
- [[wiki/sources/2026-08-17-ai-agent-memory-systems-5-ways|AI Agent Memory Systems — 5 種記憶架構設計方式]] — 記憶存儲、檢索、維護的完整比較與實測

## 相關頁面
- [[wiki/entities/hermes-agent|hermes-agent]] — 同類 local-first AI agent（自我改進 + learning loop）
- [[wiki/entities/pi-agent-core|pi-agent-core]] — waku 的 sub-agent 實驗
- [[wiki/entities/pi-mono|pi-mono]] — Pi monorepo
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 簡約 agent 設計哲學
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程
- [[wiki/concepts/loop-vs-graph-engineering|loop-vs-graph-engineering]] — Loop 與 Graph 兩種工作流模式
- [[wiki/concepts/portable-graph-agent-pattern|portable-graph-agent-pattern]] — Graph pattern 下放到輕量專家 Agent 的可攜式設計，可對照 waku graph.py 精簡版
