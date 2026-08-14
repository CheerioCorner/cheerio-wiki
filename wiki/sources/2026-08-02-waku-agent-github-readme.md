---
title: "Waku agent: GitHub README"
type: source
created: 2026-08-02
updated: 2026-08-02
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-02-shenseanchenwaku.md
    url: https://github.com/ShenSeanChen/waku-agent
tags: [github, ai-agent, local-first, memory-system]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-02-waku-agent-github-readme
provenance_raw: "raw/web/2026-08-02-shenseanchenwaku.md"
provenance_url: "https://github.com/ShenSeanChen/waku-agent"
---

> 來源：[GitHub — ShenSeanChen/waku-agent](https://github.com/ShenSeanChen/waku-agent)
> Author：[Sean Chen (@ShenSeanChen)](https://github.com/ShenSeanChen)
> Clipped：2026-08-02

## 一句話

Waku agent 是一個 local-first 個人 AI 助理，以 readable code 展示 harness + loop + memory + eval 四大 agent 支柱。

## 重點摘要

### 架構（四大支柱）

**Harness** — 完整 agent 系統架構：
```
Gateway → Working Memory → LLM ↔ Tools → Reply → Ops
                ↑                  ↓
            Retrieval Gate    Consolidate → Memory
```

**Loop** — ~95 行 plain Python：
```python
while not done:
    response = llm(messages, tools)    # reason
    if response wants tools:
        results = run(tool_calls)      # act
        messages += results            # observe
    else:
        done                           # reply
```

**Memory** — 三層記憶系統（SQLite + FTS5）：
| Pillar | 類型 | 儲存位置 |
|--------|------|----------|
| Semantic | durable facts | `state.db` facts table + `MEMORY.md` |
| Episodic | dated events | `state.db` episodes table |
| Procedural | skills | `.waku/skills/` + `SOUL.md` |

**Eval / LLM-Ops**：
- Deterministic tests（`evals/deterministic/`）— 0/1，不需 judge
- LLM-as-judge（`evals/judge/`）— scored %，需 API key
- Release gate（`make gate`）— deterministic 100% + judge threshold

### 功能清單
| 功能 | 說明 |
|------|------|
| Terminal chat | `waku` |
| Dashboard | `waku dashboard`（localhost:7777） |
| Voice | `waku voice`，wake word "waku waku"，Whisper + Kokoro TTS |
| Telegram | `waku telegram`，long-polling |
| Brief | `waku brief`，Apple Calendar + Mail 整合 |
| MCP | `.waku/mcp.json`，任何 MCP server |
| Skills | `python -m waku skill install <url>` |
| Sub-Agents | `delegate_task` → Pi coding agent（experimental） |
| Google Calendar | opt-in，`WAKU_GOOGLE_CALENDAR=1` |

### 支援的 LLM Providers
Anthropic（default）、OpenAI、Gemini、DeepSeek、MiniMax、Kimi、GLM、OpenRouter、OpenCode Zen、OpenCode Go

### 升級路徑
| Default | Upgrade |
|---------|---------|
| SQLite FTS5 | Supabase pgvector |
| Mock calendar | Apple / Google Calendar |
| Hand-built memory | mem0 / Letta / Zep |

### 與其他 Agent 的差異
- **vs ChatGPT / Claude Desktop**：這些是產品，Waku 是你可以讀懂的 codebase
- **vs OpenClaw / Hermes**：相同架構，1/100th 代碼量

## 來源
- [[raw/web/2026-08-02-shenseanchenwaku|Raw GitHub README]]

## 相關頁面
- [[wiki/entities/waku-agent|waku-agent]] — 專案 entity page
- [[wiki/sources/2026-08-02-waku-agent-code-walkthrough|Waku agent code walkthrough]] — 配套 YouTube 影片
- [[wiki/entities/hermes-agent|hermes-agent]] — 同類 local-first AI agent
- [[wiki/entities/pi-agent-core|pi-agent-core]] — waku 的 sub-agent 實驗
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 簡約 agent 設計哲學
