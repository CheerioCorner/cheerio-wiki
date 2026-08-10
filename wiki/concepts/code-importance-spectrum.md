---
title: "Code Importance Spectrum — 程式碼重要性光譜"
type: concept
created: 2026-08-10
updated: 2026-08-10
sources: 2
tags: [code-importance, verification, software-engineering, ai-coding]
collection: concepts
topics: [ai-agent]
canonical: concepts/code-importance-spectrum
---

> 一個用來判斷應該閱讀多少程式碼的框架：從不重要的 slop 到人命關天的關鍵系統，不同層級需要不同的驗證策略。

## 核心概念

**Code Importance Spectrum** 是一個框架，用來分類程式碼的重要性，並據此決定閱讀和驗證的策略。

### 兩種版本

#### 1. Fio 的 3 層光譜（較早）

| 位置 | 應用類型 | 閱讀程式碼的必要性 |
|------|---------|------------------|
| 低風險 | 單用戶 web app | 不需要閱讀 |
| 中風險 | SaaS 付費平台 | 可選擇性閱讀 |
| 高風險 | 人命關天系統 | 必須閱讀所有程式碼 |

#### 2. Theo 的 4 層光譜（更詳細）

| 層級 | 重要性 | 範例 | 閱讀策略 |
|------|--------|------|----------|
| **Tier A — Slop** | 不重要 | 寵物商店網站、side projects | 不需要閱讀 |
| **Tier B — 重要但可恢復** | 一般商業應用 | SaaS 付費平台 | 選擇性閱讀 |
| **Tier C — 非常重要** | 壞掉會出事 | 金融 ERP、核心基礎設施 | 大量閱讀 + 驗證 |
| **Tier D — 人命關天** | 壞掉會死人 | 心律調節器韌體 | 必須閱讀每一行 |

## 核心原則

### 1. 不要高估自己程式碼的重要性

- 大多數工程師認為自己的程式碼比實際更重要
- 人們傾向認為 talking 的人在光譜上與自己同級或更高
- 這使得對話變得不可能

### 2. 重要程式碼只是工作的一小部分

- 如果你 90% 的時間都在處理 Tier D 程式碼，那你接觸的程式碼還不夠多
- 沒有人 100% 的時間都在寫關鍵系統（否則他們不是好工程師）

### 3. 用 Cheap Code 驗證 Important Code

**Theo 的核心論證：**

> 「如果你的程式碼重要到每一行都要閱讀，那你應該生成更多 cheap code 來驗證它」

| 程式碼類型 | 策略 |
|-----------|------|
| Tier A/B | 可以閱讀較少，生成較多 |
| Tier C | 大量閱讀 + 生成驗證程式碼 |
| Tier D | 必須閱讀每一行，但也要生成大量驗證程式碼 |

### 4. 程式碼不只是用來合併的

- 程式碼可以用來驗證假設
- 程式碼可以用來測試理論
- 程式碼可以用來 debug
- 程式碼可以用來壓力測試
- **Code is useful for things other than merging**

## 驗證策略

### 對於 Tier C/D 程式碼

1. **生成大量 slop 來驗證**：100 行重要程式碼 → 10,000 行驗證程式碼
2. **建立自訂偵錯工具**：custom debuggers、compiler hooks
3. **建立驗證系統**：test harnesses、stress tests
4. **用笨模型測試 API**：用 Grok 等便宜模型測試 API 可用性
5. **建立自訂 lint 規則**：以前不會做，現在應該常做
6. **用 AI 做壓力測試**：讓 agent 在 AWS 上 spin up 資源測試

### Uncle Bob 的方法（互補）

- 用極端約束包圍 agents：unit tests、Gherkin tests、QA procedures
- 不直接閱讀程式碼，而是透過測試和 QA 流程驗證
- 高信心來自程式碼通過所有約束的「閘道」

## 計算公式

### 以前（手寫時代）

```
閱讀：1000 行/天
寫入：200 行/天
合併：100 行/天
比例：10:2:1（閱讀:寫入:合併）
```

### 現在（AI 時代）

```
閱讀：1000 行/天（不變）
生成：2000+ 行/天
合併：500 行/天
比例：2:4:1（閱讀:生成:合併）
```

## 常見誤解

| 誤解 | 真相 |
|------|------|
| 「AI 程式碼都是 slop」 | AI 在人類程式碼上訓練，所以人類程式碼也是 slop |
| 「我的程式碼太重要不能給 AI 碰」 | 那你應該生成更多驗證程式碼 |
| 「我應該減少閱讀」 | 不，你應該生成更多 cheap code |
| 「重要系統不需要 AI」 | 重要系統更需要 AI 來驗證 |

## 與其他概念的關係

| 概念 | 關係 |
|------|------|
| [[wiki/concepts/vibe-coding]] | 相關：vibe coding 的 slop 概念 |
| [[wiki/concepts/ai-coding-workflow]] | 互補：結構化工作流程是驗證的基礎 |
| [[wiki/entities/uncle-bob]] | 互補：Uncle Bob 的極端約束方法 |
| [[wiki/entities/theo-t3gg]] | 原創者：提出 4 層光譜 |

## 來源

- [[wiki/sources/2026-08-10-write-code-you-will-never-read-again|Write Code You Will Never Read Again]] — Theo 的 YouTube 影片
- [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code|Uncle Bob Stopped Reading AI-Generated Code]] — 互補觀點

## 相關頁面

- [[wiki/entities/theo-t3gg|Theo (t3gg)]] — 原創者
- [[wiki/entities/uncle-bob|Uncle Bob]] — 另一種方法論
- [[wiki/concepts/vibe-coding|vibe-coding]] — 自然語言驅動的程式碼生成
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程
