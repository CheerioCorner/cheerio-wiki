# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [ ] W-2026-08-030 安裝並測試 OpenCodeReview（OCR） ⏫ #ai-agent #code-review
  - next: 安裝 OCR、設定 LLM provider、跑一次 `ocr review` 測試
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、https://github.com/alibaba/open-code-review
  - 範疇：
    - 安裝：`npm install -g @alibaba-group/open-code-review`
    - 設定：`ocr config provider` + `ocr config model`
    - 測試：在現有專案跑 `ocr review`，觀察輸出品質
    - 比較：與 `code-review` skill 的結果做比較
  - 2026-08-20 完成深度分析：確定性工程×Agent混合架構、Benchmark（Token 1/9）、與 Plannotator/code-review skill 比較

- [ ] W-2026-08-031 比較實驗：OCR vs code-review skill #ai-agent #code-review
  - next: 找一個有 diff 的專案，分別用兩種工具審查，比較結果
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/entities/plannotator|Plannotator]]、~/.agents/skills/code-review/
  - blockedBy: [W-2026-08-030]
  - 範疇：
    - 準備一個有實際變更的 Git repo
    - 用 `ocr review` 審查，記錄輸出
    - 用 `code-review` skill 審查，記錄輸出
    - 比較：覆蓋率、準確性、Token 消耗、時間
    - 結論：什麼場景用哪個工具

- [ ] W-2026-08-032 整合 OCR 到 Code Review 工作流 #ai-agent #code-review
  - next: 設計 OCR → code-review skill → Plannotator 的完整流程
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[work/designs/pi-agent-learning-system|學習系統]]
  - blockedBy: [W-2026-08-031]
  - 範疇：
    - 設計工作流：OCR 初審 → skill 深度審查 → Plannotator 人類標註
    - 建立 script 或 alias 方便執行
    - 測試完整流程
    - 記錄到 wiki

- [ ] W-2026-08-029 建立 Pi Agent 更新學習系統 ⏫ #ai-agent #learning
  - next: 完成 v0.84.0 更新學習任務（全螢幕模式、AGENTS.override.md、samplingParams）
  - refs: [[work/designs/pi-agent-learning-system|學習系統設計]]、[[wiki/topics/pi-agent-learning/roadmap|學習路線圖]]、[[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 分析]]
  - 2026-08-20 建立學習系統：流程文件 + wiki 結構 + v0.84.0 分析 + 核心概念頁面

- [ ] W-2026-08-017 研究 harness 架構，開發自己的 AGENT ⏫ #ai-agent
  - 2026-08-10 新增 pi-loop-scheduler entity：Pi cron job 實作原理（@pi-agents/loop）
  - 2026-08-06 ingest YouTube 5支 AI Agent 研究：Graphify + AReaL + Context-CoT + SkillOpt + MemGraph-RAG
  - next: Pi containerization 方案（Gondolin/Docker/OpenShell）比較、Tau 三層架構分離細節、Hermes 學習迴圈機制
  - refs: [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/tau|tau]]、[[wiki/concepts/meta-harness|meta-harness]]、[[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]
  - 2026-08-06 完成 Pi Architecture Walkthrough ingest
  - 2026-08-08 ingest waku-agent：四大支柱 readable blueprint
  - 2026-08-03 ingest loop-vs-graph-engineering：Loop vs Graph 兩種工作流模式，更新 waku-agent 加入 graph engineering

- [ ] W-2026-08-010 建立 `pi-work-tracker` 並取代 `pi-todo-journal` ⏫ #extension
  - next: 抽象 TaskStore / JournalStore adapters → 測試、CI 與 npm 發布 → 搬移必要程式
  - refs: [[projects/pi-work-tracker/index|pi-work-tracker Project Bundle]]、[[projects/pi-todo-journal/index|pi-todo-journal Project Bundle]]、[[work/README|Work System]]

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

- [x] W-2026-08-028 研究 Pi Agent 使用 GitHub Copilot 訂閱作為 Provider ✅ #ai-agent #extension
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/github-copilot|github-copilot]]、[[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]、[[wiki/sources/2026-08-19-pi-agent-github-copilot-provider-setup|Copilot Provider Setup Guide]]
  - completed: 2026-08-19
  - result: Pi Agent 原生支援 Copilot OAuth，執行 `/login` → 選擇 GitHub Copilot → 完成授權即可。模型清單為靜態（from models.dev），如模型不出現需在 VS Code 中先 Enable。

## Backlog

- [ ] W-2026-08-033 研究 Hook 機制：不同 IDE / Harness 的實作比較 #ai-agent
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

- [ ] W-2026-08-008 用 `grill-me` 跑一次完整需求追問（Pi Web） ⏫ #skills
  - next: grilling 已安裝，可執行
  - refs: [[projects/pi-web-access-zh-tw/index|Pi Web project context]]

## Completed

- [x] W-2026-08-034 YouTube ingest：Wow 頻道 5支 AI Agent 前沿研究 ✅ #knowledge #ai-agent
  - completed: 2026-08-06
  - result: 5支影片無字幕，透過 yt-dlp + Gemini Deep Research 深度研究。新增 wiki：1 source note、5 entities（Graphify/AReaL/SkillOpt/MemGraph-RAG/hermes-agent）、4 concepts（Code KG/ATDP/Context-CoT/Meta-skill）。交叉分析：三大趨勢（自進化系統、確定性+機率融合、離線→線上）
  - refs: [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Source Note]]、[[raw/research/2026-08-06-wow-youtube-5-ai-agent-topics|Raw Research]]

