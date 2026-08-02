# Wiki Index

> 維護規則：[[AGENTS|AGENTS.md]] · 目前工作：[[work/current|Current Work]]

> 由 LLM 維護。每次 ingest / lint / 新增頁面後更新。
> 任務：看我就知道這個 wiki 裡有什麼。

---

## 概觀

- **建立日期**：2026-07-11
- **最後更新**：2026-08-05（ingest tau GitHub README）
- **範疇**：AI Agent 框架 / Coding Agent / Pi 生態 / 知識管理
- **Source notes**：10（全部已補 `collection: sources` 與 provenance）
- **Canonical collections**：8 concepts、10 entities、4 decisions、3 discussions
- **Project Bundles**：3（入口位於 root `projects/<project-id>/`）
- **工作系統**：`work/` — 統一管理目前工作、完成事件與 references

---

## Topics（主題式分類）

### 🤖 AI Agent
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Agent Skills 集合（162k stars） 🛠️
- [[wiki/entities/pi-mono|pi-mono]] — `badlogic/pi-mono` monorepo + 5 個子 package
- [[wiki/entities/tau|tau]] — Hugging Face 開發的 Pi Python port，三層架構，extension 相容 🛠️
- [[wiki/entities/pi-agent-core|pi-agent-core]] — agent runtime，5 檔 / 1,500 行
- [[wiki/entities/mario-zechner|mario-zechner]] — 作者。「An autonomous agent is just an LLM + tools + a loop.」
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程 🛠️
- [[wiki/concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學，2 來源、兩視角交點
- [[wiki/concepts/late-conversion|late-conversion]] — TS Declaration Merging 實作的「最晚轉換」型別策略

### 🔌 Extension 開發
- [[wiki/entities/tau|tau]] — Hugging Face 的 Pi Python port，extension 相容 🛠️
- [[wiki/entities/plannotator|plannotator]] — AI coding agent 視覺化審閱工具，支援 9 種 agent
- [[wiki/entities/pi-web-access-zh-tw|pi-web-access-zh-tw]] — Pi 繁體中文本地化套件
- [[wiki/concepts/okf-open-knowledge-format|okf-open-knowledge-format]] — OKF：Markdown + YAML frontmatter 表示知識
- [[wiki/concepts/npm-publishing-workflow|npm-publishing-workflow]] — Package Publishing 統一觀點
- [[wiki/concepts/agent-extension-installation|agent-extension-installation]] — Agent Extension 安裝位置與方式

### 🧠 元系統
- [[wiki/concepts/meta-harness|meta-harness]] — 對 harness 的再抽象/最佳化層。**📌 我們的研究專題**
- [[wiki/entities/omnigent|omnigent]] — Databricks 開源的 meta-harness。⚠️ 目前不支援 Windows

### 📚 知識管理
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Obsidian 知識庫操作 Skill（ingest / query / lint）+ youtube-to-wiki 子 skill 🛠️
- [[wiki/entities/notion-to-raw|notion-to-raw]] — Notion → Raw 抓取 Skill（花園研究觸發）
- [[wiki/entities/notionApi|notionApi]] — Notion MCP 伺服器（14 工具）+ 完整資料流
- [[wiki/entities/knowledge-garden|knowledge-garden]] — Notion 知識花園 Manifest（本地索引/快取）
- [[wiki/concepts/pi-project-workspace-model|pi-project-workspace-model]] — Pi 專案模型：不強制綁定單一實體路徑

### 🛠️ Skill
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Agent Skills 集合（162k stars）
- [[wiki/entities/wiki-knowledge|wiki-knowledge]] — Obsidian 知識庫操作 Skill（ingest / query / lint）+ youtube-to-wiki 子 skill
- [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] — 結構化 AI 編碼工作流程（grill → spec → tickets → implement）

> 🛠️ = 同時屬於多個 topic（見各頁 frontmatter `topics`）

---

## Collections（內容集合）

- [[wiki/concepts/README|Concepts]] — 可跨專案重用的抽象知識
- [[wiki/entities/README|Entities]] — 人、工具、package 與具體實作
- [[wiki/sources/README|Sources]] — 整理後的外部資料與研究紀錄
- [[wiki/decisions/README|Decisions]] — 全域／跨專案已確認選擇
- [[wiki/decisions/project-okf-bundle-boundary|Project OKF Bundle Boundary]] — Project Bundle 與 package source-of-truth 邊界
- [[wiki/decisions/shared-wiki-collection-model|Shared Wiki Collection Model]] — 五 collections 與 topics taxonomy
- [[wiki/decisions/raw-conversation-promotion-workflow|Raw Conversation Promotion Workflow]] — raw 到 canonical page 的升格流程
- [[wiki/decisions/package-knowledge-boundary|Package Knowledge Boundary]] — pi-todo-journal 與 YouTube-to-Wiki 邊界
- [[wiki/discussions/README|Discussions]] — 尚未定案的方案與研究問題
- [[wiki/discussions/youtube-to-wiki-pipeline-timing|YouTube-to-Wiki Pipeline Timing]] — pipeline 延後條件
- [[wiki/discussions/canonicalization-and-semantic-linking|Canonicalization and Semantic Linking]] — AI-first + lint guardrail
- [[wiki/discussions/topic-stub-cleanup|Existing Topic Stub Cleanup]] — compatibility stub 已移除，topic 改為導航頁
- [[wiki/discussions/notion-integration-architecture|Notion Integration Architecture]] — Notion 整合架構討論

## Topic navigation

- [[wiki/topics|Topics taxonomy]] — 依主題瀏覽 canonical collections
- [[wiki/visualizations/README|Visualizations]] — Canvas 視覺化投影

## Work（可追溯工作）

- [[work/README|Work System]] — 工作狀態與 history event contract

- [[work/current|Current Work]] — 目前工作、下一步與 references
- [[work/history/2026-08|2026-08 Work History]] — 完成、決策與重要處理事件

## Projects（專案維護）

> Root `projects/<project-id>/` 是唯一的跨 session、跨環境 Project OKF Bundle；舊 `wiki/projects/` 已退場。

- [[projects/pi-plannotator-auto/index|pi-plannotator-auto]] — ✅ Project OKF Bundle pilot；GitHub + NPM
- [[projects/pi-web-access-zh-tw/index|pi-web-access-zh-tw]] — ⚠️ Project OKF Bundle；需維護（有外部貢獻者）
- [[projects/pi-work-tracker/index|pi-work-tracker]] — 📋 新 Project OKF Bundle；取代 pi-todo-journal

---

## Sources（來源筆記）

> 原始資料依來源通道位於 `raw/web/`、`raw/youtube/`、`raw/conversations/`，這裡放 AI 整理過的摘要筆記。

- [[wiki/sources/2026-08-01-okf-extension-development|2026-08-01-okf-extension-development]] — OKF Extension 開發實作記錄
- [[wiki/sources/2026-07-11-mattpocock-skills|2026-07-11-mattpocock-skills]] — Engineering skills 研究
- [[wiki/sources/2026-07-11-plannotator-research|2026-07-11-plannotator-research]] — Plannotator 研究
- [[wiki/sources/2026-07-18-pi-resource-inventory|2026-07-18-pi-resource-inventory]] — Pi 資源盤點
- [[wiki/sources/2026-07-31-npm-publishing-setup|2026-07-31-npm-publishing-setup]] — Package publishing 實作記錄
- [[wiki/sources/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]] — Pi Agent Core 研究
- [[wiki/sources/2026-05-02-pi-mono-framework-tw|2026-05-02-pi-mono-framework-tw]] — Pi mono framework 研究
- [[wiki/sources/2026-08-02-wiki-okf-migration-complete|2026-08-02 Wiki × OKF migration complete]] — 完整架構優化、決策、Project Bundle 與 audit 紀錄
- [[wiki/sources/2026-07-22-mattpocock-skills-tutorial|2026-07-22 mattpocock/skills tutorial]] — 完整 AI Coding 工作流程教學影片
- [[wiki/sources/2026-08-03-tau-python-port-of-pi|2026-08-03 Tau: A Python Port of Pi]] — YouTube 影片：Pi 的 Python port，Textual TUI，session tree，extension 相容
- [[wiki/sources/2026-08-05-tau-github-readme|2026-08-05 Tau: GitHub README]] — Hugging Face 開發，三層架構，設計哲學，library 用法

---

## 比較與綜合

_（目前尚無）_

> 計畫中：coding-agent-comparison — Claude Code / Codex / Pi 五維對比表（待建立）

---

## 待辦 / 缺失概念

- **未獨立成頁**：`pi-ai` / `pi-tui` / `pi-web-ui` / `pi-coding-agent` — 暫收進 [[wiki/entities/pi-mono|pi-mono]]
- **未決**：是否要 ingest Zechner 原 blog 作為上游文獻
- **✅ 已完成**：YouTube 字幕抓取方案（`pytubefix` + `youtube_transcript_api`，2026-08-03 實測成功）
