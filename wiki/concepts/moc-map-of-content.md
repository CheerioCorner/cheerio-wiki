---
title: MOC — Map of Content
type: concept
created: 2026-08-13
updated: 2026-08-14
sources: 2
tags: [knowledge-mgmt, navigation, indexing]
topics: [knowledge-mgmt]
canonical: concepts/moc-map-of-content
provenance_raw: "raw/notion-ingest/2026-08-10-knowledge-management-seeds"
---

# MOC — Map of Content

> 一種導航頁面，用來連結相關的筆記。它不是筆記本身，而是一張「地圖」。

## 核心概念

| 特性 | 說明 |
|------|------|
| **導航功能** | 像目錄一樣，列出相關頁面 |
| **主題分類** | 按主題組織，而不是按時間或類型 |
| **靈活更新** | 可以隨時新增或移除連結 |
| **不負責內容** | MOC 只負責導航，內容在被導航的頁面裡 |

## 心智擠壓點（Mental Squeeze Point）

這是 Nick Milo 提出的 MOC 核心概念，解釋了 **MOC 何時該出現**。

### 什麼是心智擠壓點？

當筆記累積到某個程度，你會開始感到：

- 「東西越來越多，但我找不到之前寫的」
- 「我知道我談過這個主題，但散落在不同地方」
- 「每次想整理都不知道從哪裡開始」

這種**認知負荷過載**的狀態，就是心智擠壓點。

### MOC 是由下而上（Bottom-up）浮現的

關鍵觀念：**MOC 不是預先規劃的分類系統，而是自然長出來的。**

| 傳統分類（Top-down） | MOC（Bottom-up） |
|---------------------|-----------------|
| 先決定類別，再放東西 | 先有東西，再發現主題 |
| 結構是預設的 | 結構是浮現的 |
| 可能分錯類 | 連結反映真實關聯 |
| 需要意志力維護 | 因為有用所以自然維護 |

心智擠壓點就是那個「啊，這些東西該歸在一起」的時刻。MOC 是解決方案，不是起點。

### 我們的實作對應

在我們的 wiki 裡，MOC 對應 `topics/` 目錄下的檔案。建立時機：

1. **不要急著建 topic** — 讓筆記先自然累積
2. **觀察心智擠壓點** — 當你發現某個主題的頁面散落各處、難以一次看完時
3. **才建立 topic MOC** — 此時 MOC 是「整理」而非「規劃」，會更貼近真實需求

## 與我們系統的對應

| MOC 概念 | 我們的實作 |
|---------|-----------|
| 導航頁面 | `topics/`：每個 topic 就是一個 MOC |
| 主題分類 | `topics/*.md`：按主題組織導航 |
| 連結集合 | `index.md`：全域導航頁（最大的 MOC） |
| 子主題 MOC | `topics/*.md` 可以連結到更細的子頁面 |

## 層級架構

```
全域 MOC（index.md）
    ↓
主題 MOC（topics/ai-agent.md）
    ↓
子主題 MOC（topics/ai-agent/coding-tools.md）
    ↓
個別頁面（wiki/entities/claude-code.md）
```

**關鍵洞察**：MOC 的價值在於「讓你不用知道東西在哪，只要知道它屬於什麼主題」。這是 Zettelkasten 索引頁的現代化版本。

## 設計哲學

1. **MOC 不是筆記** — 它只負責導航，不負責內容。把內容放進 MOC 是錯誤的用法
2. **可以有多層 MOC** — 全域 MOC → 主題 MOC → 子主題 MOC。層級越多，導航越精確
3. **定期更新** — 新增頁面時，記得更新相關的 MOC。MOC 過時 = 導航失效
4. **不要預先建 MOC** — 等心智擠壓點出現再建。過早建立的 topic 會變形、難維護

## 相關頁面

- [[wiki/concepts/zettelkasten|Zettelkasten]] — MOC 的前身是 Zettelkasten 的索引頁
- [[wiki/concepts/para|PARA]] — PARA 的 Resources 層可以用 MOC 來組織
- [[wiki/topics|Topics]] — 我們的 MOC 實作
- [[wiki/topics/knowledge-mgmt|知識管理]] — 知識管理 Topic

<page url="https://www.notion.so/MOC-Map-of-Content-Visual-Map-3bc5979e3a8c812f88fce945b7396cdb">MOC — Map of Content 視覺地圖</page>
