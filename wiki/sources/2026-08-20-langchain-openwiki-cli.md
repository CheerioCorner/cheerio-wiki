---
title: "LangChain OpenWiki CLI — 給 AI Agent 讀的自動化 Repo Wiki"
type: source
created: 2026-08-20
updated: 2026-08-20
sources: 1
tags: [langchain, openwiki, okf, cli, agent-documentation]
topics: [okf, agent-runtime-implementations]
provenance_raw: "raw/youtube/2026-08-20-langchain-openwiki-cli-for-code-wikis.md"
provenance_url: "https://www.youtube.com/watch?v=XNX-1h2K-9U"
---

# LangChain OpenWiki CLI — 給 AI Agent 讀的自動化 Repo Wiki

> LangChain 團隊发布的 CLI 工具，自動產生和維護 repo wiki，專門給 AI agent 消費，採用 Google OKF v0.2 格式。

## 核心論點

LangChain CEO Harrison 問「AI 領域的下一件大事是什麼？」，講者回答是「general purpose memory」`[01:02]`。OpenWiki 從 code-based docs 切入，目標是建立一個通用的記憶解決方案，最終擴展到更多記憶領域 `[01:22]`。

## 三個設計原則

1. **專為 agent 設計**（不是人類）`[01:44]`：Agent 需要 self-contained fragments、精確 headings、context window 優化 `[03:45]`。人類則需要敘事流暢、截圖、影片 `[03:08]`。實作後發現人類也想讀，因此加入 diagrams `[10:13]`。
2. **CLI 一行安裝**：`npm install openwiki` → `openwiki init` → 設定 API keys、model、wiki brief `[04:28]`。
3. **自動更新**：GitHub Actions daily cron，檢查 git history，有變更才跑 agent，自動開 PR `[12:15]`。

## OKF v0.2 整合

OpenWiki 採用 Google OKF（Open Knowledge Format）v0.2 `[06:07]`。OKF 在每個 markdown 檔案頂部加上 YAML frontmatter（type, title, description, resource, tags, timestamp）`[07:29]`。這些 deterministic fields 讓 agent 可以快速過濾和搜尋 `[07:44]`。

文件結構：
- `quickstart.md` — agent 入口，取得 repo 高層概覽 `[05:24]`
- 分類目錄 — 每個檔案聚焦單一主題 `[05:55]`
- `index.md` — 目錄索引 `[06:20]`
- `log.md` — 變更歷史（對 agent 和人類都有用）`[06:31]`

文件間的 markdown 連結是關鍵：agent 從 doc A 拉到片段，看到對 doc B 的引用，可以快速跳轉 `[08:35]`。

## 初步 Eval 結果

使用 DeepSWE benchmark（coding agent benchmark），20 個任務 `[09:07]`：
- 無 OpenWiki：7-8 個成功任務 `[09:36]`
- 有 OpenWiki：9-10 個成功任務 `[09:36]`
- **token 消耗顯著下降** `[09:48]`

講者強調這是早期結果，更多 benchmark 即將發布 `[10:01]`。

## 更新流程

1. GitHub Actions daily cron 觸發 `openwiki update` `[12:48]`
2. 檢查 `last-update.json`，若無 git 變更則跳過 `[14:14]`
3. 有變更 → 讀取上次運行後的所有 merged commits `[14:30]`
4. Agent 根據變更更新 wiki `[14:47]`
5. 自動開 PR，merge 後即完成更新 `[14:53]`

## 開源與生態系

- MIT license `[15:11]`
- npm 安裝 `[15:13]`
- 支援 10-15 個 LLM provider `[15:17]`
- 可 fork 修改 `[15:21]`
- GitHub 上有大量 forks `[15:02]`

## 下一步

- 更好的 prompting（分析更大 repo）`[15:49]`
- Search and retrieval tools — 目前只靠 AGENTS.md/CLAUDE.md 告訴 agent OpenWiki 的存在，下一步是給 agent 專用的搜尋和過濾工具 `[16:00]`

## 相關頁面

- [[wiki/concepts/okf-open-knowledge-format|OKF]] — OpenWiki 採用的知識格式標準
- [[wiki/entities/langchain-openwiki|OpenWiki（LangChain）]] — 工具規格頁
- [[wiki/topics/okf|OKF Topic]] — OKF 導航頁
