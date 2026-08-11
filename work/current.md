# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [x] W-2026-08-049 Wiki 大整理：Redis + CodeReview + Agentic AI + Knowledge Management ✅ #knowledge #wiki
  - completed: 2026-08-10
  - refs: [[wiki/concepts/redis|Redis]]、[[wiki/concepts/ai-code-review|AI Code Review]]、[[wiki/concepts/agentic-ai|Agentic AI]]、[[wiki/concepts/knowledge-management|Knowledge Management]]
  - 已完成：
    - ✅ Wiki Lint 修復：斷裂連結、provenance 缺漏、孤立頁面、index 數據、交叉引用
    - ✅ Redis 整理：9 個 source notes + concept page（快取、叢架、Eviction、記憶體、效能、安全、ACL、Sentinel、授權）
    - ✅ Code Review 整理：concept + topic + 2 個 source notes（OCR、code-review-graph）
    - ✅ Agentic AI：concept page（四大支柱、設計模式、框架比較）
    - ✅ Knowledge Management：concept page + ai-related-seeds source
    - ✅ 其他 ingest：Defect Escape Rate 指南、rust-analyzer LSP、Redis Licenses
    - ✅ 修復 provenance 連結（qwenpaw、waku-agent）
    - ✅ 刪除重複檔案（Cluster Architecture 1.md）
  - 統計：新增 15 個 wiki 頁面，ingest 12 個 raw 檔案

- [x] W-2026-08-048 知識系統架構改進：4 輪圓桌會議 + 花園更新 ✅ #knowledge #meta #notion
  - completed: 2026-08-10
  - refs: [[wiki/decisions/knowledge-system-architecture-decision|架構決策]]、[[wiki/concepts/knowledge-system-architecture|架構概念]]、Notion 知識花園
  - 已完成：
    - ✅ 4 輪圓桌會議（Pi + Gemini + Copilot）：確立「三個操作 + 一個機制 + 一個回流」架構
    - ✅ 修改 AGENTS.md：新增知識演化協定、雙向連結規範、種子成熟標準
    - ✅ 修改 wiki-lint skill：新增矛盾偵測、孤立頁面、半衰期、Source Fidelity
    - ✅ 修改 wiki-ingest skill：新增查詢步驟、Backfill 流程、職責劃分
    - ✅ 補強 index.md：完整列出所有 concepts、entities、sources
    - ✅ 新增 wiki/concepts/knowledge-system-architecture.md
    - ✅ 新增 wiki/decisions/knowledge-system-architecture-decision.md
    - ✅ 更新 Notion 知識花園：研究專題 + 視覺地圖 + 5 個新種子
    - ✅ 同步 cheerio-skills repo：wiki-lint + wiki-ingest + README
  - 核心架構：三個操作（Ingest/Query/Lint）+ 一個機制（知識幫助知識）+ 一個回流（花園→大腦）

- [ ] W-2026-08-047 知識系統健檢機制全面盤點 + AGENTS.md 整理 🆕 #knowledge #meta
  - next: 加強花園巡檢 skill（量化標準 + 結構化輸出）、建立工作追蹤 lint、重建 cron 排程
  - 已完成：
    - ✅ 三大健檢機制盤點（大腦 wiki-lint / 花園 knowledge-garden / 工作 work-tracker）
    - ✅ AGENTS.md 與實際佈局比對（發現 raw/ 2 個 + work/ 5 個未記錄項目）
    - ✅ AGENTS.md 品質掃描（14 項問題：5 過時 + 6 冗餘 + 3 可選）
    - ✅ AGENTS.md 修復：§1 架構合併 + 補資料夾、§2.1 更新 skill 引用、§3.3 改引用 wiki-lint skill、§4.2 provenance 統一、§9 §10 去冗餘、日期更新
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki-lint skill]]、work-tracker SKILL.md
  - 待辦：
    - ⏳ 花園巡檢加強（量化停滯標準、Relation 一致性、Sync Status、視覺地圖完整性）
    - ⏳ 工作追蹤 lint（current.md 格式、refs 完整性、history 一致性）
    - ⏳ 重建 cron 排程（每週一 wiki lint + 每週三花園巡檢）
    - ⏳ §2.4 work/ 定義與 work-tracker SKILL.md 對齊

