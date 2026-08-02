---
title: "Tau: GitHub README"
type: source
created: 2026-08-05
updated: 2026-08-05
sources: 1
tags: [pi, tau, python, coding-harness, textual, huggingface]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-05-tau-github-readme
provenance:
  - kind: external
    url: https://github.com/huggingface/tau
---

> 來源：[huggingface/tau GitHub README](https://github.com/huggingface/tau)
> Clipped：2026-08-02

## 一句話

Tau 是由 Hugging Face 開發的 Pi Python port，三層架構分離可攜大腦與前端，可作為 library 使用。

## 重點摘要

### 組織與授權
- 開發者：**Hugging Face**（非 Pi 團隊內部）
- License：MIT
- GitHub：`huggingface/tau`
- PyPI：`tau-ai`
- Documentation：[twotimespi.dev](https://twotimespi.dev/)

### 三層架構

```
tau_coding  →  tau_agent  →  tau_ai
```

- **`tau_ai`**：provider 翻譯層，將各家 API 轉為 provider-neutral stream
- **`tau_agent`**：可攜大腦，包含 messages、tools、events、loop、harness、session primitives
- **`tau_coding`**：coding app wrapper，包含 CLI、TUI、file/shell tools、provider config、skills、sessions

重要邊界：`AgentHarness`（可攜大腦）vs `CodingSession`（coding-agent 環境）vs `TUI`（一種前端）。核心不知道 Textual、Rich、本地路徑、slash commands 或渲染；前端消費 events。

### 內建工具

`read`、`write`、`edit`、`bash` — 與 Pi 相同

### 安裝方式

| 方法 | 命令 |
|---|---|
| uv tool | `uv tool install tau-ai` |
| pipx | `pipx install tau-ai` |
| conda-forge | `pixi global install tau-ai` |
| 一行 script (macOS/Linux) | `curl -LsSf https://twotimespi.dev/install.sh \| sh` |
| 一行 script (Windows) | `irm https://twotimespi.dev/install.ps1 \| iex` |

### 設計哲學

1. **Small layers beat magic** — 每個 package 只做一件事，可獨立閱讀
2. **Events are the contract** — 所有組件透過 typed event stream 溝通
3. **The core stays portable** — 可攜 harness 不依賴前端或工具
4. **Tools are ordinary typed functions** — tool = schema + async executor
5. **Sessions are durable and inspectable** — append-only JSONL
6. **Documentation follows implementation** — public docs + dev-notes build journal

### Session 儲存

```
~/.tau/sessions/<working-directory>/
├── <session-id>.jsonl    # 每行一個 JSON 物件
└── ...
```

支援 resume 與 branching。

### Library 用法

可直接 import 使用：

```python
from tau_agent import AgentHarness, AgentHarnessConfig

harness = AgentHarness(AgentHarnessConfig(
    provider=provider, model="my-model",
    system="You are a helpful coding agent.", tools=tools
))
async for event in harness.prompt("Explain this package"):
    print(event)
```

### Provider 支援

- Subscription：Codex、Anthropic、GitHub Copilot
- API key：OpenAI、Anthropic、OpenRouter、Hugging Face、Custom OpenAI-compatible
- Catalog：`src/tau_coding/data/catalog.toml`，可自訂 `~/.tau/catalog.toml`

### Project Instructions

Tau 讀取 `AGENTS.md`、`.tau/`、`.agents/` 資源——與 Pi 的 workspace model 完全相容。

## 與既有來源的差異

本 README 補充了 [[wiki/sources/2026-08-03-tau-python-port-of-pi|YouTube 影片]] 未涵蓋的內容：
- **Hugging Face 開發**（非「Pi 團隊內部」）
- **三層架構圖**：tau_ai / tau_agent / tau_coding 的明確分工
- **Library 用法**：可作為 Python package 嵌入其他專案
- **6 條設計哲學**
- **完整安裝選項**：uv / pipx / conda-forge / pixi
- **MIT License**

## 來源

- [[raw/web/2026-08-02-huggingfacetau A Python port of Pi’s minimalist coding agent|Raw — GitHub README]]

## 相關頁面

- [[wiki/entities/tau|tau]] — entity 頁面
- [[wiki/entities/pi-mono|pi-mono]] — Tau 的上游原型
- [[wiki/sources/2026-08-03-tau-python-port-of-pi|2026-08-03 Tau: A Python Port of Pi]] — YouTube 影片 source note
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學
