# Log

> 變更日誌。連結改用普通文字，避免 structural files 變成 graph 超級節點。
> 需要追溯時，查 frontmatter 的 `provenance` 或 `sources` 欄位。

## [2026-08-09] seed | Obsidian — AI Agent 時代的知識管理基礎設施

- 新增種子：Obsidian（Notion + 視覺地圖）
- 新增 wiki 頁面：wiki/entities/obsidian.md
- 來源：Gemini Deep Research + 本機 wiki 知識庫
- 新增：wiki/visualizations/obsidian-seed-map.md（視覺地圖 wiki 頁面）
- 新增：.pi/obsidian-visualmap.svg（SVG 視覺地圖）
- 更新：knowledge-garden.md manifest（加入 Obsidian 種子）
- 重點：疫情催生→150 萬用戶、LLM Wiki 範式、raw→wiki→Notion 資料流、OKF 90%+ 相容
- 關聯專題：AI Agent 架構研究、知識管理系統
- refs: [[wiki/entities/obsidian|Obsidian]]、[[wiki/visualizations/obsidian-seed-map|視覺地圖]]、Notion 種子 #3b75979e-3a8c-81d8-b7aa-f1f4c88aa957

## [2026-08-08] ingest | Content Redesign 實作：重寫 page-content + 更新 visualmap

- 重寫 knowledge-garden-page-content：
  - 移除模板綁定（🌱/🌿/🌳 不再決定內容結構）
  - 改為按標的物類型（種子/專題/地圖）
  - 四層通用骨架（識別→客觀事實→主觀現實→roadmap）
  - 加入 Mode 切換（Quick Draft vs Enriched）
  - 加入品質關卡（Evaluator-Optimizer Loop）
- 更新 knowledge-garden-visualmap：
  - 視覺地圖 Database = 索引/註冊表（不是內容存放）
  - 6 個欄位：頁面、類型、關聯種子、關聯專題、建立時間、更新時間
- 更新架構頁：加入 Content 設計區塊
- refs: [[wiki/concepts/content-redesign|Content Redesign]]、[[wiki/concepts/knowledge-garden-skill-architecture|架構圖]]

## [2026-08-08] ingest | Content 設計 Redesign（Copilot + Gemini 三輪討論）

- 確立四個共識：
  - 四層通用骨架（識別→客觀事實→主觀現實→roadmap）
  - 成長階段≠模板，內容結構由標的物類型決定
  - 研究專題≠種子（有限收斂 vs 無窮演進）
  - 視覺地圖 Database schema（索引/註冊表，不是內容存放）
- 新增 concepts：content-redesign
- 更新 topics：knowledge-mgmt
- refs: [[wiki/concepts/content-redesign|Content Redesign]]

## [2026-08-08] ingest | 知識花園 Skill 架構重構 + 命名統一 + 三輪討論決策

- 重構 10 個相關 skills + 命名統一
- Copilot + Gemini 三輪架構討論，確立6項最終決策
- Schema 遷移到 knowledge-garden/schemas/（跟著主 skill）
- 新增 concepts：knowledge-garden-skill-architecture
- refs: [[wiki/concepts/knowledge-garden-skill-architecture|架構圖]]

## [2026-08-08] ingest | Plannotator Copilot CLI 安裝手冊（PDF）+ MarkItDown 研究

- 來源：PDF `C:/Users/User/Downloads/安裝手冊.pdf` → markitdown 提取 → raw/web/
- 新增 entities：markitdown（Microsoft 萬能格式→Markdown 轉換器）
- 新增 sources：2026-07-23-plannotator-copilot-setup
- 更新 entities：plannotator（+Copilot CLI 整合安裝、+source reference）
- 重點：三種安裝方式（Skill / Prompt / 手動）、資安提醒、常用指令、FAQ
- 方法：先用 pdftotext（中文亂碼），後改用 markitdown（完整保留中文與結構）
- 清理：卸載我安裝的 PyMuPDF（markitdown 已取代）
- refs: [[wiki/sources/2026-07-23-plannotator-copilot-setup|source note]]、[[wiki/entities/plannotator|plannotator]]、[[wiki/entities/markitdown|markitdown]]、[[raw/web/2026-07-23-plannotator-copilot-setup-manual|raw]]

