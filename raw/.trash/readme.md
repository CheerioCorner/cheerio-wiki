# Raw Web Sources

Browser / Obsidian Web Clipper 原始輸入放置處。

## Web Clipper template

- Note destination: `raw/web/`
- Filename: `{{date|date:"YYYY-MM-DD"}}-{{title}}`
- Format: Markdown
- Body: 保留原始 `{{content}}`
- Images: template 不承諾圖片路徑；若工具能指定，使用 `raw/assets/`；否則由 ingest normalize 後修正引用

建議 frontmatter：

```yaml
---
title: "{{title}}"
source_url: "{{url}}"
author: "{{author}}"
published: "{{published}}"
clipped: "{{date|date:\"YYYY-MM-DD\"}}"
type: raw-source
source_kind: web
immutable: true
tags:
  - raw
  - web-clip
---
```
