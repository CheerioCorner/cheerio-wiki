---
title: "Skill 複雜組合技比較 — Claude Code / GitHub Copilot / Pi Agent"
type: comparison
created: 2026-08-14
updated: 2026-08-14
sources: 3
tags: [skill, comparison, claude-code, github-copilot, pi-agent, composition, delegation, toolchain]
topics: [skill, ai-development-tools, ai-agent]
canonical: concepts/skills-complex-composition-comparison
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-github-copilot-vs-claude-code-skills-ecosystem.md"
  - kind: raw
    path: "raw/web/2026-08-14-agent-skills.md"
  - kind: entity-ref
    path: "wiki/entities/pi-agent-core.md"
---

# Skill 複雜組合技比較

> 三大主流 Agent 框架在處理複雜、組合技 Skill 時的機制與設計取捨：如何組合呼叫、有無巢狀/委派機制、如何與其他工具鏈整合。
>
> ⚠️ **本頁為骨架**。部分進階資訊（如 Copilot Workspace 的多檔案任務流程、Pi Agent 的 skill 互動層攔截細節）待後續 raw 來源補充。請勿因資料不完整而新建頁面——所有新資料應直接擴充本頁。

## 比較總覽

| 面向 | Claude Code | GitHub Copilot | Pi Agent |
|------|------------|---------------|----------|
| **Skill 格式** | SKILL.md（三層漸進式揭露） | Extensions（服務整合） | SKILL.md（markup 注入） |
| **組合方式** | 多 Skill 同時載入（≤8/請求） | Extensions @mentions | Skills 逐一手動觸發 |
| **巢狀/委派** | ✅ Subagents（獨立 context） | ⚠️ Workspace preview | ❌ 刻意不做 |
| **工具鏈整合** | MCP（開放標準） | Extensions（策劃合作夥伴） | Extension System |
| **鉤子/生命週期** | ✅ Hooks（PreToolUse 等） | ❌ 無 | ✅ Extension Events |
| **Context 管理** | 200K + Compaction | 8K（標準）/ 更大（Enterprise） | Compaction |
| **生態規模** | 34,000+（開放） | 20+（策劃） | 本地安裝 |

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
- 多個 Skill 可同時處理事實上複雜的工作流（如「分析資料 + 產生簡報」）
- 每個 Skill 的中繼資料在系統提示中獨立存在，但只有被觸發的 Skill 才載入完整指令

### GitHub Copilot：Extensions 獨立呼叫

- Extensions 以 @mention 方式觸發（如 `@docker`、`@azure`）
- 每次對話通常只涉及一個 Extension
- **無內建的多 Extension 組合機制**——需要使用者分別觸發
- Copilot Workspace（preview）可處理多檔案任務，但非真正的 Skill 組合

### Pi Agent：逐一手動觸發

- Skills 在 system prompt 中以 markup 列出（name + description）
- 使用者透過 `/skill:<name>` 手動觸發
- 一次只載入一個 Skill 的內容
- **無內建的多 Skill 同時載入機制**

> ⚠️ **待補充**：Pi Agent 是否有透過 extension 或自訂機制實現多 Skill 組合的案例？目前資料不足。

## 2. 巢狀與委派機制

### Claude Code：Subagents（獨立子代理）

Claude Code 支援啟動 subagents 並行處理任務，每個 subagent 有獨立的 context：

```
主 Agent
├── Subagent 1：CLAUDE.md 合規性檢查
├── Subagent 2：Bug 掃描
├── Subagent 3：Git 歷史分析
└── 綜合評分（confidence scoring ≥ 80）
```

- Subagents 可被 Skills 觸發（如 `/code-review` 同時啟動 4 個 review agent）
- 每個 subagent 獨立執行、獨立產出結果
- 主 agent 最後綜合所有結果

### GitHub Copilot：無巢狀機制

- Extensions 是**服務整合**，不是行為自訂
- 無 subagent 或 nested skill 概念
- Copilot Workspace（preview）可拆分多檔案任務，但仍是單一 agent 流程

### Pi Agent：刻意不做

Pi Agent 的設計哲學是**極簡主義**（5 個檔 / 1,500 行）：

- 核心**無 subagent**、**無 plan mode**、**無 maxSteps**
- Skill 內容**不自動注入**——LLM 主動用 `read` 工具讀取
- 巢狀/委派由**下游擴充**處理（如 Prime Agent 用 RLM function 新增 sub-agent）

> 📖 詳見 [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] 和 [[wiki/entities/prime-agent|Prime Agent]]（下游 RLM 擴充案例）

## 3. 工具鏈整合