## [2026-08-08] ingest | HTML Slide Deck 製作工作流 + presentation skill

- 來源：Plannotator × Obsidian 簡報製作過程的經驗沉澱
- 新增 concept：html-slide-deck-workflow（整合 guizang + huashu 設計系統）
- 新增 skill：presentation（讓「做簡報」自動觸發正確工作流）
- 更新：skill.md topic nav、index.md
- 重點：guizang 靛藍瓷主題 + huashu deck_stage.js + 反 AI slop + Gemini/Copilot 雙審閱
- refs: [[wiki/concepts/html-slide-deck-workflow|concept]]、presentation skill

## [2026-08-08] ingest | Plannotator × Obsidian 簡報企劃

- 來源：Pi 整理的簡報企劃文件（經過 Gemini + Copilot 雙重審閱）
- 新增：source note（2026-08-08-plannotator-obsidian-presentation）
- 更新：skill.md、knowledge-mgmt.md topic nav、index.md
- 重點：14 頁 HTML slide deck 企劃，知識迴流系統報告，系統架構課分享會
- refs: [[wiki/sources/2026-08-08-plannotator-obsidian-presentation|source note]]

## [2026-08-08] ingest | guizang-ppt-skill + huashu-design

- 來源：raw/web/ — 歸藏 PPT Skill + 花叔 Huashu Design GitHub README
- 新增 entities：guizang-ppt-skill、huashu-design
- 新增 sources：2026-08-08-guizang-ppt-skill、2026-08-08-huashu-design
- 更新：skill.md topic nav、index.md
- 重點：兩款 HTML 原生設計技能，guizang 專注 PPT（雙視覺系統+演講者模式），huashu 覆蓋原型/動畫/MP4/評審
- refs: [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill]]、[[wiki/entities/huashu-design|huashu-design]]、[[wiki/sources/2026-08-08-guizang-ppt-skill|source 1]]、[[wiki/sources/2026-08-08-huashu-design|source 2]]

## [2026-08-21] ingest | LangGraph in 10 Minutes (Explained Clearly)

- 來源：YouTube — Cloud Codes 解析 LangGraph runtime 架構
- 新增：source note + entity（langgraph）+ concept（agent-durability-patterns）
- 更新：loop-vs-graph-engineering（+1 source）、pi-agent-core（+cross-ref）、ai-agent topic nav、index
- 重點：Pregel model、reducers、checkpointing、delta channels（5.3GB→129MB）、Deep Agents / Functional API 替代方案
- 新 concept：Agent Durability Patterns — Pi vs LangGraph 三種持久化路徑比較，作為未來 agent 設計的取捨參考
- refs: [[wiki/sources/2026-08-21-langgraph-in-10-minutes|source note]]、[[wiki/entities/langgraph|langgraph]]、[[wiki/concepts/agent-durability-patterns|agent-durability-patterns]]

## [2026-08-07] ingest | Notion「任何當下 → AI相關」頁面 ingest

- 來源：Notion「任何當下 → AI相關」頁面（Skill、Copilot、Claude、AI Gateway）
- 新增 entities：agentskills-io-standard、axway-amplify-ai-gateway
- 更新 topics：ai-agent.md、skill.md
- 方法：Notion MCP 讀取 + agy Gemini 視覺分析 19 張截圖
- refs: [[wiki/entities/agentskills-io-standard|agentskills-io-standard]]、[[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]]

## [2026-08-07] work | 花園內容檢查 + Learning Loop 建立

- 檢查花園：7 顆種子全有內容，5 個專題全有定義
- 發現連結缺口：OKF、OpenCodeReview 缺專題連結；知識管理系統缺種子
- 新增 learning-loop entity + skill
- 修正 knowledge-garden SKILL：種子建立流程
- refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki/entities/learning-loop|learning-loop]]

