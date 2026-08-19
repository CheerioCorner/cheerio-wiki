---
title: pi-mono — Agent Toolkit Monorepo
type: entity
created: 2026-07-11
updated: 2026-08-19
sources: 5
tags: [pi, coding-agent, monorepo]
collection: entities
topics: [pi-agent-ecosystem, extension-dev]
canonical: entities/pi-mono
---

> [`earendil-works/pi`](https://github.com/earendil-works/pi)（前身 `badlogic/pi-mono`）是 Mario Zechner 維護的 TypeScript agent toolkit monorepo。核心定位：**「你的 agent 平台」**——不是單一既定體驗,而是一組可組合的 runtime、模型抽象層、UI。License：MIT。

## 四個子 package（2026-08 更新）

| Package | 角色 | 備註 |
|---|---|---|
| `@earendil-works/pi-ai` | 模型抽象層 | 處理多 provider 切換與自訂模型;`~/.pi/agent/models.json` 是入口 |
| `@earendil-works/pi-agent-core` | agent runtime | 文章 [[wiki/sources/2026-02-10-pi-agent-core-design]] 重點解剖的對象;5 檔 / 1,500 行 |
| `@earendil-works/pi-coding-agent` | 最上層 CLI | `npm install -g …pi-coding-agent` 即裝,`pi` 即可執行 |
| `@earendil-works/pi-tui` | terminal UI | 互動介面層 |

> ~~`pi-web-ui`~~（web UI）已不在 README 列出，可能已移除或獨立。Package scope 從 `@mariozechner` 改為 `@earendil-works`。

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

### 三、provider 抽象完整(現況 2026-08)
- 訂閱登入:ChatGPT Plus / Pro、Claude Pro / Max、GitHub Copilot
- API key:OpenAI、Anthropic、Gemini、Bedrock、OpenRouter、Groq、Mistral、Cloudflare
- 自訂:`~/.pi/agent/models.json`
- 抽象目標:OpenAI-compatible / Anthropic Messages / Google Generative AI 三種 API 類型
- 重點:讓你能「換 provider 不用換 CLI」

#### GitHub Copilot 設定方式

```bash
pi
/login
# 選擇 GitHub Copilot → 瀏覽器完成 OAuth
```

Token 自動存入 `~/.pi/agent/auth.json`，之後自動刷新。模型清單為靜態（from models.dev），如模型不出現需在 VS Code 中先 Enable。

→ 詳見 [[wiki/sources/2026-08-19-pi-agent-github-copilot-provider-setup|Copilot Provider Setup Guide]]

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

## Containerization（沙箱隔離）

Pi 預設以使用者權限運行，無內建權限系統。需強制隔離時有三種方案：

| 方案 | 說明 |
|---|---|
| **Gondolin extension** | `pi` 與 provider auth 留在 host，built-in tools 與 `!` commands 路由到本地 Linux micro-VM |
| **Docker** | 整個 `pi` process 在本地 container 中運行 |
| **OpenShell** | 整個 `pi` process 在 policy-controlled sandbox 中運行 |

## Supply-chain Security

- 外部依賴釘死確切版本（`save-exact=true`）
- `package-lock.json` 為 dependency ground truth，pre-commit 阻擋意外更動
- `npm run check` 驗證 pinned deps、TypeScript import 相容性、shrinkwrap
- 發布前 local release smoke test（`npm run release:local`）
- CI 用 `npm ci --ignore-scripts`，定期 `npm audit`

## Session Publishing

OSS session 可發布到 Hugging Face 幫助改進 coding agent：

```bash
# 使用 badlogic/pi-share-hf 工具
# 需要 Hugging Face 帳號 + HF CLI
badlogicgames/pi-mono on Hugging Face: https://huggingface.co/datasets/badlogicgames/pi-mono
```

## 跨語言移植

**Tau**（[[wiki/entities/tau|tau]]）是 Pi 的 **Python port**，由 Hugging Face 開發。架構與 Pi 完全相同（session tree、skills、extensions、system prompt），差異只在 TUI 層使用 Textual 框架。Tau 的存在證明了 Pi 架構的可移植性——extension events API 跨語言相容。

## CLI Entry Point（Walkthrough 補充）

來源：[[wiki/sources/2026-08-06-pi-architecture-walkthrough]]

1. `client.ts`：接收 `pi` 命令 → 設定 process title → 呼叫 `main()`
2. `main.ts`：
   - 解析 arguments
   - 解析 configuration（cwd 等）
   - **載入 extensions**
   - 建立 agent session（初始化 PyCore）
   - 依模式執行：interactive / RPC / print-to-STDIO

## Extensions Events 系統

每個 agentic loop 步驟都會觸發 events，extension 可訂閱：
`tool_call` / `agent_response` / `user_message` 等

Extension 能力清單：
- 註冊新 tools / 註冊 commands
- 新增 keyboard shortcuts / CLI flags
- 更新 system prompt / 渲染 custom messages

## Read-only Mode

`--tools read,grep,find`：啟用隱藏的 grep + find 工具，停用 bash/write/edit，適用於 RPC / 程式化自動化。

## 相關頁面
- Entities:[[wiki/entities/mario-zechner]]、[[wiki/entities/pi-agent-core]]、[[wiki/entities/tau]]
- Sources:[[wiki/sources/2026-02-10-pi-agent-core-design]] / [[wiki/sources/2026-05-02-pi-mono-framework-tw]] / [[wiki/sources/2026-08-03-tau-python-port-of-pi]] / [[wiki/sources/2026-08-05-pi-github-readme]] / [[wiki/sources/2026-08-06-pi-architecture-walkthrough]]
- Concepts:[[wiki/concepts/late-conversion]]、[[wiki/concepts/minimal-agent-philosophy]]
- Synthesis:（待建立：coding-agent-comparison — Claude Code / Codex / Pi 五維對比表）
