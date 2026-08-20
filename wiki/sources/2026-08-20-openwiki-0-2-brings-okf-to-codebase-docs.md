---
title: "OpenWiki 0.2 brings OKF to codebase documentation"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [langchain, openwiki, okf, blog, announcement]
topics: [okf]
provenance_url: "https://www.langchain.com/blog/openwiki-0-2-adds-okf-support"
---

# OpenWiki 0.2 brings OKF to codebase documentation

> LangChain 官方部落格文章（2026-07-16），宣布 OpenWiki 0.2 採用 Google OKF 標準。

## 核心訊息

OpenWiki 0.2 採用 OKF（Open Knowledge Format），讓大型 wiki 的結構化 discovery、update、search、review 變得更容易 `https://www.langchain.com/blog/openwiki-0-2-adds-okf-support`。

## OKF 在 OpenWiki 0.2 中的實作

### YAML Frontmatter

每個 wiki 檔案加上標準 frontmatter：

```yaml
---
type: <Type name>                    # REQUIRED
title: <Optional display name>
description: <Optional one-line summary>
resource: <Optional canonical URI>
tags: [<tag>, <tag>, …]
timestamp: <ISO 8601 datetime>
---
```

### 兩個 Reserved Files

- `index.md` — 用 `description` 欄位確定性生成目錄
- `logs.md` — 變更歷史（⚠️ 此處用 `logs.md`，但 know.2nth.ai 和 Antão Almada 用 `log.md`，拼寫不一致）

### 確定性搜尋

Structured metadata 讓 agent 可以用 tag/category/description 過濾，不必仰賴 agentic search（慢且貴）。

## OKF 生態系

- Google 官方 OKF wiki visualizer（開源）
- OWOX 整理的 ecosystem tools：`https://www.owox.com/blog/articles/okf-ecosystem-tools`

## 安裝

```bash
npm install -g openwiki@latest
openwiki --init
```

## 相關頁面

- [[wiki/concepts/okf-open-knowledge-format|OKF]]
- [[wiki/entities/langchain-openwiki|OpenWiki]]
- [[wiki/sources/2026-08-20-langchain-openwiki-cli|YouTube 影片 source note]]
