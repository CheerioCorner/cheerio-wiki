---
title: "透過 API 使用 Agent Skills — 完整指南"
type: source
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [skill, anthropic, api, container, versioning]
topics: [skill-system]
provenance_raw: "raw/web/2026-08-14-透過api使用agent-skills.md"
---

# 透過 API 使用 Agent Skills — 完整指南

> Source: [透過 API 使用 Agent Skills](https://platform.claude.com/docs/zh-TW/build-with-claude/skills-guide)
> Clipped: 2026-08-14

## 重點摘要

Claude API 使用 Agent Skills 的完整技術指南，涵蓋 Messages API 整合、自訂 Skills 管理、版本控制與最佳實踐。

### Anthropic vs 自訂 Skills

| 面向 | Anthropic Skills | 自訂 Skills |
|------|-----------------|------------|
| Type 值 | `anthropic` | `custom` |
| Skill ID | 簡短名稱：pptx/xlsx/docx/pdf | 自動產生：`skill_01Ab...` |
| 版本格式 | 日期：`20251013` 或 `latest` | Epoch 時間戳記或 `latest` |
| 管理方式 | Anthropic 預建維護 | Skills API 上傳管理 |

### 先決條件

1. Claude API 金鑰
2. Beta Headers：`code-execution-2025-08-25` + `skills-2025-10-02`
3. 啟用程式碼執行工具

### 多輪對話

透過 `container.id` 復用容器，延續上下文。

### 長時間運行操作

處理 `pause_turn` stop reason，在同一容器中繼續。

### 自訂 Skills CRUD

- **建立**：`ant beta:skills create --file skill.zip`
- **列出**：`ant beta:skills list [--source custom]`
- **擷取**：`ant beta:skills retrieve --skill-id <id>`
- **刪除**：先刪所有版本，再刪 Skill

### 版本管理

- 新版本是**完整快照**（非差異更新）
- 生產環境：固定特定版本
- 開發環境：使用 `latest`

### 提示快取

相同 Skills 清單（含順序）產生相同可快取前綴。變更清單會使快取失效。

### 限制

- 每個請求最多 8 個 Skills
- 上傳大小 < 30 MB
- 無網路存取、無執行時套件安裝

## 相關頁面

- [[wiki/sources/2026-08-14-agent-skills-api-quickstart|API Quickstart]] — 入門版
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]]
- [[wiki/concepts/agent-skills-api-usage|Agent Skills API 使用方法]] — 概念整合頁
