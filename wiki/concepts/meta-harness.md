---
title: Meta-Harness
type: concept
created: 2026-07-13
updated: 2026-07-30
sources: 3
tags: [harness, optimization, orchestration, multi-agent, llm, research-project]
collection: concepts
topics: [meta-systems]
canonical: concepts/meta-harness
---

# Meta-Harness

> Meta-Harness 是「對 harness 本身的再抽象/最佳化層」——有兩個獨立但共享核心隱喻的主流定義。

---

## 核心概念鍊

```
被測試的元件 → Harness（包裝層） → Meta-Harness（對 harness 的 meta 層）
```

傳統 **harness（鞍具/測試封裝）** 是圍繞一個元件寫的包裝程式碼，負責設定環境、餵入輸入、執行、收集結果、清理環境。Meta-Harness 則是圍繞 harness 本身的層。

---

## 兩種定義

### 定義 A：學術研究 — Harness 自動演化（Stanford IRIS Lab）

出自 [*Meta-Harness: End-to-End Optimization of Model Harnesses*](https://arxiv.org/abs/2603.28052)（Lee et al., 2026）

**問題：** LLM 系統的表現不只取決於模型權重，也取決於 **harness**——圍繞固定基礎模型的程式碼，決定「存什麼、取什麼、秀什麼」。但這些 harness 長期是手寫的。

**解法：** 用一個**演化搜尋迴圈**自動尋找最優的 harness：

```
(1) Proposer（coding agent）讀取完整檔案系統 —
     所有候選 harness 原始碼 + 執行記錄 + 分數
(2) Proposer 提出新的 harness 候選
(3) 在 held-out tasks 上評估
(4) logs 存回檔案系統，loop 重複
```

**關鍵創新（vs 傳統方法）：**

| 面向 | 傳統方法（Self-Refine, OPRO, TextGrad…） | Meta-Harness |
|---|---|---|
| 歷史壓縮方式 | 摘要 / scalar score / sliding window | **完整檔案系統**（原始碼 + traces + scores） |
| 可用 context 量 | ≤ **26K tokens** | ≤ **10M tokens**（proposer 用 grep/cat 自行探索） |
| 診斷能力 | 從分數猜原因 | 可 trace 到特定 harness 決策 |

**成果：**
- Text Classification：比 ACE 方法 **+7.7 分**，4× 更少 context tokens
- Math Reasoning（IMO 級）：5 個 unseen 模型平均 **+4.7 分**
- TerminalBench-2：Claude Opus 4.6 **#2**（76.4%），Claude Haiku 4.5 **#1**（37.6%）

**延伸專案：**
- `harness-forge` — 將 Meta-Harness 實作為 Claude Code skill（尚未建立 Wiki entity 頁）
- `SuperagenticAI-metaharness` — Python library + CLI（尚未建立 Wiki entity 頁）
- 「Don't Train the Model, Evolve the Harness」 — 應用於 Harvey Legal Agent Benchmark

---

### 定義 B：業界工具 — Multi-Agent Orchestration Layer

> 代表性實作：OmniAgent、MindStudio

**問題：** 沒有單一 AI agent（Claude Code、Codex CLI、Gemini…）在所有任務上最強。手動切換會中斷 context、遺失狀態。

**解法：** 在所有 agent 之上加一層 **orchestration layer**，稱為 meta-harness：

```
使用者輸入
    │
    ▼
┌─────────────────────┐
│    Meta-Harness     │ ← 路由、context 管理、錯誤處理
│   (Orchestrator)    │
└──────┬──────┬──────┘
       │      │
       ▼      ▼
  Claude Code  Codex CLI  ...
```

**核心功能：**
- **Agent selection** — 判斷任務適合哪個 agent
- **Context management** — 跨 agent 的共享/轉換 context
- **Session continuity** — 不同 agent 輪流上場，使用者感覺同一場對話
- **Parallel execution** — 同時跑多個 agents
- **Failure handling** — 主要 agent 失敗時 fallback
- **Output normalization** — 統一不同 agent 的回應格式

**與單純 model switching 的區別：**
- Model switching = dropdown（手動選模型）
- Meta-harness = 有邏輯的 orchestration（自動 routing + context 管理 + 錯誤處理）

---

## 兩種定義的關係

它們共享同一個核心隱喻，只是操作對象不同：

| | 定義 A（Stanford） | 定義 B（OmniAgent） |
|---|---|---|
| 操作的對象 | LLM 的程式碼 harness | 完整的 AI coding agents |
| 目標 | 自動尋找最優的 harness 程式 | 讓多個 agents 協同工作 |
| 「meta」的意義 | meta-optimization | meta-orchestration |

兩者並不互斥：你可以用定義 A 的方法去最佳化定義 B 中的某個 agent harness，或者用定義 B 的方法協調多個定義 A 的演化迴圈。

---

### 定義 B 的具體實作

| 實作 | 開發者 | 特色 |
|---|---|---|
| `OmniAgent` | MindStudio | 最早提出 meta-harness 作為多 agent orchestration 的概念；尚未建立 Wiki entity 頁 |
| [[wiki/entities/omnigent]] | Databricks | 開源（Apache 2.0），強調 security policies + 即時協作，roadmap 包含 GEPA 整合 |

## 相關頁面

- `harness-concept` — 傳統測試 harness 與 model harness 的基本概念（尚未建立 Wiki concept 頁）
- [[wiki/concepts/minimal-agent-philosophy]] — Pi 的 minimal agent 哲學，與 meta-harness 的「加法」取徑形成對比
- [[wiki/entities/plannotator]] — 另一種 agent 視覺化審閱工具

## 我們的研究定位

> **Meta-Harness 是我們的研究專題。** 這是我們正在探索的一個重要方向，未來會持續收集相關的概念、工具和實作案例。

### 已收集的樹苗

| 樹苗 | 來源 | 成長階段 | 備註 |
|------|------|---------|------|
| [[wiki/entities/omnigent]] | Databricks | 🌱 種子期 | 開源 meta-harness，但目前不支援 Windows |

### 未來可能的樹苗

- Stanford IRIS Lab 的 Meta-Harness 論文實作（定義 A）
- MindStudio OmniAgent（定義 B）
- 其他 multi-agent orchestration 工具

### 研究方向

1. **理解兩種定義的差異與互補性** — 學術 vs 業界
2. **評估哪些工具適合我們的工作流** — 特別是 Windows 環境
3. **探索 meta-harness 與 Pi 的關係** — 加法取徑 vs 最小化哲學
4. **收集更多實作案例** — 建立比較分析

---

## 來源

- arXiv 論文：*Meta-Harness: End-to-End Optimization of Model Harnesses*（https://arxiv.org/abs/2603.28052）
- GitHub repo：stanford-iris-lab/meta-harness（https://github.com/stanford-iris-lab/meta-harness）
- Yoonho Lee 的說明頁：https://yoonholee.com/meta-harness/
- MindStudio 部落格：What is Meta-Harness for AI Agents（https://www.mindstudio.ai/blog/what-is-meta-harness-ai-agents-omniagent）
- Databricks Blog：Introducing Omnigent（https://www.databricks.com/blog/introducing-omnigent-meta-harness-combine-control-and-share-your-agents）