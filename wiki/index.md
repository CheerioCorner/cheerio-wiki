# Wiki Index

> 維護規則：[[AGENTS|AGENTS.md]] · 目前工作：[[work/current|Current Work]]

> 由 LLM 維護。每次 ingest / lint / 新增頁面後更新。
> 任務：看我就知道這個 wiki 裡有什麼。

---

## 📚 AI Agent 學習系統

> 從「使用工具」到「理解原理」到「自己建造」的學習路徑。

- [[wiki/topics/pi-agent-learning/roadmap|🗺️ 學習路線圖]] — 四階段學習路徑
- [[wiki/topics/pi-agent-learning/progress|📊 學習進度]] — 追蹤每次學習成果
- [[work/designs/pi-agent-learning-system|📋 學習系統設計]] — 完整流程文件

### Pi Agent 更新追蹤

- [[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 更新分析]] — 全螢幕 TUI、Mermaid 渲染、samplingParams

### 核心概念

- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought]] — 讓模型「先想再答」
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — 模型資源分配

---

## 概觀

- **建立日期**：2026-07-11
- **最後更新**：2026-08-13（ingest Prime Agent 官方部落格 + AI郵報分析，修正 Prime Intelligence → Prime Intellect）
- **範疇**：AI Agent 框架 / Coding Agent / Pi 生態 / 知識管理
- **Source notes**：56（全部已補 provenance）
- **Canonical collections**：31 concepts、44 entities、5 decisions、4 discussions
- **Project Bundles**：3（入口位於 root `projects/<project-id>/`）

---

## Topics（主題式分類）

> 點進 topic page 看該主題下的 entities 和 concepts。

- [[wiki/topics/ai-agent|🤖 AI Agent]] — Pi、Tau、Hermes、Waku、QwenPaw 等 agent 框架
- [[wiki/topics/pi-agent-learning|📚 AI Agent 學習]] — 從使用到建造的學習路徑
- [[wiki/topics/extension-dev|🔌 Extension 開發]] — Pi extension 生態、OKF、publishing
- [[wiki/topics/meta-systems|🧠 元系統]] — meta-harness 研究
- [[wiki/topics/backend-systems|🔧 後端系統]] — Redis、Caching、Database patterns
- [[wiki/topics/knowledge-mgmt|📚 知識管理]] — Obsidian wiki、Notion 花園、ingest 流程
- [[wiki/topics/skill|🛠️ Skill]] — AI coding skills、workflow
- [[wiki/topics/code-review|🔍 Code Review]] — AI 代碼審查工具、方法論、Benchmark

> 🛠️ = 同時屬於多個 topic（見各頁 frontmatter `topics`）

---

## Concepts（抽象知識）

