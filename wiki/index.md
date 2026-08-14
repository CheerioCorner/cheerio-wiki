# Wiki Index

> 維護規則：[[AGENTS|AGENTS.md]] · 目前工作：[[work/current|Current Work]]

> 由 LLM 維護。每次 ingest / lint / 新增頁面後更新。
> 任務：看我就知道這個 wiki 裡有什麼。

---

## Topics（主題式分類）

> 點進 topic page 看該主題下的 entities 和 concepts。

- [[wiki/topics/README|README]]
- [[wiki/topics/agent-infrastructure|Agent Infrastructure]]
- [[wiki/topics/agent-research|Agent Research]]
- [[wiki/topics/ai-agent|AI Agent]]
- [[wiki/topics/ai-development-tools|AI Development Tools]]
- [[wiki/topics/backend-systems|Backend Systems]]
- [[wiki/topics/code-review|Code Review]]
- [[wiki/topics/extension-dev|Extension Development]]
- [[wiki/topics/knowledge-mgmt|Knowledge Management]]
- [[wiki/topics/meta-systems|Meta Systems]]
- [[wiki/topics/pi-agent|Pi Agent]]
- [[wiki/topics/skill|Skill]]

> 🛠️ = 同時屬於多個 topic（見各頁 frontmatter `topics`）

---

## Concepts（抽象知識）— 38 頁

