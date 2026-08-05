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

- [x] W-2026-08-021 建立知識花園導覽 Database ✅ #knowledge #notion
  - 2026-08-11 建立 Notion Database + 導覽頁 + 研究專題管理頁
  - 2026-08-12 確認 Database 已嵌入花園主頁（inline, ID: 3b25979e-3a8c-807b-a51a-ecc63800531c），標題仍為 "New database"
  - 2026-08-12 導覽頁 + 研究專題管理頁結構完成，統計為 placeholder
  - 2026-08-18 每週巡檢：✅ Notion token 修復完成；⚠️ 導覽 Database 被歸檔（in_trash）
  - 2026-08-05 ✅ 建立新 Database（ID: f3aa419a，inline 於花園主頁）+ 遷移 6 顆種子 + 更新 SKILL.md + 搬移種子內容 + 補齊欄位資料
  - completed: 2026-08-19
  - result:
  1) 建立 Database（11 欄位：種子、成長狀態、Tags、來源 URL、一句話、給我的啟發？、備註、研究專題、Wiki GitHub、種下日期、最後更新）
  2) 遷移 6 顆種子（Plannotator 🌿、NPM 🌿、OKF 🌱、mattpocock 🌱、Omnigent 🌱、Extension 🌱）
  3) 建立 6 個新頁面（套用模板格式）
  4) 歸檔舊頁面
  5) 更新 SKILL.md（反映最新結構、來源 URL 定義）
  6) 補齊所有種子的「給我的啟發？」欄位
  7) 清空 NPM Publishing、Agent Extension 的「來源 URL」（個人經驗無原始來源）
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[Design Spec](work/designs/knowledge-garden-navigator.md)

- [ ] W-2026-08-025 研究 AI Agent 網路查詢能力：Extension 機制與 Search 架構 #ai-agent
  - next: 調研主流 AI agent（Pi、Claude Code、Cursor、Copilot）的 web search / deep research extension 實作方式
  - 範疇：
    - Extension 機制：新裝 agent 如何加入查詢能力（MCP server？built-in tool？skill？）
    - Search 適配性：什麼样的搜尋方式適合 AI consumption（structured results vs raw HTML）
    - AI-fetch 與 AI-browser：是否需要專為 AI 設計的 fetcher（如 firecrawl、jina reader）或 browser（如 Playwright headless）
    - Deep Research 模式：Gemini Deep Research、Perplexity 等深度研究的實作模式
    - Private writer / output 機制：查詢結果如何回傳給 agent（inline context vs file output）
    - 安全與隱私：web search 的 data retention、privacy implications
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]、W-2026-08-024（gemini-deep-research skill）

- [ ] W-2026-08-026 優化 Gemini Deep Search Prompt：提升查詢品質與引用可靠性 #ai-agent #skills
  - next: 設計 prompt template，確保 Gemini deep search 結果具備：多元化來源、精準度、reference/citation
  - 範疇：
    - Prompt engineering：如何指示 Gemini 進行多角度搜尋（避免 echo chamber）
    - Citation 要求：強制每段結論附上 sources（URL + 標題 + 摘要）
    - 品質控制：如何驗證搜尋結果的可靠性（交叉比對、來源權威度）
    - 輸出格式：structured report format（evidence → conclusion → refs）
    - 與 agy CLI 整合：prompt template 如何嵌入 gy skill 的呼叫流程
  - refs: W-2026-08-024（gemini-deep-research skill）、[[wiki/entities/hermes-agent|hermes-agent]]

- [ ] W-2026-08-027 設定 Gemini CLI 環境：安裝 MCP Server 與常用插件 #ai-agent #extension
  - next: 調研 Gemini CLI 支援的 MCP server 生態，挑選必裝工具並實作安裝
  - 範疇：
    - MCP server 挑選：web search（Tavily/Exa）、file system、browser automation、notion
    - 安裝流程：Gemini CLI 如何設定 MCP server（config 檔案格式、啟動方式）
    - 常用工具優先序：哪些是「一定會用到的」先裝（search、fetch、file ops）
    - 與 Pi 的 MCP 設定差異比較
    - 驗證：安裝後測試每個 server 是否正常運作
  - refs: [[wiki/entities/pi-mono|pi-mono]]、W-2026-08-025（AI Agent 網路查詢能力研究）
  - 與 W-025 有重疊：W-025 做研究，W-027 做實作

