---
title: "Uncle Bob (Robert C. Martin) — Clean Code 作者、軟體工程資深實踐者"
type: entity
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [uncle-bob, robert-c-martin, clean-code, software-engineering, methodology]
collection: entities
topics: [agent-coding-practices]
canonical: entities/uncle-bob
---

> Uncle Bob（Robert C. Martin）是《Clean Code》和《Clean Architecture》作者，軟體工程界的標誌性人物，近期因「不再閱讀 AI 生成的程式碼」言論引發熱議。

## 基本資訊

| 項目 | 內容 |
|------|------|
| 全名 | Robert C. Martin |
| 綽號 | Uncle Bob |
| 職業 | 軟體工程師、作家、講師 |
| 關鍵著作 | 《Clean Code》、《Clean Architecture》 |
| 程式設計起始 | 1960 年代末 |
| 知名觀點 | SOLID 原則、Clean Code 方法論 |

## 核心觀點

### 對 AI 程式碼的立场

**「我不再閱讀 AI 生成的程式碼」**

這是他在 2026 年初的病毒式推文核心內容，獲得近 300 萬次瀏覽。

**他的策略：**
- 用極端約束包圍 AI agents
- 不直接閱讀程式碼，而是透過測試和 QA 流程驗證
- 高信心來自程式碼通過所有約束的「閘道」

**他使用的約束類型：**
- Unit tests
- Gherkin tests（行為驅動開發）
- QA procedures
- Quality metrics
- Mutation testing
- Test coverage
- 其他品質保證流程

### 方法論基礎

Uncle Bob 的方法論根植於：
1. **測試驅動開發（TDD）**：先寫測試，再寫程式碼
2. **品質約束**：用自動化測試和品質指標包圍程式碼
3. **架構完整性**：用架構測試防止破壞既有的設計決策

## 爭議與影響

### 為什麼這個觀點引發熱議

1. **發言者的影響力**：作為 Clean Code 作者，他的觀點有重量
2. **挑戰傳統觀念**：許多開發者將「閱讀程式碼」視為核心職責
3. **未來焦慮**：引發「工程師角色是否被取代」的討論

### 兩極化反應

- **支持者**：認為這是務實的工程思維，程式碼只是 artifact
- **反對者**：認為不閱讀程式碼是不負責任，尤其在高風險系統

## 與其他概念的關係

| 概念 | 關係 |
|------|------|
| [[wiki/concepts/vibe-coding]] | 相關：兩者都涉及 AI 程式碼生成，但方法論不同 |
| [[wiki/concepts/ai-coding-workflow]] | 互補：結構化工作流程是驗證的基礎 |
| [[wiki/concepts/defect-metrics]] | 相關：mutation testing 和 code coverage 是他的核心工具 |

## 來源

- [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code|Uncle Bob Stopped Reading AI-Generated Code]] — YouTube 影片分析

## 相關頁面

- [[wiki/entities/theo-t3gg|Theo (t3gg)]] — 互補觀點：用 cheap code 驗證 important code
- [[wiki/concepts/code-importance-spectrum|Code Importance Spectrum]] — 程式碼重要性光譜框架
- [[wiki/concepts/vibe-coding|vibe-coding]] — 自然語言驅動的程式碼生成
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Skills 集合