- [x] W-2026-08-044 建立「圓桌會議」skill：多 AI 對談討論 ✅ #skills #ai-agent
  - completed: 2026-08-11
  - 已完成：
    - ✅ 設計文件 v2（subagent 參與者架構）
    - ✅ 建立 SKILL.md（完整流程、prompt 模板、CLI 指令）
    - ✅ 建立 ~/.agents/skills/round-table/
    - ✅ 新增 Claude 為預設參與者（Claude + Gemini + Copilot）
    - ✅ 建立 chat-with-claude skill（使用 Claude Code CLI）
    - ✅ 新增量化共識偵測機制（newArguments、coverageRate、agreementRate、openDisputes）
    - ✅ 新增主持人結論（每輪 round-N-summary.md）
    - ✅ 新增論點追蹤表（arguments-tracker.md）
    - ✅ 修復重複 Gemini prompt 模板
    - ✅ 新增分歧點阻止結束條件
    - ✅ 更新 wiki entity page（wiki/entities/round-table.md）
  - refs: [[wiki/entities/round-table|round-table entity]]、[[wiki/topics/skill|Skill Topic]]、copilot skill、gy skill、pi-subagents
  - 說明：Pi 主持（不參與），派出 subagent（使用者指定模型）+ Claude + Gemini + Copilot 共同討論
  - 決策：
    - Pi 只主持，不坐在桌上
    - 使用者可指定 0-N 個 subagent 參與者，每個可指定 model
    - 發言順序由 Pi 每輪動態決定（平衡發言、回應缺口、辯證張力）
    - 兩處都存（.pi/round-table/ + work/history/）
    - 結束條件：maxRounds + 量化共識偵測 + 無未解決分歧 + 人類介入
    - 新增：每輪主持人結論 + 論點追蹤表 + 分歧點阻止結束
    - Claude 成為圓桌會議預設參與者（2026-08-11 決策）

- [x] W-2026-08-043 Content 設計 Redesign：四層骨架 + 主觀現實 + Roadmap ✅ #knowledge #skill
  - refs: [[wiki/concepts/content-redesign|Content Redesign]]、Copilot+Gemini 三輪討論
  - 已完成：
    - ✅ Copilot + Gemini 三輪討論，確立四個共識
    - ✅ 重寫 page-content skill（四層骨架+主觀現實+roadmap）
    - ✅ 更新 visualmap skill（索引/註冊表設計）
    - ✅ 更新架構頁
  - 待辦（手動）：
    - ✅ 手動在 Notion 視覺地圖 Database 加進階欄位（類型、關聯種子、關聯專題）
    - ✅ 建立視覺地圖 ↔ 種子/專題的 Relation 綁定（Plannotator 已完成）

- [x] W-2026-08-042 知識花園 Skill 架構重構：Schema + 模板 + 資料流 ✅ #knowledge #skill
  - refs: [[wiki/concepts/knowledge-garden-skill-architecture|架構圖]]、Copilot+Gemini 三輪討論決策
  - 已完成：
    - ✅ Copilot + Gemini 三輪架構討論，確立6項最終決策
    - ✅ 建立 knowledge-garden/schemas/seed_schema.yaml（跟著主 skill）
    - ✅ 重構 knowledge-garden（移除模板、加引用）
    - ✅ 重構 page-content（加品質關卡、mode 切換）
    - ✅ 更新架構頁（含完整資料流圖）