- [ ] W-2026-08-028 研究 Pi Agent 使用 GitHub Copilot 訂閱作為 Provider #ai-agent #extension
  - next: 調查 Pi Agent 如何設定 GitHub Copilot 作為 model provider（browser-based auth）
  - 範疇：
    - Pi Agent 支援的 provider 類型：OpenAI-compatible / Anthropic Messages / Google Generative AI
    - GitHub Copilot 認證流程：browser-based auth 如何運作（OAuth token 取得）
    - models.json 設定：如何在 `~/.pi/agent/models.json` 配置 Copilot 模型
    - 模型可用性：Copilot 訂閱能用哪些模型（GPT-4o、Claude Sonnet、Gemini？）
    - 與 Claude Code / Cursor 的 Copilot 整合比較
    - 實測：到公司後實際設定並驗證
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/github-copilot|github-copilot]]、[[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]、[[wiki/sources/2026-08-19-pi-agent-github-copilot-provider-setup|Copilot Provider Setup Guide]]
  - 備註：目前這台電腦無 Copilot 訂閱，需到公司後實測
  - 2026-08-19 研究完成：Pi Agent 原生支援 Copilot OAuth，執行 `/login` → 選擇 GitHub Copilot → 完成授權即可。模型清單為靜態（from models.dev），如模型不出現需在 VS Code 中先 Enable。

- [x] W-2026-08-020 盤點 wiki 內容，找出可轉化為花園種子的素材 ✅ #knowledge #notion
  - completed: 2026-08-18
  - result: 巡檢完成，wiki 35 個頁面中僅 6 個進花園（覆蓋率 30%）。建議優先種植：claude-code、copilot、codex、MCP、tau、ai-coding-workflow、vibe-coding 等高價值 entity/concept
  - refs: [[wiki/index|Wiki Index]]、[[wiki/entities/knowledge-garden|knowledge-garden]]

## Notion 工作清單（已整合到 W-2026-08-021）

> 以下事項已合併到 W-2026-08-021 的 next 清單中。

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

- [x] W-2026-08-024 建立 `gemini-deep-research` skill：Gemini 深度研究 + 出處標註 ✅ #skills
  - completed: 2026-08-06
  - result: 建立完整 skill 結構，包含 3 個 prompt 模板（general/comparison/tutorial）、引用驗證腳本、README 文檔。可透過 agy CLI 執行深度研究，強制要求 citations。可與 wiki-knowledge 整合自動 ingest。
  - refs: [[wiki/entities/mattpocock-skills|mattpocock-skills]]、[[wiki/topics/skill|Skill topic]]、[[wiki/entities/hermes-agent|hermes-agent]]
  - 範疇：Prompt 模板設計（強制 citation 輸出格式）、Deep Research mode 啟用、結果後處理（格式化 report）、與 wiki-knowledge 整合（自動 ingest 研究結果）

## Completed

- [x] W-2026-08-019 研究 LSP（Language Server Protocol）與 Code Graph ✅ #ai-agent
  - refs: [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 研究]]、[[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]、[[wiki/entities/lsp|LSP]]、[[wiki/entities/tree-sitter|Tree-sitter]]、[[wiki/concepts/code-graph|Code Graph]]、[[wiki/entities/mcp-model-context-protocol|MCP]]、[[wiki/entities/github-copilot|GitHub Copilot]]、[[wiki/entities/claude-code|Claude Code]]、[[wiki/entities/openai-codex|OpenAI Codex]]
  - completed: 2026-08-04
  - result: Gemini Deep Research 完成兩份深度研究報告（LSP+CodeGraph + OKF+LSP+CodeGraph AI Agent 整合）；新增 wiki 頁面：2 source notes、6 entities、1 concept；20+ 篇 raw/web URLs 已加入知識庫

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
