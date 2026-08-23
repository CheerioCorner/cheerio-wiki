---
title: "AI Coding Workflow — 結構化 AI 編碼工作流程"
type: concept
created: 2026-07-22
updated: 2026-07-22
sources: 2
tags: [ai-coding, workflow, methodology, planning]
collection: concepts
topics: [ai-development-tools]
canonical: concepts/ai-coding-workflow
---

> 一套結構化的 AI 編碼工作流程，把模糊想法變成可追踪、可審查的程式碼變更。

## 核心流程

```
想法 → grill with docs → spec → tickets → implement → code review → commit
```

### 各階段職責

| 階段 | 輸入 | 輸出 | 關鍵問題 |
|------|------|------|---------|
| **Grill with Docs** | 模糊想法 | 共識 + 領域模型 | 「我們真的理解要做什麼嗎？」 |
| **To Spec** | 討論記錄 | 正式規格書 | 「目的地長什麼樣？」 |
| **To Tickets** | 規格書 | 可執行 tickets | 「怎麼一步步到達？」 |
| **Implement** | Tickets + Spec | 程式碼變更 | 「實際動手做」 |
| **Code Review** | 變更 + Spec + Standards | 審查報告 + commit | 「做出來的東西對嗎？」 |

## 設計原則

### 1. Context Window 感知
- 每個 ticket 應能在一個「smart zone」（~140k tokens）內完成
- 超過 smart zone → 拆 ticket 或開新 session
- Spec + Tickets 是跨 session 的「狀態持久化」機制

### 2. 規格驅動（Spec-driven）
- 先有規格，再有實作
- Code review 對照 spec 檢查完整性
- 避免 agent「做完就忘了最初目標」
- 詳見 [[wiki/concepts/spec-driven-development|Spec-Driven Development]] 概念頁

### 3. Sub-agent 審查
- Code review 用獨立 sub-agent 執行
- 避免 self-review 的認知偏見
- 雙軸審查：Spec compliance + Coding standards

### 4. 可選式流程（Opt-in）
- 每個階段可獨立使用
- 小型工作 → grill with docs + implement
- 大型工作 → 完整五階段
- 不強制，按需組合

## 與其他方法論的比較

| 方法 | 流程控制 | Context 管理 | 適用場景 |
|------|---------|-------------|---------|
| **AI Coding Workflow** | 鬆耦合，可選 | Smart zone 感知 | 中大型功能開發 |
| [[wiki/concepts/vibe-coding|Vibe Coding]] | 無 | 無 | 快速原型 |
| [[wiki/concepts/rapid-application-development|RAD]] | 四階段迭代 | 無 | 快速原型（歷史先驅） |
| **GSD/BMAD** | 強制全流程 | 固定 | 全專案管理 |
| [[wiki/concepts/legacy-code-modernization\|Legacy Code Modernization]] | Plan→Execute→Verify 三步 | 單一變更原則 + checklist | Brownfield 現代化（無文件/無測試/無 CI/CD）|

> RAD（1991）是 AI coding workflow 的歷史先驅，兩者都強調「先建原型再定 spec」的思路。

## 實作要點

### Issue Tracker 整合
- Spec 和 tickets 需要持久化儲存
- 支援：GitHub Issues、Local Markdown、Jira、Linear
- 本地開發 → Local Markdown 最簡單

### Token 效率
- User-invoked skill 佔用極少 context（~660 tokens）
- Skill 描述精簡，不自動滲透 context
- 按需載入，不強制預載

## 來源

- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|mattpocock/skills — 完整 AI Coding 工作流程教學]]
- [[wiki/sources/2026-07-11-mattpocock-skills|mattpocock/skills — 工程實踐 Skills 集合]]

## 相關頁面

- [[wiki/concepts/ai-code-review|AI Code Review]] — Code Review 階段的詳細工具與方法論
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — 實作此工作流程的 skills repo
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/vibe-coding|vibe-coding]] — 自然語言驅動的程式碼生成，與本工作流程互補
- [[wiki/concepts/legacy-code-modernization|Legacy Code Modernization]] — 針對 brownfield 現代化的特化版本（多了 sandbox 隔離 + recon-first + 三層驗證）
- [[wiki/concepts/rapid-application-development|RAD]] — 歷史先驅：1991 年的快速應用開發方法論
- [[wiki/concepts/spec-driven-development|Spec-Driven Development]] — 規格驅動開發的核心機制
- [[wiki/concepts/late-conversion|late-conversion]] — 型別策略
