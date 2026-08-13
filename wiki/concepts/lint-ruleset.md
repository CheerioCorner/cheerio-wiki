---
title: Lint 規則集
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [knowledge-mgmt, quality, automation]
topics: [knowledge-mgmt]
canonical: concepts/lint-ruleset
provenance_raw: "raw/notion-ingest/2026-08-10-knowledge-management-seeds"
---

# Lint 規則集

> 定義「什麼是好的知識」的標準，用來自動化檢查知識庫的品質。

## 核心概念

Lint 是**保護網**——防止壞知識進入 wiki。它不需要人工檢查所有頁面，而是用自動化規則掃描。

### 我們目前的 Lint 檢查項目

| 檢查項 | 說明 | 嚴重度 |
|--------|------|--------|
| **結構完整性** | frontmatter、provenance 是否完整 | 🔴 必要 |
| **孤立頁面** | 沒有 inbound links 的頁面 | 🟡 警告 |
| **矛盾偵測** | 頁面間互相矛盾的主張 | 🟡 警告 |
| **過時檢查** | 超過半衰期的內容 | 🟡 警告 |
| **Source Fidelity** | wiki 是否忠實原始來源 | 🔴 必要 |

## 設計哲學

1. **自動化優先** — Lint 的價值在於「不需要人類記得要做」。每次 ingest 後自動執行，不依賴人類記憶
2. **可擴展** — 隨著知識庫成長，可以加入更多檢查項目。規則是模組化的
3. **分級處理** — 🔴 必要問題需要人類判斷，🟡 警告可以自動處理

## 與 Agentic AI 的關係

Lint 本質上是**Procedural Memory**（程序記憶）的一種形式——它記住了「什麼是好的知識」這個判斷標準。在 Agent 系統中，這類知識通常以 Skill 文件或 Rule 文件的形式存在。

## 相關頁面

- [[wiki/concepts/semantic-relations|語意關係]] — Lint 矛盾偵測的基礎
- [[wiki/concepts/agentic-ai|Agentic AI]] — Agent 的 Procedural Memory 概念
- [[wiki/topics/knowledge-mgmt|知識管理]] — 知識管理 Topic
