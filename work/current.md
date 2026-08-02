# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [ ] W-2026-08-010 建立 `pi-work-tracker` 並取代 `pi-todo-journal` ⏫ #extension
  - next: 等現行 agent 維運流程實際使用並穩定後，再建立新 repository 並搬移必要程式
  - refs: [[projects/pi-work-tracker/index|pi-work-tracker Project Bundle]]、[[work/README|Work System]]

- [ ] W-2026-08-002 完成 `pi-todo-journal` Phase 3：抽象 TaskStore / JournalStore adapters 🔼 #extension
  - next: 確認 adapter boundary 與 implementation plan
  - refs: [[projects/pi-todo-journal/index|pi-todo-journal Project Bundle]]

- [ ] W-2026-08-003 完成 `pi-todo-journal` Phase 4：測試、CI 與 npm 發布 🔼 #extension
  - next: 等 Phase 3 adapter boundary 確認後拆解 tickets
  - refs: [[projects/pi-todo-journal/index|pi-todo-journal Project Bundle]]

## Backlog

- [ ] W-2026-08-004 建立 Notion ↔ Obsidian 雙向同步機制 ⏫ #notion
  - refs: [[wiki/entities/notionApi|notionApi]]

- [ ] W-2026-08-005 測試 URL → raw/web → ingest 全流程 🔼 #knowledge
  - refs: [[wiki/sources/README|Sources]]、[[raw/web|Raw web sources]]

- [x] W-2026-08-006 研究 YouTube 字幕抓取方案 🔼 #knowledge ✅
  - refs: [[raw/youtube|Raw YouTube sources]]、https://github.com/coleam00/cole-medin-knowledge-base/blob/main/.claude/skills/channel-to-kb/SKILL.md
  - 結論：`fetch_transcripts.py`（pytubefix + youtube_transcript_api）可直接複用；Extract→Canonicalize→Write pipeline 可改造為適合我們架構的版本
  - 2026-08-02 實測：成功抓取 `qo1QNxWcm28`（Tau: A Python Port of Pi），25:03 / 261 segments → raw/youtube/tau-python-port-of-pi.md
  - 2026-08-03 完成 ingest：source note + entity page → wiki

- [x] W-2026-08-012 建立 `youtube-to-wiki` skill ⏫ #knowledge ✅
  - next: —
  - refs: [[wiki/discussions/youtube-to-wiki-pipeline-timing|YouTube-to-Wiki Pipeline Timing]]、[[wiki/entities/wiki-knowledge|wiki-knowledge]]
  - 2026-08-03 建立 skill，固化 YouTube 字幕抓取 → raw/youtube/ → wiki ingest 流程
  - 2026-08-03 更新：補入 Step 3 canonical 確認 + Step 5 topic 導航更新

- [ ] W-2026-08-007 確認 canonical wiki 頁面與交叉引用一致 🔼 #wiki
  - refs: [[wiki/index|Wiki Index]]

- [ ] W-2026-08-008 用 `grill-me` 跑一次完整需求追問（Pi Web） ⏫ #skills
  - refs: [[projects/pi-web-access-zh-tw/index|Pi Web project context]]

- [ ] W-2026-08-009 建立每週 wiki lint 與花園巡檢提醒 ⏫ #meta
  - refs: [[wiki/entities/wiki-knowledge|wiki-knowledge]]

## Completed

- [x] W-2026-08-001 釐清 Obsidian vault 架構 ✅ #knowledge
  - refs: [[wiki/index|Wiki Index]]、[[raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff|Wiki × OKF session handoff]]
  - completed: 2026-08-03
  - result: 完成全域 lint 檢查：修正 4 處 visualizations broken links，補上 4 個 topic pages frontmatter；確認 96 個 wikilink targets 全部正常，無孤立頁面。

## Work record contract

- 任務狀態只在本檔維護。
- 完成、決策、重要討論與處理結果追加至 `work/history/YYYY-MM.md`。
- 每個 history event 必須包含 `refs:`，至少指向 raw conversation、project 或 wiki 其中之一。
- 沒有形成工作進展或可追溯結果的對話，不需要建立事件。
