---
title: Zettelkasten — 卡片盒筆記法
type: concept
created: 2026-08-13
updated: 2026-08-14
sources: 1
tags: [knowledge-mgmt, note-taking, linking]
topics: [knowledge-mgmt]
canonical: concepts/zettelkasten
provenance_raw: "raw/notion-ingest/2026-08-10-knowledge-management-seeds"
---

# Zettelkasten — 卡片盒筆記法

> 由德國社會學家 Niklas Luhmann 實踐的知識管理方法，經 Sönke Ahrens《How to Take Smart Notes》系統化後廣泛流行。

## 核心概念

| 概念 | 定義 | 實踐方式 |
|------|------|---------|
| **原子化筆記** | 每個筆記只記一個想法 | 每個 wiki 頁面只記一個概念/實體 |
| **雙向連結** | 筆記之間互相連結，形成網路 | `[[wikilink]]` 雙向連結 |
| **索引頁** | 作為入口，導航到相關筆記 | `index.md` + `topics/` |
| **連結優先** | 連結比分類更重要 | 不要強迫分類，讓連結自然形成網路 |

## 與我們系統的對應

| Zettelkasten 概念 | 我們的實作 |
|------------------|-----------|
| 原子化筆記 | `concepts/` + `entities/`：每個頁面只記一個概念/實體 |
| 雙向連結 | `[[wikilink]]`：頁面之間互相連結 |
| 索引頁 | `index.md` + `topics/`：作為入口導航 |
| 卡片盒 | `wiki/`：整個 wiki 就是我們的卡片盒 |

## 歷史與出處

Zettelkasten 由德國社會學家 **Niklas Luhmann**（1927–1998）在其學術生涯中實踐並發展，他用這個方法維護了 70 本書和 400 篇論文。現代 Zettelkasten 的流行，主要歸功於 **Sönke Ahrens** 在 2017 年出版的《How to Take Smart Notes》一書，將 Luhmann 的方法論系統化並介紹給大眾。

- **官方典藏**：[Niklas Luhmann Archive](https://niklas-luhmann-archiv.de/)（正確來源，非 luhmann.com）
- **推薦入門**：Sönke Ahrens《How to Take Smart Notes》

## 設計哲學

1. **連結比分類更重要** — 不要想著「這該放哪個資料夾」，而是想著「這跟哪些頁面有關」。連結形成網路，分類只是人為的樹狀結構
2. **定期回顧** — 每隔一段時間回顧舊筆記，看看有沒有新的連結可以建立。新知識可能連結到舊筆記
3. **讓知識自然生長** — 不要強迫分類，讓連結自然形成網路。這是 emergent order（湧現秩序）

## 與 PARA 的比較

| 面向 | Zettelkasten | PARA |
|------|-------------|------|
| **核心理念** | 連結優先 | 行動優先 |
| **分類方式** | 網狀連結 | 樹狀分類 |
| **適用場景** | 知識探索、研究 | 專案管理、生產力 |
| **我們的使用** | wiki/ 知識圖譜 | projects/ + work/ |

**關鍵洞察**：Zettelkasten 和 PARA 不衝突，而是互補。PARA 管理「要做的事」，Zettelkasten 管理「要知道的事」。我們的系統兩者都用。

## 相關頁面

- [[wiki/concepts/para|PARA]] — 另一種知識組織方法（行動導向 vs 連結導向）
- [[wiki/concepts/moc-map-of-content|MOC]] — Zettelkasten 索引頁的現代化版本
- [[wiki/concepts/semantic-relations|語意關係]] — 連結的語意層
- [[wiki/topics/knowledge-mgmt|知識管理]] — 知識管理 Topic
