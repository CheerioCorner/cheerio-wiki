# Sources

> 整理後的外部資料、研究紀錄與可追溯來源筆記。

原始資料仍保留在 `raw/` 且永遠只讀。Source note **必須**透過 frontmatter `provenance` 指向 raw、外部 URL、project 或 session context，不把 source note 當成 raw 的替代品。

## Source note contract

每個 `wiki/sources/` 下的頁面**必須**有以下 frontmatter（除 README.md 外）：

```yaml
---
title: 頁面標題
type: source
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: N             # 引用過幾個 raw 來源（相容性摘要欄位）
tags: [...]
provenance:            # ⚠️ 必填：至少一筆
  - kind: raw          # 本機 raw 檔案
    path: raw/youtube/video-slug.md   # raw 相對路徑
  - kind: external     # 外部 URL（網頁、GitHub repo 等）
    url: https://github.com/...
  - kind: project      # Project Bundle 內的資料
    url: https://github.com/.../tree/main/docs
  - kind: session      # 對話 session context
    path: raw/conversations/session-file.md
---
```

### provenance 欄位規則

| kind | 必填欄位 | 說明 |
|------|---------|------|
| `raw` | `path` | 指向 `raw/` 下的相對路徑。YouTube 字幕、Web Clip、對話紀錄都算 raw |
| `external` | `url` | 外部來源 URL（GitHub README、部落格文章、文件等） |
| `project` | `url` | Project Bundle 內的參考資料 |
| `session` | `path` | 對話 session handoff 或 annotator 原始紀錄 |

**一個 source note 可以有多筆 provenance**（例如同時引用 raw transcript 和 external GitHub README）。

### 範例：YouTube 來源

```yaml
---
title: "Hermes Architecture EXPLAINED"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [hermes, ai-agent]
provenance:
  - kind: raw
    path: raw/youtube/hermes-architecture-explained.md
  - kind: external
    url: https://youtu.be/n32qq7Kwzh0
---
```

### 範例：Web Clip 來源

```yaml
---
title: "Defect Density & Escape Rate"
type: source
created: 2026-08-02
updated: 2026-08-02
sources: 2
tags: [defect-metrics, quality]
provenance:
  - kind: raw
    path: "raw/web/2026-08-02-Defect Density & Escape Rate Agile Metrics Guide 2026.md"
  - kind: raw
    path: "raw/web/2026-08-02-Step-by-step guide on how to measure Defect Escape Rate.md"
  - kind: external
    url: https://example.com/original-article
---
```

### 範例：多來源研究

```yaml
---
title: "Plannotator 研究"
type: source
created: 2026-07-11
updated: 2026-07-30
sources: 2
tags: [plannotator, research]
provenance:
  - kind: external
    url: https://github.com/backnotprop/plannotator
  - kind: external
    url: https://plannotator.ai/
---
```

## Migration records

- [[wiki/sources/2026-08-02-wiki-okf-migration-complete|2026-08-02 Wiki × OKF migration complete]]
