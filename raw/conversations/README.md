# Raw Conversations

Annotator / agent session 的原始對話與 handoff 保存位置。

## 類型

- `source_kind: session-handoff` — annotator / agent 對話交接紀錄
- `source_kind: thought` — 看影片／文章時的當下想法（觸發來源在 frontmatter `related_raw:` 連結）

## 想法檔規範（source_kind: thought）

想法檔必須同時使用兩種方式連結觸發來源：
1. **frontmatter `related_raw:`** — 指向 `raw/youtube/`、`raw/web/` 或其他 raw 來源的相對路徑（字串或陣列）
2. **正文 `[[wikilink]]`** — 連結到來源檔，讓 Obsidian Graph View 能視覺化

原始紀錄只讀；後續決策、工作事件與 canonical knowledge 透過 `refs:` 連到此處。