- [x] W-2026-08-038 Notion 整合設計全面重構：Schema + Skill + 回流 ✅ #knowledge #notion
  - next: 為 7 筆種子撰寫完整知識卡片（用 knowledge-garden-page-content skill）
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki/entities/notionApi|notionApi]]、.pi/gemini-runs/notion-review/session-summary.md
  - 已完成：
    - ✅ Phase 1：Topic 重組（ai-agent 拆分為 5 個子 topic）+ Schema 對齊 + Manifest 重構
    - ✅ Phase 2：Notion Relation 多對多修復（7 筆種子全部修復）
    - ✅ Phase 3：建立 3 個新 Skill（notion-page-content、trigger、visualmap）
    - ✅ Phase 4：Gemini 完整存取（gemini-notion-workflow skill）
    - ✅ Phase 5：Notion→Wiki 回流機制（notion-wiki-feedback skill）
  - 待辦（🔴 高優先）：
    - ✅ 為 7 筆種子撰寫完整知識卡片
    - ✅ 為 5 個專題撰寫完整研究報告
    - ✅ 為 NPM Publishing 建立 wiki 頁面
  - 待辦（🟡 中優先）：
    - ⏳ 為 2-3 個種子建立視覺地圖
    - ⏳ 測試回流機制
    - ⏳ 更新所有 Sync Status
  - 待辦（🟢 低優先）：
    - ⏳ 建立 gemini-wiki-co-maintainer Skill
    - ⏳ 設定 cron 定期觸發 Gemini 巡檢
    - ⏳ 測試完整資料流閉環

- [x] W-2026-08-039 Notion 頁面內容逐一手動調整 ✅ #knowledge #notion
  - completed: 2026-08-09
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、W-2026-08-038
  - 已完成：
    - ✅ 7 筆種子頁面全部更新（OpenCodeReview、Plannotator、NPM Publishing、OKF、mattpocock/skills、Omnigent、Pi Agent）
    - ✅ 補充大腦資料（從 wiki 提取完整資訊）
    - ✅ 檢查 GitHub 更新（最新 stars、功能）
    - ✅ 更新成長狀態（Plannotator、OKF、mattpocock/skills、Pi Agent 升級為 🌿 成長期）
    - ✅ 建立 6 個視覺地圖
  - 種子頁面清單：
    - 種子：Plannotator, OpenCodeReview, Omnigent, OKF, mattpocock/skills, Pi Agent 架構研究, NPM Publishing
    - 視覺地圖：6 個建立/更新
  - 進行方式：人類在 Notion UI 直接編輯，或告訴 AI 要改什麼，AI 執行 ntn pages update

- [x] W-2026-08-037 知識花園加強：Relation 關聯 + 視覺地圖 + 改名 ✅ #knowledge #notion
  - completed: 2026-08-09
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、https://app.notion.com/p/5f2a0e0f-91de-466f-879e-9042c8a02169
  - 已完成：
    - ✅ 建立種子 ↔ 專題雙向 Relation 關聯（6 顆種子 + 5 個專題）
    - ✅ 更新 Plannotator 視覺地圖頁面（完整 Skills 介紹、分類、安裝狀態）
    - ✅ 修正視覺地圖頁面位置（從 CHEERIO CORNER 移到種子頁面子頁面）
    - ✅ 改名 CHEERIO CORNER → Cheerio 知識花園
    - ✅ 建立視覺地圖 Database（在 Cheerio 知識花園下）
    - ✅ 加入 Plannotator 視覺地圖 entry
    - ✅ 本地 manifest 同步 + git push
    - ✅ 建立 6 個視覺地圖（OpenCodeReview、Plannotator、OKF、mattpocock/skills、Omnigent、Pi Agent）
    - ✅ 所有種子頁面連結到視覺地圖
  - 視覺地圖清單：
    - OpenCodeReview：https://app.notion.com/p/OpenCodeReview-Agent-3b75979e3a8c81798bddfa2ab9971178
    - Plannotator：https://app.notion.com/p/Plannotator-AI-Agent-3b55979e3a8c8146a69acc9b3ca2292d
    - OKF：https://app.notion.com/p/OKF-AI-3b75979e3a8c8106b476d1e6e78ac394
    - mattpocock/skills：https://app.notion.com/p/mattpocock-skills-AI-Coding-Skills-3b75979e3a8c81708c52e10d8c9e38c6
    - Omnigent：https://app.notion.com/p/Omnigent-Meta-Harness-3b75979e3a8c81e3882cf369ac94f150
    - Pi Agent：https://app.notion.com/p/Pi-Agent-Runtime-Extension-3b75979e3a8c81559c26f28d8db3788b

