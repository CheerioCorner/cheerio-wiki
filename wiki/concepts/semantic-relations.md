---
title: 語意關係 — Semantic Relations
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [knowledge-mgmt, ontology, linking]
topics: [knowledge-mgmt]
canonical: concepts/semantic-relations
provenance_raw: "raw/notion-ingest/2026-08-10-knowledge-management-seeds"
---

# 語意關係 — Semantic Relations

> 定義頁面之間「怎麼連結」的方式——不只是「這兩個頁面有關」，而是「A 支持 B」或「A 矛盾 B」。

## 核心定義

| 關係 | 意義 | 使用場景 |
|------|------|---------|
| **supports** | A 支持 B 的論點 | 引用來源、補強論述 |
| **contradicts** | A 與 B 矛盾 | 標記衝突、觸發仲裁 |
| **extends** | A 擴展 B 的內容 | 補充說明、深入研究 |
| **depends_on** | A 依賴 B | 建立前置知識圖 |
| **related_to** | A 與 B 相關（未定義具體關係） | 一般性關聯 |

## 與我們系統的對應

| 語意關係 | 我們的實作 |
|---------|-----------|
| contradicts | Lint 矛盾偵測：自動發現頁面間的衝突 |
| supports | Source Fidelity：wiki 是否忠實原始來源 |
| related_to | 交叉連結：`[[wikilink]]` 建立關聯網路 |
| depends_on | 前置知識圖：學習路徑的基礎 |

## 設計哲學

1. **關係比連結更重要** — 不只是「有連結」，還要「知道是什麼關係」。`[[page-a]]` 只說「有關」，`supports` 說「A 支持 B」
2. **矛盾要標記** — 當發現矛盾時，明確標記 `contradicts`，而不是忽略。矛盾是知識演化的觸發器
3. **關係可以查詢** — 未來可以用語意關係來查詢：「哪些頁面支持 X？」比「哪些頁面連結到 X？」更有價值

## 實作挑戰

目前我們的 wiki 還沒有正式的語意關係系統。`[[wikilink]]` 是單向連結，不區分關係類型。要實作語意關係，需要：
- 在 frontmatter 中定義 `relations` 欄位
- 或建立專門的 `relations.md` 檔案
- Lint 規則需要能讀取和驗證這些關係

## 相關頁面

- [[wiki/concepts/lint-ruleset|Lint 規則集]] — Lint 矛盾偵測依賴語意關係
- [[wiki/concepts/zettelkasten|Zettelkasten]] — 雙向連結的基礎
- [[wiki/topics/knowledge-mgmt|知識管理]] — 知識管理 Topic
