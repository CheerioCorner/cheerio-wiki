---
title: "Theo (t3gg) — 程式碼重要性光譜與驗證方法論倡導者"
type: entity
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [theo, t3gg, youtuber, software-engineering, ai-coding]
collection: entities
topics: [ai-agent]
canonical: entities/theo-t3gg
---

> Theo（t3gg）是科技 YouTube 頻道主，以務實的軟體工程觀點聞名，近期提出「程式碼重要性光譜」和「用 cheap code 驗證 important code」的方法論。

## 基本資訊

| 項目 | 內容 |
|------|------|
| 網名 | Theo / t3gg |
| 職業 | YouTuber、軟體工程師 |
| 頻道 | Theo - t3gg |
| 知名觀點 | 程式碼重要性光譜、Code Verification Funnel |
| 背景 | 曾在 Twitch 影片基礎設施團隊工作 |

## 核心觀點

### 程式碼重要性光譜（4 層）

Theo 提出的框架，用來判斷應該閱讀多少程式碼：

| 層級 | 重要性 | 範例 | 閱讀策略 |
|------|--------|------|----------|
| **Tier A — Slop** | 不重要 | 寵物商店網站、side projects | 不需要閱讀 |
| **Tier B — 重要但可恢復** | 一般商業應用 | SaaS 付費平台 | 選擇性閱讀 |
| **Tier C — 非常重要** | 壞掉會出事 | 金融 ERP、核心基礎設施 | 大量閱讀 + 驗證 |
| **Tier D — 人命關天** | 壞掉會死人 | 心律調節器韌體 | 必須閱讀每一行 |

### 核心論證

**「如果你的程式碼重要到每一行都要閱讀，那你應該生成更多 cheap code 來驗證它」**

1. **大多數工程師閱讀太多，生成不夠**
2. **程式碼現在很便宜**：應該生成更多 code 來驗證重要的 code
3. **不是要你減少閱讀，而是要你生成更多**
4. **重要程式碼只是工作的一小部分**：如果你 90% 的時間都在處理 Tier D 程式碼，那你接觸的程式碼還不夠多

### 關鍵策略

- **生成大量 slop 來驗證**：100 行重要程式碼 → 10,000 行驗證程式碼
- **建立自訂偵錯工具**：custom debuggers、compiler hooks
- **建立驗證系統**：test harnesses、stress tests
- **用笨模型測試 API**：用 Grok 等便宜模型測試 API 可用性
- **建立自訂 lint 規則**：以前不會做，現在應該常做

### 關於 Uncle Bob 的回應

Theo 間接回應 Uncle Bob 的「不閱讀 AI 程式碼」觀點：
- 如果你的程式碼重要到每一行都要驗證，那你應該生成更多驗證程式碼
- 你不能說「AI 不能碰我的程式碼」但又不建立驗證系統
- 如果核心程式碼太重要不能給 AI 碰，那就再往上一層抽象

## 與其他概念的關係

| 概念 | 關係 |
|------|------|
| [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code]] | 互補：Uncle Bob 的極端約束 vs Theo 的 cheap code 驗證 |
| [[wiki/entities/uncle-bob]] | 對比：兩種不同的 AI 程式碼驗證方法論 |
| [[wiki/concepts/vibe-coding]] | 相關：vibe coding 的 slop 概念 |
| [[wiki/concepts/ai-coding-workflow]] | 互補：結構化工作流程是驗證的基礎 |

## 來源

- [[wiki/sources/2026-08-10-write-code-you-will-never-read-again|Write Code You Will Never Read Again]] — YouTube 影片分析

## 相關頁面

- [[wiki/entities/uncle-bob|Uncle Bob]] — 另一種驗證方法論
- [[wiki/concepts/vibe-coding|vibe-coding]] — 自然語言驅動的程式碼生成
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程
