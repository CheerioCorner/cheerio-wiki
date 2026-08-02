---
title: "OKF Knowledge Catalog — Google Cloud 參考實作"
type: source
created: 2026-08-01
updated: 2026-08-05
sources: 1
tags: [okf, knowledge-format, google-cloud, bigquery, data-catalog, ai-agent]
collection: sources
topics: [extension-dev, knowledge-mgmt]
canonical: sources/2026-08-01-okf-knowledge-catalog
provenance_raw: "raw/web/2026-08-01-knowledge-catalog-okf-at-main.md"
provenance_url: https://github.com/GoogleCloudPlatform/knowledge-catalog---

# OKF Knowledge Catalog — Google Cloud 參考實作

> Google Cloud 開發的 OKF 參考實作，包含 reference agent 和視覺化工具。

## 重點摘要

- **定位：** OKF v0.2 規範的 proof-of-concept producer 和 consumer
- **Reference Agent：** 兩階段運作（BQ pass + web pass），自動產生 OKF bundles
- **視覺化工具：** 自包含 HTML，force-directed graph + 詳情面板 + 搜尋
- **三個 ready-to-browse bundles：** GA4、Stack Overflow、Bitcoin

## 關鍵發現

### OKF 的優勢（來自 Google 實作觀點）
- **Human- and agent-readable：** 不需要 SDK 或查詢語言
- **Version-controllable：** Bundle 就是目錄，git 天然支援
- **Portable and lock-in free：** 沒有 vendor API
- **Mixed structured + unstructured data：** Frontmatter 查詢 + Markdown 閱讀
- **Trust, provenance, freshness as first-class：** 內建來源、驗證、時效追蹤
- **Progressive disclosure：** `index.md` 逐層揭露

### Reference Agent 架構
- **BQ pass：** 從 BigQuery metadata 產生 OKF docs
- **Web pass：** LLM 作為 crawler，從 seed URLs 擴展知識
- **`--web-max-pages` cap：** 防止 agent 過度擴展

### Visualize 工具
- 自包含 HTML（Cytoscape.js + marked.js）
- Force-directed graph + detail panel + backlinks + search
- 用法：`python -m reference_agent visualize --bundle ./bundles/<name>`

## 與我們的關係

- 我們的 [[wiki/concepts/okf-open-knowledge-format]] 概念頁基於此規範
- pi-plannotator-auto 的 `docs/` 結構遵循 OKF 模式
- Reference agent 的 BQ + web 兩階段 approach 可作為未來知識 ingest 參考

## 來源

- GitHub: https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf
- OKF v0.2 Spec: https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md
