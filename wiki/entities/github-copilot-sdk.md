---
title: "GitHub Copilot SDK — Custom Agents 平台"
type: entity
created: 2026-08-14
updated: 2026-08-14
sources: 22
tags: [github-copilot, sdk, custom-agents, hooks, fleet-mode, mcp, agent-loop]
topics: [copilot-sdk, ai-development-tools, ai-agent]
canonical: entities/github-copilot-sdk
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-custom-skills.md"
  - kind: raw
    path: "raw/web/2026-08-14-the-agent-loop.md"
  - kind: raw
    path: "raw/web/2026-08-14-custom-agents-and-sub-agent-orchestration.md"
  - kind: raw
    path: "raw/web/2026-08-14-fleet-mode.md"
  - kind: raw
    path: "raw/web/2026-08-14-working-with-hooks.md"
  - kind: raw
    path: "raw/web/2026-08-14-steering-and-queueing.md"
---

# GitHub Copilot SDK — Custom Agents 平台

> GitHub Copilot SDK 是 Copilot 的**程式化代理平台**，提供 Custom Agents、Sub-agent Orchestration、Custom Skills、Fleet Mode、Hooks、MCP 整合等完整 agent 能力。**⚠️ 此頁面基於 GitHub 官方文件（docs.github.com），修正了先前基於第三方文章的多處錯誤描述。**

## 平台架構

```
Copilot SDK (TypeScript/Python/Go/.NET/Rust)
├── Copilot CLI (orchestrator，運行 agentic tool-use loop)
├── Custom Agents (scoped tools + prompts + MCP)
├── Custom Skills (SKILL.md 漸進式揭露)
├── Hooks (7+ lifecycle callback)
├── Fleet Mode (parallel sub-agent orchestration)
├── Steering & Queueing (即時/佇列訊息投遞)
└── MCP Servers (Model Context Protocol 整合)
```

SDK 是**傳輸層**——透過 JSON-RPC 將 prompt 傳給 CLI，CLI 是真正的 orchestrator，運行 agentic tool-use loop。

## 核心能力一覽

| 能力 | 說明 | 對應頁面 |
|------|------|---------|
| **Agent Loop** | 多輪 tool-use loop，每輪一次 LLM API call | [[wiki/concepts/copilot-agent-loop\|Agent Loop]] |
| **Custom Agents** | 定義 scoped tools/prompts 的 sub-agent，runtime 自動委派 | [[wiki/concepts/copilot-custom-agents\|Custom Agents]] |
| **Custom Skills** | SKILL.md 格式，漸進式揭露，可 per-agent 預載入 | [[wiki/concepts/copilot-custom-agents\|Custom Agents]] |
| **Fleet Mode** | 並行 sub-agent orchestration，SQL todos 協調 | [[wiki/concepts/copilot-fleet-mode\|Fleet Mode]] |
| **Steering & Queueing** | 即時转向 / FIFO 佇列兩種訊息投遞模式 | [[wiki/concepts/copilot-steering-queueing\|Steering & Queueing]] |
| **Hooks** | 7+ lifecycle callback（權限、審計、通知、錯誤處理） | [[wiki/concepts/copilot-hooks-system\|Hooks 系統]] |
| **MCP Servers** | per-agent MCP 整合，支援 stdio/HTTP | [[wiki/entities/mcp-model-context-protocol\|MCP]] |

## Custom Agents 核心特性

- **定義**：每個 agent 有獨立 `name`、`prompt`、`tools`（scoped）、`mcpServers`
- **自動委派**：runtime 分析使用者 prompt，自動匹配最適合的 agent
- **隔離執行**：sub-agent 在隔離 context 中執行，事件串流回 parent session
- **Per-agent Skills**：可預載入特定 skills（`skills` 陣列，eagerly preloaded）
- **Per-agent Model**：可指定不同 model 和 reasoning effort
- **Inference 控制**：`infer: false` 可阻止自動選取

## Custom Skills 系統

與 Anthropic Agent Skills 類似的 SKILL.md 格式：

```
skills/
├── code-review/
│   └── SKILL.md
└── documentation/
    └── SKILL.md
```

- 載入方式：`skillDirectories` 參數
- 禁用方式：`disabledSkills` 陣列
- 與 Custom Agents 整合：agent 的 `skills` 陣列會 eagerly preload

## Hooks 系統（7+ 種）

| Hook | 觸發時機 | 用途 |
|------|---------|------|
| `onSessionStart` | Session 開始（新或恢復） | 注入 context、載入偏好 |
| `onUserPromptSubmitted` | 使用者送出訊息 | 重寫 prompt、加入 context |
| `onPreToolUse` | 工具執行前 | 允許/拒絕/修改呼叫 |
| `onPostToolUse` | 工具執行後（成功） | 轉換結果、遮蔽密鑰、審計 |
| `onPostToolUseFailure` | 工具執行後（失敗） | 注入重試指引、記錄失敗 |
| `onSessionEnd` | Session 結束 | 清理、記錄指標 |
| `onErrorOccurred` | 錯誤發生 | 自訂日誌、重試邏輯、警報 |