- [ ] W-2026-08-030 安裝並測試 OpenCodeReview（OCR） ⏫ #ai-agent #code-review
  - next: 安裝 OCR、設定 LLM provider、跑一次 `ocr review` 測試
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/sources/2026-08-20-opencode-review-deep-research|Gemini 深度研究]]、https://github.com/alibaba/open-code-review
  - 範疇：
    - 安裝：`npm install -g @alibaba-group/open-code-review`
    - 設定：`ocr config provider` + `ocr config model`
    - 測試：在現有專案跑 `ocr review`，觀察輸出品質
    - 比較：與 `code-review` skill 的結果做比較
  - 2026-08-20 完成深度分析（README 整理）
  - 2026-08-20 完成 Gemini 深度研究：阿里內部故事、社群討論、SonarQube 互補、私有模型適配、AI 趨勢洞察

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
  - refs: [[work/designs/pi-agent-learning-system|學習系統設計]]、[[wiki/topics/pi-agent-learning/roadmap|學習路線圖]]、[[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 分析]]、[[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought]]、[[wiki/entities/ai-agent-core/token-budget|Token 預算]]
  - 2026-08-20 建立學習系統：流程文件 + wiki 結構 + v0.84.0 分析 + 核心概念頁面（chain-of-thought、token-budget）
  - 2026-08-20 完成 OpenCodeReview 深度分析 + Gemini 深度研究（9 個來源、7 個新洞察）

- [ ] W-2026-08-017 研究 harness 架構，開發自己的 AGENT ⏫ #ai-agent
  - 2026-08-10 新增 pi-loop-scheduler entity：Pi cron job 實作原理（@pi-agents/loop）
  - 2026-08-06 ingest YouTube 5支 AI Agent 研究：Graphify + AReaL + Context-CoT + SkillOpt + MemGraph-RAG
  - next: Pi containerization 方案（Gondolin/Docker/OpenShell）比較、Tau 三層架構分離細節、Hermes 學習迴圈機制
  - refs: [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/tau|tau]]、[[wiki/concepts/meta-harness|meta-harness]]、[[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]]
  - 2026-08-06 完成 Pi Architecture Walkthrough ingest
  - 2026-08-08 ingest waku-agent：四大支柱 readable blueprint
  - 2026-08-03 ingest loop-vs-graph-engineering：Loop vs Graph 兩種工作流模式，更新 waku-agent 加入 graph engineering
  - 2026-08-06 知識花園：建立研究專題 Database + 映射種子到專題
  - 2026-08-10 ingest Chunkless RAG + Docling：IBM Technology 影片，Chunkless RAG 保留文件 tree structure 讓 Agent 推理導航

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



## 🔴 Phase 1：前置知識（建立 Agent 前必學）

> 目標：理解 Agent 的核心組件，知道要建造什麼。

### 🔥 立即可做（簡單、快速、有價值）

- [ ] W-2026-08-030 安裝並測試 OpenCodeReview（OCR） ⏫ #ai-agent #code-review
  - next: 安裝 OCR、設定 LLM provider、跑一次 `ocr review` 測試
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/sources/2026-08-020-opencode-review-deep-research|Gemini 深度研究]]
  - 預估時間：30 分鐘
  - 為什麼先做：安裝簡單，能立即體驗「確定性工程 × Agent」混合架構
  - 步驟：
    1. `npm install -g @alibaba-group/open-code-review`
    2. `ocr config provider` + `ocr config model`
    3. 在本專案跑 `ocr review`

- [ ] W-2026-08-029 學習系統 + v0.84.0 學習任務 ⏫ #ai-agent #learning
  - next: 嘗試全螢幕模式、測試 AGENTS.override.md、玩 samplingParams
  - refs: [[work/designs/pi-agent-learning-system|學習系統設計]]、[[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 分析]]
  - 預估時間：1 小時
  - 為什麼先做：這些是 Pi 的新功能，動手玩能加深理解

### 📚 核心研究（需要時間，但很重要）