- [x] W-2026-08-021 建立知識花園導覽 Database ✅ #knowledge #notion
  - completed: 2026-08-19
  - result: 建立 Database（11 欄位）+ 遷移 6 顆種子 + 建立 6 個新頁面 + 歸檔舊頁面 + 更新 SKILL.md + 補齊「給我的啟發？」欄位
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[Design Spec](work/designs/knowledge-garden-navigator.md)

- [x] W-2026-08-026 優化 Gemini Deep Search Prompt ✅ #ai-agent #skills
  - completed: 2026-08-06
  - result: 建立 3 個 prompt 模板（general/comparison/tutorial），加入 Source Quality Tiers（T1/T2/T3）、Claim Confidence 標記（VERIFIED/INFERRD/UNVERIFIED）、強制引用規則
  - refs: W-2026-08-024（gemini-deep-research skill）、[[wiki/entities/hermes-agent|hermes-agent]]

- [x] W-2026-08-027 設定 Antigravity CLI 環境 ✅ #ai-agent #extension
  - completed: 2026-08-06
  - result: agy v1.1.10 已安裝，設定 5 個 MCP server：notion-mcp-server、gmp-code-assist、chrome-devtools-mcp、context7、sequential-thinking
  - refs: [[wiki/entities/pi-mono|pi-mono]]、W-2026-08-025（AI Agent 網路查詢能力研究）

- [x] W-2026-08-020 盤點 wiki 內容，找出可轉化為花園種子的素材 ✅ #knowledge #notion
  - completed: 2026-08-18
  - result: 巡檢完成，wiki 35 個頁面中僅 6 個進花園（覆蓋率 30%）。建議優先種植：claude-code、copilot、codex、MCP、tau、ai-coding-workflow、vibe-coding 等高價值 entity/concept
  - refs: [[wiki/index|Wiki Index]]、[[wiki/entities/knowledge-garden|knowledge-garden]]

- [x] W-2026-08-019 研究 LSP（Language Server Protocol）與 Code Graph ✅ #ai-agent
  - refs: [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 研究]]、[[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF+LSP+CodeGraph AI Agent 研究]]、[[wiki/entities/lsp|LSP]]、[[wiki/entities/tree-sitter|Tree-sitter]]、[[wiki/concepts/code-graph|Code Graph]]
  - completed: 2026-08-04
  - result: Gemini Deep Research 完成兩份深度研究報告；新增 wiki 頁面：2 source notes、6 entities、1 concept

- [x] W-2026-08-011 Wiki Lint：批量補上 source note provenance ✅ #wiki
  - completed: 2026-08-10
  - result: 25 個 source notes 補上 provenance

- [x] W-2026-08-014 新增 pi-loop-scheduler entity ✅ #ai-agent
  - completed: 2026-08-10
  - result: 記錄 @pi-agents/loop 架構、Idle gating、Anti-thundering-herd、Durable vs Session tasks

- [x] W-2026-08-024 建立 `gemini-deep-research` skill ✅ #skills
  - completed: 2026-08-06
  - result: 完整 skill 結構，3 個 prompt 模板 + 引用驗證腳本 + README

- [x] W-2026-08-004 建立 Notion ↔ Obsidian 雙向同步機制 ✅ #notion
  - completed: 2026-08-06

- [x] W-2026-08-016 測試 Notion 端到端流程 ✅ #notion
  - completed: 2026-08-06

- [x] W-2026-08-013 規劃 skill GitHub repos ✅ #skills
  - completed: 2026-08-08

- [x] W-2026-08-018 建立 skills-repo-manager skill ✅ #skills
  - completed: 2026-08-08

- [x] W-2026-08-015 建立 Notion → raw 抓取流程 ✅ #notion
  - completed: 2026-08-05

- [x] W-2026-08-006 研究 YouTube 字幕抓取方案 ✅ #knowledge
  - completed: 2026-08-03

- [x] W-2026-08-012 建立 `youtube-to-wiki` skill ✅ #knowledge
  - completed: 2026-08-03

- [x] W-2026-08-009 建立每週 wiki lint 與花園巡檢提醒 ✅ #meta
  - completed: 2026-08-10
  - result: 設定每週一 09:00 wiki lint（ID: 7b26bb64）、每週三 10:00 花園巡檢（ID: f9aa104e）

- [x] W-2026-08-007 確認 canonical wiki 頁面與交叉引用一致 ✅ #wiki
  - completed: 2026-08-09
  - result: 解決 graph 超級節點問題，structural files 連結數減少 87%

- [x] W-2026-08-005 測試 URL → raw/web → ingest 全流程 ✅ #knowledge
  - completed: 2026-08-09

- [x] W-2026-07-022 YouTube ingest mattpocock/skills ✅ #knowledge #wiki
  - completed: 2026-07-22

- [x] W-2026-08-001 釐清 Obsidian vault 架構 ✅ #knowledge
  - completed: 2026-08-03

## Work record contract

- 任務狀態只在本檔維護。
- 完成、決策、重要討論與處理結果追加至 `work/history/YYYY-MM.md`。
- 每個 history event 必須包含 `refs:`，至少指向 raw conversation、project 或 wiki 其中之一。
- 沒有形成工作進展或可追溯結果的對話，不需要建立事件。
