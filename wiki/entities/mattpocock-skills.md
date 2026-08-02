---
title: "mattpocock/skills — AI Coding Agent Skills 集合"
type: entity
created: 2026-07-22
updated: 2026-07-22
sources: 2
tags: [skills, ai-coding, matt-pocock, npm, github]
collection: entities
topics: [ai-agent]
canonical: entities/mattpocock-skills
---

> Matt Pocock 維護的 AI coding agent skills 集合，162k+ stars，7.5M+ downloads。

## 基本資訊

| 屬性 | 值 |
|------|-----|
| **作者** | Matt Pocock（Total TypeScript 作者） |
| **倉庫** | [github.com/mattpocock/skills](https://github.com/mattpocock/skills) |
| **Stars** | 162,000+ |
| **Downloads** | 7.5M+ |
| **安裝方式** | `npx skills@latest add mattpocock/skills` |
| **授權** | MIT |

## 核心 Skills

### 主要工作流程

| Skill | 類型 | 說明 |
|-------|------|------|
| `grill-with-docs` | User | 深度追問 + 自動建立領域模型 |
| `to-spec` | User | 對話 → 正式規格書 |
| `to-tickets` | User | 規格書 → 可執行 tickets |
| `implement` | User | 依照 tickets 實作 + code review |
| `code-review` | Model | 雙軸審查：Standards + Spec |

### 工程實踐

| Skill | 類型 | 說明 |
|-------|------|------|
| `tdd` | Model | 紅-綠-重構 TDD 循環 |
| `diagnosing-bugs` | Model | 紀律化 bug 診斷 |
| `prototype` | Model | Throwaway prototype 驗證設計 |
| `domain-modeling` | Model | 建立維護領域模型 |
| `improve-codebase-architecture` | User | 掃描 codebase 找改善之處 |

## 設計哲學

1. **User-invoked 為主** — 不自動滲透 context，token 佔用極低
2. **模型無關** — Claude Code / Codex / Cursor / Pi 通用
3. **可組合** — 不強制流程，按需搭配
4. **工程導向** — TDD、code review、領域建模，非 vibe coding

## 安裝範圍

- **Project**（推薦團隊）：技能安裝在專案目錄，團隊共享
- **Global**（個人）：技能安裝在 home 目錄

## Agent 支援

- Universal agents：Cursor、Codex、Claude Code 等
- Claude skills 需額外設定（symlink 或 copy）

## 來源

- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|mattpocock/skills — 完整 AI Coding 工作流程教學]]
- [[wiki/sources/2026-07-11-mattpocock-skills|mattpocock/skills — 工程實踐 Skills 集合]]

## 相關頁面

- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 核心工作流程概念
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — 另一個 skills 實例
