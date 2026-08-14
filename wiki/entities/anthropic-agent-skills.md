---
title: "Anthropic Agent Skills — 官方 Skill 系統"
type: entity
created: 2026-08-14
updated: 2026-08-14
sources: 2
tags: [skill, anthropic, agent-skills, progressive-disclosure]
topics: [skill, ai-development-tools]
canonical: entities/anthropic-agent-skills
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-agent-skills.md"
  - kind: raw
    path: "raw/web/2026-08-14-anthropicsskills-public-repository-for-agent.md"
---

# Anthropic Agent Skills — 官方 Skill 系統

> Anthropic 官方定義的檔案系統驅動 Skill 系統，透過三層漸進式揭露讓 Claude 按需載入專業知識。**這是 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 在 Claude 生態系上的具體實作。**

## 核心架構：三層漸進式揭露（Progressive Disclosure）

Skills 以目錄形式存在於 Claude 的虛擬機器中，三層載入確保只有相關內容佔用 context window：

| 層級 | 載入時機 | Token 成本 | 內容 |
|------|---------|-----------|------|
| **第 1 層：中繼資料** | 始終（啟動時） | ~100 tokens/Skill | YAML `name` + `description` |
| **第 2 層：指令** | Skill 被觸發時 | <5k tokens | SKILL.md 主體 |
| **第 3 層以上：資源** | 按需 | 存取前為零 | 腳本、參考檔案、模板 |

**關鍵設計**：腳本透過 bash 執行，只有**輸出**進入 context（腳本程式碼本身永不載入），這使得捆綁大量資源的 Skill 不會造成 context 負擔。

## 預建 Skills

四個由 Anthropic 官方維護的文件處理 Skills：

| Skill ID | 功能 | 可用平台 |
|----------|------|---------|
| `pptx` | 建立/編輯 PowerPoint 簡報 | API / claude.ai / AWS / Foundry |
| `xlsx` | 建立/分析 Excel 試算表 | API / claude.ai / AWS / Foundry |
| `docx` | 建立/編輯 Word 文件 | API / claude.ai / AWS / Foundry |
| `pdf` | 生成 PDF 文件和報告 | API / claude.ai / AWS / Foundry |

## 跨平台使用

### Claude API

透過 Messages API 的 `container` 參數指定 Skills，需要程式碼執行工具 + beta header `skills-2025-10-02`。Skills 在沙箱容器中執行，無網路存取。

### Claude Code

基於檔案系統，無需 API 上傳。放置於 `~/.claude/skills/`（個人）或 `.claude/skills/`（專案）。也可透過 Plugin Marketplace 安裝（`/plugin marketplace add anthropics/skills`）。

### claude.ai

Settings > Features 上傳 zip 檔案。適用於啟用程式碼執行的 Pro/Max/Team/Enterprise 方案。

⚠️ **自訂 Skills 不跨平台同步**，需在每個平台分別管理。

## 官方 GitHub Repo：anthropics/skills

[anthropics/skills](https://github.com/anthropics/skills) 包含四大類 Skill 範例：

- **Creative & Design**：藝術、音樂、設計
- **Development & Technical**：Web App 測試、MCP Server 生成
- **Enterprise & Communication**：通訊、品牌工作流
- **Document Skills**：docx/pdf/pptx/xlsx（source-available 作參考）

大部分開源（Apache 2.0），文件處理 Skills 為 source-available。

## 自訂 Skill 結構

```
my-skill/
├── SKILL.md              # 主要指令（觸發時載入）
├── REFERENCE.md          # 參考資料（按需載入）
└── scripts/
    └── helper.py         # 工具腳本（執行，不載入）
```

### SKILL.md Frontmatter 規範

```yaml
---
name: my-skill-name          # ≤64 字元，小寫/數字/連字號
description: ...             # ≤1024 字元，說明功能+使用時機
---
```

- `name`：不能含 XML 標籤、不能用保留字（anthropic/claude）
- `description`：第三人稱，包含功能**以及**觸發條件

## 安全性考量

- **僅使用可信來源**：惡意 Skill 可指示 Claude 執行任意程式碼
- **外部 URL 擷取風險**：擷取內容可能含惡意指令
- **視同安裝軟體**：生產環境部署需完整稽核

## 限制

| 限制項 | 說明 |
|-------|------|
| 每請求 Skills 上限 | 8 個 |
| 上傳大小 | < 30 MB |
| API 網路存取 | 無 |
| API 套件安裝 | 無（僅預先安裝） |
| 跨平台同步 | 無 |
| ZDR 適用性 | 不在 ZDR 範圍內 |

## 與 agentskills.io 的關係

[agentskills.io](https://agentskills.io/) 是**跨平台開放標準**，定義 Skill 的可移植格式。Anthropic Agent Skills 是該標準在 Claude 生態系上的**官方實作**，但兩者不完全等同：

| 面向 | agentskills.io | Anthropic Agent Skills |
|------|---------------|----------------------|
| 性質 | 開放標準 | 廠商實作 |
| 範圍 | 跨多個 AI 工具 | Claude 專用 |
| 分發 | `npx skills` / Registry | API 上傳 / 本地目錄 |
| 規範 | 格式定義 | 三層揭露 + 安全模型 |

## 來源

- [[wiki/sources/2026-08-14-agent-skills-overview|Agent Skills 概覽]] — 官方定義文件
- [[wiki/sources/2026-08-14-anthropics-skills-github-repo|anthropics/skills Repo]] — 官方 GitHub 倉庫

## 相關頁面

- [[wiki/entities/agentskills-io-standard|agentskills.io 標準]] — 跨平台開放標準
- [[wiki/entities/claude-code|Claude Code]] — Claude Code 的 Skill 使用方式
- [[wiki/entities/github-copilot|GitHub Copilot]] — Copilot Extensions 比較
- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]]
- [[wiki/concepts/agent-skills-api-usage|Agent Skills API 使用方法]]
- [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理]]
- [[wiki/concepts/skills-complex-composition-comparison|Skill 複雜組合技比較]] — 跨框架組合機制比較
- [[wiki/concepts/github-copilot-vs-claude-code-skills-ecosystem|Copilot vs Claude Code 比較]] — 廣泛生態比較