## [2026-08-07] new entity | Learning Loop — 自動學習優化系統

- 新增 entity：`wiki/entities/learning-loop.md`
- 新增 skill：`~/.agents/skills/learning-loop/SKILL.md`
- 功能：任務後自動沉淀、工作模式分析、Skill 自我優化
- 修正 knowledge-garden SKILL：種子建立流程（先建 Database 記錄，再補內容）
- 首次測試：回顧 2026-08-07 工作，建立 Learning Journal
- refs: [[wiki/entities/learning-loop|Learning Loop]]、[[wiki/entities/knowledge-garden|knowledge-garden]]

## [2026-08-06] ingest | Wow YouTube 5支 AI Agent 前沿研究

- 來源：Wow 頻道 5 支影片（Knowledge Graph、AReaL2.0、Context-CoT、SkillOpt、MemGraph-RAG）
- 新增 source note：`wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research.md`
- 新增 entities：graphify、area-l、skillopt、memgraph-rag、hermes-agent（更新）
- 新增 concepts：code-knowledge-graph、atdp、context-cot、skillopt-meta-skill
- 更新 topics：ai-agent（+5 entities, +4 concepts）、skill（+1 entity, +1 concept）
- 原始研究報告：`raw/research/2026-08-06-wow-youtube-5-ai-agent-topics.md`
- 方法：yt-dlp 提取影片 metadata → Gemini Deep Research 深度研究 → wiki 整合
- 注意：所有5支影片均無字幕（subtitles disabled），內容來自描述 + Gemini 研究

## [2026-08-20] ingest | OpenCodeReview Gemini 深度研究

- 新增 source：`wiki/sources/2026-08-20-opencode-review-deep-research.md`
- 新增 raw：`raw/research/2026-08-20-opencode-review-deep-research.md`
- 來源：Gemini 2.5-pro 深度研究（9 個來源，3 Tier-1）
- 重點：阿里內部使用故事、社群討論細節、SonarQube 互補關係、私有模型適配、AI 趨勢洞察
- refs: [[wiki/entities/open-code-review|OpenCodeReview]]

## [2026-08-20] work | 新增 3 個 OpenCodeReview 相關任務

- W-2026-08-030：安裝並測試 OCR
- W-2026-08-031：比較實驗 OCR vs code-review skill
- W-2026-08-032：整合 OCR 到 Code Review 工作流
- refs: [[wiki/entities/open-code-review|OpenCodeReview]]

## [2026-08-20] ingest | OpenCodeReview 深度分析與比較

- 新增 entity：`wiki/entities/open-code-review.md`（阿里巴巴 AI 代碼審查工具）
- 內容：核心設計（確定性工程 × Agent 混合）、爆紅原因、使用方式、Benchmark、與 Plannotator / code-review skill 比較
- 重點：19.3k stars、解決通用 Agent 做 code review 的痛點（覆蓋不全、位置漂移、效果不穩定）、Token 消耗僅 1/9
- refs: W-2026-08-029

## [2026-08-20] ingest | 建立 Pi Agent 更新學習系統

- 新增 topic：`wiki/topics/pi-agent-learning/roadmap.md`（四階段學習路線圖）
- 新增 topic：`wiki/topics/pi-agent-learning/progress.md`（學習進度追蹤）
- 新增 entity：`wiki/entities/pi-agent/changelog/v0.84.0.md`（v0.84.0 更新分析）
- 新增 entity：`wiki/entities/ai-agent-core/chain-of-thought.md`（Chain-of-thought 概念）
- 新增 entity：`wiki/entities/ai-agent-core/token-budget.md`（Token 預算概念）
- 新增 design：`work/designs/pi-agent-learning-system.md`（完整學習系統設計）
- 更新 index：新增 AI Agent 學習系統區塊
- 更新 work/current：新增 W-2026-08-029
- 重點：從「使用工具」到「理解原理」到「自己建造」的學習路徑，每次 Pi 更新時執行分析學習迴路

