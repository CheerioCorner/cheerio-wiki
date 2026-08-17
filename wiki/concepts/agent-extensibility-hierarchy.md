---
title: "Agent 擴充架構層級 — 從 Skill、Hook 到 Extension 的能力邊界"
type: concept
created: 2026-08-17
updated: 2026-08-17
sources: 1
tags: [agent-architecture, skill, hooks, extensions, extensibility, subagents]
topics: [skill, ai-agent, agent-infrastructure]
canonical: concepts/agent-extensibility-hierarchy
provenance:
  - kind: local
    path: "Report/devops-skill-presentation/"
---

# Agent 擴充架構層級 — 從 Skill、Hook 到 Extension 的能力邊界

> 當 Skill 不足以控制流程，下一層是什麼？系統化比較 Skill / Hook / Extension 的能力光譜與適用場景。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的擴充層。**

## 核心主張

Skill 讓 AI「變懂」，提供知識與流程。但要**強制**它照規則走，需要另一層東西。三層各有擅場，不能互相取代。

## 三層能力光譜

### Layer 1：Skill（本場主角）

| 面向 | 說明 |
|------|------|
| **本質** | 宣告式指導——提供知識、流程、規則 |
| **驅動方式** | 被動：要靠人類記得叫它 |
| **能力範圍** | 依意圖執行流程與決策、I/O 走腳本、寫入過安全門 |
| **優勢** | 邊界清楚、可獨立、可累積、跨平台可攜 |
| **限制** | 要靠人類記得叫它；LLM 可能選錯 skill、誤解意圖、跳過步驟、產生幻覺 |
| **適用場景** | 重複性任務、有明確流程的工作、需要領域知識的操作 |

### Layer 2：Hook（之後另一場）

| 面向 | 說明 |
|------|------|
| **本質** | 事件驅動強制——在生命週期中攔截、檢查、強制規則 |
| **驅動方式** | 主動：事件觸發，不必人類每次記得叫 |
| **能力範圍** | 存檔、提交、對話節點等事件的攔截與把關 |
| **優勢** | 補 Skill 的「被動」——強制流程，不靠 AI 自律 |
| **限制** | 需要平台支援（Extension 系統）；設定成本較高 |
| **適用場景** | 安全把關、合規審計、不可逆操作的前置檢查 |

**Pi Agent 的 Hook 實現**：Extension 系統的事件（`tool_call`、`agent_settled`、`session_shutdown` 等）

**Claude Code 的 Hook 實現**：三層架構（Event/Matcher/Handler），31 種 Event、5 種 Handler，deterministic 強制執行 → [[wiki/concepts/claude-code-hooks-architecture|詳見 Claude Code Hooks 架構]]

### Layer 3：Extension / Agent（之後另一場）

| 面向 | 說明 |
|------|------|
| **本質** | 平台原生能力延伸——把角色、權限、工具封裝成執行單元 |
| **驅動方式** | 常駐服務、跨系統整合 |
| **能力範圍** | Skill 觸及不到的平台能力（自訂工具、UI、Provider 等） |
| **優勢** | 補 Skill 的「範圍」——延伸到平台層 |
| **限制** | 開發成本最高；與平台耦合 |
| **適用場景** | 需要自訂工具、需要存取平台 API、需要常駐服務 |

## 選擇決策樹

```
需要 AI 執行重複性任務？
├── 是 → 需要強制照規則走？
│   ├── 否 → 用 Skill
│   └── 是 → 需要跨系統整合？
│       ├── 否 → 用 Hook（Extension 事件）
│       └── 是 → 用 Extension / Agent
└── 否 → 不需要 skill
```

## 與既有頁面的關係

| 面向 | 本頁 | 既有頁面 |
|------|------|---------|
| Skill 定義 | 不重複，引用根概念 | `agent-skills.md` 詳述 |
| Hook 機制 | 概念層介紹 | `wiki/concepts/harness.md` 詳述 Pi 的 hook 系統 |
| Extension 開發 | 概念層介紹 | Pi 文件 `docs/extensions.md` 詳述 |
| 組合技 | 不重複 | `skills-complex-composition-comparison.md` 比較框架層差異 |

## 來源

- [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支 SKILL？· DevOps Board 系列]] — Cheer 自製簡報 P27-P28

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 上層導覽
- [[wiki/concepts/skill-design-methodology|Skill 設計方法論]] — 設計層方法論
- [[wiki/concepts/harness|Harness]] — LLM 的驅動層（Hook 系統）
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]] — 框架層組合機制
