---
title: "Uncle Bob Stopped Reading AI-Generated Code"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [ai-coding, code-generation, verification, software-engineering, uncle-bob]
collection: sources
topics: [agent-coding-practices]
canonical: sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code
provenance_raw: "raw/youtube/uncle-bob-stopped-reading-ai-generated-code.md"
provenance_url: "https://www.youtube.com/watch?v=sClTAvkQDOU"
---

> 來源：[YouTube — Uncle Bob Stopped Reading AI-Generated Code](https://www.youtube.com/watch?v=sClTAvkQDOU)
> 原始字幕：[[raw/youtube/uncle-bob-stopped-reading-ai-generated-code|raw transcript]]

## 一句話

Uncle Bob（Clean Code 作者）宣布不再閱讀 AI 生成的程式碼，改用極端約束（測試、QA、mutation testing）來驗證品質，引發軟體工程界對「工程師角色轉變」的熱議。

## 重點摘要

### Uncle Bob 的核心主張

- **不再閱讀 AI 生成的程式碼**：這是提升生產力的唯一方式
- **用極端約束包圍 agents**：unit tests、Gherkin tests、QA procedures、quality metrics、mutation testing、test coverage
- **高信心來自測試閘道**：程式碼必須通過所有約束和測試才能上線

### 爭議背景

- Uncle Bob（Robert C. Martin）是《Clean Code》和《Clean Architecture》作者
- 推文獲得近 300 萬次瀏覽
- 爭議部分來自他的個人形象（支持者與反對者立場鮮明）

### 程式碼關鍵性光譜（来自 Fio 的影片）

| 位置 | 應用類型 | 閱讀程式碼的必要性 |
|------|---------|------------------|
| 低風險 | 單用戶 web app | 不需要閱讀 |
| 中風險 | SaaS 付費平台 | 可選擇性閱讀 |
| 高風險 | 人命關天系統 | 必須閱讀所有程式碼 |

### 驗證方法（不需閱讀程式碼）

1. **Manual QA** — 打開應用測試功能
2. **Unit Tests** — 自動化單元測試
3. **Integration Tests** — 整合測試
4. **Architecture Tests** — 架構測試（防止 agent 破壞架構）
5. **Skills** — 告訴 agent 如何驗證自己產生的程式碼
6. **Custom Debuggers** — 自訂偵錯工具
7. **Harnesses** — 確保程式碼在產生過程中正確
8. **Mutation Testing** — 變異測試
9. **Code Coverage** — 程式碼覆蓋率（衡量測試覆蓋範圍）

### 核心觀點

- **「所有 AI 程式碼都是 slop」是錯的**：AI 是在人類程式碼上訓練的，所以人類程式碼也是 slop
- **可以用 AI 產生驗證程式碼**：產生更多測試、skills、harnesses 來驗證應用程式碼
- **程式碼只是 artifact**：真正的價值是業務功能，程式碼只是 facilitate value creation
- **工程師角色轉變**：從「寫程式碼」轉向「驗證與工程」

### 未來展望

- 軟體工程師的未來依然光明（強調「工程師」部分）
- 需要重新定義開發者的角色
- 將製作另一部影片深入討論「如果 agents 產生大部分程式碼，什麼仍然重要」

## 相關影片

- Fio 的「Code Criticality Spectrum」影片（原始推文回覆者的分析）
- 作者的 AI Agent Workflow 影片（深入介紹其工作流程）

## 來源

- [[raw/youtube/uncle-bob-stopped-reading-ai-generated-code|Raw transcript — YouTube sClTAvkQDOU]]

## 相關頁面

- [[wiki/concepts/vibe-coding|vibe-coding]] — 自然語言驅動的程式碼生成
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程
- [[wiki/concepts/defect-metrics|defect-metrics]] — 缺陷指標
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Skills 集合
