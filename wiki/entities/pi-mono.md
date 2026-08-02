---
title: pi-mono — Agent Toolkit Monorepo
type: entity
created: 2026-07-11
updated: 2026-07-11
sources: 2
tags: [pi, coding-agent, monorepo]
collection: entities
topics: [ai-agent]
canonical: entities/pi-mono
---

> `badlogic/pi-mono` 是 Mario Zechner 維護的 TypeScript agent toolkit monorepo。截至 2026-05-02 約 43,777 stars、release v0.72.1,高頻更新中(Node.js ≥ 20.6.0)。
>
> 核心定位:**「你的 agent 平台」**——不是單一既定體驗,而是一組可組合的 runtime、模型抽象層、UI。

## 五個子 package

| Package | 角色 | 備註 |
|---|---|---|
| `@mariozechner/pi-ai` | 模型抽象層 | 處理多 provider 切換與自訂模型;`~/.pi/agent/models.json` 是入口 |
| `@mariozechner/pi-agent-core` | agent runtime | 文章 [[wiki/sources/2026-02-10-pi-agent-core-design]] 重點解剖的對象;5 檔 / 1,500 行 |
| `@mariozechner/pi-coding-agent` | 最上層 CLI | `npm install -g …pi-coding-agent` 即裝,`pi` 即可執行 |
| `@mariozechner/pi-tui` | terminal UI | 互動介面層 |
| `@mariozechner/pi-web-ui` | web UI | 瀏覽器前端對應層 |

> 這五個 package **不一定各開獨立 wiki 頁**——目前只在這張總表中說明。日後哪個子 package 出現第二來源深度討論,才考慮升級為獨立 entity 頁。

## 設計立場(整合 A 與 B 兩文)

### 一、不是單一 CLI,而是 toolkit
你可以拿其中幾層(只保留 `pi-ai` 切換層、或保留 `pi-tui` / `pi-web-ui` 介面能力),接自己的 agent workflow。
→ A 與 B 完全一致。

### 二、主打「adapt pi to your workflows」
五類擴充點:
- **Skills**(工作流指令集)
- **Prompt Templates**(模板)
- **Extensions**(完整外掛)
- **Themes**(TUI 視覺)
- **Pi Packages**(`pi install` / `list` / `update` 的一系列 unit)

### 三、provider 抽象完整(現況 2026-05)
- 訂閱登入:ChatGPT Plus / Pro、Claude Pro / Max、GitHub Copilot
- API key:OpenAI、Anthropic、Gemini、Bedrock、OpenRouter、Groq、Mistral、Cloudflare
- 自訂:`~/.pi/agent/models.json`
- 抽象目標:OpenAI-compatible / Anthropic Messages / Google Generative AI 三種 API 類型
- 重點:讓你能「換 provider 不用換 CLI」

### 四、刻意不做(原文 Philosophy 條列)
缺 ≠ bug,以下為立場——全部交由 extension 自補:

| 不做 | 替代方案 |
|---|---|
| **無 MCP** | MCP 通訊成本被認為太高(占上下文 7-9%);原作者主張用 CLI + README 透過 `bash` 按需載入 |
| **無 sub-agents** | 「黑盒子中的黑盒子」喪失可觀測性;透過 `bash` 自我呼叫保留輸出可見性 |
| **無 plan mode** | 用文件 `PLAN.md` 替代,可版本控制、跨會話共享 |
| **無 permission popups** | 「安全劇場」(security theater);YOLO by default |
| **無 built-in to-dos** | — |
| **無 maxSteps** | 迴圈自然結束。Zechner 原話:「我從來沒找到需要 maxSteps 的用例,所以為什麼要加?」 |

→ 這是 [[wiki/sources/2026-02-10-pi-agent-core-design]] 7.1 + [[wiki/sources/2026-05-02-pi-mono-framework-tw]] 第 4 判斷的合併視角。

## 入口最常見的快取誤判(原文)
- 「沒內建 MCP 就是不完整」→ 不,它認為 MCP 不該在 runtime
- 「換個 CLI 換 provider」→ 不,這恰恰是它的設計透點
- 「適合裝了就用」的 team-wide 標準化 → 不,builder-oriented 優先

## 跨語言移植

**Tau**（`wiki/entities/tau`）是 Pi 的 **Python port**，由 Pi 團隊內部開發。架構與 Pi 完全相同（session tree、skills、extensions、system prompt），差異只在 TUI 層使用 Textual 框架而非 TypeScript 從零打造。Tau 的存在證明了 Pi 架構的可移植性——extension events API 跨語言相容。

## 相關頁面
- Entities:[[wiki/entities/mario-zechner]]、[[wiki/entities/pi-agent-core]]、[[wiki/entities/tau]]
- Sources:[[wiki/sources/2026-02-10-pi-agent-core-design]] / [[wiki/sources/2026-05-02-pi-mono-framework-tw]] / [[wiki/sources/2026-08-03-tau-python-port-of-pi]]
- Concepts:[[wiki/concepts/late-conversion]]、[[wiki/concepts/minimal-agent-philosophy]]
- Synthesis:（待建立：coding-agent-comparison — Claude Code / Codex / Pi 五維對比表）
