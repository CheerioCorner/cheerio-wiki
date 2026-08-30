---
title: Multi-Agent Orchestration 設計模式
type: entity
created: 2026-08-25
updated: 2026-08-25
sources: 2
tags: [multi-agent, orchestration, design-patterns, herdr, langgraph]
canonical: entities/multi-agent-orchestration-patterns
topics: [agent-runtime-implementations, coding-agent, agent-infrastructure]
---

> 多 Agent 編排的三大核心設計模式：常駐背景（Persistent Background）、語義狀態偵測（Semantic State Detection）、原子化 Prompt（Atomic Prompt）。這些模式解決了長工期任務中的容錯、狀態感知與競態條件問題。

## 三大設計模式

### 1. 常駐背景（Persistent Background）

**核心問題**：LLM 無狀態，長工期任務容易因中斷而前功盡棄。

**解決方案**：Client-Server 架構，背景伺服器託管所有 agent 進程。

| 工具 | 實現程度 | 實作方式 |
|---|---|---|
| **Herdr** | ⭐⭐⭐⭐⭐ | Rust 背景伺服器託管 PTY，關蓋/斷線不中斷，支援自動恢復 |
| **Pi + pi-herdr-agents** | ⭐⭐⭐⭐ | 透過整合套件將子任務 Spawning 到 Herdr PTY Pane |
| **Claude Code** | ⭐⭐⭐⭐ | `context: fork` + `background: true` 生成背景子 agent |
| **Copilot SDK** | ⭐⭐⭐ | Infinite Sessions 自動 Checkpoint，Fleet Mode 平行執行 |
| **LangGraph** | ⭐⭐⭐ | 純邏輯級持久化（Checkpointer），不託管背景進程 |

### 2. 語義狀態偵測（Semantic State Detection）

**核心問題**：agent 隨時可能遇到 blocked（CAPTCHA、登入牆、人工確認），需要精準判斷狀態。

**四種偵測機制**：

| 機制 | 原理 | 代表工具 |
|---|---|---|
| **TSR（任務狀態表示法）** | 結構化 JSON 追蹤進度，比對截圖判斷動作有效性 | 學術研究 |
| **PTY 螢幕匹配** | 讀取終端底部緩衝區，TOML 正則匹配已知 UI 模式 | Herdr（fallback） |
| **Lifecycle Hook 自我宣告** | Agent 主動向 Socket 匯報狀態 | Herdr + Pi（精準權威） |
| **圖形狀態機 + Checkpoint** | 狀態流傳播至 END 節點即 Done，interrupt 即 Blocked | LangGraph |

**各工具偵測策略比較**：

| 工具 | Working 偵測 | Blocked 偵測 | Done 偵測 |
|---|---|---|---|
| **Herdr** | Hook 或 PTY 活動 | 嚴格 TOML 匹配，fallback idle | PTY 回到 Shell 提示符 |
| **Pi** | `agent_start` 事件 | `ask_user` 主動申報 | `agent_settled` 事件 |
| **Claude Code** | JSONL 日誌 Token 計數 | 75% 警告，85% 硬性阻斷 | Token 佔用穩定 |
| **Copilot SDK** | `message_delta` 事件 | `onPermissionRequest` | Streaming 結束 |
| **LangGraph** | 節點執行中 | `interrupt` 中斷點 | 狀態流至 `END` |

### 3. 原子化 Prompt（Atomic Prompt）

**核心問題**：多 agent 並行時，容易產生競態條件（搶任務、覆蓋檔案、环境污染）。

**四種防競態機制**：

| 機制 | 原理 | 代表工具 |
|---|---|---|
| **任務認領鎖** | 檔案排他鎖，同時只有一個 agent 認領任務 | Claude Code Agent Teams |
| **分散式檔案鎖** | `.claude/locks/{agent}.lock`，寫入排他 | Praetorian Platform |
| **Git Worktrees** | 每個 agent 獨立 checkout 工作目錄 | Herdr + Pi |
| **原子化 API 呼叫** | Prompt 發送 + Wait 監聽封裝在單次請求 | Herdr `agent prompt --wait` |

**各工具實現程度比較**：

| 工具 | 原子化 Prompt | 環境隔離 | 檔案鎖 |
|---|---|---|---|
| **Herdr** | `agent prompt --wait` 原子呼叫 | Git Worktrees Tier 4 | — |
| **Pi** | `herdr_delegate` 封裝 spawn→prompt→wait | Git Worktrees | — |
| **Claude Code** | Skills 兩層漸進式載入 | `context: fork` | `~/.claude/locks/` |
| **Copilot SDK** | SystemMessage 12 切片操控 | Fleet Mode | — |
| **LangGraph** | 節點高度原子化 | Thread ID 隔離 | Pending Writes 保留 |

## Cheerio 助理應用架構

```
                    [下載 HTML 原始碼]
                           │
                           ▼
             [執行 Syntactic Fast Path]
            (快取的 Cheerio 靜態選擇器)
                           │
                 ┌─────────┴─────────┐
         (成功) │                   │ (失敗/Null)
                 ▼                   ▼
           [寫入資料庫]     [觸發 Blocked / Failed]
                                   │
                                   ▼
                       [Inference Core]
                     (LLM 語義提取數據)
                                   │
                                   ▼
                       [Self-Healing Agent]
                     (反向推導新 CSS Selector)
                                   │
                                   ▼
                         [更新選擇器 Cache]
                    (下次回歸 $0 快速路徑)
```

**五個微型 Agent 角色**：
1. **Orchestrator（領班協調器）**：解析 Sitemap，分派任務，不擁有寫入權
2. **Fetcher（網頁獲取者）**：HTTP 請求，動態升級到 Playwright
3. **Distiller（DOM 淨化器）**：Cheerio 快速剪枝，注入 ARIA Labels
4. **Extractor（語義提取器）**：LLM 無選擇器語義解析，Zod/Pydantic 驗證
5. **Self-Healing（自癒工程師）**：反向推導新 Selector，更新快取

## 來源

- [[wiki/sources/2026-08-25-multi-agent-orchestration-patterns|2026-08-25 Multi-Agent Orchestration 設計模式深度研究]]
- [[wiki/sources/2026-08-25-herdr-deep-research|2026-08-25 Herdr 深度研究]]

## 相關頁面

- [[wiki/entities/herdr|herdr]] — 實現常駐背景與原子化 Prompt 的代表工具
- [[wiki/entities/langgraph|LangGraph]] — 圖形狀態機 + Checkpoint 的代表
- [[wiki/concepts/sub-agents-pattern|sub-agents-pattern]] — 子 agent 委派模式
- [[wiki/concepts/agent-durability-patterns|agent-durability-patterns]] — 狀態持久化模式
