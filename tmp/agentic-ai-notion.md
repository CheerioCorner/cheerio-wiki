# Agentic AI — 自主 AI 系統核心概念

> **成長階段：** 🌱 種子期
> **Tags：** 🔬 研究

---

## 這是什麼？

Agentic AI 是具備**自主性**、能**使用工具**、**跨步驟執行任務**的 AI 系統。核心公式：

```
Agentic AI = LLM + Tools + a Loop
```

Agent 不是「更大的模型」，而是「讓模型能做事的包裝層」。模型負責推理，agent 負責：決定做什麼 → 執行操作 → 觀察結果 → 決定下一步。

---

## 客觀事實

| 概念 | 發現 | 來源 |
|------|------|------|
| 四大支柱 | Harness（執行環境）、Loop（迴圈）、Memory（記憶）、Tools（工具） | wiki/concepts/agentic-ai |
| Minimal Agent Philosophy | 「自主 agent = LLM + tools + a loop，其餘都是鍋。」— Mario Zechner | wiki/concepts/minimal-agent-philosophy |
| Agent Durability Patterns | 三種路徑：無持久化（⭐）→ Checkpoint（⭐⭐）→ Full Persistence（⭐⭐⭐） | wiki/concepts/agent-durability-patterns |
| Loop vs Graph | Loop（while loop + LLM call）適合簡單任務；Graph（DAG + 狀態機）適合複雜工作流 | wiki/concepts/loop-vs-graph-engineering |
| Context Management | Smart Zone（~140k tokens 最佳工作區）、Compaction（壓縮歷史）、Session Tree（跨 session 傳遞） | wiki/entities/ai-agent-core |
| Memory 類型 | Working Memory（context window）、Session Memory（檔案/DB）、Long-term Memory（Vector DB）、Procedural Memory（Skill 文件） | wiki/concepts/agentic-ai |

---

## 深入技術分析

### 架構剖析：一個 Agent 的完整生命週期

```
使用者輸入: "幫我重構這個函式"
    ↓
┌─────────────────────────────────────────────────┐
│  Phase 1: Reasoning（推理）                      │
│  LLM 分析請求 → 決定策略：先讀取檔案、再分析、    │
│  最後寫入重構結果                                 │
│  Output: plan = [read_file, analyze, rewrite]    │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│  Phase 2: Tool Use（工具呼叫）                    │
│  執行 plan[0]: read_file("src/utils.ts")         │
│  回傳: 檔案內容 2,847 字元                        │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│  Phase 3: Observation（觀察結果）                 │
│  LLM 讀取檔案內容 → 發現 3 個可優化的模式          │
│  判斷: 需要更多資訊，先檢查 type definitions       │
└─────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────┐
│  Phase 4: Decision（決定下一步）                  │
│  選擇: 繼續（不是停止，因為任務未完成）            │
│  新 plan: [read_types, analyze_deps, rewrite]    │
└─────────────────────────────────────────────────┘
    ↓
    ... 迴圈繼續直到任務完成或達到停止條件 ...
    ↓
┌─────────────────────────────────────────────────┐
│  Phase 5: Completion（完成）                      │
│  輸出: 重構後的程式碼 + 變更說明                   │
│  停止條件: LLM 判斷「任務已完成」                  │
└─────────────────────────────────────────────────┘
```

### 四大支柱的設計抉擇

#### 1. Harness（執行環境）

Harness 是 agent 的「操作系統」——管理工具註冊、事件處理、權限控制。

| 設計選擇 | 優點 | 缺點 | 適用場景 |
|---------|------|------|---------|
| **單體 Harness** | 簡單、易除錯 | 難以擴展 | 個人工具、CLI |
| **Plugin Harness** | 靈活、可擴展 | 複雜度高 | 平台型 agent |
| **Meta-Harness** | 可管理多個 harness | 架構最複雜 | Multi-agent orchestration |

**我們的選擇**：Pi Agent 採用 Plugin Harness（extension 系統），Omnigent 探索 Meta-Harness 方向。

#### 2. Loop（迴圈設計）

| 迴圈模式 | 停止條件 | 錯誤處理 | 適用場景 |
|---------|---------|---------|---------|
| **計數器** | maxSteps 達到 | 重試 N 次 | 簡單任務 |
| **LLM 判斷** | LLM 說「完成了」 | LLM 決定如何處理 | 一般任務 |
| **外部訊號** | 人類介入 / 超時 | 排程器處理 | 關鍵任務 |
| **混合** | 計數器 + LLM 判斷 | 分級處理 | 生產環境 |

**Pi 的選擇**：LLM 判斷為主，計數器為安全網。簡單但有效。

#### 3. Memory（記憶系統）

