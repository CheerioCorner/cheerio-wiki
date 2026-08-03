# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [ ] W-2026-08-017 研究 harness 架構，開發自己的 AGENT ⏫ #ai-agent
  - next: Pi containerization 方案（Gondolin/Docker/OpenShell）比較、Tau 三層架構分離細節、Hermes 學習迴圈機制
  - refs: [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/tau|tau]]、[[wiki/concepts/meta-harness|meta-harness]]、[[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]
  - 2026-08-06 完成 Pi Architecture Walkthrough ingest
  - 2026-08-08 ingest waku-agent：四大支柱 readable blueprint

- [ ] W-2026-08-010 建立 `pi-work-tracker` 並取代 `pi-todo-journal` ⏫ #extension
  - next: 抽象 TaskStore / JournalStore adapters → 測試、CI 與 npm 發布 → 搬移必要程式
  - refs: [[projects/pi-work-tracker/index|pi-work-tracker Project Bundle]]、[[projects/pi-todo-journal/index|pi-todo-journal Project Bundle]]、[[work/README|Work System]]

- [ ] W-2026-08-020 研究 Gemini CLI 整合方案 ⏫ #ai-agent
  - next: 安裝 Gemini CLI → 測試 headless 模式 (`gemini -p`) → 實作 subagent 呼叫流程
  - refs: [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 2026-08-09 決策：採用 Gemini CLI headless 模式整合（免費 1000 req/day）

## Backlog

- [x] W-2026-08-005 測試 URL → raw/web → ingest 全流程 ✅ #knowledge
  - refs: [[wiki/sources/README|Sources]]、[[raw/web|Raw web sources]]
  - completed: 2026-08-09

- [x] W-2026-08-007 確認 canonical wiki 頁面與交叉引用一致 ✅ #wiki
  - refs: [[wiki/index|Wiki Index]]
  - completed: 2026-08-09
  - result: 解決 graph 超級節點問題，structural files 連結數減少 87%

- [ ] W-2026-08-008 用 `grill-me` 跑一次完整需求追問（Pi Web） ⏫ #skills
  - next: grilling 已安裝，可執行
  - refs: [[projects/pi-web-access-zh-tw/index|Pi Web project context]]

- [ ] W-2026-08-009 建立每週 wiki lint 與花園巡檢提醒 ⏫ #meta
  - refs: [[wiki/entities/wiki-knowledge|wiki-knowledge]]

## Completed

- [x] W-2026-08-004 建立 Notion ↔ Obsidian 雙向同步機制 ✅ #notion
  - refs: [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]]、[[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki/entities/notionApi|notionApi]]
  - completed: 2026-08-06

- [x] W-2026-08-016 測試 Notion 端到端流程 ✅ #notion
  - refs: [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]]
  - completed: 2026-08-06

- [x] W-2026-08-013 規劃 skill GitHub repos ✅ #skills
  - refs: [[wiki/entities/wiki-knowledge|wiki-knowledge]]、`~/.agents/skills/`、https://github.com/CheerioCorner/cheerio-skills
  - completed: 2026-08-08

- [x] W-2026-08-018 建立 skills-repo-manager skill ✅ #skills
  - refs: https://github.com/CheerioCorner/cheerio-skills
  - completed: 2026-08-08

- [x] W-2026-08-015 建立 Notion → raw 抓取流程 ✅ #notion
  - refs: [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]]、[[wiki/entities/knowledge-garden|knowledge-garden]]
  - completed: 2026-08-05

- [x] W-2026-08-006 研究 YouTube 字幕抓取方案 ✅ #knowledge
  - refs: [[raw/youtube|Raw YouTube sources]]、https://github.com/coleam00/cole-medin-knowledge-base/blob/main/.claude/skills/channel-to-kb/SKILL.md
  - completed: 2026-08-03

- [x] W-2026-08-012 建立 `youtube-to-wiki` skill ✅ #knowledge
  - refs: [[wiki/discussions/youtube-to-wiki-pipeline-timing|YouTube-to-Wiki Pipeline Timing]]、[[wiki/entities/wiki-knowledge|wiki-knowledge]]
  - completed: 2026-08-03

- [x] W-2026-07-022 YouTube ingest mattpocock/skills ✅ #knowledge #wiki
  - refs: [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|mattpocock/skills tutorial]]、[[wiki/topics/skill|Skill topic]]
  - completed: 2026-07-22

- [x] W-2026-08-001 釐清 Obsidian vault 架構 ✅ #knowledge
  - refs: [[wiki/index|Wiki Index]]、[[raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff|Wiki × OKF session handoff]]
  - completed: 2026-08-03

## Work record contract

- 任務狀態只在本檔維護。
- 完成、決策、重要討論與處理結果追加至 `work/history/YYYY-MM.md`。
- 每個 history event 必須包含 `refs:`，至少指向 raw conversation、project 或 wiki 其中之一。
- 沒有形成工作進展或可追溯結果的對話，不需要建立事件。
