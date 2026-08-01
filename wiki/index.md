# Wiki Index

> 由 LLM 維護。每次 ingest / lint / 新增頁面後更新。
> 任務：看我就知道這個 wiki 裡有什麼。

---

## 概觀

- **建立日期**：2026-07-11
- **最後更新**：2026-08-01
- **範疇**：AI Agent 框架 / Coding Agent / Pi 生態 / 知識管理
- **總來源數**：8
- **總頁面數**：24
- **新手入口**：[[快速開始]] — 三種收集資料的方法與基本操作

---

## Topics（主題式分類）

### 🤖 AI Agent
- [[topics/ai-agent/pi-mono|pi-mono]] — `badlogic/pi-mono` monorepo + 5 個子 package
- [[topics/ai-agent/pi-agent-core|pi-agent-core]] — agent runtime，5 檔 / 1,500 行
- [[topics/ai-agent/mario-zechner|mario-zechner]] — 作者。「An autonomous agent is just an LLM + tools + a loop.」
- [[topics/ai-agent/minimal-agent-philosophy|minimal-agent-philosophy]] — 「減法大於加法」哲學，2 來源、兩視角交點
- [[topics/ai-agent/late-conversion|late-conversion]] — TS Declaration Merging 實作的「最晚轉換」型別策略

### 🔌 Extension 開發
- [[topics/extension-dev/plannotator|plannotator]] — AI coding agent 視覺化審閱工具，支援 9 種 agent
- [[topics/extension-dev/pi-web-access-zh-tw|pi-web-access-zh-tw]] — Pi 繁體中文本地化套件
- [[topics/extension-dev/okf-open-knowledge-format|okf-open-knowledge-format]] — OKF：Markdown + YAML frontmatter 表示知識
- [[topics/extension-dev/npm-publishing-workflow|npm-publishing-workflow]] — Package Publishing 統一觀點
- [[topics/extension-dev/agent-extension-installation|agent-extension-installation]] — Agent Extension 安裝位置與方式

### 🧠 元系統
- [[topics/meta-systems/meta-harness|meta-harness]] — 對 harness 的再抽象/最佳化層。**📌 我們的研究專題**
- [[topics/meta-systems/omnigent|omnigent]] — Databricks 開源的 meta-harness。⚠️ 目前不支援 Windows

### 📚 知識管理
- [[topics/knowledge-mgmt/wiki-knowledge|wiki-knowledge]] — Obsidian 知識庫操作 Skill（ingest / query / lint）
- [[topics/knowledge-mgmt/notionApi|notionApi]] — Notion MCP 伺服器（14 工具）
- [[topics/knowledge-mgmt/pi-project-workspace-model|pi-project-workspace-model]] — Pi 專案模型：不強制綁定單一實體路徑

---

## Projects（專案維護）

- [[projects/pi-plannotator-auto/README|pi-plannotator-auto]] — ✅ 已發布（GitHub + NPM）
- [[projects/pi-web-access-zh-tw/README|pi-web-access-zh-tw]] — ⚠️ 需維護（有外部貢獻者）

---

## Sources（來源筆記）

> 原始資料在 `raw/`，這裡放 AI 整理過的摘要筆記。

- [[sources/2026-08-01-okf-extension-development|2026-08-01-okf-extension-development]] — OKF Extension 開發實作記錄

---

## 比較與綜合

_（目前尚無）_

> 計畫中：[[coding-agent-comparison]] — Claude Code / Codex / Pi 五維對比表

---

## 待辦 / 缺失概念

- **未獨立成頁**：`pi-ai` / `pi-tui` / `pi-web-ui` / `pi-coding-agent` — 暫收進 [[topics/ai-agent/pi-mono|pi-mono]]
- **未決**：是否要 ingest Zechner 原 blog 作為上游文獻
- **待研究**：YouTube 字幕抓取方案
