# Wiki Index

> 由 LLM 維護。每次 ingest / lint / 新增頁面後更新。
> 任務：看我就知道這個 wiki 裡有什麼。

---

## 概觀

- **建立日期**：2026-07-11
- **最後更新**：2026-08-02（Notion 整合更新）
- **範疇**：AI Agent 框架 / Coding Agent / Pi 生態 / 知識管理
- **Source notes**：8（全部已補 `collection: sources` 與 provenance）
- **Canonical collections**：7 concepts、8 entities、4 decisions、3 discussions
- **Project Bundles**：3（入口位於 root `projects/<project-id>/`）
- **任務系統**：`todos/` — 統一管理所有待辦事項

---

## Topics（主題式分類）

### 🤖 AI Agent
- [[entities/pi-mono|pi-mono]] — `badlogic/pi-mono` monorepo + 5 個子 package
- [[entities/pi-agent-core|pi-agent-core]] — agent runtime，5 檔 / 1,500 行
- [[entities/mario-zechner|mario-zechner]] — 作者。「An autonomous agent is just an LLM + tools + a loop.」
- [[concepts/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學，2 來源、兩視角交點
- [[concepts/late-conversion|late-conversion]] — TS Declaration Merging 實作的「最晚轉換」型別策略

### 🔌 Extension 開發
- [[entities/plannotator|plannotator]] — AI coding agent 視覺化審閱工具，支援 9 種 agent
- [[entities/pi-web-access-zh-tw|pi-web-access-zh-tw]] — Pi 繁體中文本地化套件
- [[concepts/okf-open-knowledge-format|okf-open-knowledge-format]] — OKF：Markdown + YAML frontmatter 表示知識
- [[concepts/npm-publishing-workflow|npm-publishing-workflow]] — Package Publishing 統一觀點
- [[concepts/agent-extension-installation|agent-extension-installation]] — Agent Extension 安裝位置與方式

### 🧠 元系統
- [[concepts/meta-harness|meta-harness]] — 對 harness 的再抽象/最佳化層。**📌 我們的研究專題**
- [[entities/omnigent|omnigent]] — Databricks 開源的 meta-harness。⚠️ 目前不支援 Windows

### 📚 知識管理
- [[entities/wiki-knowledge|wiki-knowledge]] — Obsidian 知識庫操作 Skill（ingest / query / lint）
- [[entities/notionApi|notionApi]] — Notion MCP 伺服器（14 工具）
- [[concepts/pi-project-workspace-model|pi-project-workspace-model]] — Pi 專案模型：不強制綁定單一實體路徑

---

## Collections（內容集合）

- [[concepts/README|Concepts]] — 可跨專案重用的抽象知識
- [[entities/README|Entities]] — 人、工具、package 與具體實作
- [[sources/README|Sources]] — 整理後的外部資料與研究紀錄
- [[decisions/README|Decisions]] — 全域／跨專案已確認選擇
- [[decisions/project-okf-bundle-boundary|Project OKF Bundle Boundary]] — Project Bundle 與 package source-of-truth 邊界
- [[decisions/shared-wiki-collection-model|Shared Wiki Collection Model]] — 五 collections 與 topics taxonomy
- [[decisions/raw-conversation-promotion-workflow|Raw Conversation Promotion Workflow]] — raw 到 canonical page 的升格流程
- [[decisions/package-knowledge-boundary|Package Knowledge Boundary]] — pi-todo-journal 與 YouTube-to-Wiki 邊界
- [[discussions/README|Discussions]] — 尚未定案的方案與研究問題
- [[discussions/youtube-to-wiki-pipeline-timing|YouTube-to-Wiki Pipeline Timing]] — pipeline 延後條件
- [[discussions/canonicalization-and-semantic-linking|Canonicalization and Semantic Linking]] — AI-first + lint guardrail
- [[discussions/topic-stub-cleanup|Existing Topic Stub Cleanup]] — compatibility stub 最終策略

## Topic navigation

- [[topics/README|Topics taxonomy]] — 依主題瀏覽 canonical collections

## Projects（專案維護）

> Root `projects/<project-id>/` 是唯一的跨 session、跨環境 Project OKF Bundle；舊 `wiki/projects/` 已退場。

- [[projects/pi-plannotator-auto/index|pi-plannotator-auto]] — ✅ Project OKF Bundle pilot；GitHub + NPM
- [[projects/pi-web-access-zh-tw/index|pi-web-access-zh-tw]] — ⚠️ Project OKF Bundle；需維護（有外部貢獻者）
- [[projects/pi-todo-journal/index|pi-todo-journal]] — ✅ Project OKF Bundle；Phase 2 完成，Phase 3 待開始

---

## Sources（來源筆記）

> 原始資料在 `raw/`，這裡放 AI 整理過的摘要筆記。

- [[sources/2026-08-01-okf-extension-development|2026-08-01-okf-extension-development]] — OKF Extension 開發實作記錄
- [[sources/2026-07-11-mattpocock-skills|2026-07-11-mattpocock-skills]] — Engineering skills 研究
- [[sources/2026-07-11-plannotator-research|2026-07-11-plannotator-research]] — Plannotator 研究
- [[sources/2026-07-18-pi-resource-inventory|2026-07-18-pi-resource-inventory]] — Pi 資源盤點
- [[sources/2026-07-31-npm-publishing-setup|2026-07-31-npm-publishing-setup]] — Package publishing 實作記錄
- [[sources/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]] — Pi Agent Core 研究
- [[sources/2026-05-02-pi-mono-framework-tw|2026-05-02-pi-mono-framework-tw]] — Pi mono framework 研究
- [[sources/2026-08-02-wiki-okf-migration-complete|2026-08-02 Wiki × OKF migration complete]] — 完整架構優化、決策、Project Bundle 與 audit 紀錄

---

## 比較與綜合

_（目前尚無）_

> 計畫中：coding-agent-comparison — Claude Code / Codex / Pi 五維對比表（待建立）

---

## 待辦 / 缺失概念

- **未獨立成頁**：`pi-ai` / `pi-tui` / `pi-web-ui` / `pi-coding-agent` — 暫收進 [[entities/pi-mono|pi-mono]]
- **未決**：是否要 ingest Zechner 原 blog 作為上游文獻
- **待研究**：YouTube 字幕抓取方案（collection、linking contract、lint 與 migration 規則穩定後才建立獨立 pipeline）
