# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [ ] W-2026-08-017 研究 harness 架構，開發自己的 AGENT ⏫ #ai-agent
  - 2026-08-10 新增 pi-loop-scheduler entity：Pi cron job 實作原理（@pi-agents/loop）
  - next: Pi containerization 方案（Gondolin/Docker/OpenShell）比較、Tau 三層架構分離細節、Hermes 學習迴圈機制
  - refs: [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/tau|tau]]、[[wiki/concepts/meta-harness|meta-harness]]、[[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]
  - 2026-08-06 完成 Pi Architecture Walkthrough ingest
  - 2026-08-08 ingest waku-agent：四大支柱 readable blueprint
  - 2026-08-03 ingest loop-vs-graph-engineering：Loop vs Graph 兩種工作流模式，更新 waku-agent 加入 graph engineering

- [ ] W-2026-08-010 建立 `pi-work-tracker` 並取代 `pi-todo-journal` ⏫ #extension
  - next: 抽象 TaskStore / JournalStore adapters → 測試、CI 與 npm 發布 → 搬移必要程式
  - refs: [[projects/pi-work-tracker/index|pi-work-tracker Project Bundle]]、[[projects/pi-todo-journal/index|pi-todo-journal Project Bundle]]、[[work/README|Work System]]

- [ ] W-2026-08-021 建立知識花園導覽 Database ⏫ #knowledge #notion
  - 2026-08-11 建立 Notion Database + 導覽頁 + 研究專題管理頁
  - next: 人類調整 Notion 結構後，遷移 6 顆種子、建立 Views
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[Design Spec](work/designs/knowledge-garden-navigator.md)

## Backlog

- [x] W-2026-08-005 測試 URL → raw/web → ingest 全流程 ✅ #knowledge
  - refs: [[wiki/sources/README|Sources]]、[[raw/web|Raw web sources]]
  - completed: 2026-08-09

- [x] W-2026-08-007 確認 canonical wiki 頁面與交叉引用一致 ✅ #wiki
  - refs: [[wiki/index|Wiki Index]]
  - completed: 2026-08-09
  - result: 解決 graph 超級節點問題，structural files 連結數減少 87%；設定 graph exclusion 排除 work/、log.md、README.md、raw/

- [ ] W-2026-08-008 用 `grill-me` 跑一次完整需求追問（Pi Web） ⏫ #skills
  - next: grilling 已安裝，可執行
  - refs: [[projects/pi-web-access-zh-tw/index|Pi Web project context]]

- [x] W-2026-08-009 建立每週 wiki lint 與花園巡檢提醒 ✅ #meta
  - refs: [[wiki/entities/wiki-knowledge|wiki-knowledge]]、[[wiki/entities/knowledge-garden|knowledge-garden]]
  - completed: 2026-08-10
  - result: 設定每週一 09:00 wiki lint（ID: 7b26bb64）、每週三 10:00 花園巡檢（ID: f9aa104e）

- [x] W-2026-08-019 研究 LSP（Language Server Protocol）與 Code Graph ✅ #ai-agent
  - refs: [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 研究]]、[[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]、[[wiki/entities/lsp|LSP]]、[[wiki/entities/tree-sitter|Tree-sitter]]、[[wiki/concepts/code-graph|Code Graph]]、[[wiki/entities/mcp-model-context-protocol|MCP]]、[[wiki/entities/github-copilot|GitHub Copilot]]、[[wiki/entities/claude-code|Claude Code]]、[[wiki/entities/openai-codex|OpenAI Codex]]
  - completed: 2026-08-04
  - result: Gemini Deep Research 完成兩份深度研究報告（LSP+CodeGraph + OKF+LSP+CodeGraph AI Agent 整合）；新增 wiki 頁面：2 source notes、6 entities、1 concept；20+ 篇 raw/web URLs 已加入知識庫

- [ ] W-2026-08-020 盤點 wiki 內容，找出可轉化為花園種子的素材
  - next: 瀏覽 wiki/entities/、wiki/concepts/、wiki/topics/，評估哪些適合放入 Notion 知識花園
  - refs: [[wiki/index|Wiki Index]]、[[wiki/entities/knowledge-garden|knowledge-garden]]

- [ ] W-2026-08-021 研究 Hook 機制：不同 IDE / Harness 的實作比較 #ai-agent
  - next: 調研 Claude Code（Anthropic）、Cursor、Windsurf、GitHub Copilot App、Pi Agent 的 hook 系統
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 範疇：Hook 類型（pre/post tool call、on error、session lifecycle）、觸發時機、擴充方式、與 skill 的整合

- [ ] W-2026-08-022 研究 MCP Server 架構與實作 #ai-agent
  - next: 調研 MCP protocol spec、server 生態（official/community servers）、 transport 層（stdio/SSE/streamable HTTP）、tool/resource/prompt 三大原語
  - refs: [[wiki/entities/pi-mono|pi-mono]]
  - 範疇：Server 開發框架（TypeScript/Python SDK）、安全模型、permission 系統、與 harness 整合方式

- [ ] W-2026-08-023 研究 MCP Registry 與企業級管理 #ai-agent
  - next: 調研 MCP registry 方案（官方 registry、企業自建）、server 發現與註冊機制、版本管控、存取控制
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 範疇：企業 MCP 管理平台設計、server 託管與審核流程、多 team 共用策略、audit logging、compliance

## Completed

- [x] W-2026-08-011 Wiki Lint：批量補上 source note provenance ✅ #wiki
  - refs: [[wiki/index|Wiki Index]]、[[wiki/sources/README|Sources README]]
  - completed: 2026-08-10
  - result: 25 個 source notes 補上 provenance，指向對應的 raw/web/、raw/youtube/、raw/conversations/ 檔案

- [x] W-2026-08-014 新增 pi-loop-scheduler entity：Pi Cron Job 實作原理 ✅ #ai-agent
  - refs: [[wiki/entities/pi-loop-scheduler|pi-loop-scheduler]]、[[wiki/sources/2026-08-10-pi-cron-job-explained|Pi Cron Job Explained]]
  - completed: 2026-08-10
  - result: 記錄 @pi-agents/loop 架構、Idle gating、Anti-thundering-herd、Durable vs Session tasks

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
