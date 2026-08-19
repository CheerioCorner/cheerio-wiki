---
title: "Agent Skills — Skill 系統根概念"
type: concept
created: 2026-08-14
updated: 2026-08-17
sources: 5
tags: [skill, progressive-disclosure, agent-extensibility, modular-capability]
topics: [skill-system, ai-development-tools, agent-infrastructure]
canonical: concepts/agent-skills
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-agent-skills.md"
  - kind: raw
    path: "raw/web/2026-08-14-skill-撰寫最佳實踐.md"
  - kind: raw
    path: "raw/web/2026-08-14-企業級-skills.md"
  - kind: raw
    path: "raw/web/2026-08-14-anthropicsskills-public-repository-for-agent.md"
---

# Agent Skills — Skill 系統根概念

> Skill 是**可重複使用、基於檔案系統的模組化能力單元**，為 AI Agent 提供特定領域的專業知識，將通用代理轉變為專家。

## 基礎層：Skill 是什麼

### 核心定義

Skill = **指令 + 中繼資料 + 可選資源**（腳本、範本、參考文件），封裝成一個目錄。與一次性 prompt 不同，Skill 會按需載入，建立一次、自動使用。

### 三層漸進式揭露（Progressive Disclosure）

這是 Skill 系統最關鍵的架構設計——確保只有相關內容佔用 context window：

| 層級 | 載入時機 | Token 成本 | 內容 |
|------|---------|-----------|------|
| **第 1 層：中繼資料** | 始終（啟動時） | ~100 tokens/Skill | YAML `name` + `description` |
| **第 2 層：指令** | Skill 被觸發時 | <5k tokens | SKILL.md 主體 |
| **第 3 層以上：資源** | 按需 | 存取前為零 | 腳本、參考檔案、模板 |

**關鍵設計**：腳本透過 bash 執行，只有**輸出**進入 context（腳本程式碼本身永不載入），這使得捆綁大量資源的 Skill 不會造成 context 負擔。

### Skill 結構

```
my-skill/
├── SKILL.md              # 主要指令（觸發時載入）
├── REFERENCE.md          # 參考資料（按需載入）
├── FORMS.md              # 進階指南（按需載入）
└── scripts/
    ├── analyze.py        # 工具腳本（執行，不載入 context）
    └── validate.py       # 驗證腳本
```

### SKILL.md Frontmatter

```yaml
---
name: my-skill-name          # ≤64 字元，小寫/數字/連字號
description: ...             # ≤1024 字元，功能 + 使用時機
---
```

## 基礎層：怎麼寫好 Skill

> 詳見 [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]]

### 核心原則

1. **簡潔**：只添加 Claude 尚未擁有的上下文，預設假設 Claude 已經聰明
2. **適當自由度**：匹配任務脆弱性（窄橋→低自由度、開闊原野→高自由度）
3. **跨模型測試**：Haiku/Sonnet/Opus 各有不同需求

### 結構規範

- SKILL.md < 500 行
- 參考距離只一層深（避免深層巢狀）
- 命名用動名詞形式（`processing-pdfs`）
- 描述用第三人稱，包含功能**以及**觸發條件

### 常見模式

- **範本模式**：嚴格 vs 彈性指引
- **範例模式**：輸入/輸出配對
- **條件式工作流程**：決策點引導
- **驗證循環**：執行驗證器 → 修復 → 重複

## 基礎層：API 中如何使用

> 詳見 [[wiki/concepts/agent-skills-api-usage|Agent Skills API 使用方法]]

### Claude API 整合

透過 Messages API 的 `container` 參數指定 Skills：

```yaml
container:
  skills:
    - type: anthropic       # 或 "custom"
      skill_id: pptx
      version: latest
tools:
  - type: code_execution_20250825
    name: code_execution
```

需要：程式碼執行工具 + beta header `skills-2025-10-02`

### 跨平台使用

| 平台 | 安裝方式 | 共享範圍 |
|------|---------|---------|
| Claude API | Skills API 上傳 | 工作區 |
| Claude Code | 本地目錄 + Plugin Marketplace | 個人/專案 |
| claude.ai | Settings > Features 上傳 zip | 個人 |

⚠️ 自訂 Skills **不跨平台同步**。

## 基礎層：企業級應用