## [2026-08-19] source | 新增 Pi Agent GitHub Copilot Provider 設定指南

- 新增 source note：2026-08-19-pi-agent-github-copilot-provider-setup
- 內容：Pi Agent 原生支援 Copilot OAuth 設定流程、模型清單、常見問題
- 重點：執行 `/login` → 選擇 GitHub Copilot → 完成授權即可；模型清單為靜態，如模型不出現需在 VS Code 中先 Enable
- 來源：Pi Agent 官方文件 + GitHub Issue #4599
- refs: W-2026-08-028

## [2026-08-19] work | 新增 4 個工作項目

- W-2026-08-025：AI Agent 網路查詢能力研究
- W-2026-08-026：優化 Gemini Deep Search Prompt
- W-2026-08-027：設定 Gemini CLI 環境
- W-2026-08-028：研究 Pi Agent 使用 GitHub Copilot 訂閱作為 Provider

- 新增 Notion Database：`4f0c3d63-8d5f-4e02-b6a9-e7d6540bebb3`（9 個欄位）
- 新增導覽頁：`3b25979e-3a8c-81b6-a2ee-ff4945a79e43`（使用指南、統計、相關連結）
- 新增研究專題管理頁：`3b25979e3a8c-81a6-9f98-c1016d370dff`（專題生命週期管理）
- 更新 knowledge-garden manifest：新增 Database 結構、Views、相關連結
- 更新 knowledge-mgmt topic：新增 knowledge-garden entity、Notion 花園連結
- 更新 work/current：新增 W-2026-08-021
- 重點：人機共用導覽系統、混合式組織（成長狀態 + Tags）、6 Views
- 待辦：遷移 6 顆種子、人類調整 Notion 結構、建立 Views

## [2026-08-11] work | Notion 工作盤點

- 整理所有 Notion 相關待辦工作到 work/current.md
- 包括：遷移種子、建立 Views、盤點 wiki 素材、確認 Database 結構等
- 新增 Notion 工作清單區塊

## [2026-08-04] ingest | OKF + LSP + Code Graph 在 AI Coding Agent 生態系的整合應用

- 新增 source：`wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research.md`
- 新增 entity：`wiki/entities/mcp-model-context-protocol.md`（Model Context Protocol）
- 新增 entity：`wiki/entities/github-copilot.md`（GitHub Copilot 生態系）
- 新增 entity：`wiki/entities/claude-code.md`（Claude Code + MCP）
- 新增 entity：`wiki/entities/openai-codex.md`（OpenAI Codex CLI）
- 更新 topic：`wiki/topics/ai-agent.md`（加入 Copilot、Claude Code、Codex、MCP）
- 更新 topic：`wiki/topics/meta-systems.md`（加入 MCP）
- 更新 index：Sources 22→23，Entities 15→19
- 來源：Gemini Deep Research + raw/web（15+ 篇 URLs）
- 重點：三位一體架構（OKF+LSP+CodeGraph）、主流 AI Agent 分析、語言工具鏈、安裝指南、安全性、演進時間線

## [2026-08-04] ingest | LSP 與 Code Graph 技術深度研究

- 新增 source：`wiki/sources/2026-08-04-lsp-code-graph-research.md`
- 新增 entity：`wiki/entities/lsp.md`（Language Server Protocol）
- 新增 entity：`wiki/entities/tree-sitter.md`（Parser Generator）
- 新增 concept：`wiki/concepts/code-graph.md`（代碼語意圖譜）
- 更新 topic：`wiki/topics/meta-systems.md`（加入 LSP、Tree-sitter、Code Graph）
- 更新 topic：`wiki/topics/ai-agent.md`（加入 Code Graph concept、LSP source）
- 更新 index：Sources 21→22，Entities 13→15，Concepts 10→11
- 來源：Gemini Deep Research + raw/web（5 篇 URL）
- 重點：LSP $M \times N \rightarrow M + N$ 架構革新、Tree-sitter 增量解析、Code Graph 語意網路、AI Code Assistant 整合（Graph-RAG、Self-Correction Loop、SCIP/LSIF）