- [[wiki/concepts/agent-durability-patterns|Agent Durability Patterns]] — 狀態持久化的三種路徑
- [[wiki/concepts/knowledge-system-architecture|知識系統架構]] — 三個操作 + 一個機制 + 一個回流
- [[wiki/concepts/knowledge-management|Knowledge Management]] — 知識管理方法論與工具鏈 🛠️
- [[wiki/concepts/agent-extension-installation|Agent Extension 安裝]] — 安裝位置與方式
- [[wiki/concepts/agentic-ai|Agentic AI]] — AI Agent 核心定義、四大支柱、設計模式 🛠️
- [[wiki/concepts/ai-coding-workflow|AI Coding Workflow]] — 結構化 AI 編碼工作流程
- [[wiki/concepts/ai-code-review|AI Code Review]] — AI 自動化代碼審查工具與方法論 🛠️
- [[wiki/concepts/atdp|ATDP]] — Agent Trajectory Data Protocol
- [[wiki/concepts/code-graph|Code Graph]] — AI Code Assistant 的核心基礎設施
- [[wiki/concepts/chunkless-rag|Chunkless RAG]] — 保留文件結構的 RAG 方法，Agent 推理導航而非 chunks 相似度搜尋
- [[wiki/concepts/code-importance-spectrum|Code Importance Spectrum]] — 程式碼重要性光譜
- [[wiki/concepts/code-knowledge-graph|Code Knowledge Graph]] — tree-sitter AST + 圖論算法
- [[wiki/concepts/content-redesign|Content 設計 Redesign]] — 從套模板到針對性報告
- [[wiki/concepts/context-cot|Context-CoT]] — 上下文思維鏈
- [[wiki/concepts/defect-metrics|Defect Metrics]] — 缺陷密度與逃脫率指標
- [[wiki/concepts/html-slide-deck-workflow|HTML Slide Deck 製作工作流]] — 整合 guizang + huashu
- [[wiki/concepts/knowledge-garden-skill-architecture|知識花園 Skill 架構]] — 8 個 skill 的全景圖
- [[wiki/concepts/late-conversion|Late Conversion]] — 延遲轉換模式
- [[wiki/concepts/loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種核心工作流模式
- [[wiki/concepts/minimal-agent-philosophy|Minimal Agent Philosophy]] — 「減法大於加法」哲學
- [[wiki/concepts/npm-publishing-workflow|NPM Publishing Workflow]] — Package Publishing 流程
- [[wiki/concepts/code-execution-as-tool-calling|Code Execution as Tool Calling]] — 模型寫 Python 腳本執行多操作取代逐次 tool call 🛠️
- [[wiki/concepts/recursive-language-model|Recursive Language Model]] — 把 context 當變數、以函式呼叫觸發的獨立 agent session 🛠️
- [[wiki/concepts/continual-harness|Continual Harness]] — harness 狀態 CRUD 化、`/refine` 自我改進 pipeline、reward hacking 風險 🛠️
- [[wiki/concepts/arc-agi-3-benchmark|ARC-AGI-3 Benchmark]] — 互動式推理 benchmark，同模型換 harness 表現差距達 3 倍以上 🛠️
- [[wiki/concepts/okf-open-knowledge-format|OKF]] — Open Knowledge Format
- [[wiki/concepts/pi-project-workspace-model|Pi Project Workspace Model]] — 專案工作區模型
- [[wiki/concepts/redis|Redis]] — In-memory key-value 資料庫，快取、Session、即時排行 🛠️
- [[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill]] — 技能優化元技能
- [[wiki/concepts/vibe-coding|Vibe Coding]] — 自然語言驅動的程式碼生成

## Entities（具體實作）

- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — AI Agent Skill 開放標準
- [[wiki/entities/area-l|AReaL]] — Ant Group 開源 Agent 強化學習基礎設施
- [[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]] — 企業 AI 治理層
- [[wiki/entities/claude-code|Claude Code]] — Anthropic AI Coding Agent
- [[wiki/entities/docling|Docling]] — IBM 文件解析框架，把 PDF 轉成結構化 tree document
- [[wiki/entities/github-copilot|GitHub Copilot]] — GitHub AI 程式碼輔助工具
- [[wiki/entities/graphify|Graphify]] — 開源代碼知識圖譜工具
- [[wiki/entities/hermes-agent|hermes-agent]] — Nous Research 自我改進 AI agent
- [[wiki/entities/knowledge-garden|knowledge-garden]] — Notion 知識花園 Manifest
- [[wiki/entities/knowledge-garden-to-raw|knowledge-garden-to-raw]] — Notion → Raw 抓取 Skill
- [[wiki/entities/langgraph|LangGraph]] — Graph-based agent runtime
- [[wiki/entities/lsp|LSP]] — Language Server Protocol
- [[wiki/entities/mario-zechner|Mario Zechner]] — Pi 作者
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Agent Skills 集合
- [[wiki/entities/mcp-model-context-protocol|MCP]] — Model Context Protocol
- [[wiki/entities/memgraph-rag|MemGraph-RAG]] — KDD 2026 記憶圖譜多智能體 RAG
- [[wiki/entities/morphir-dotnet|Morphir .NET]] — FINOS .NET binding
- [[wiki/entities/notionApi|notionApi]] — Notion MCP server
- [[wiki/entities/omnigent|Omnigent]] — Databricks 開源 meta-harness
- [[wiki/entities/openai-codex|OpenAI Codex]] — OpenAI AI Coding Agent
- [[wiki/entities/prime-agent|prime-agent]] — Prime Intellect 開源 self-improving coding harness，RLM + Continual Harness，ARC-AGI-3 95.5% 🛠️
- [[wiki/entities/pi-agent-core|pi-agent-core]] — Agent runtime
- [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]] — BlackBeltTechnology 即時 Web Dashboard
- [[wiki/entities/pi-loop-scheduler|pi-loop-scheduler]] — Pi 的 Cron Job 與排程系統
- [[wiki/entities/qwenpaw|QwenPaw]] — AgentScope 本地 AI 助理
- [[wiki/entities/round-table|round-table]] — 多 AI 圓桌會議，量化共識偵測 + 主持人結論
- [[wiki/entities/skillopt|SkillOpt]] — 微軟技能優化器
- [[wiki/entities/tau|tau]] — Hugging Face 的 Pi Python port
- [[wiki/entities/tree-sitter|Tree-sitter]] — 增量解析器
- [[wiki/entities/waku-agent|waku-agent]] — Local-first personal AI agent
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Obsidian 知識庫操作 Skill

## Sources（來源筆記）

- [[wiki/sources/2026-02-10-pi-agent-core-design|Pi Agent Core 設計]] — Mario Zechner 原始設計文件
- [[wiki/sources/2026-05-02-pi-mono-framework-tw|Pi Mono Framework 中文介紹]] — 中文入門文章
- [[wiki/sources/2026-07-11-mattpocock-skills|mattpocock/skills — 工程實踐 Skills 集合]] — repo 研究與完整清單
- [[wiki/sources/2026-07-11-plannotator-research|Plannotator 研究]] — Pi extension 研究
- [[wiki/sources/2026-07-13-omnigent-meta-harness-databricks|Omnigent Meta-Harness]] — Databricks 開源 multi-agent orchestration
- [[wiki/sources/2026-07-18-pi-resource-inventory|Pi Resource Inventory]] — Pi 資源盤點
- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|mattpocock/skills — 完整 AI Coding 工作流程教學]] — 官方教學影片
- [[wiki/sources/2026-07-31-npm-publishing-setup|Package Publishing 首次實作]] — npm publish 流程建立
- [[wiki/sources/2026-08-01-okf-extension-development|OKF Extension 開發實作]] — pi-plannotator-auto OKF 知識包建立
- [[wiki/sources/2026-08-01-okf-knowledge-catalog|OKF Knowledge Catalog]] — Google Cloud 參考實作
- [[wiki/sources/2026-08-02-defect-density-escape-rate|Defect Density & Escape Rate]] — 敏捷指標指南
- [[wiki/sources/2026-08-02-morphir-dotnet-agents|Morphir .NET Agents]] — FINOS .NET binding
- [[wiki/sources/2026-08-02-qwenpaw-github-readme|QwenPaw GitHub README]] — AgentScope 本地 AI 助理
- [[wiki/sources/2026-08-02-vibe-coding-implications|Vibe Coding Implications]] — Vibe Coding 對開發工具的影響分析
- [[wiki/sources/2026-08-02-waku-agent-code-walkthrough|Waku Agent Code Walkthrough]] — 原始碼解析（YouTube）
- [[wiki/sources/2026-08-02-waku-agent-github-readme|Waku Agent GitHub README]] — Local-first personal AI agent
- [[wiki/sources/2026-08-02-wiki-okf-migration-complete|Wiki × OKF 架構優化]] — 完整 migration 工作紀錄
- [[wiki/sources/2026-08-03-loop-vs-graph-engineering|Loop vs Graph Engineering]] — 兩種 AI Agent 工作流模式
- [[wiki/sources/2026-08-03-tau-python-port-of-pi|Tau — Pi Python Port]] — Hugging Face Python 版
- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]] — Gemini Deep Research 報告
- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF + LSP + CodeGraph AI Agent 研究]] — 三者整合分析
- [[wiki/sources/2026-08-05-hermes-agent-github-readme|Hermes Agent GitHub README]] — 自我改進 AI agent
- [[wiki/sources/2026-08-05-pi-github-readme|Pi GitHub README]] — Pi repo 結構與安裝
- [[wiki/sources/2026-08-05-tau-github-readme|Tau GitHub README]] — Tau repo 結構
- [[wiki/sources/2026-08-06-pi-architecture-walkthrough|Pi Architecture Walkthrough]] — 完整架構解析（YouTube）
- [[wiki/sources/2026-08-06-wow-youtube-5-ai-agent-research|Wow 5支AI Agent研究]] — 知識圖譜+自我進化+Context-CoT+SkillOpt+MemGraph-RAG
- [[wiki/sources/2026-08-08-hermes-architecture-explained|Hermes Architecture Explained]] — 架構深度解析（YouTube）
- [[wiki/sources/2026-08-08-plannotator-obsidian-presentation|Plannotator × Obsidian 簡報企劃]] — 知識迴流系統報告簡報企劃
- [[wiki/sources/2026-08-08-qwenpaw-youtube-intro|QwenPaw YouTube Intro]] — QwenPaw 介紹（YouTube）
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Caching Patterns]] — Redis 快取模式整合
- [[wiki/sources/2026-08-10-antigravity-cli-integration|Antigravity CLI 整合]] — Gemini CLI 整合方案
- [[wiki/sources/2026-08-10-pi-cron-job-explained|Pi Cron Job 實作原理]] — @pi-agents/loop 排程系統
- [[wiki/sources/2026-08-13-prime-agent-youtube|Prime Agent YouTube 介紹]] — 用 Python Runtime 取代 Tool Calling 的開源 Coding Agent
- [[wiki/sources/2026-08-13-prime-agent-official-blog|Prime Agent 官方部落格全文]] — RLM/Continual Harness 完整定義、ARC-AGI-3 與長文本評測
- [[wiki/sources/2026-08-13-prime-agent-arc-agi-3-aiposthub|Prime Agent ARC-AGI-3 分析（AI郵報）]] — 拆解三種成績口徑、reward hacking 風險提醒
- [[wiki/sources/2026-08-19-pi-agent-github-copilot-provider-setup|Pi GitHub Copilot Provider Setup]] — Copilot 整合設定

