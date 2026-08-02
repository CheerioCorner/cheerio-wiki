---
title: tau — Pi 的 Python 移植版
type: entity
created: 2026-08-03
updated: 2026-08-05
sources: 2
tags: [pi, tau, python, coding-harness, textual]
collection: entities
topics: [ai-agent]
canonical: entities/tau
---

> Tau 是 Pi 的 **Python port**，由 [Hugging Face](https://huggingface.co) 開發並開源（MIT license）。架構與 Pi 完全相同，差異只在 TUI 層使用 Textual 框架。

## 基本資訊

| 項目 | 內容 |
|---|---|
| 語言 | Python |
| 原型 | [[wiki/entities/pi-mono\|pi-mono]]（TypeScript） |
| TUI 框架 | [Textual](https://github.com/Textualize/textual) |
| 平台 | Mac / Linux / Windows |
| 安裝 | `uv tool install tau-ai` / `pipx install tau-ai` / conda-forge / 一行 script |

## 三層架構

```
tau_coding  →  tau_agent  →  tau_ai
```

- **`tau_ai`**：將 model provider 轉譯為 Tau 的 provider-neutral stream
- **`tau_agent`**：可攜大腦：messages, tools, events, loop, harness, session primitives
- **`tau_coding`**：包裝成真實 coding app：CLI, TUI, file/shell tools, provider config, project instructions, skills, sessions

重要邊界：`AgentHarness`（可攜大腦）vs `CodingSession`（coding-agent 環境）vs `TUI`（一種前端）。核心不知道 Textual、Rich、本地路徑、slash commands 或渲染。前端消費 events。

## 核心設計

Tau 與 Pi 共享：
- **Session 管理**：tree-based（非 list），JSONL 儲存，支援 fork
- **Skills 系統**：procedural markdown instructions，agent 可自動呼叫
- **Custom Prompts**：slash commands，前端 level 文字替換
- **Extensions**：使用相同 events，完全相容（需 port 語言）
- **System prompt**：與 Pi 相同
- **Provider 支援**：Subscription / API key / Custom / Hugging Face

## Tau 獨有功能

| 功能 | 說明 |
|---|---|
| `/skills` | 列出所有已載入 skills（Pi 在 session header 顯示） |
| `/prompts` | 列出自訂 prompts |
| `/tools` | 列出所有工具及其來源（built-in vs extension） |
| 通知 | 背景工作完成時跳出通知（Pi 需 extension 補） |
| Themes | JSON 格式自訂 TUI 主題（tau-light, Catppuccin Mocha 等） |

## Session 儲存

```
.tau/sessions/<working-directory>/
├── <session-id>.jsonl    # 每行一個 JSON 物件
└── ...
```

JSONL 結構：`{ id, parent_id, timestamp, type, contents }`

## 與 Pi 的差異

| 面向 | Pi | Tau |
|---|---|---|
| 語言 | TypeScript | Python |
| TUI | 從零打造 | Textual framework |
| Session header | 顯示 skills | 不顯示（用 `/skills` 命令） |
| 通知 | 需 extension | 內建 |
| Session auto-rename | ✗ | ✓（首條訊息後自動命名） |

## 設計哲學

1. **Small layers beat magic** — 每個 package 只做一件事，可獨立閱讀
2. **Events are the contract** — Providers、renderers、TUI、custom frontends 透過 typed event stream 溝通
3. **The core stays portable** — 可攜 harness 不依賴 CLI、Textual、Rich 或 Tau 的檔案結構
4. **Tools are ordinary typed functions** — tool = schema + async executor → structured result
5. **Sessions are durable and inspectable** — append-only JSONL；active context 可 compact 而不改寫記錄
6. **Documentation follows implementation** — public docs 解釋結果；`dev-notes/` 保留逐階段 build journal

## Library 用法

Tau 可作為 Python library 使用：

```python
from tau_agent import AgentHarness, AgentHarnessConfig

harness = AgentHarness(AgentHarnessConfig(provider=provider, model="my-model", system="...", tools=tools))
async for event in harness.prompt("Explain this package"):
    print(event)
```

## 來源

- [[wiki/sources/2026-08-03-tau-python-port-of-pi|2026-08-03 Tau: A Python Port of Pi]] — YouTube 影片
- [[wiki/sources/2026-08-05-tau-github-readme|2026-08-05 Tau: GitHub README]] — GitHub repo README

## 相關頁面

- [[wiki/entities/pi-mono|pi-mono]] — Tau 的上游原型
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi runtime 核心
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/meta-harness|meta-harness]] — harness 再抽象研究
