---
title: "Skill 複雜組合技比較 — Claude Code / GitHub Copilot / Pi Agent"
type: comparison
created: 2026-08-14
updated: 2026-08-17
sources: 26
tags: [skill, comparison, claude-code, github-copilot, pi-agent, composition, delegation, toolchain]
topics: [skill, ai-development-tools, ai-agent]
canonical: concepts/skills-complex-composition-comparison
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-agent-skills.md"
  - kind: raw
    path: "raw/web/2026-08-14-custom-skills.md"
  - kind: raw
    path: "raw/web/2026-08-14-custom-agents-and-sub-agent-orchestration.md"
  - kind: raw
    path: "raw/web/2026-08-14-fleet-mode.md"
  - kind: raw
    path: "raw/web/2026-08-14-working-with-hooks.md"
  - kind: raw
    path: "raw/web/2026-08-14-steering-and-queueing.md"
  - kind: entity-ref
    path: "wiki/entities/pi-agent-core.md"
---

# Skill 複雜組合技比較

> 三大主流 Agent 框架在處理複雜、組合技 Skill 時的機制與設計取捨：如何組合呼叫、巢狀/委派機制、工具鏈整合。
>
> 📝 **2026-08-14 修正紀錄**：本頁 GitHub Copilot 欄位已根據官方文件（docs.github.com 22 篇）全面修正。先前依據第三方文章（aiskill.market）的多處錯誤描述（「無巢狀機制」「無 hooks」「Extensions 僅為服務整合」等）已更正。官方文件優先於第三方部落格，修正原因記錄於各章節。

## 修正紀錄

| 項目 | 第三方文章（舊） | 官方文件（新） | 修正原因 |
|------|----------------|---------------|---------|
| Skill 格式 | Extensions（服務整合） | SKILL.md（與 Anthropic 格式兼容） | 官方 docs 明確定義 SKILL.md 格式 |
| 組合方式 | Extensions @mentions | `skillDirectories` + per-agent `skills` preload | SDK 支援多 Skill 載入 |
| 巢狀/委派 | ⚠️ Workspace preview | Custom Agents + Sub-agent Orchestration + Fleet Mode | 官方 SDK 完整 sub-agent 機制 |
| 工具鏈整合 | Extensions（策劃） | MCP（per-agent）+ Custom Tools | SDK 原生 MCP 整合 |
| 鉤子/生命週期 | ❌ 無 | 7+ Hooks（完整 lifecycle） | 官方 hooks 文件 7 種 |
| Context | 8K tokens | 依模型（GPT-5.4 等） | 第三方數據過時 |
| 生態規模 | 20+（策劃） | 開放 SDK + 社群 Skills | SDK 已開放 |

## 比較總覽

| 面向 | Claude Code | GitHub Copilot SDK | Pi Agent |
|------|------------|-------------------|----------|
| **Skill 格式** | SKILL.md（三層漸進式揭露） | SKILL.md（漸進式揭露，格式兼容） | SKILL.md（markup 注入） |
| **組合方式** | 多 Skill 同時載入（≤8/請求） | 多 Skill + per-agent preload | 逐一手動觸發 |
| **巢狀/委派** | ✅ Subagents（獨立 context） | ✅ Custom Agents + Sub-agent Orchestration + Fleet Mode | ❌ 刻意不做 |
| **工具鏈整合** | MCP（開放標準） | MCP（per-agent）+ Custom Tools | Extension System |
| **鉤子/生命週期** | ✅ Hooks（PreToolUse 等） | ✅ 7+ Hooks（完整 lifecycle） | ✅ Extension Events |
| **訊息控制** | ❌ 無 | ✅ Steering + Queueing | ❌ 無 |
| **並行編排** | ⚠️ Subagents（有限） | ✅ Fleet Mode（SQL todos） | ❌ 無 |
| **Context 管理** | 200K + Compaction | 依模型 | Compaction |

## 1. Skill 如何組合呼叫

### Claude Code：多 Skill 並行載入

Claude API 支援在單一請求中指定多個 Skills（最多 8 個）：

```yaml
container:
  skills:
    - {type: anthropic, skill_id: xlsx}
    - {type: anthropic, skill_id: pptx}
    - {type: custom, skill_id: skill_01AbCd...}
```

- Claude 自動將請求與相關 Skill 配對
- 多個 Skill 可同時處理事實上複雜的工作流
- 每個 Skill 的中繼資料在系統提示中獨立存在

### GitHub Copilot SDK：多 Skill + Per-agent Preload

Copilot SDK 支援兩層 Skill 載入：

