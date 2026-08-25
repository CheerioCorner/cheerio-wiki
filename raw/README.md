 # Raw Sources

> `raw/` 保存未整理、可追溯的原始輸入。Raw content 原則上只讀；整理後的知識進入 `wiki/`。

## Source channels

- [[raw/web|Web]] — Browser / Obsidian Web Clipper 輸入
- [[raw/youtube|YouTube]] — 字幕、逐字稿與影片資訊
- [[raw/conversations|Conversations]] — annotator / agent 對話原始紀錄
- [[raw/deep-research|Deep Research]] — 深度研究的原始輸出（Gemini Deep Research、NotebookLM 等）
- [[raw/assets|Assets]] — raw sources 使用的圖片與附件

## Rules

- 來源類型在入口目錄分開，避免所有 raw 檔案混在一起。
- 附件集中於 `assets/`，檔名應帶來源 slug；移動附件時必須同步修正引用。
- URL、來源識別與原始內容保留在 source note；語義整理與 wikilinks 在 ingest 後建立。
- Raw note 不直接取代 `wiki/sources/` 的整理後 source note。
