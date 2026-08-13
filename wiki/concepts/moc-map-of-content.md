---
title: MOC — Map of Content
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 1
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

## 相關頁面

- [[wiki/concepts/zettelkasten|Zettelkasten]] — MOC 的前身是 Zettelkasten 的索引頁
- [[wiki/concepts/para|PARA]] — PARA 的 Resources 層可以用 MOC 來組織
- [[wiki/topics|Topics]] — 我們的 MOC 實作
- [[wiki/topics/knowledge-mgmt|知識管理]] — 知識管理 Topic