### Claude Code：MCP（Model Context Protocol）

- 原生支援 MCP，透過 `.mcp.json` 或 `claude mcp add` 串接
- 支援三種傳輸層：stdio（本地進程）、SSE（遠端 OAuth）、HTTP（REST）
- MCP Server 範例：LSP Server、Database、Custom API
- Skill 可引用 MCP 工具（`ServerName:tool_name`）

### GitHub Copilot：Extensions 生態

- Extensions 是**服務整合**（@docker/@azure/@sentry）
- 無統一的 tool protocol（每個 Extension 自訂整合方式）
- Copilot Workspace 可跨檔案操作，但受限於 Extensions 可用性

### Pi Agent：Extension System

- Extension 以目錄形式安裝於 `~/.agents/extensions/`
- 透過 lifecycle events（agent_start/turn_start/tool_execution_start 等）介入
- 支援 intercept 模式（如 interactive layer 攔截 `/skill:` 指令）
- MCP 支援透過 extension（如 Pi LSP Bridge）間接實現

> 📖 詳見 [[wiki/concepts/agent-extension-installation|Agent Extension 安裝位置與方式]]

## 4. 設計哲學差異

### Claude Code：開放 + 能力上限

- **開放生態**：34,000+ Skills，任何人可建立發布
- **能力上限高**：Subagents + MCP + Hooks + Extended Thinking
- **代價**：設定複雜、學習曲線陡

### GitHub Copilot：策劃 + 簡單性

- **策劃生態**：20+ 官方 Extensions，品質優先
- **簡單性**：內聯建議、最低摩擦
- **代價**：自訂深度有限、無法修改 coding style

### Pi Agent：極簡 + 可干預

- **極簡核心**：5 個檔、刻意不做多事
- **可觀測性**：三層事件生命週期、tool call 不隱藏
- **可干預**：steering / follow-up 雙 queue
- **代價**：需要下游擴充才能處理複雜場景

> 📖 詳見 [[wiki/concepts/harness|Harness]] 和 [[wiki/concepts/meta-harness|Meta-Harness]]

## 5. 實際組合場景比較

### 場景：「分析銷售資料 → 產生 PDF 報告 → 發送 Email」

| 步驟 | Claude Code | GitHub Copilot | Pi Agent |
|------|------------|---------------|----------|
| 分析資料 | xlsx Skill（自動觸發） | @datadog Extension | 自訂 Skill + bash |
| 產生報告 | pdf Skill（組合載入） | ❌ 需手動或自訂 | 自訂 Skill + bash |
| 發送 Email | MCP Email Server | ❌ 無內建 | Extension（如 email-ext） |
| 組合方式 | 單一請求多 Skill | 需多次呼叫 | 需多次觸發 + 自訂 |

### 場景：「Code Review + 安全掃描 + 測試覆蓋率」

| 步驟 | Claude Code | GitHub Copilot | Pi Agent |
|------|------------|---------------|----------|
| Code Review | Subagent（並行） | Copilot Chat | chat-with-claude skill |
| 安全掃描 | Subagent（並行） | @sentry Extension | 自訂 Extension |
| 測試覆蓋率 | Subagent（並行） | 內建建議 | 自訂 Skill |
| 綜合評分 | confidence scoring ≥ 80 | ❌ 無 | Round Table skill |

## 待補充區域

以下資訊在現有 7 篇來源中不足，需要後續 raw 來源補充：

- [ ] **Copilot Workspace** 的多檔案任務完整流程（目前僅 preview）
- [ ] **Claude Code Subagents** 的實際 Skill 呼叫案例與限制
- [ ] **Pi Agent** 的多 Skill 組合實踐（是否有社群案例？）
- [ ] **跨框架 Skill 移植**的實際經驗（agentskills.io 標準的真實相容性）
- [ ] **性能比較**：多 Skill 同時載入對延遲和 token 消耗的影響
- [ ] **企業級組合**：大規模 Skill 套件的版本管理和衝突解決

> 📝 後續 raw 來源放入 `raw/web/` 後，使用同一頁面擴充，不要新建頁面。

## 來源

- [[wiki/sources/2026-08-14-copilot-vs-claude-code-skills-ecosystem|Copilot vs Claude Code 比較]] — 第三方分析
- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills 概覽]] — Anthropic 官方
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi Agent Skill 機制

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 基礎層導航
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Claude Code Skill 系統
- [[wiki/entities/github-copilot|GitHub Copilot]] — Copilot Extensions
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi Agent Skill 機制
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — Pi 的極簡哲學
- [[wiki/concepts/harness|Harness]] — LLM 驅動層三層分類 🌱