- [[wiki/concepts/ai-code-review.md|AI Code Review]] — 用 AI 自動化代碼審查，從「人工逐行檢查」進化到「AI 初審 + 人類終審」。
- [[wiki/concepts/ai-coding-workflow.md|AI Coding Workflow — 結構化 AI 編碼工作流程]] — 一套結構化的 AI 編碼工作流程，把模糊想法變成可追踪、可審查的程式碼變更。
- [[wiki/concepts/arc-agi-3-benchmark.md|ARC-AGI-3 Benchmark]] — ARC-AGI-3——ARC Prize Foundation 於 2026 年推出的互動式推理 benchmark，測試 agent 在完全未知規則的模擬世界中探索、建立假設、修正錯誤與長期規劃的能力，用 **RHAE**（Relativ
- [[wiki/concepts/atdp.md|ATDP — Agent Trajectory Data Protocol]] — Agent 軌跡數據協定，將 Agent 與環境互動的每一步結構化標準化，為自我進化奠定數據基礎。
- [[wiki/concepts/agent-durability-patterns.md|Agent Durability Patterns — 狀態持久化的三種路徑]] — Agent 需要「記住自己做到哪裡」的程度不同，對應三種持久化路徑。選錯路徑的代價：要么 crash 後重來，要么 complexity tax 太重。
- [[wiki/concepts/agent-extension-installation.md|Agent Extension 安裝位置與方式]] — 不同的 Agent 框架有各自安裝 Extension 的方式和儲存位置。記錄這些差異有助於理解 Agent 的運作機制。
- [[wiki/concepts/agentic-ai.md|Agentic AI]] — 具備自主性、能使用工具、跨步驟執行任務的 AI 系統。核心公式：**LLM + Tools + Loop**。
- [[wiki/concepts/chunkless-rag.md|Chunkless RAG — 保留文件結構的檢索增強生成]] — Chunkless RAG 是一種保留文件 tree structure 的 RAG 方法，讓 AI Agent 透過推理導航文件，而非切成 chunks 做相似度搜尋。
- [[wiki/concepts/code-execution-as-tool-calling.md|Code Execution as Tool Calling]] — 「模型寫 Python 腳本執行多操作」取代「一次一個 tool call」的模式——單一腳本可等同 50 次 tool call，加速執行並透過程式內過濾節省 context window。
- [[wiki/concepts/code-graph.md|Code Graph]] — 跨檔案、跨模組的代碼語意圖譜，結合 AST、控制流圖、資料流圖與全域符號表，是現代 Code Intelligence 與 AI RAG 的核心基礎。
- [[wiki/concepts/code-importance-spectrum.md|Code Importance Spectrum — 程式碼重要性光譜]] — 一個用來判斷應該閱讀多少程式碼的框架：從不重要的 slop 到人命關天的關鍵系統，不同層級需要不同的驗證策略。
- [[wiki/concepts/code-knowledge-graph.md|Code Knowledge Graph — 代碼知識圖譜]] — 結合 tree-sitter AST 解析與圖論算法，將代碼庫轉換為結構化知識圖譜，讓 AI Agent 精確理解代碼架構。
- [[wiki/concepts/content-redesign.md|Content 設計 Redesign]] — 知識花園 Content 設計重構：從「套模板」到「針對性報告」。經 Copilot + Gemini 三輪討論確立。
- [[wiki/concepts/context-cot.md|Context-CoT — 上下文思維鏈]] — 透過「三階煉獄」推理合成 pipeline，強制 LLM 嚴格錨定上下文進行推理，解決「作弊」問題。
- [[wiki/concepts/continual-harness.md|Continual Harness]] — Continual Harness——把 agent harness 自身的狀態（prompt、skills、memory、sub-agents）抽象成 agent 可以 create/read/update/delete（CRUD）的物件
- [[wiki/concepts/harness.md|Harness — LLM 的驅動層]] — 包住 LLM 的驅動層，定義 loop / 工具 / 權限 / context / 記憶，三層分類（coding agent → meta-harness → multi-agent orchestration）🌱
- [[wiki/concepts/defect-metrics.md|Defect Metrics — 軟體品質測量]] — 兩個關鍵的軟體品質指標：Defect Density（缺陷密度）和 Escape Rate（逃脫率）。
- [[wiki/concepts/html-slide-deck-workflow.md|HTML Slide Deck 製作工作流]] — 用 AI agent 製作高品質 HTML 簡報的完整工作流，整合 guizang-ppt-skill 和 huashu-design 兩套設計系統。
- [[wiki/concepts/knowledge-management.md|Knowledge Management]] — 系統化地收集、組織、檢索、維護知識的方法論與工具鏈。
- [[wiki/concepts/late-conversion.md|Late Conversion(最晚轉換)]] — 一種型別設計策略:**整個內部邏輯都以「應用層 AgentMessage」運作,只在邊界(打 LLM 前一刻)才濾成 LLM 能懂的 `Message[]`**。換句話說:保留訊息的「應用程式語義」直到最後一刻再翻譯。
- [[wiki/concepts/lint-ruleset.md|Lint 規則集]] — 定義「什麼是好的知識」的標準，用來自動化檢查知識庫的品質。
- [[wiki/concepts/loop-vs-graph-engineering.md|Loop vs Graph Engineering — AI Agent 兩種核心工作流模式]] — Loop 與 Graph 是 AI Agent 系統中兩種互補的工作流模式：Loop 讓 agent 自主迭代探索，Graph 用預定義流程平行執行。兩者共存而非替代。
- [[wiki/concepts/moc-map-of-content.md|MOC — Map of Content]] — 一種導航頁面，用來連結相關的筆記。它不是筆記本身，而是一張「地圖」。
- [[wiki/concepts/meta-harness.md|Meta-Harness]] — Meta-Harness 是「對 harness 本身的再抽象/最佳化層」——有兩個獨立但共享核心隱喻的主流定義。
- [[wiki/concepts/minimal-agent-philosophy.md|Minimal Agent Philosophy — 「減法大於加法」]] — 兩篇來源、兩個視角,**落到同一個結論**:agent 框架的價值不取決於堆了多少功能,而取決於它**拒絕排除了多少東西**。
- [[wiki/concepts/okf-open-knowledge-format.md|OKF (Open Knowledge Format) — 統一的知識表示格式]] — 用 **Markdown + YAML frontmatter** 來表示知識，讓 **人類和 AI 都能讀**。
- [[wiki/concepts/para.md|PARA — Projects/Areas/Resources/Archives]] — Tiago Forte 在《Building a Second Brain》中提出的信息組織系統，把所有信息分成四類。
- [[wiki/concepts/npm-publishing-workflow.md|Package Publishing Workflow — 統一的發佈流程觀點]] — **核心觀點：** 不管叫什麼名字（Package、Extension、Marketplace），發佈到市場的流程本質上是一樣的。差異只在平台、市場和來源不同。
- [[wiki/concepts/recursive-language-model.md|Recursive Language Model (RLM)]] — RLM（Recursive Language Model）——把 **context 當變數**、把 subagent delegation 當成 persistent REPL 內函式呼叫的架構，讓模型能寫「以自己的 context 為操
- [[wiki/concepts/redis.md|Redis]] — In-memory key-value 資料庫，μs 級延遲，適合快取、Session、即時排行、Rate Limiting。
- [[wiki/concepts/skillopt-meta-skill.md|SkillOpt Meta-skill — 技能優化元技能]] — 關於「如何編寫與重構技能規範」的高階指導法則，控制 LLM 以受控預算進行 SKILL.md 的文字級修復。
- [[wiki/concepts/vibe-coding.md|Vibe Coding — 自然語言驅動的程式碼生成]] — 用自然語言和 AI 工具生成和優化程式碼的開發方式，降低門檻但引入新的安全挑戰。
- [[wiki/concepts/zettelkasten.md|Zettelkasten — 卡片盒筆記法]] — 由德國社會學家 Niklas Luhmann 實踐的知識管理方法，經 Sönke Ahrens《How to Take Smart Notes》系統化後廣泛流行。
- [[wiki/concepts/design-md-format.md|design.md Format]] — Google 定義的品牌設計文件格式，讓 AI coding agent 在產生設計前先讀懂品牌規範，確保所有輸出 on-brand。
- [[wiki/concepts/pi-project-workspace-model.md|pi-project-workspace-model — Pi 專案/工作區模型]] — Pi 的「專案」概念**不強制綁定單一實體路徑**。專案 = **設定作用域**（`.pi/` 目錄存在的位置），而非工作目錄本身。這使得「一個實體路徑對應多個專案人格」成為可能。
- [[wiki/concepts/knowledge-system-architecture.md|知識系統架構]] — 經過 4 輪圓桌會議（Pi + Gemini + Copilot）討論確定，並於 2026-08-12 修正為雙模型共識機制（取代人類確認）的知識管理系統架構。詳見 [[wiki/decisions/knowledge-system-arc
- [[wiki/concepts/knowledge-garden-skill-architecture.md|知識花園 Skill 架構]] — 知識花園相關 skills 的架構全景圖，描述 11 個 skill 的職責、資料流和關聯。
- [[wiki/concepts/semantic-relations.md|語意關係 — Semantic Relations]] — 定義頁面之間「怎麼連結」的方式——不只是「這兩個頁面有關」，而是「A 支持 B」或「A 矛盾 B」。

---

## Entities（具體實體）— 46 頁

- [[wiki/entities/area-l.md|AReaL — 開源 Agent 強化學習基礎設施]] — Ant Group 等機構發表的 AReaL2.0 系統，實現 Agent 自我進化的線上強化學習框架。
- [[wiki/entities/axway-amplify-ai-gateway.md|Axway Amplify AI Gateway — 企業 AI 治理層]] — 企業級 AI 閘道，在前端應用程式、AI Agents 與企業後端 IT 生態系之間搭建安全、受控且靈活的橋樑。
- [[wiki/entities/ai-agent-core/chain-of-thought.md|Chain-of-thought (CoT)]] — 讓模型「先想再答」的推理技術。
- [[wiki/entities/claude-code.md|Claude Code — Anthropic AI Coding Agent]] — Claude Code 是 Anthropic 推出的 agentic coding tool，以終端機為起點，擴展到 Desktop App、Web、IDE、GitHub（`@claude` tag）。核心建立在 MCP（Model Co
- [[wiki/entities/claude-design.md|Claude Design]] — Anthropic 的 AI 設計工具，讓使用者透過簡單 prompt 建立 Wireframe、Prototype 與動畫，並可透過 MCP 匯出到 Claude Code 實作。
- [[wiki/entities/docling.md|Docling — IBM 文件解析與結構化框架]] — Docling 是 IBM 開源的文件解析框架，把 PDF 等非結構化文件轉換成結構化的 tree document，支援 Chunkless RAG。
- [[wiki/entities/github-copilot.md|GitHub Copilot — AI 程式碼輔助生態系]] — GitHub Copilot 是 GitHub（Microsoft 旗下）推出的 AI 程式碼輔助產品線，從 IDE 補全演化為完整 Agent 生態系：Extensions SDK、Agent Skills、Cloud Agent、Cod
- [[wiki/entities/graphify.md|Graphify — 開源代碼知識圖譜工具]] — 接近 10 萬 Star 的 GitHub 開源項目，將代碼庫轉換為知識圖譜，大幅提升 AI Coding Agent 的理解能力。
- [[wiki/entities/lsp.md|LSP (Language Server Protocol)]] — 微軟提出的語言伺服器協定，將程式語言語意分析從編輯器中解耦，實現 $M \times N \rightarrow M + N$ 的架構革新。
- [[wiki/entities/langgraph.md|LangGraph — Graph-based Agent Runtime with Durability]] — LangGraph 是 LangChain 團隊開發的低階 agent orchestration 框架，核心價值在 durability（checkpointing / human-in-the-loop / time travel），而
- [[wiki/entities/mcp-model-context-protocol.md|MCP (Model Context Protocol)]] — Anthropic 推出的開放標準，將 LSP、Code Graph 等服務封裝為 AI Agent 可呼叫的 Tools。
- [[wiki/entities/mario-zechner.md|Mario Zechner]] — 維護 `badlogic/pi-mono` 整套 monorepo 的作者。他的立場:**「自主 agent 就是 LLM + tools + 一個迴圈」**。
- [[wiki/entities/markitdown.md|MarkItDown]] — Microsoft 出品的「萬能格式 → Markdown」轉換器，專為 LLM 和文字分析流程設計。
- [[wiki/entities/memgraph-rag.md|MemGraph-RAG — 記憶圖譜多智能體檢索增強生成]] — KDD 2026 頂會論文，透過三層全局記憶架構與三大 AI 神探多智能體協作，實現 0.061 秒極速檢索。
- [[wiki/entities/obsidian.md|Obsidian — AI Agent 時代的知識管理基礎設施]] — 從疫情催生的個人專案，成長為 150 萬活躍用戶的 AI Agent 最佳知識儲存庫。
- [[wiki/entities/omnigent.md|Omnigent]] — Databricks 開源的 meta-harness，位於現有 AI agents 之上，提供組合（Combine）、控制（Control）、協作（Share）三大能力。
- [[wiki/entities/openai-codex.md|OpenAI Codex]] — OpenAI 的 AI Coding Agent，從 Code Completion 演化為命令列 Agent 工具。
- [[wiki/entities/open-code-review.md|OpenCodeReview]] — 阿里巴巴開源的 AI 代碼審查 CLI 工具，19.3k stars，確定性工程 × Agent 混合架構。
- [[wiki/entities/pi-agent/changelog/v0.84.0.md|Pi Agent v0.84.0 更新分析]] — 版本日期：2026-08-06 | 分析日期：2026-08-20 | 重要性：⭐⭐⭐⭐（4/5）
- [[wiki/entities/plannotator.md|Plannotator]] — AI coding agent 的視覺化審閱工具，在瀏覽器中標注 plan、code review、HTML artifact。
- [[wiki/entities/prime-agent.md|Prime Agent — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent]] — Prime Intellect 於 2026-08-05 發布的開源 self-improving coding harness，建構在 [[wiki/entities/pi-agent-core|pi-agent-core]] 之上，核心
- [[wiki/entities/qwenpaw.md|QwenPaw — Agent OS 架構的本地 AI 助理]] — AgentScope 團隊開發的開源個人 AI 助理，以 Agent OS 架構為核心，強調本地部署、安全沙箱與多通道整合。
- [[wiki/entities/round-table.md|Round Table — 多 AI 圓桌會議]] — Pi 主持的多 AI 討論機制。Pi 不參與討論，派出 subagent 搭配 Claude、Gemini、Copilot 進行序列討論。
- [[wiki/entities/skillopt.md|SkillOpt — 微軟技能優化器]] — 微軟提出的 SkillOpt 框架，將 SKILL.md 視為可訓練的外部參數，不修改閉源模型權重實現 Agent 自我進化。
- [[wiki/entities/theo-t3gg.md|Theo (t3gg) — 程式碼重要性光譜與驗證方法論倡導者]] — Theo（t3gg）是科技 YouTube 頻道主，以務實的軟體工程觀點聞名，近期提出「程式碼重要性光譜」和「用 cheap code 驗證 important code」的方法論。
- [[wiki/entities/ai-agent-core/token-budget.md|Token 預算]] — 模型的「記憶力」和「思考力」都是有限的，要學會分配。
- [[wiki/entities/tree-sitter.md|Tree-sitter]] — 高效能 Parser Generator，支援毫秒級增量解析，是現代 Code Intelligence 的核心基礎設施。
- [[wiki/entities/uncle-bob.md|Uncle Bob (Robert C. Martin) — Clean Code 作者、軟體工程資深實踐者]] — Uncle Bob（Robert C. Martin）是《Clean Code》和《Clean Architecture》作者，軟體工程界的標誌性人物，近期因「不再閱讀 AI 生成的程式碼」言論引發熱議。
- [[wiki/entities/agentskills-io-standard.md|agentskills.io — AI Agent Skill 開放標準]] — 一個包含**指令、腳本、資源**的可移植工作流模塊。
- [[wiki/entities/guizang-ppt-skill.md|guizang-ppt-skill — AI Agent HTML PPT 技能]] — 歸藏（@op7418）開發的 AI agent 簡報技能，生成單文件 HTML 橫向翻頁 PPT、配圖和多平台封面，內建演講者模式。
- [[wiki/entities/hermes-agent.md|hermes-agent — 開源 AI Agent 框架]] — Nous Research 開源的 AI Agent，具備 40+ 工具鏈、跨會話記憶與動態 Skill 庫生成機制。
- [[wiki/entities/huashu-design.md|huashu-design — HTML 原生設計技能]] — 花叔（@alchaincyf）開發的 HTML 原生設計技能，一句話 prompt 生成高保真原型、幻燈片、動畫，含 20 設計哲學 + 5 維評審 + MP4 導出。MIT 開源。
- [[wiki/entities/knowledge-garden.md|knowledge-garden — Cheerio 知識花園 Manifest]] — **Notion 知識花園**的架構藍圖與離線快取。
- [[wiki/entities/knowledge-garden-to-raw.md|knowledge-garden-to-raw — Notion → Raw 抓取 Skill]] — 從 Notion 知識花園抓取頁面內容，建立 raw 來源檔案，準備進行 wiki ingest。
- [[wiki/entities/learning-loop.md|learning-loop]] — 自動學習優化系統，建立完整的學習迴圈：做 → 記 → 學 → 優化 → 做。
- [[wiki/entities/mattpocock-skills.md|mattpocock/skills — AI Coding Agent Skills 集合]] — Matt Pocock 維護的 AI coding agent skills 集合，162k+ stars，7.5M+ downloads。
- [[wiki/entities/morphir-dotnet.md|morphir-dotnet]] — FINOS 開發的 .NET binding for Morphir ecosystem，提供 IR（intermediate representation）和 tooling。
- [[wiki/entities/notionApi.md|notionApi — Notion MCP 伺服器]] — **MCP 伺服器**：`notionApi`，在 `~/.pi/agent/mcp.json` 設定，提供 Notion API 存取能力。
- [[wiki/entities/pi-agent-core.md|pi-agent-core — 5 檔 / 1,500 行的 Agent Runtime]] — pi-mono monorepo 內的 `packages/agent/`,全文為 agent runtime。**整個 runtime 由 5 個檔、約 1,500 行 TypeScript 構成**(以 [[[../../raw/web
- [[wiki/entities/pi-agent-dashboard.md|pi-agent-dashboard — Real-time Web Dashboard for Pi Sessions]] — BlackBeltTechnology 開發的 Pi coding-agent 即時 Web Dashboard。一個瀏覽器 tab 管理多個 pi session——**與 TUI 並存，不取代它**。
- [[wiki/entities/pi-loop-scheduler.md|pi-loop-scheduler — Pi 的 Cron Job 與排程系統]] — `@pi-agents/loop` — Pi 的排程 extension，提供 cron job、動態自節奏、idle gating 等功能。非 core runtime 內建，而是透過 extension 機制載入。
- [[wiki/entities/pi-mono.md|pi-mono — Agent Toolkit Monorepo]] — [`earendil-works/pi`](https://github.com/earendil-works/pi)（前身 `badlogic/pi-mono`）是 Mario Zechner 維護的 TypeScript agent t
- [[wiki/entities/pi-web-access-zh-tw.md|pi-web-access-zh-tw — Web 存取擴充的繁體中文本地化版]] — **你維護的 `pi-web-access` 繁體中文本地化套件**，已安裝到 Pi 全域（`pi install file:../../../Cheerio/pi-web-access-zh-tw`）。
- [[wiki/entities/tau.md|tau — Pi 的 Python 移植版]] — Tau 是 Pi 的 **Python port**，由 [Hugging Face](https://huggingface.co) 開發並開源（MIT license）。架構與 Pi 完全相同，差異只在 TUI 層使用 Textual 
- [[wiki/entities/waku-agent.md|waku-agent — Local-first personal AI agent with harness + loop + memory + eval]] — Waku agent 是一個 local-first 個人 AI 助理，以 readable code 展示 agent 系統的四大支柱：harness、loop、memory、eval/LLM-Ops。
- [[wiki/entities/wiki-knowledge.md|wiki-knowledge — Obsidian 知識庫操作 Skill（已刪除）]] — ⚠️ **已刪除**（2026-08-08）。原始的三大流程已拆成三個獨立 skill，dispatcher 功能不再需要。

---

## Sources（來源筆記）— 58 頁

- [[wiki/sources/2026-07-11-plannotator-research.md|2026-07-11 Plannotator 研究]] — title: 2026-07-11 Plannotator 研究
- [[wiki/sources/2026-07-13-omnigent-meta-harness-databricks.md|2026-07-13-omnigent-meta-harness-databricks]] — Databricks 官方部落格文章，宣布開源 Omnigent — 一個位於現有 AI agents 之上的 meta-harness 層。
- [[wiki/sources/2026-07-18-pi-resource-inventory.md|2026-07-18 Pi 資源完整盤點]] — 來源：人類在 Pi 互動模式下要求「檢視我們現在擁有的所有資源」，Agent 掃描專案目錄、Pi 設定目錄、Obsidian 知識庫，彙整而成。
- [[wiki/sources/2026-07-31-npm-publishing-setup.md|2026-07-31 Package Publishing 首次實作]] — 今天完成了第一個 Package 的發佈流程設定。雖然這次是用 NPM，但學到的流程適用於所有市場。
- [[wiki/sources/2026-08-01-okf-extension-development.md|2026-08-01 OKF Extension 開發實作]] — title: 2026-08-01 OKF Extension 開發實作
- [[wiki/sources/2026-08-02-wiki-okf-migration-complete.md|2026-08-02 Wiki × OKF 架構優化完整工作紀錄]] — 本頁是本輪 Wiki × OKF 架構優化的完整 canonical work record。它記錄決策、實作、驗證、Git commits、保留邊界與未來工作；不是 package source code、PLAN 或 raw conve
- [[wiki/sources/2026-08-02-defect-density-escape-rate.md|2026-08-02-defect-density-escape-rate]] — 兩篇關於軟體品質指標的文章彙整：Defect Density（缺陷密度）和 Escape Rate（逃脫率）。
- [[wiki/sources/2026-08-13-garden-guard-incident.md|2026-08-13 花園巡檢事故與 garden-guard extension 建立全記錄]] — 類型：內部事故報告 + 修復全過程記錄
- [[wiki/sources/2026-08-10-antigravity-cli-integration.md|Antigravity CLI 整合 Session]] — 2026-08-10 的實作 session，整合 Antigravity CLI 取代已停用的 Gemini CLI 免費層。
- [[wiki/sources/2026-08-02-defect-escape-rate-guide.md|Defect Escape Rate（DER）測量指南]] — 來源：[Step-by-step guide on how to measure Defect Escape Rate](https://instatus.com/blog/der)
- [[wiki/sources/2026-08-05-hermes-agent-github-readme.md|Hermes Agent: GitHub README — 自我改進 AI Agent]] — 來源：[NousResearch/hermes-agent GitHub README](https://github.com/NousResearch/hermes-agent)
- [[wiki/sources/2026-08-08-hermes-architecture-explained.md|Hermes Architecture EXPLAINED: Memory, Context & Gateways]] — 來源：[YouTube — Hermes Architecture EXPLAINED: Memory, Context & Gateways](https://youtu.be/n32qq7Kwzh0)
- [[wiki/sources/2026-08-13-claude-design-youtube.md|How To Use Claude Design To Build Beautiful Sites]] — AI LABS 教學影片，介紹 Claude Design 的五階段工作流程，從 design.md 到實際部署。
- [[wiki/sources/2026-08-04-lsp-code-graph-research.md|LSP 與 Code Graph 技術深度研究]] — Gemini Deep Research 完成的 LSP（Language Server Protocol）與 Code Graph 技術研究報告，涵蓋架構、實作案例、AI 整合應用。
- [[wiki/sources/2026-08-21-langgraph-in-10-minutes.md|LangGraph in 10 Minutes (Explained Clearly)]] — 來源：[YouTube — LangGraph in 10 Minutes (Explained Clearly)](https://www.youtube.com/watch?v=BwZbdCzmZJc)
- [[wiki/sources/2026-08-03-loop-vs-graph-engineering.md|Loop vs Graph Engineering — AI Agent Concepts Demystified]] — 來源：[YouTube — Loop vs Graph Engineering — AI Agent Concepts Demystified](https://youtu.be/IMLwvK08JVc)
- [[wiki/sources/2026-08-07-ai-related-seeds.md|Notion AI 相關種子 — agentskills.io + Axway AI Gateway]] — 來源：Notion「任何當下 → AI相關」頁面
- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research.md|OKF + LSP + Code Graph 在 AI Coding Agent 生態系的整合應用]] — Gemini Deep Research 完成的三位一體程式碼智慧架構研究，涵蓋 OKF、LSP、Code Graph 在主流 AI Coding Agent 的整合應用。
- [[wiki/sources/2026-08-01-okf-knowledge-catalog.md|OKF Knowledge Catalog — Google Cloud 參考實作]] — Google Cloud 開發的 OKF 參考實作，包含 reference agent 和視覺化工具。
- [[wiki/sources/2026-08-20-opencode-review-deep-research.md|OpenCodeReview 深度研究（Gemini Research）]] — Gemini 2.5-pro 執行的深度研究，涵蓋技術架構、社群爆紅原因、競爭對手比較、實際使用場景。
- [[wiki/sources/2026-08-19-pi-agent-github-copilot-provider-setup.md|Pi Agent GitHub Copilot Provider 設定指南]] — 整理自 Pi Agent 官方文件與 GitHub Issues，供 W-2026-08-028 參考使用。
- [[wiki/sources/2026-08-06-pi-architecture-walkthrough.md|Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained]] — 來源：[YouTube — Pi Architecture Walkthrough — Full Agent Core + Interactive Layer Explained](https://www.youtube.com/watch
- [[wiki/sources/2026-08-10-pi-cron-job-explained.md|Pi Cron Job 實作原理 — @pi-agents/loop 架構解析]] — 從 `@pi-agents/loop` extension 的原始碼與文件，解析 Pi 的 cron job 系統如何實作。
- [[wiki/sources/2026-08-05-pi-github-readme.md|Pi GitHub README — Agent Harness 總覽]] — 來源：[earendil-works/pi GitHub README](https://github.com/earendil-works/pi)
- [[wiki/sources/2026-07-23-plannotator-copilot-setup.md|Plannotator IT 安裝手冊：GitHub Copilot CLI 整合]] — 來源：PDF 安裝手冊（2026-07-23），描述 Plannotator 與 GitHub Copilot CLI 在 Windows 上的整合安裝流程。
- [[wiki/sources/2026-08-08-plannotator-obsidian-presentation.md|Plannotator × Obsidian — 知識迴流系統報告簡報企劃]] — 來源：Pi 整理的簡報企劃文件，經過 Gemini + Copilot 雙重審閱
- [[wiki/sources/2026-08-13-prime-agent-youtube.md|Prime Agent — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent]] — Martí Blanes 介紹 Prime Agent：建構於 Pi 之上，用 Python runtime 取代傳統 tool calling 的開源 coding agent。
- [[wiki/sources/2026-08-13-prime-agent-arc-agi-3-aiposthub.md|Prime Agent 是什麼？ARC-AGI-3「95.5%」背後（AI郵報分析）]] — AI 郵報（作者 Philo）針對 [[wiki/entities/prime-agent|Prime Agent]] 發布的獨立中文分析文，2026-08-06 刊出。核心貢獻是拆解 ARC-AGI-3「95.5%」宣稱背後的三種成績口徑
- [[wiki/sources/2026-08-13-prime-agent-official-blog.md|Prime Agent: A self-improving RLM agent（官方部落格）]] — Prime Intellect 官方部落格原文，2026-08-05 發布，宣布開源 self-improving coding harness [[wiki/entities/prime-agent|Prime Agent]]，作者 Se
- [[wiki/sources/2026-08-02-qwenpaw-github-readme.md|QwenPaw: GitHub README — 完整功能與安裝指南]] — 來源：[agentscope-ai/QwenPaw GitHub README](https://github.com/agentscope-ai/QwenPaw)
- [[wiki/sources/2026-08-08-qwenpaw-youtube-intro.md|QwenPaw: Your Personal AI Assistant — YouTube 介紹影片]] — 來源：[YouTube — QwenPaw: Your Personal AI Assistant](https://youtu.be/QE6FVnNcd3Y)
- [[wiki/sources/2026-08-03-redis-acl.md|Redis ACL（Access Control List）完整指南]] — 來源：[Redis ACL](https://redis.io/docs/latest/operate/oss_and_stack/management/security/acl/)
- [[wiki/sources/2026-08-09-redis-caching-patterns.md|Redis Design Patterns for Caching and Session Management]] — 來源：Artem Khrienov (2026-03-05) — Medium 26 分鐘長文，涵蓋 Redis 數據結構、緩存模式、Session 管理、Rate Limiting 與最佳實踐。
- [[wiki/sources/2026-08-03-redis-cluster-architecture.md|Redis Enterprise Cluster Architecture]] — 來源：[Redis Enterprise Cluster Architecture](https://redis.io/technology/redis-enterprise-cluster-architecture/)
- [[wiki/sources/2026-08-03-redis-eviction-policy.md|Redis Eviction Policy 完整指南]] — 來源：[Redis Eviction Policy](https://redis.io/docs/latest/operate/rs/databases/memory-performance/eviction-policy/)
- [[wiki/sources/2026-08-03-redis-memory-optimization.md|Redis Memory Optimization 策略]] — 來源：[Redis Memory Optimization](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/memory-optimiz
- [[wiki/sources/2026-08-03-redis-performance-tuning.md|Redis Performance Tuning 最佳實踐]] — 來源：[Redis Performance Tuning Best Practices](https://redis.io/faq/doc/1mebipyp1e/performance-tuning-best-practices)
- [[wiki/sources/2026-08-03-redis-security-practices.md|Redis Security Best Practices]] — 來源：[Redis Recommended Security Practices](https://redis.io/docs/latest/operate/rs/security/recommended-security-practice
- [[wiki/sources/2026-08-03-redis-sentinel-client-spec.md|Redis Sentinel Client Spec — 客戶端整合規範]] — 來源：[Redis Sentinel Client Spec](https://redis.io/docs/latest/develop/reference/sentinel-clients/)
- [[wiki/sources/2026-08-03-redis-licenses.md|Redis 授權指南 — 企業導入必讀]] — 來源：[Redis Licenses](https://redis.io/legal/licenses/)
- [[wiki/sources/2026-08-03-tau-python-port-of-pi.md|Tau: A Python Port of Pi — Coding Harness Deep Dive]] — 來源：[YouTube — Tau: A Python Port of Pi](https://www.youtube.com/watch?v=qo1QNxWcm28)（25:03）
- [[wiki/sources/2026-08-05-tau-github-readme.md|Tau: GitHub README]] — 來源：[huggingface/tau GitHub README](https://github.com/huggingface/tau)
- [[wiki/sources/2026-08-10-uncle-bob-stopped-reading-ai-generated-code.md|Uncle Bob Stopped Reading AI-Generated Code]] — 來源：[YouTube — Uncle Bob Stopped Reading AI-Generated Code](https://www.youtube.com/watch?v=sClTAvkQDOU)
- [[wiki/sources/2026-08-02-vibe-coding-implications.md|Vibe Coding 的未來與影響]] — Blackduck 部落格文章，探討 vibe coding（用自然語言和 AI 工具生成程式碼）的風險與最佳實踐。
- [[wiki/sources/2026-08-02-waku-agent-code-walkthrough.md|Waku Waku! Waku agent — code walkthrough]] — 來源：[YouTube — Waku Waku! Waku agent is your personal AI agent](https://youtu.be/rvRyBhILrls)
- [[wiki/sources/2026-08-02-waku-agent-github-readme.md|Waku agent: GitHub README]] — 來源：[GitHub — ShenSeanChen/waku-agent](https://github.com/ShenSeanChen/waku-agent)
- [[wiki/sources/2026-08-10-chunkless-rag-docling.md|What Is Chunkless RAG? How Docling & AI Agents Navigate Documents]] — 來源：[YouTube — What Is Chunkless RAG? How Docling & AI Agents Navigate Documents](https://www.youtube.com/watch?v=vRZNJWw
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research.md|Wow 頻道 5 支 AI Agent 前沿研究影片]] — 來源：[Wow YouTube 頻道](https://www.youtube.com/@wow.insight) 5 支前沿 AI Agent 研究影片
- [[wiki/sources/2026-08-10-write-code-you-will-never-read-again.md|Write Code You Will Never Read Again]] — 來源：[YouTube — Write Code You Will Never Read Again](https://youtu.be/434cG4g5KLE)
- [[wiki/sources/2026-08-04-code-review-graph.md|code-review-graph：用知識圖譜幫 AI Code Review 省下 8 倍 Token]] — 來源：[別再讓 AI 讀整個專案了！code-review-graph 用知識圖譜幫你省下 8 倍 Token](https://repoinside.com/tirth8205/code-review-graph)
- [[wiki/sources/2026-08-08-guizang-ppt-skill.md|guizang-ppt-skill — 歸藏 HTML PPT 技能 GitHub README]] — 來源：[op7418/guizang-ppt-skill GitHub README](https://github.com/op7418/guizang-ppt-skill)
- [[wiki/sources/2026-08-08-huashu-design.md|huashu-design — 花叔 HTML 原生設計技能 GitHub README]] — 來源：[alchaincyf/huashu-design GitHub README](https://github.com/alchaincyf/huashu-design)
- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial.md|mattpocock/skills — 完整 AI Coding 工作流程教學]] — 來源：[YouTube — mattpocock/skills: A complete AI Coding workflow, end-to-end](https://www.youtube.com/watch?v=M6mYodf0dJM)
- [[wiki/sources/2026-07-11-mattpocock-skills.md|mattpocock/skills — 工程實踐 Skills 集合]] — 視覺化：[[wiki/visualizations/mattpocock-skills.canvas|mattpocock/skills Canvas]]
- [[wiki/sources/2026-08-02-morphir-dotnet-agents.md|morphir-dotnet AGENTS.md — AI Agent 貢獻指南]] — FINOS morphir-dotnet 專案的 AGENTS.md，展示企業級 .NET 專案如何組織 AI agent 指南。
- [[wiki/sources/2026-08-04-rust-analyzer.md|rust-analyzer — Rust 語言的 LSP 實作]] — 來源：[rust-analyzer Introduction](https://rust-analyzer.github.io/book/)
- [[wiki/sources/2026-02-10-pi-agent-core-design.md|下一代Agent架构——Pi Agent Core 设计逻辑深度解析]] — 這裡做為來源頁收存。目的是:反映從這篇文章推導出的主張、以及被這篇文章影響、我們接著在 wiki 內拆解的 entity / concept / comparison 頁。
- [[wiki/sources/2026-05-02-pi-mono-framework-tw.md|別再被複雜框架綁架:pi-mono 回歸直覺的 TypeScript AI Agent 開發框架]] — 與 [[wiki/sources/2026-02-10-pi-agent-core-design]] 為姊妹篇:**同一個專題的兩個視角**——A 篇解剖 `packages/agent/src/` 五支檔,B 篇俯瞰整個 monorepo

---

## Decisions（已確認決策）— 5 頁

- [[wiki/decisions/package-knowledge-boundary.md|Package Knowledge Boundary]] — title: Package Knowledge Boundary
- [[wiki/decisions/project-okf-bundle-boundary.md|Project OKF Bundle Boundary]] — title: Project OKF Bundle Boundary
- [[wiki/decisions/raw-conversation-promotion-workflow.md|Raw Conversation Promotion Workflow]] — title: Raw Conversation Promotion Workflow
- [[wiki/decisions/shared-wiki-collection-model.md|Shared Wiki Collection Model]] — title: Shared Wiki Collection Model
- [[wiki/decisions/knowledge-system-architecture-decision.md|知識系統架構改進決策]] — 版本：v2.0

---

## Discussions（討論中）— 5 頁

- [[wiki/discussions/ai-frontend-handoff-methodology.md|把前端交給 AI 的有效方法論]] — 研究種子：把前端交給 AI 怎麼交才有效（含 H1–H8 可實測假設）🛠️
- [[wiki/discussions/canonicalization-and-semantic-linking.md|Canonicalization and Semantic Linking]] — title: Canonicalization and Semantic Linking
- [[wiki/discussions/topic-stub-cleanup.md|Existing Topic Stub Cleanup]] — title: Existing Topic Stub Cleanup
- [[wiki/discussions/notion-integration-architecture.md|Notion Integration Architecture]] — title: Notion Integration Architecture
- [[wiki/discussions/wiki-youtube-pipeline-timing.md|YouTube-to-Wiki Pipeline Timing]] — title: YouTube-to-Wiki Pipeline Timing

---

## Staging（暫存草稿）— 0 頁


---

> 本頁由 LLM 自動重建。如需修改結構，編輯本檔案即可。