```
┌─────────────────────────────────────────┐
│            Memory Hierarchy              │
│                                          │
│  ┌─────────────────────────────────┐    │
│  │  Working Memory (Context Window) │    │
│  │  ~140k tokens, 即時讀寫         │    │
│  │  生命週期: 單次 session          │    │
│  └─────────────────────────────────┘    │
│              ↓ Compaction                │
│  ┌─────────────────────────────────┐    │
│  │  Session Memory (File/DB)        │    │
│  │  session.json, 對話歷史          │    │
│  │  生命週期: 跨 session            │    │
│  └─────────────────────────────────┘    │
│              ↓ Summarization             │
│  ┌─────────────────────────────────┐    │
│  │  Long-term Memory (Vector DB)    │    │
│  │  嵌入向量, 語義搜尋              │    │
│  │  生命週期: 永久                  │    │
│  └─────────────────────────────────┘    │
│              ↓ Learning                  │
│  ┌─────────────────────────────────┐    │
│  │  Procedural Memory (Skill Files) │    │
│  │  SKILL.md, 學到的能力            │    │
│  │  生命週期: 永久, 可版本控制      │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
```

**關鍵洞察**：Procedural Memory（Skill 文件）是 agent 「學到什麼」的載體。Pi 的 skill 系統本質上就是 Procedural Memory 的實作。

#### 4. Tools（工具系統）

| 工具類型 | 範例 | 用途 | 安全等級 |
|---------|------|------|---------|
| **檔案操作** | read, write, edit | 讀寫本地檔案 | ⚠️ 中 |
| **Shell 執行** | bash | 執行任何命令 | 🔴 高 |
| **API 呼叫** | HTTP client, MCP | 存取外部服務 | ⚠️ 中 |
| **資料庫** | SQL client | 資料查詢與修改 | ⚠️ 中 |
| **瀏覽器** | Puppeteer | 網頁互動 | ⚠️ 中 |
| **人類互動** | ask_user_question | 尋求確認 | 🟢 低 |

**工具是 agent 能力的上限**：agent 不能做的事情，取決於它沒有對應的工具。這就是為什麼 Extension 系統如此重要——它讓 agent 的能力可以動態擴展。

---

## 我的觀點

### Agent 的本質是「包裝層」，不是「更大的模型」

很多人誤以為 Agentic AI 需要更大的模型或更複雜的架構。事實相反：

> **Minimal Agent Philosophy**：前沿模型已被 RL 訓練得足夠理解「編碼 Agent」是什麼，不需要 10,000 token 的系統提示詞。

Pi 的實踐驗證了這一點：用最小的包裝（bash + README），讓模型自己理解該做什麼。複雜度是敵人，簡單是朋友。

### 來源的做法 vs 我們的做法

| 面向 | 主流做法 | Pi 的做法 | 觀察 |
|------|---------|----------|------|
| 工具註冊 | MCP Protocol | CLI + README 按需載入 | Pi 更簡單，但 MCP 更標準化 |
| 狀態持久化 | Full Persistence（event sourcing） | 無持久化（while loop + JSON） | Pi 選擇最小複雜度，大多數任務 30 秒內完成 |
| 記憶系統 | Vector DB + RAG | Skill 文件 + Context window | Pi 的 Procedural Memory 是文件驅動，不是向量驅動 |
| 多 Agent | Sub-agent orchestration | Round-table（Pi 主持，不參與） | Pi 用「人類 + 多模型」取代「多 agent」 |
| 安全控制 | Sandbox + Rate limiting | Human-in-the-loop | Pi 信任人類判斷，不設自動防護 |

### 盲區警示

- **Tool Use 是瓶頸**：Agent 的能力上限取決於工具品質。一個不穩定的 bash 工具會讓整個 agent 系統不可靠
- **Memory 是被忽視的痛點**：大多數 agent 的記憶系統還很原始，跨 session 的知識傳遞仍然困難
- **Loop 的停止條件是難題**：「LLM 判斷是否完成」這個機制本身不可靠——LLM 可能過早宣布完成，也可能陷入無限迴圈
- **Multi-agent 的真實成本**：多 agent 協作的 token 消耗是單 agent 的 N 倍，需要仔細評估 ROI

---

## 下一步

- [ ] 比較 Pi vs Claude Code vs Cursor 的 Agent 架構差異
- [ ] 研究 Agent Memory 的最新進展（2026 年的 state of the art）
- [ ] 測試 Minimal Agent Philosophy 在複雜任務上的表現
- [ ] 建立 Agent Durability Patterns 的實作 prototype
- [ ] 評估 MCP Protocol 是否值得導入 Pi

---

## 連結

- Wiki：[[wiki/concepts/agentic-ai|agentic-ai]]
- 相關概念：[[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]]、[[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]]、[[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]]
- 相關種子：[[wiki/entities/pi-agent-core|Pi Agent]]、[[wiki/entities/hermes-agent|Hermes Agent]]、[[wiki/entities/langgraph|LangGraph]]
- 相關專題：🤖 AI Agent 架構研究、🧩 Meta-Harness 元鞍具