- [ ] W-2026-08-017 研究 harness 架構，開發自己的 AGENT ⏫ #ai-agent
  - next: Pi containerization 方案比較、Tau 三層架構、Hermes 學習迴圈
  - refs: [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/tau|tau]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：多天（持續研究）
  - 為什麼重要：這是 Agent 的骨架，決定了整個架構設計
  - 已完成：Pi Architecture Walkthrough、waku-agent、loop-vs-graph-engineering

- [ ] W-2026-08-025 研究 AI Agent 網路查詢能力：Extension 機制與 Search 架構 #ai-agent
  - next: 調研 web search / deep research extension 實作方式
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2-3 小時
  - 為什麼重要：Tool 系統是 Agent 的手腳，怎麼擴充功能

- [ ] W-2026-08-022 研究 MCP Server 架構與實作 #ai-agent
  - next: 調研 MCP protocol spec、transport 層、tool/resource/prompt 三大原語
  - refs: [[wiki/entities/pi-mono|pi-mono]]
  - 預估時間：2-3 小時
  - 為什麼重要：MCP 是 Tool 系統的標準協定

- [ ] W-2026-08-033 研究 Hook 機制：不同 IDE / Harness 的實作比較 #ai-agent
  - next: 調研 Claude Code、Cursor、Windsurf、Pi Agent 的 hook 系統
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2-3 小時
  - 為什麼重要：理解 Agent 的事件系統

- [ ] W-2026-08-NEW-001 研究 Session 管理：對話如何持久化與壓縮 #ai-agent
  - next: 調研 Pi、Claude Code、LangChain 的 session storage 機制
  - refs: [[wiki/entities/pi-agent-core|pi-agent-core]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2 小時
  - 為什麼重要：Agent 需要記住對話，這是核心功能

- [ ] W-2026-08-NEW-002 研究 Context 管理：長對話如何處理 #ai-agent
  - next: 調研 compaction、sliding window、summarization 等策略
  - refs: [[wiki/entities/ai-agent-core/token-budget|Token 預算]]、[[wiki/entities/pi-agent-core|pi-agent-core]]
  - 預估時間：2 小時
  - 為什麼重要：Context window 有限，必須有效管理

## 🟡 Phase 2：實作經驗（在現有 Agent 上練功）

> 目標：親手操作，把知識變成經驗。

- [ ] W-2026-08-031 比較實驗：OCR vs code-review skill #ai-agent #code-review
  - next: 找一個有 diff 的專案，分別用兩種工具審查，比較結果
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/entities/plannotator|Plannotator]]、~/.agents/skills/code-review/
  - blockedBy: [W-2026-08-030]
  - 預估時間：1 小時
  - 為什麼重要：親身體驗不同 code review 方式的差異

- [ ] W-2026-08-032 整合 OCR 到 Code Review 工作流 #ai-agent #code-review
  - next: 設計 OCR → code-review skill → Plannotator 的完整流程
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[work/designs/pi-agent-learning-system|學習系統]]
  - blockedBy: [W-2026-08-031]
  - 預估時間：1-2 小時
  - 為什麼重要：建立自己的 code review 流程

- [ ] W-2026-08-010 建立 `pi-work-tracker` 並取代 `pi-todo-journal` ⏫ #extension
  - next: 抽象 TaskStore / JournalStore adapters → 測試、CI 與 npm 發布
  - refs: [[projects/pi-work-tracker/index|pi-work-tracker Project Bundle]]、[[work/README|Work System]]
  - 預估時間：多天
  - 為什麼重要：Extension 開發經驗，以後自己的 Agent 也可能需要類似機制

## 🟢 Phase 3：工具整合（提升效率）

> 目標：把好的工具整合到工作流。

- [ ] W-2026-08-028 設定 Pi Agent 使用 GitHub Copilot 訂閱作為 Provider #ai-agent #extension
  - next: 執行 `/login` → 選擇 GitHub Copilot → 完成 OAuth
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/github-copilot|github-copilot]]
  - 預估時間：30 分鐘
  - 為什麼重要：有更多模型可用
  - 備註：有免費 GitHub Copilot 訂閱，可在本機設定

