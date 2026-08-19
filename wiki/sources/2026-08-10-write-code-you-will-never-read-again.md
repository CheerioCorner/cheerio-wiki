---
title: "Write Code You Will Never Read Again"
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [ai-coding, code-generation, verification, software-engineering, code-importance]
collection: sources
topics: [agent-coding-practices]
canonical: sources/2026-08-10-write-code-you-will-never-read-again
provenance_raw: "raw/youtube/write-code-you-will-never-read-again.md"
provenance_url: "https://youtu.be/434cG4g5KLE"
---

> 來源：[YouTube — Write Code You Will Never Read Again](https://youtu.be/434cG4g5KLE)
> 原始字幕：[[raw/youtube/write-code-you-will-never-read-again|raw transcript]]

## 一句話

Theo 論證「如果你的程式碼重要到每一行都要閱讀，那你應該生成更多 cheap code 來驗證它」，提出四層程式碼重要性光譜和驗證漏斗概念。

## 重點摘要

### 核心論點

- **大多數工程師閱讀太多程式碼，但生成不夠多** `[00:22]`
- **程式碼現在很便宜**：應該生成更多 code 來驗證重要的 code `[08:26]`
- **不是要你減少閱讀，而是要你生成更多**：「I'm not telling you to make your code cheaper. I'm telling you to make more cheap code.」`[16:24]`

### 程式碼重要性光譜（4 層）

| 層級 | 重要性 | 範例 | 閱讀策略 |
|------|--------|------|----------|
| **Tier A — Slop** | 不重要 | 寵物商店網站、side projects | 不需要閱讀 `[02:32]` |
| **Tier B — 重要但可恢復** | 一般商業應用 | SaaS 付費平台 | 選擇性閱讀 |
| **Tier C — 非常重要** | 壞掉會出事 | 金融 ERP、核心基礎設施 | 大量閱讀 + 驗證 `[08:52]` |
| **Tier D — 人命關天** | 壞掉會死人 | 心律調節器韌體 | 必須閱讀每一行 `[02:40]` |

### 讀取 vs 生成比例的變化

| 時代 | 閱讀 | 寫入 | 合併 |
|------|------|------|------|
| **以前** | 1000 行 | 200 行 | 100 行 `[05:46]` |
| **現在** | 1000 行 | 2000+ 行 | 500 行 `[07:06]` |

### 關鍵策略：用 Cheap Code 驗證 Important Code

如果你的程式碼是 Tier C/D，你應該：

1. **生成大量 slop 來驗證**：100 行重要程式碼 → 10,000 行驗證程式碼 `[09:07]`
2. **建立自訂偵錯工具**：custom debuggers、compiler hooks `[10:06]`
3. **建立驗證系統**：test harnesses、stress tests `[10:44]`
4. **用笨模型測試 API**：用 Grok 等便宜模型測試 API 可用性 `[21:01]`
5. **建立自訂 lint 規則**：以前不會做，現在應該常做 `[14:39]`
6. **用 AI 做壓力測試**：讓 agent 在 AWS 上 spin up 資源測試 `[15:08]`

### 實際案例

- **10,000 行 JavaScript 組織檔案**：以前不合理，現在 code 免費所以合理 `[13:20]`
- **用 10 個 agent 測試 API**：用便宜模型測試 API 是否可用 `[21:01]`
- **自訂 debugger 和 compiler hooks**：以前依賴內建工具，現在自己造 `[15:08]`
- **壓力測試**：以前要找朋友幫忙，現在讓 agent 做 `[15:08]`

### 關於 Uncle Bob 的回應

Theo 間接回應 Uncle Bob 的「不閱讀 AI 程式碼」觀點：
- 如果你的程式碼重要到每一行都要驗證，那你應該生成更多驗證程式碼 `[09:07]`
- 你不能說「AI 不能碰我的程式碼」但又不建立驗證系統 `[09:34]`
- 如果核心程式碼太重要不能給 AI 碰，那就再往上一層抽象 `[10:06]`

### 核心觀點

- **Code is useful for things other than merging**：程式碼不只是用來合併的 `[04:58]`
- **重要程式碼只是你工作的一小部分**：如果你 90% 的時間都在處理 Tier D 程式碼，那你接觸的程式碼還不夠多 `[15:48]`
- **創造力是關鍵**：找不到用 cheap code 驗證重要 code 的方法 → 你還不夠有創造力 `[14:14]`

## 來源

- [[raw/youtube/write-code-you-will-never-read-again|Raw transcript — YouTube 434cG4g5KLE]]

## 相關頁面

- [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code|Uncle Bob Stopped Reading AI-Generated Code]] — 互補觀點
- [[wiki/entities/uncle-bob|Uncle Bob]] — 另一種驗證方法論
- [[wiki/concepts/vibe-coding|vibe-coding]] — 自然語言驅動的程式碼生成
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程