## [2026-08-10] ingest | Antigravity CLI 整合 — Gemini CLI 免費層停用後的整合方案

- 新增 source：`wiki/sources/2026-08-10-antigravity-cli-integration.md`
- 更新 topic：`wiki/topics/ai-agent.md`（加入新 source）
- 更新 index：Sources 20→21
- 來源：raw/conversations/2026-08-10-antigravity-cli-integration.md
- 重點：Gemini CLI 免費層已停用（2026/6/18）、Antigravity CLI（agy）安裝與整合、gy skill 建立、週制配額注意事項

## [2026-08-10] ingest | pi-loop-scheduler — Pi Cron Job 實作原理

- 新增 entity：`wiki/entities/pi-loop-scheduler.md`（@pi-agents/loop 架構解析）
- 新增 source：`wiki/sources/2026-08-10-pi-cron-job-explained.md`
- 更新 topic：`wiki/topics/ai-agent.md`、`wiki/topics/extension-dev.md`
- 更新 index：Entities 12→13，Sources 19→20
- 來源：@pi-agents/loop 原始碼與文件
- 重點：Idle gating（只在 agent 閒置時觸發）、Anti-thundering-herd（50% jitter）、Durable vs Session tasks、Multi-instance safety、Cron expression 5-field format

## [2026-08-03] ingest | Loop vs Graph Engineering — AI Agent Concepts Demystified

- 新增 source：`wiki/sources/2026-08-03-loop-vs-graph-engineering.md`
- 新增 concept：`wiki/concepts/loop-vs-graph-engineering.md`
- 更新 entity：`wiki/entities/waku-agent.md`（加入 graph engineering 章節，sources 2→3）
- 更新 topic：`wiki/topics/ai-agent.md`（加入 loop-vs-graph-engineering concept）
- 更新 index：Sources 18→19，Concepts 10→11
- 來源：raw/youtube（YouTube IMLwvK08JVc，21:30，610 segments）
- 重點：AI Agent 工程階梯（Prompt→Context→Skills→Loop→Graph）、Loop 自主迭代 vs Graph 預定義流程、兩者共存非替代、Graph 內含 Loop

## [2026-08-09] ingest | Redis Caching Patterns — 完整指南

- 新增 source：`wiki/sources/2026-08-09-redis-caching-patterns.md`
- 更新 index：Sources 區塊加入新 source note（sources 17→18）
- 來源：raw/web（Medium Artem Khrienov 文章）
- 重點：Cache-Aside + stampede 防護、Write-Through/Write-Behind 權衡、Hashes 省頻寬、滑動視窗 Rate Limiting、Circuit breaker graceful degradation
- 測試：URL → web_fetch → raw/web/ → ingest 全流程驗證成功

## [2026-08-08] ingest | Waku agent — Local-first personal AI agent

- 新增 entity：`wiki/entities/waku-agent.md`（四大支柱 readable blueprint）
- 新增 source：`wiki/sources/2026-08-02-waku-agent-code-walkthrough.md`（YouTube 20:49）
- 新增 source：`wiki/sources/2026-08-02-waku-agent-github-readme.md`
- 更新 topic：`wiki/topics/ai-agent.md`
- 來源：raw/youtube + raw/web

## [2026-08-08] ingest | pi-agent-dashboard — BlackBeltTechnology Web Dashboard

- 新增 entity：`wiki/entities/pi-agent-dashboard.md`
- 更新 topics：`wiki/topics/ai-agent.md`、`wiki/topics/extension-dev.md`
- 來源：raw/web

## [2026-08-08] ingest | QwenPaw — YouTube + GitHub README

