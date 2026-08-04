# Log

> 變更日誌。連結改用普通文字，避免 structural files 變成 graph 超級節點。
> 需要追溯時，查 frontmatter 的 `provenance` 或 `sources` 欄位。

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

---

*此 log 為 append-only。舊紀錄保留在 git 歷史中。*
