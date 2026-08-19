---
title: "Agent Skills — Anthropic 官方概覽"
type: source
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [skill, anthropic, agent-skills]
topics: [skill-system]
provenance_raw: "raw/web/2026-08-14-agent-skills.md"
---

# Agent Skills — Anthropic 官方概覽

> Source: [Agent Skills](https://platform.claude.com/docs/zh-TW/agents-and-tools/agent-skills/overview)
> Clipped: 2026-08-14

## 重點摘要

Anthropic 官方 Agent Skills 定義文件，完整說明 Skills 的架構、運作方式與使用範圍。

### 核心定義

Skills 是可重複使用、基於檔案系統的資源，為 Claude 提供特定領域的專業知識（工作流程、上下文和最佳實踐），將通用代理轉變為專家。

### 三層漸進式揭露（Progressive Disclosure）

| 層級 | 載入時機 | Token 成本 | 內容 |
|------|---------|-----------|------|
| **第 1 層：中繼資料** | 始終（啟動時） | ~100 tokens/Skill | YAML frontmatter `name` + `description` |
| **第 2 層：指令** | Skill 被觸發時 | <5k tokens | SKILL.md 主體 |
| **第 3 層以上：資源** | 按需 | 存取前為零 | 捆綁的檔案、腳本、參考資料 |

### 預建 Skills

- **PowerPoint (pptx)**：建立/編輯簡報
- **Excel (xlsx)**：建立/分析試算表
- **Word (docx)**：建立/編輯文件
- **PDF (pdf)**：生成 PDF 文件

### 跨平台使用

- **Claude API**：透過 `container` 參數 + beta header `skills-2025-10-02`
- **Claude Code**：基於檔案系統，`~/.claude/skills/`（個人）或 `.claude/skills/`（專案）
- **claude.ai**：Settings > Features 上傳 zip

### 安全性考量

- 僅使用可信來源的 Skills
- 惡意 Skill 可能指示 Claude 執行任意程式碼
- 外部 URL 擷取的 Skills 風險特別高

### 限制

- 自訂 Skills **不跨平台同步**
- API：無網路存取、無執行時套件安裝
- 每個請求最多 8 個 Skills
- Skill 上傳大小 < 30 MB

## 相關頁面

- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — 核心實體頁
- [[wiki/sources/2026-08-14-agent-skills-api-quickstart|API Quickstart]]
- [[wiki/sources/2026-08-14-agent-skills-api-guide|API 完整指南]]
- [[wiki/sources/2026-08-14-skill-writing-best-practices|撰寫最佳實踐]]
- [[wiki/sources/2026-08-14-agent-skills-enterprise-deployment|企業級部署]]
- [[wiki/entities/agentskills-io-standard|agentskills.io 標準]] — 跨平台開放標準