- 新增 entity：`wiki/entities/qwenpaw.md`
- 新增 source：`wiki/sources/2026-08-08-qwenpaw-youtube-intro.md`
- 新增 source：`wiki/sources/2026-08-02-qwenpaw-github-readme.md`
- 更新 topic：`wiki/topics/ai-agent.md`
- 來源：raw/web + raw/youtube

## [2026-08-08] skills-repo | 建立 cheerio-skills 私有 repo

- 盤點自有 skills 並分類（Agent-agnostic / Obsidian-dependent / Pi-specific）
- 建立 `C:/cheerio/pi/cheerio-skills/`，格式符合 `npx skills add` 規範
- 推送到 https://github.com/CheerioCorner/cheerio-skills

## [2026-08-06] e2e-test | Notion ↔ Obsidian 雙向同步測試完成 ✅

- 測試流程：raw → wiki → Notion → raw（回環測試）
- 結果：全部通過

## [2026-08-06] update | Notion ↔ Obsidian 雙向同步機制

- `knowledge-garden` skill 新增 §Manifest 自動同步
- 三向資料流完整：raw → wiki → Notion → raw

## [2026-08-08] lint | Provenance 格式修正 + Vibe Coding 連結修復

- Provenance 格式從 `provenance_raw` + `provenance_url` 遷移為 `provenance: [{kind, path, url}]`
- Vibe Coding 頁面修復 2 個 broken wikilinks
- 所有 sources 頁面已補 provenance

## [2026-08-06] ingest | 批次 ingest 5 筆 raw

- omnigent、OKF catalog、defect metrics、morphir-dotnet、vibe-coding
- 修復 4 個 topic page 遺漏
- 強化 skill/AGENTS.md topic 同步步驟

## [2026-08-06] ingest | Pi Architecture Walkthrough

- 完成 Pi Architecture Walkthrough ingest
- agentic loop 三步驟、session tree、compaction、skills 機制、extensions events

## [2026-08-05] ingest | Notion → raw 抓取流程

- 建立 `notion-to-raw` skill
- 三條路徑：只是看看 / 寫 raw / deep research

## [2026-08-05] ingest | YouTube 字幕抓取方案

- `fetch_transcripts.py`（pytubefix + youtube_transcript_api）可直接複用
- 實測抓取 Tau 影片成功

## [2026-08-03] ingest | YouTube-to-Wiki skill

- 固化 YouTube 字幕抓取 → raw/youtube/ → wiki ingest 流程

## [2026-08-03] ingest | Tau Python Port of Pi

- 建立 entity：tau
- 建立 2 個 source notes（YouTube + GitHub README）

## [2026-08-03] lint | 全域 lint 檢查

- 修正 4 處 visualizations broken links
- 補上 4 個 topic pages frontmatter
- 確認 96 個 wikilink targets 全部正常

## [2026-07-22] ingest | mattpocock/skills tutorial

- ingest 17:16 影片 → source note + entity + concept
- 建立 Skill topic
- 實作多 topic 關聯（topics 多值陣列 + 🛠️ 標記）

## 2026-08-09

- Skills 更名：`copilot` → `chat-with-copilot`、`gy` → `chat-with-gemini`、`gemini-deep-research` → `chat-with-gemini-research`
- 新增 skill：`round-table`（多 AI 圓桌會議，Pi 主持 + subagent 參與者 + Gemini + Copilot）
- 更新 cheerio-skills repo（已 push）
- 更新 wiki：github-copilot.md（skill 引用）、skill topic page（新增本地 skills 區塊 + round-table 設計文件）

---

*此 log 為 append-only。舊紀錄保留在 git 歷史中。*

## [2026-08-09] update | 視覺地圖規則更新：直接寫入 Mermaid code block

- 更新 skills：knowledge-garden-visualmap、knowledge-garden-page-content
- 更新 AGENTS.md：visualizations 規則
- 更新 visualizations/README.md：新增 Mermaid 視覺地圖區塊
- 重點：不再轉換為 SVG/PNG，直接在 Notion 寫入 Mermaid code block
- refs: knowledge-garden-visualmap skill、AGENTS.md
