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
topics: [topic1, topic2]  # 所屬 topics（建議填，方便 topic page 自動連結）
provenance_raw: "raw/web/filename.md"  # 本機 raw 檔案（相對路徑）
provenance_url: https://...             # 外部來源 URL
provenance_session: "description"       # 對話 session（選填）
---
```

### provenance 欄位規則

| 欄位 | 說明 |
|------|------|
| `provenance_raw` | 指向 `raw/` 下的相對路徑。YouTube 字幕、Web Clip、對話紀錄都算 raw |
| `provenance_url` | 外部來源 URL（GitHub README、部落格文章、文件等） |
| `provenance_session` | 對話 session 描述（選填，用於非 raw/external 來源） |

**一個 source note 可以有多個 provenance 欄位**（例如同時有 `provenance_raw` 和 `provenance_url`）。

### 範例：YouTube 來源

```yaml
---
title: "Hermes Architecture EXPLAINED"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [hermes, ai-agent]
provenance_raw: "raw/youtube/[[../../raw/youtube/[[../../raw/youtube/hermes-architecture-explained|[[../../raw/youtube/hermes-architecture-explained|hermes-architecture-explained]]]]|[[../../raw/youtube/[[../../raw/youtube/hermes-architecture-explained|hermes-architecture-explained]]|hermes-architecture-explained]]]].md"
provenance_url: https://youtu.be/n32qq7Kwzh0
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
provenance_raw: "raw/web/[[../../raw/web/2026-08-02-defect-density-escape-rate-agile-metrics-guide-2026|2026-08-02-defect-density-escape-rate-agile-metrics-guide-2026]].md"
provenance_url: https://example.com/original-article
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
provenance_url: https://github.com/backnotprop/plannotator
---
```

## Migration records

- [[wiki/sources/2026-08-02-wiki-okf-migration-complete|2026-08-02 Wiki × OKF migration complete]]
