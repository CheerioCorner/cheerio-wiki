---
title: "Skill 設計方法論 — 從人類意圖到 Skill 邊界"
type: concept
created: 2026-08-17
updated: 2026-08-17
sources: 1
tags: [skill, design-methodology, intention-first, event-storming, ddd, bounded-context, decision-tree, domain-knowhow]
topics: [skill-system, ai-development-tools]
canonical: concepts/skill-design-methodology
provenance:
  - kind: local
    path: "Report/devops-skill-presentation/"
---

# Skill 設計方法論 — 從人類意圖到 Skill 邊界

> 在「怎麼寫好一支 skill」之前，先回答「這支 skill 的邊界在哪裡」。結合 Intention-First、Event Storming、DDD 與決策樹的頂層設計方法論。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的設計層，填補既有專題「如何決定 skill 邊界」的空白。**

## 核心主張

既有 Agent Skills 專題假設「skill 邊界已經確定」，分別談怎麼寫（best practices）、怎麼用（API）、怎麼管（governance）。本方法論填補的是**更上游**的空白——邊界怎麼決定。

**關鍵洞察：流程圖不是起點，領域知識才是。** 地基不穩，上面的流程圖與 skill 都會塌。

## 五步驟方法論

### Step 1：Intention-First 分析順序

先不要急著拆 Skill，先看員工真正想完成什麼。

| 順序 | 步驟 | 說明 |
|------|------|------|
| 1 | **Human Intention** | 人的意圖——為什麼要做這件事？ |
| 2 | **Event** | 狀態變化——這次更新代表什麼？ |
| 3 | **Boundary** | 責任邊界——哪些是同一件事？ |
| 4 | **Skill** | 可執行框架——切出可獨立執行的單元 |

**核心：系統設計要從人的意圖開始往下拆。** 太早看系統（有哪些 API、資料表怎麼設計）會迷失方向。

### Step 2：Event Storming 拆邊界

把「發生的事」攤在時間軸上，從中找出邊界。

**判準：動詞不一樣的地方，就是邊界。**

- 「標記完成」→ 寫入
- 「記錄工時」→ 寫入
- 「新增工作」→ 寫入
- 「查進度」→ 讀取 ← **動詞改變了，邊界在這裡**

對應 DDD 概念：
- **Event Storming**：把事件攤在時間軸上找邊界
- **Bounded Context**：同一個詞在不同情境是不同的東西
- **Single Responsibility**：一支只做一件事
- **Design-First**：先把邊界與契約想清楚，才動手寫

### Step 3：Domain Know-how 地基

人類先把意圖與領域脈絡講清楚，AI 才可能把 skill 切在對的邊界上。

```
┌─────────────────────────┐
│  成品：13 支 Skills      │  ← 表象
├─────────────────────────┤
│  規格：意圖決策樹        │  ← 流程圖
├─────────────────────────┤
│  地基：Domain Know-how   │  ← 人類餵養的領域知識
│  12 條 Human 意圖        │
│  ＋ 領域脈絡             │
└─────────────────────────┘
```

**12 條 Human 意圖**（以 DevOps Board 為例）：
- Collection 層級（2 條）：調整 Role、複製 Process
- Project 層級（2 條）：複製 Settings、管理 Wiki
- Team 層級（2 條）：建立 Iterations、調整 Board 設定
- Board 層級（2 條）：取得 Board 設計、取得 WIT 資料
- Work 層級（3 條）：查工作/工時、新增/更新工作、指派/移 Sprint
- Analytics 層級（3 條）：分析案子/工作狀況、設計分析規則

### Step 4：決策樹方法

「流程圖，就是規格。」

把 12 個意圖畫成一棵決策樹：
- 根節點：Human 意圖（自然語言）
- 中間節點：「跟哪個層級／類別有關？」
- **葉節點：每一個 = 一支 skill 的邊界**

12 意圖 + 1 獨立 Auth = **13 支 skill**

### Step 5：交辦包設計

把每支 skill 想成「交辦一件事給一位新同事」：

| 檔案 | 角色 | 必要性 |
|------|------|--------|
| `SKILL.md` | 交辦單：怎麼做、規則是什麼 | 必要 |
| `scripts/` | 現成工具：照著跑就對 | 選用 |
| `references/` | 參考手冊：卡住才翻 | 選用 |
| `assets/` | 空白範本：照格式填 | 選用 |

**Progressive Disclosure**：先看標籤（name + description）→ 再讀交辦單（SKILL.md）→ 卡住才翻附錄（scripts/references/assets）

## 範例：DevOps Board 的切邊界過程

**一句人話**：「升級案處理完已知問題、運作正常，接下來我要準備結案會議簡報——幫我更新工作。」

**Event Storming 攤開**：
1. 標記目前工作完成（寫入）
2. 記錄花了多少工時（寫入）
3. 新增結案簡報工作（寫入）
4. 查目前進度與工時（讀取）← 動詞改變

**切邊界**：
- work-maintainer（寫入）：①②③
- work-status（讀取）：④

**三個好處**：叫得對 Skill（動詞 → 哪一支）、風險隔離（寫的跟分開）、各修各的（獨立維護）

## 來源

- [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支 SKILL？· DevOps Board 系列]] — Cheer 自製簡報 P09-P17

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 上層導覽
- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]] — 決定邊界後怎麼寫
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 生命週期管理
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]] — 碰到限制後的下一層