- [ ] W-2026-08-008 用 `grill-me` 跑一次完整需求追問（Pi Web） #skills
  - next: grilling 已安裝，可執行
  - refs: [[projects/pi-web-access-zh-tw/index|Pi Web project context]]
  - 預估時間：1 小時
  - 為什麼重要：確認 Pi Web 專案需求

## ⚪ Phase 4：延伸研究（有空再做）

> 目標：深入了解，為未來做準備。

- [ ] W-2026-08-023 研究 MCP Registry 與企業級管理 #ai-agent
  - next: 調研 MCP registry 方案、server 發現與註冊機制、版本管控
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2 小時
  - 為什麼可以等：企業級管理，個人建造不需要

## Backlog

（目前無其他待辦任務）

## Completed

- [x] W-2026-08-041 YouTube ingest：LangGraph in 10 Minutes ✅ #knowledge #ai-agent
  - completed: 2026-08-08
  - result: 抓 auto-generated English 字幕（347 segments → 40 paragraphs）。新增 wiki：source note + entity（langgraph）+ concept（agent-durability-patterns）。重點：Pregel model、reducers、checkpointing、delta channels（5.3GB→129MB）。建立 Pi vs LangGraph 三種持久化路徑比較，作為未來 agent 設計取捨參考。
  - refs: [[wiki/sources/2026-08-21-langgraph-in-10-minutes|source note]]、[[wiki/entities/langgraph|langgraph]]、[[wiki/concepts/agent-durability-patterns|agent-durability-patterns]]

- [x] W-2026-08-039 PDF 處理流程建立：markitdown + pymupdf + pdf-to-wiki skill ✅ #knowledge #tools
  - completed: 2026-08-08
  - result: 建立完整 PDF → Markdown → wiki 流程。markitdown 取代 pdftotext（解決中文亂碼），pymupdf 提取圖片。新建 pdf-to-wiki skill 並推送到 cheerio-skills。處理安裝手冊 PDF（9 張截圖 + 完整中文 Markdown）。
  - 新增 wiki：markitdown entity、plannotator-copilot-setup source
  - 更新：plannotator entity（+Copilot CLI 整合）、AGENTS.md（PDF 處理方式）、wiki-knowledge skill
  - refs: [[wiki/entities/markitdown|markitdown]]、[[wiki/sources/2026-07-23-plannotator-copilot-setup|source]]、cheerio-skills/pdf-to-wiki

- [x] W-2026-08-040 cheerio-skills 同步：7 個新 skills + 2 個更新 ✅ #skills
  - completed: 2026-08-08
  - result: 同步本地 skills 到 cheerio-skills repo。新增 gemini-notion-workflow、knowledge-garden-trigger、knowledge-garden-visualmap、notion-page-content、notion-wiki-feedback、learning-loop、todos。更新 knowledge-garden、wiki-knowledge。
  - refs: https://github.com/CheerioCorner/cheerio-skills

- [x] W-2026-08-036 Notion「任何當下 → AI相關」頁面 ingest ✅ #knowledge #notion
  - completed: 2026-08-07
  - result: 讀取 Notion「任何當下 → AI相關」頁面（Skill、Copilot、Claude、AI Gateway）。新增 wiki 2 entities：agentskills-io-standard（Skill 開放標準）、axway-amplify-ai-gateway（企業 AI Gateway 治理）。使用 agy + Gemini 視覺分析 19 張 AI Gateway 截圖，提取完整企業 AI 治理教材。花園種子已備妥待寫入（raw/notion-ingest/2026-08-07-ai-related-seeds.md）
  - refs: [[wiki/entities/agentskills-io-standard|agentskills-io-standard]]、[[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]]、[[raw/notion-ingest/2026-08-07-ai-related-seeds|待寫入花園種子]]