> 詳見 [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]]

### 安全審查

5 級風險評估（程式碼執行/指令操縱/MCP 引用/網路存取/硬編碼憑證）+ 8 步審查清單。

### 生命週期管理

規劃 → 建立與審查 → 測試 → 部署 → 監控 → 迭代或棄用。

### 組織策略

- 每請求最多 **8 個 Skills**（召回限制）
- 從具體開始，之後再整合
- 基於角色的套件（銷售/工程/財務）

## 進階層：已知限制

以下限制來自 7 篇 Anthropic 官方來源的明確記載：

### API 層限制

| 限制項 | 說明 | 來源 |
|-------|------|------|
| 每請求 Skills 上限 | **8 個** | overview + API guide |
| 上傳大小 | < **30 MB**（未壓縮） | API guide |
| 網路存取 | API 容器**無網路存取** | overview |
| 套件安裝 | 無執行時安裝，僅預先安裝 | overview |
| 模型限制 | 需要支援程式碼執行工具的模型 | quickstart |

### 平台層限制

| 限制項 | 說明 |
|-------|------|
| 跨平台同步 | 自訂 Skills **不跨平台**，需分別管理 |
| claude.ai 共享 | 僅限個人，不支援組織級分發 |
| Claude Code 共享 | 個人或專案，可透過 Plugin 共享 |
| ZDR 適用性 | Skills **不在** Zero Data Retention 範圍內 |

### 品質與營運限制

| 限制項 | 說明 |
|-------|------|
| 召回準確性 | Skills 過多時中繼資料競爭注意力，可能選錯 |
| 版本管理 | 新版本是完整快照（非差異），30MB 限制 |
| 監控 | 目前**無法透過 API 取得使用分析** |
| 使用分析 | 需自訂應用層日誌記錄 |

### 設計限制（隱含）

| 限制項 | 說明 |
|-------|------|
| 無巢狀 Skill | Skill 之間無法直接呼叫或嵌套 |
| 無動態組合 | 無法在執行時動態合併多個 Skill |
| 無優先權機制 | 多 Skill 衝突時無內建仲裁 |
| 沙箱隔離 | API Skills 無法存取外部系統 |

### 認知層限制（LLM 推理失敗模式）

> 2026-08-17 新增。與上述系統面限制互補——那些是「系統會怎麼壞」，這些是「LLM 推理會怎麼壞」。

| 失敗模式 | 說明 | 對應閘門 |
|---------|------|----------|
| **選錯 Skill** | 13 支擺在那裡，AI 挑錯了那一支 | 好的 description（觸發詞寫準，AI 才叫得對人） |
| **誤解意圖** | 話講得太短、太含糊，理解就偏了 | 安全門與確認點（關鍵動作卡一次人類） |
| **自行判斷，跳過步驟** | 它覺得不是必要條件，就自己省略掉 | 真實環境測試（文件說可以不算數，跑過才算） |
| **資訊不足，開始幻覺** | 查不到就自己編一個看起來合理的答案 | 必要時交給 Hook/Extension（用平台層強制流程，不靠 AI 自律） |

**核心**：承認 LLM 的限制，不是讓 AI 不出錯，而是讓它出錯時人類接得住。

## 進階層：複雜 Skill 組合與跨框架比較

> 詳見 [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]]

三大主流 Agent 框架（Claude Code、GitHub Copilot、Pi Agent）在處理複雜、組合技 Skill 時的機制與設計取捨各不相同：

| 面向 | Claude Code | GitHub Copilot SDK | Pi Agent |
|------|------------|-------------------|----------|
| Skill 格式 | SKILL.md（三層漸進式揭露） | SKILL.md（格式兼容，per-agent preload） | SKILL.md（markup 注入） |
| 組合方式 | 多 Skill 同時載入（≤8/請求） | 多 Skill + per-agent eagerly preload | 逐一手動觸發 |
| 巢狀/委派 | ✅ Subagents（獨立 context） | ✅ Custom Agents + Sub-agent Orchestration + Fleet Mode | ❌ 刻意不做 |
| 工具鏈整合 | MCP（開放標準） | MCP（per-agent）+ Custom Tools | Extension System |
| 鉤子/生命週期 | ✅ Hooks | ✅ 7+ Hooks（完整 lifecycle） | ✅ Extension Events |
| 訊息控制 | ❌ | ✅ Steering + Queueing | ❌ |
| 並行編排 | ⚠️ Subagents | ✅ Fleet Mode（SQL todos） | ❌ |