**Session 層**：透過 `skillDirectories` 載入，`disabledSkills` 禁用：

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills/code-review", "./skills/documentation"],
    disabledSkills: ["experimental-feature"],
});
```

**Agent 層**：agent 的 `skills` 陣列會 **eagerly preload**（完整內容注入 context）：

```typescript
customAgents: [{
    name: "security-auditor",
    skills: ["security-scan", "dependency-check"],  // eagerly preloaded
}]
```

> 📝 **修正**：第三方文章稱 Copilot「無內建多 Extension 組合機制」。實際上官方 SDK 支援多 Skill 載入 + per-agent preload，格式與 Anthropic SKILL.md 兼容。

### Pi Agent：逐一手動觸發

- Skills 在 system prompt 中以 markup 列出（name + description）
- 使用者透過 `/skill:<name>` 手動觸發
- 一次只載入一個 Skill 的內容
- **無內建的多 Skill 同時載入機制**

## 2. 巢狀與委派機制

### Claude Code：Subagents（獨立子代理）

- Subagents 可被 Skills 觸發，每個 subagent 有獨立 context
- 綜合評分（confidence scoring ≥ 80）

### GitHub Copilot SDK：Custom Agents + Fleet Mode

Copilot SDK 有**完整的 sub-agent orchestration**：

**Custom Agents**：定義 scoped tools/prompts 的 agent，runtime 自動委派：

```typescript
customAgents: [
    { name: "researcher", tools: ["grep", "glob", "view"], prompt: "..." },
    { name: "editor", tools: ["view", "edit", "bash"], prompt: "..." },
]
```

- **自動委派**：runtime 分析 prompt，自動匹配最適合的 agent
- **隔離執行**：sub-agent 在隔離 context 中執行
- **事件串流**：`subagent.started/completed/failed/selected/deselected`
- **Per-agent Model**：可指定不同 model 和 reasoning effort
- **Agent-exclusive Tools**：`defaultAgent.excludedTools` 隱藏工具，強制委派

**Fleet Mode**：並行 orchestration，SQL todos 協調：

```typescript
await session.rpc.fleet.start({
    prompt: "Refactor each SDK package independently",
});
```

- 多個 sub-agent 並行執行獨立 units
- SQL todos 追蹤 `pending → in_progress → done/blocked`
- 適合：多檔案重構、批次 review、並行研究

> 📝 **修正**：第三方文章稱 Copilot「無巢狀機制」「僅 Workspace preview」。實際上官方 SDK 有完整的 Custom Agents + Sub-agent Orchestration + Fleet Mode。

### Pi Agent：刻意不做

Pi Agent 的設計哲學是**極簡主義**（5 個檔 / 1,500 行）：

- 核心**無 subagent**、**無 plan mode**、**無 maxSteps**
- 巢狀/委派由**下游擴充**處理（如 Prime Agent 用 RLM function）

> 📖 詳見 [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]]

## 3. 工具鏈整合

### Claude Code：MCP（Model Context Protocol）

- 原生支援 MCP，三種傳輸層：stdio/SSE/HTTP
- Skill 可引用 MCP 工具（`ServerName:tool_name`）

### GitHub Copilot SDK：MCP（per-agent）+ Custom Tools

- **Per-agent MCP**：每個 custom agent 可有自己的 MCP server
- **Custom Tools**：`defineTool()` 定義自訂工具
- **Tool scoping**：`tools` 陣列限制 agent 可用工具
- **Agent-exclusive Tools**：`excludedTools` 從預設 agent 隱藏工具

```typescript
customAgents: [{
    name: "db-analyst",
    mcpServers: { "database": { command: "npx", args: [...] } },
}]
```

> 📝 **修正**：第三方文章稱 Copilot「無統一 tool protocol」。實際上 SDK 原生支援 MCP + Custom Tools。

### Pi Agent：Extension System

- Extension 以目錄形式安裝，透過 lifecycle events 介入
- MCP 支援透過 extension 間接實現

## 4. 額外能力：Copilot 獨有

### Hooks 系統（7+ 種）

| Hook | 觸發時機 |
|------|---------|
| `onSessionStart` | Session 開始 |
| `onUserPromptSubmitted` | 使用者送出訊息 |
| `onPreToolUse` | 工具執行前（權限控制） |
| `onPostToolUse` | 工具執行後（成功） |
| `onPostToolUseFailure` | 工具執行後（失敗） |
| `onSessionEnd` | Session 結束 |
| `onErrorOccurred` | 錯誤發生 |

> 📝 **修正**：第三方文章稱 Copilot「無 hooks」。實際上有 7+ 種 lifecycle hooks。

### Steering & Queueing

| 模式 | 行為 |
|------|------|
| `"immediate"` (steering) | 注入當前 turn，即時轉向 |
| `"enqueue"` (queueing) | 排隊等 turn 結束後 FIFO 處理 |

## 5. 設計哲學差異

### Claude Code：開放 + 能力上限

- 開放生態（34,000+ Skills）
- 能力上限高（Subagents + MCP + Hooks + Extended Thinking）
- 代價：設定複雜

### GitHub Copilot SDK：程式化平台 + 深度自訂

- **程式化 SDK**：TypeScript/Python/Go/.NET/Rust
- **Custom Agents**：scoped tools + auto-delegation + Fleet Mode
- **完整 Hooks**：7+ lifecycle callback
- **MCP 整合**：per-agent MCP + Custom Tools
- **Multi-tenancy**：per-session 隔離、水平擴展
- 代價：需要程式化整合（非純 CLI）

> 📝 **修正**：第三方文章將 Copilot 定位為「策劃 + 簡單性」。實際上 SDK 是**程式化 agent 平台**，支援深度自訂。

### Pi Agent：極簡 + 可干預

- 極簡核心（5 個檔 / 1,500 行）
- 可觀測性（三層事件）
- 可干預（steering / follow-up 雙 queue）
- 代價：需要下游擴充

## 6. 實際組合場景比較

### 場景：「Code Review + 安全掃描 + 測試覆蓋率」

| 步驟 | Claude Code | GitHub Copilot SDK | Pi Agent |
|------|------------|-------------------|----------|
| Code Review | Subagent（並行） | Custom Agent（scoped tools） | chat-with-claude skill |
| 安全掃描 | Subagent（並行） | Custom Agent + MCP security server | 自訂 Extension |
| 測試覆蓋率 | Subagent（並行） | Custom Agent（test tools） | 自訂 Skill |
| 綜合評分 | confidence scoring | Fleet Mode（SQL todos） | Round Table skill |
| 權限控制 | Permission Prompting | onPreToolUse hook | Extension events |

### 場景：「分析資料 → 產生報告 → 發送通知」

| 步驟 | Claude Code | GitHub Copilot SDK | Pi Agent |
|------|------------|-------------------|----------|
| 分析資料 | xlsx Skill | Custom Agent + MCP db server | 自訂 Skill + bash |
| 產生報告 | pdf Skill | Custom Agent（report tools） | 自訂 Skill + bash |
| 發送通知 | MCP Email Server | onPostToolUse hook → Slack webhook | Extension |
| 組合方式 | 單一請求多 Skill | Fleet Mode 並行 | 多次觸發 |

## 應用層案例：Skill 鏈式協作（Chain Composition）

> 2026-08-17 新增。上述比較的是**框架層**組合機制差異；本節補充**應用層**具體案例——單一領域內的 skill 鏈設計。

**情境**：「這張 User Story 一直被退，幫我查清楚問題出在哪，然後更新它。」

```
work-query → wiki → analytics → work-maintainer
   ↓           ↓         ↓            ↓