- [x] W-2026-08-034 YouTube ingest：Wow 頻道 5支 AI Agent 前沿研究 ✅ #knowledge #ai-agent
  - completed: 2026-08-06
  - result: 5支影片無字幕，透過 yt-dlp + Gemini Deep Research 深度研究。新增 wiki：1 source note、5 entities（Graphify/AReaL/SkillOpt/MemGraph-RAG/hermes-agent）、4 concepts（Code KG/ATDP/Context-CoT/Meta-skill）。交叉分析：三大趨勢（自進化系統、確定性+機率融合、離線→線上）
  - refs: [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Source Note]]、[[raw/research/2026-08-06-wow-youtube-5-ai-agent-topics|Raw Research]]

- [x] W-2026-08-021 建立知識花園導覽 Database ✅ #knowledge #notion
  - completed: 2026-08-19
  - result: 建立 Database（12 欄位）+ 遷移 6 顆種子 + 建立 6 個新頁面 + 歸檔舊頁面 + 更新 SKILL.md + 補齊「給我的啟發？」欄位
  - 2026-08-06 調整欄位：移除「一句話」，新增「視覺地圖頁面」
  - 2026-08-06 重建 mattpocock/skills 視覺地圖頁面
  - 2026-08-06 建立研究專題 Database（5 個專題：AI Agent 架構、Extension 開發、Meta-Harness、AI Coding Workflow、知識管理）
  - 2026-08-06 映射 6 顆種子到專題
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[Design Spec](work/designs/knowledge-garden-navigator.md)

- [x] W-2026-08-035 知識花園：建立研究專題 Database + 映射種子 ✅ #knowledge #notion
  - completed: 2026-08-06
  - result: 建立研究專題 Database（10 欄位）+ 5 個初始專題 + 映射 6 顆種子到專題 + 更新 SKILL.md 和 manifest
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、[[work/current|Current Work]]

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

- [x] W-2026-08-046 Obsidian 種子建立：AI Agent 時代的知識管理基礎設施 ✅ #knowledge #notion
  - completed: 2026-08-09
  - refs: [[wiki/entities/obsidian|Obsidian]]、[[wiki/visualizations/obsidian-seed-map|視覺地圖]]、Notion 種子 #3b75979e-3a8c81d8-b7aa-f1f4c88aa957
  - 已完成：
    - ✅ Gemini Deep Research 深度研究（8 個來源、6 個不同網域）
    - ✅ 建立 Notion 種子（🌱 種子期）
    - ✅ 建立視覺地圖（Mermaid code block 直接寫入 Notion）
    - ✅ 建立 wiki 頁面：wiki/entities/obsidian.md
    - ✅ 更新 knowledge-garden manifest
    - ✅ 更新所有花園 Skill：視覺地圖改為直接寫入 Mermaid
    - ✅ 同步 cheerio-skills repo
  - 重點：疫情催生→150 萬用戶、LLM Wiki 範式、raw→wiki→Notion 資料流、OKF 90%+ 相容
  - 關聯專題：AI Agent 架構研究、知識管理系統

- [x] W-2026-08-045 知識花園種子全面更新 + 研究專題重構 ✅ #knowledge #notion
  - completed: 2026-08-09
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、Notion 知識花園
  - 已完成：
    - ✅ 7 筆種子頁面全部更新（內容補充、成長狀態更新）
    - ✅ 建立 4 個新種子（LSP、Code Graph、MCP、LOOP Engineering）
    - ✅ 強化 knowledge-garden skill（批量更新、狀態評估、GitHub 檢查）
    - ✅ 強化 knowledge-garden-visualmap skill（自動從 wiki 產生、模板系統）
    - ✅ 更新 knowledge-garden-page-content skill（四層骨架模板）
    - ✅ 5 個研究專題全部更新（豐富內容、關聯種子、建立視覺地圖）
    - ✅ 建立 8 個視覺地圖
    - ✅ 種子總數從 7 顆增加到 11 顆
  - 種子更新：
    - OpenCodeReview、Plannotator、NPM Publishing、OKF、mattpocock/skills、Omnigent、Pi Agent
  - 新種子：
    - LSP、Code Graph、MCP、LOOP Engineering
  - 研究專題更新：
    - 知識管理系統、AI 驅動的開發系統、Meta-Harness 元鞍具、Extension 開發生態、AI Agent 架構研究