> 📝 本章節已根據 GitHub 官方文件（22 篇）修正。先前基於第三方文章的多處錯誤描述已更正。詳見比較頁的「修正紀錄」。

## 跨平台標準：agentskills.io

[agentskills.io](https://agentskills.io/) 是**跨平台開放標準**，定義 Skill 的可移植格式。各框架的關係：

| 面向 | agentskills.io | Anthropic 實作 | Pi Agent |
|------|---------------|---------------|----------|
| 性質 | 開放標準 | 廠商實作 | 廠商實作 |
| 格式 | SKILL.md 格式定義 | 三層揭露 + 安全模型 | markup 注入 + read tool |
| 分發 | `npx skills` / Registry | API 上傳 / 本地目錄 | 本地安裝 / npm |

## 來源

- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills 概覽]]
- [[wiki/sources/2026-08-14-skill-writing-best-practices|撰寫最佳實踐]]
- [[wiki/sources/2026-08-14-agent-skills-enterprise-deployment|企業級部署]]
- [[wiki/sources/2026-08-14-anthropics-skills-github-repo|anthropics/skills Repo]]

## 進階層：Meta-Skills 生態迴圈

> 「做 Skill 的，也是 Skill」——用一組 meta-skills 來幫我做 skill，高品質 skill 不是一次寫對，而是一輪一輪被問清楚、找參考、生成、對齊、真跑、沉澱出來的。

### Skills Help Skills 迴圈

1. **grill-me**（反問人類）：讓 AI 反過來質詢，把模糊需求問清楚，逼出真正的意圖
2. **find-skills**（找現成的）：先找當下可用的 skill 或外部參考，不重新發明輪子
3. **skill-creator**（生第一版）：產生 skill 骨架與 description，把意圖快速變成初稿
4. **plannotator UI**（視覺化對齊）：把規劃視覺化，拿來跟 AI 討論與對齊
5. **feedback / memory**（沉澱經驗）：把真實決策、破例與踩雷跨 session 記住，下次不重犯

↺ 修正後回到步驟 1，越跑越準

> 📝 與 [[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill]] 的差異：SkillOpt 是**文字級**自動修補 SKILL.md 本身；本節的 Meta-Skills Loop 是**多支 skill 協作**完成另一支 skill 的發現/建立/規劃/回饋（系統架構層）。

## 進階層：Beyond Skill — 下一層抽象

> 當 Skill 不足以控制流程，下一層是什麼？詳見 [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]。

| 層級 | 本質 | 驅動方式 | 適用場景 |
|------|------|---------|----------|
| **Skill** | 宣告式指導 | 被動（人類叫它） | 重複性任務、有明確流程 |
| **Hook** | 事件驅動強制 | 主動（事件觸發） | 安全把關、合規審計 |
| **Extension** | 平台能力延伸 | 常駐服務 | 跨系統整合、自訂工具 |

**核心**：Skill 讓 AI 變懂；Hook 強制它照規則走；Extension 延伸到平台層。

## 相關頁面

### 基礎層導航
- [[wiki/concepts/skill-design-methodology|Skill 設計方法論]] — 設計層：如何決定 skill 邊界 🆕
- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]] — 實作層：怎麼寫好一支 skill
- [[wiki/concepts/agent-skills-api-usage|Agent Skills API 使用方法]] — 整合層：API 如何使用
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]] — 治理層：安全審查與生命週期

### 進階層導航
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]] — 框架層組合機制
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]] — 擴充層：Skill/Hook/Extension 🆕

### 實體導航
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Anthropic 官方系統
- [[wiki/entities/agentskills-io-standard|agentskills.io 標準]] — 跨平台標準
- [[wiki/entities/claude-code|Claude Code]] — Claude Code 的 Skill 使用
- [[wiki/entities/github-copilot|GitHub Copilot]] — Copilot Extensions
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Pi 的 Skill 機制

### 概念導航
- [[wiki/concepts/harness|Harness]] — LLM 的驅動層 🌱
- [[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill]] — 技能優化元技能