## Fleet Mode（並行 Orchestration）

- **用途**：將工作拆分為獨立 units，分配給多個 sub-agent 並行執行
- **協調機制**：SQL todos（`pending → in_progress → done/blocked`）
- **觸發方式**：`session.rpc.fleet.start()` 或 plan mode 的 `autopilot_fleet` exit action
- **Plugin sub-agents**：可透過 `--plugin-dir` 載入外部 agent 類型

## Steering & Queueing

| 模式 | 行為 | 適用場景 |
|------|------|---------|
| `"immediate"` (steering) | 注入**當前** LLM turn | Agent 走錯路時即時轉向 |
| `"enqueue"` (queueing) | 排隊等當前 turn 結束後處理 | 追加後續任務 |

## 部署與設定

- **Default setup**：Node.js/.NET SDK 內含 CLI（bundled）
- **Backend services**：server-side 部署，CLI 作為 headless server
- **Multi-tenancy**：per-session 隔離，多使用者服務
- **GitHub OAuth**：使用者 GitHub 帳號認證
- **Azure Managed Identity**：BYOK + Azure Identity SDK
- **OpenTelemetry**：分散式追蹤
- **Microsoft Agent Framework**：與 Azure OpenAI、Anthropic 等 provider 整合

## 與其他框架的比較

> ⚠️ 此比較已根據官方文件修正。先前基於第三方文章的錯誤描述已更正（見各欄位附註）。

| 面向 | GitHub Copilot SDK | Claude Code | Pi Agent |
|------|-------------------|-------------|----------|
| **Skill 格式** | SKILL.md（漸進式揭露） | SKILL.md（三層漸進式揭露） | SKILL.md（markup 注入） |
| **組合方式** | 多 Skill + per-agent preload | 多 Skill 同時載入（≤8/請求） | 逐一手動觸發 |
| **巢狀/委派** | ✅ Custom Agents + Sub-agent Orchestration + Fleet Mode ~~❌ 無~~ | ✅ Subagents（獨立 context） | ❌ 刻意不做 |
| **工具鏈整合** | MCP（per-agent）+ Custom Tools | MCP（開放標準） | Extension System |
| **鉤子/生命週期** | ✅ 7+ Hooks（完整 lifecycle） ~~❌ 無~~ | ✅ Hooks（PreToolUse 等） | ✅ Extension Events |
| **訊息控制** | ✅ Steering + Queueing | ❌ 無 | ❌ 無 |
| **並行編排** | ✅ Fleet Mode（SQL todos 協調） | ⚠️ Subagents（有限並行） | ❌ 無 |
| **Context** | 依模型（GPT-5.4 等） | 200K tokens | Compaction |

> 📝 **修正紀錄**：2026-08-14，根據 GitHub 官方文件（docs.github.com 22 篇）修正了先前基於 aiskill.market 第三方比較文的多處錯誤。第三方文章宣稱 Copilot「無巢狀機制」「無 hooks」「Extensions 僅為服務整合」等描述均與官方文件不符。詳見 [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]] 的修正紀錄。

## 客觀事實

| 概念 | 發現 |
|------|------|
| SDK 語言 | TypeScript、Python、Go、.NET、Rust |
| CLI 整合 | Node.js/.NET SDK 內含 bundled CLI |
| Agent Loop | 多輪 tool-use，每輪 = 一次 LLM API call |
| Custom Agents | 動態委派 + 隔離執行 + 事件串流 |
| Skills 格式 | SKILL.md（與 Anthropic 格式相容） |
| Fleet Mode | SQL todos 協調，實驗性功能 |
| Hooks | 7+ 種 lifecycle callback |
| MCP | per-agent MCP server 整合 |
| Multi-tenancy | per-session 隔離 |
| 授權 | GitHub OAuth + Azure Managed Identity |

## 來源

- [[wiki/sources/2026-08-14-custom-skills|Custom Skills]]
- [[wiki/sources/2026-08-14-the-agent-loop|The Agent Loop]]
- [[wiki/sources/2026-08-14-custom-agents-and-sub-agent-orchestration|Custom Agents and Sub-agent Orchestration]]
- [[wiki/sources/2026-08-14-fleet-mode|Fleet Mode]]
- [[wiki/sources/2026-08-14-working-with-hooks|Working with Hooks]]
- [[wiki/sources/2026-08-14-steering-and-queueing|Steering and Queueing]]
- 16 additional setup/deployment/hook source notes（見下方列表）

## 相關頁面

- [[wiki/entities/github-copilot|GitHub Copilot]] — 產品面概述
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Claude Skill 系統
- [[wiki/entities/claude-code|Claude Code]] — Claude Code
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi Agent Skill 機制
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]] — 三框架比較（已修正）