## Decisions & Discussions

- [[wiki/decisions/README|Decisions]] — 全域／跨專案已確認選擇
- [[wiki/discussions/README|Discussions]] — 尚未定案的方案與研究問題

## Visualizations

- [[wiki/visualizations/README|Visualizations]] — 知識花園視覺地圖
- [[wiki/visualizations/obsidian-seed-map|Obsidian 功能全景圖]] — 功能與延伸應用全景圖

---

## Work & Projects

- [[work/current|Current Work]] — 目前工作、下一步與 references
- [[projects/pi-plannotator-auto/index|pi-plannotator-auto]] — ✅ Project OKF Bundle pilot
- [[projects/pi-web-access-zh-tw/index|pi-web-access-zh-tw]] — ⚠️ 需維護
- [[projects/pi-work-tracker/index|pi-work-tracker]] — 📋 取代 pi-todo-journal

---

## 進階式揭露

> 這是 OKF 的核心原則：先看 overview，再深入特定主題。

```
index.md（你這裡）
    ↓
Topic pages（6 個主題入口）
    ↓
Entities / Concepts（具體知識）
    ↓
Sources（原始來源）
```

---

## 待辦 / 缺失概念

- **未獨立成頁**：`pi-ai` / `pi-tui` / `pi-web-ui` / `pi-coding-agent` — 暫收進 [[wiki/entities/pi-mono|pi-mono]]
- **未決**：是否要 ingest Zechner 原 blog 作為上游文獻
- **✅ 已完成**：YouTube 字幕抓取方案（2026-08-03 實測成功）
