---
title: PARA — Projects/Areas/Resources/Archives
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [knowledge-mgmt, productivity, organization]
topics: [knowledge-mgmt]
canonical: concepts/para
provenance_raw: "raw/notion-ingest/2026-08-10-knowledge-management-seeds"
---

# PARA — Projects/Areas/Resources/Archives

> Tiago Forte 在《Building a Second Brain》中提出的信息組織系統，把所有信息分成四類。

## 核心定義

PARA 是一個**行動導向**的資訊分類框架：

| 類別 | 定義 | 範例 | 生命週期 |
|------|------|------|---------|
| **Projects** | 有明確截止日期的短期目標 | 完成專案、寫報告、發佈套件 | 有開始和結束 |
| **Areas** | 需要長期維護的責任範圍 | 健康、財務、職涯、知識管理 | 持續維護 |
| **Resources** | 有興趣但不需要行動的主題 | AI Agent、知識管理方法論 | 隨時取用 |
| **Archives** | 已完成或不再活躍的內容 | 舊專案、過時資料、已完成的 tasks | 歸檔保存 |

**關鍵洞察**：PARA 的分類不是基於「主題」，而是基於**可操作性**（actionability）。Projects 最可操作，Archives 最不可操作。

## 與我們系統的對應

| PARA 類別 | 我們的資料夾 | 說明 |
|-----------|-------------|------|
| Projects | `projects/` | Project OKF Bundles，有明確邊界 |
| Areas | `work/` | 工作狀態管理，持續維護 |
| Resources | `wiki/` | 結構化知識，隨時候用 |
| Archives | `raw/.trash/` | 歸檔的 raw 資料 |

## 設計哲學

1. **生命週期意識** — 知識有生命週期：從 Inbox → 活躍 → 歸檔。不是所有東西都要永久保存
2. **定期整理** — 每隔一段時間，把不再活躍的內容移到 Archives。不整理 = 資料堆積
3. **行動導向** — 區分「需要行動」和「只需要記錄」的內容。Projects 需要行動，Resources 只需要記錄

## 相關頁面

- [[wiki/concepts/zettelkasten|Zettelkasten]] — 另一種知識組織方法（連結導向 vs PARA 的行動導向）
- [[wiki/concepts/moc-map-of-content|MOC]] — 導航頁面，可作為 PARA 的索引層
- [[wiki/topics/knowledge-mgmt|知識管理]] — 知識管理 Topic
