# Sources

> 整理後的外部資料、研究紀錄與可追溯來源筆記。

原始資料仍保留在 `raw/` 且永遠只讀。Source note 應透過 frontmatter `provenance` 指向 raw、外部 URL、project 或 session context，不把 source note 當成 raw 的替代品。

## Source note contract

```yaml
collection: sources
provenance:
  - kind: raw | external | project | session
    path: raw/example.md       # raw provenance when applicable
    url: https://example.com   # external/project URL when applicable
```

現階段保留 `sources: N` 作為相容性摘要欄位；`provenance` 是逐步補強的可追溯來源資料。
