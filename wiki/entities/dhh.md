---
title: "DHH — Ruby on Rails 作者、AI 工作流實踐者"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [dhh, ruby-on-rails, 37signals, ai-workflow, parallel-agents, lex-fridman]
canonical: entities/dhh
topics: [coding-agent, agent-architecture]
---

# DHH — Ruby on Rails 作者、AI 工作流實踐者

> David Heinemeier Hansson（DHH），Ruby on Rails 框架創建者、37signals（Basecamp）創辦人。2026 年起公開分享其大規模並行 AI Agent 工作流實踐。

## 基本資訊

| 項目 | 內容 |
|---|---|
| 全名 | David Heinemeier Hansson |
| 網名 | DHH |
| 已知時長 | 約 20+ 年軟體開發經驗 |
| 代表作 | Ruby on Rails、Basecamp、REWORK |
| 部落格 | [dhh.dk](https://dhh.dk) |

## AI 工作流實踐（2026 年）

### 時間線

| 時期 | 工作流狀態 | 來源 |
|------|-----------|------|
| 2026 年 1 月 | 離「90% 代碼由 Agent 寫」還很遠，認可監督式協作 | `≈[00:09]` |
| 2026 年 3 月 | 把 Basecamp 改造成 Agent 可訪問的系統（API + CLI） | `≈[02:49]` |
| 2026 年 4 月 | 兩個模型並行（一快一強），中間夾 Neovim + LazyGit 做 Review | `≈[00:09]` |
| 2026 年 8 月 | 4-5 台小主機，~16 條 thread 並行，使用 Amabot 協調 | `≈[00:09]` |

### 核心工具棧

| 工具 | 角色 | 說明 |
|------|------|------|
| **Herdr** | 可觀測性層 | 帶 Agent 通知的 tmux，提供 working/blocked/idle/done 狀態偵測 `≈[02:49]` |
| **Amabot** | 協調者層 | 定時掃 issue/PR，篩選後彙總成決策郵件，未公開源碼 `≈[05:18]` |
| **Basecamp** | 任務管理 | Agent 可訪問的 API + CLI，異步任務隊列 `≈[02:49]` |
| **GL.iNet Comet** | 硬體 | IP KVM，HDMI + USB 直接接管機器，遠端救回 `≈[01:56]` |
| **Tailscale** | 網路 | WireGuard 底層，不暴露公網埠 `≈[01:56]` |
| **mise** | 工具鏈管理 | 管理 Agent CLI 版本與環境 `≈[01:56]` |

### 核心哲學

> 「循環裡的那個人才是極限，機器還有余量，人先跟不上了。」 `≈[00:09]`

> 「執行變得廉價之後，判斷力在漲價。」 `≈[07:43]`

> 「很多組織本來就不缺實現能力，卡住他們的是想法、視野和品味。Execution 廉價了，Judgment 在漲價。」 `≈[08:33]`

### 瓶頸遷移四階段

1. Agent 等待時間 → 多 Agent 並行 `≈[06:34]`
2. 不知道誰在等你 → Herdr 可觀測性層 `≈[06:34]`
3. 任務和上下文管理 → 異步任務隊列 + Plan 載體 `≈[06:34]`
4. Review 數量爆炸 → Agent 初審 + 協調者摘要 `≈[06:34]`

### 關鍵觀察

- **Agent 跑得越快，DHH 能維持的 thread 反而越少**，因為極限從來不在機器那邊，在人消費輸出的速度上 `≈[06:34]`
- **資深工程師從 Agent 身上拿到的增益比初級工程師大得多**，因為資深的人有能力判斷輸出能不能上生產 `≈[06:34]`
- **多 Agent 的調度器最終長得像 Jira 和 Basecamp**，長得像 issue 隊列，不會長成一排聊天窗口 `≈[02:49]`

### 失敗案例

- **Basecamp 5**：設計師用 Agent 實現需求，單個 PR 合理但合起來架構碎了 `≈[04:03]`
- **Amabot 並發衝突**：主協調 Agent 踩到另一個 Agent 的工作 `≈[05:18]`
- **GitHub 封號**：8 個 Agent 做 QA，12 秒內提交 28 個問題，被判定為 spam `≈[05:18]`

### 量化數據

- Omarchy 階段三個月合併 1000+ PR，訪談時還有約 400 個排隊 `≈[04:03]`
- 100 個 Agent 事件壓縮成 10 個人類決策（透過 Amabot）`≈[05:18]`

## 與其他頁面的關係

| 頁面 | 關係 |
|------|------|
| [[wiki/entities/herdr\|herdr]] | DHH 使用的可觀測性工具（非 DHH 開發） |
| [[wiki/concepts/meta-harness\|Meta-Harness]] | 本影片未提及，但瓶頸遷移與協調者層概念有互補性 |

## 來源

- [[wiki/sources/2026-09-02-dhh-ai-workflow-practices|DHH：AI 工作流的實踐經驗 — 16 條並行工作流與瓶頸遷移]]

## 相關頁面

- [[wiki/entities/herdr|herdr]] — DHH 的可觀測性工具
- [[wiki/concepts/meta-harness|Meta-Harness]] — 協調者/orchestration 概念（未在本影片出現）
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — Agent 工作流模式
