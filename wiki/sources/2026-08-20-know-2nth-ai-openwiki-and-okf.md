---
title: "OpenWiki & OKF — know.2nth.ai Explainer"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [okf, openwiki, explainer, spec]
topics: [okf]
provenance_url: "https://know.2nth.ai/explainers/agents/openwiki"
---

# OpenWiki & OKF — know.2nth.ai Explainer

> 第三方詳細說明文，釐清 OKF v0.1 規範與 OpenWiki 的關係。關鍵澄清：OKF 只有一個必填欄位。

## OKF 時間軸

- **2026-06-12**: Google Cloud 發布 OKF v0.1
- **2026-07-16**: LangChain OpenWiki 0.2 採用 OKF

## OKF v0.1 規範

### 只有一個必填欄位

OKF v0.1 requires exactly **one** frontmatter field: `type`, a non-empty short string describing "the kind of concept — consumers use this for routing, filtering, and presentation." Everything else — `title`, `description`, `resource`, `tags`, `timestamp` — is **optional**（spec 稱 recommended）`https://know.2nth.ai/explainers/agents/openwiki`。

### 六個標準欄位

| 欄位 | 狀態 | 說明 |
|------|------|------|
| `type` | **Required** | 概念類型（非空中字串） |
| `title` | Optional | 人類可讀名稱 |
| `description` | Optional | 一行摘要（agent 用來判斷相關性） |
| `resource` | Optional | URL 或 identifier |
| `tags` | Optional | 自由標籤 |
| `timestamp` | Optional | 最後整理時間 |

Producer 可以加自訂 keys，consumer 必須保留未知 keys。

### Reserved Files

- `index.md` — 目錄清單，無 frontmatter
- `log.md` — 變更歷史，最新在前

### Bundle Structure

```
bundle/
├── index.md
├── log.md
├── modules/
│   ├── index.md
│   ├── auth.md
│   └── payments.md
└── models/
    ├── index.md
    └── user.md
```

### Cross-links

Standard markdown links between concept documents express relationships. OKF 不定義 edge types；broken links 被容忍。

## OKF vs Agent Skills vs MCP

三層能力的互補關係：
- **OKF**: 知識表示（knowledge representation）
- **Agent Skills**: 能力定義（capability definition）
- **MCP**: 工具存取（tool access）

## 重要限制

OKF **不保證**文件與程式碼同步——純結構規範，無 staleness 保證。

## 相關頁面

- [[wiki/concepts/okf-open-knowledge-format|OKF]]
- [[wiki/entities/langchain-openwiki|OpenWiki]]
- [[wiki/concepts/agent-skills|Agent Skills]]
- [[wiki/entities/mcp-model-context-protocol|MCP]]