先撈出這張    去讀該     比對相似      確認後回寫
WIT 的現況、  Team 的    欄位與工時    欄位與工時
歷程與退件    驗收準則   （過安全門）
紀錄          與規範
```

**關鍵**：複雜系統不是一支 skill 就能處理——**切成小支，才有辦法照情境串起來用**。簡單問題用 skill，複雜問題用 skill 的協作。

> 與框架層組合的差異：框架層決定了「怎麼編排多支 skill」（同時載入 / Subagent / Fleet Mode），應用層決定了「哪些 skill 應該串成一條鏈」。兩者是不同層次的問題。

## 來源

### GitHub Copilot SDK（官方文件，22 篇）
- [[wiki/sources/2026-08-14-custom-skills|Custom Skills]]
- [[wiki/sources/2026-08-14-the-agent-loop|The Agent Loop]]
- [[wiki/sources/2026-08-14-custom-agents-and-sub-agent-orchestration|Custom Agents]]
- [[wiki/sources/2026-08-14-fleet-mode|Fleet Mode]]
- [[wiki/sources/2026-08-14-working-with-hooks|Working with Hooks]]
- [[wiki/sources/2026-08-14-steering-and-queueing|Steering and Queueing]]
- 16 additional setup/hook source notes

### Anthropic Agent Skills（官方文件）
- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills 概覽]]

### Pi Agent
- [[wiki/entities/pi-agent-core|pi-agent-core]]

### 第三方（已標註為不準確）
- [[wiki/sources/2026-08-14-copilot-vs-claude-code-skills-ecosystem|Copilot vs Claude Code 比較]] — ⚠️ 第三方文章，Copilot 資訊已被官方文件修正

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 基礎層導航
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Claude Code Skill 系統
- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK]] — Copilot SDK 平台
- [[wiki/entities/github-copilot|GitHub Copilot]] — 產品面概述
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi Agent Skill 機制
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — Pi 的極簡哲學
- [[wiki/concepts/harness|Harness]] — LLM 驅動層三層分類 🌱
