# Log

> Append-only 時間日誌。每條以前綴開頭，方便 grep：
> `grep "^## \[" log.md | head -10`

## [2026-07-13] ingest | Omnigent — Databricks 開源 meta-harness

- 人類提供 Databricks 部落格文章
- 來源：https://www.databricks.com/blog/introducing-omnigent-meta-harness-combine-control-and-share-your-agents
- 發現：Omnigent 是定義 B（業界版 meta-harness）的具體開源實作，由 Databricks 開發
- 新頁：
  - raw:[[2026-07-13-omnigent-meta-harness-databricks]]
  - Entities:[[omnigent]]
- 更新頁：[[meta-harness]] — 加入定義 B 的實作比較表（Omnigent vs OmniAgent）、更新來源
- 更新 index 計數：來源 3 → 4，頁面 10 → 11

---

## [2026-07-13] query | Meta-Harness 概念整理

- 人類提問「meta-harness 是什麼？跟一般 harness 有何不同？」
- 來源：arXiv 論文（Lee et al., 2026）、Stanford GitHub repo、MindStudio 部落格
- 發現：該詞有兩種獨立定義——
  - 學術版（Stanford）：自動演化搜尋最佳 LLM harness 的框架
  - 業界版（OmniAgent）：多 AI agent 的 orchestration layer
- 新頁：
  - Concepts:[[meta-harness]]—— 兩種定義 + 比較表 + 延伸
- 更新 index 計數：來源 3 → 3，頁面 9 → 10

---

## [2026-07-12] setup | 完成系統初始化與使用指南

- 補完 `AGENTS.md` 所有預留位置，替換 `PERSON_NAME]`、`[ADDRESS]` 等佔位符
- 新增 [[快速開始]] 頁面——三種收集資料方法與基本操作說明
- 更新 [[index]] 與 [[overview]] 加入新頁面連結
- 系統現在可以直接使用

---

## [2026-07-12] dev | pi-web-access-zh-tw 繁體中文本地化完成

- 從本機安裝 `pi-web-access-zh-tw` 到 Pi（`pi install C:/Cheerio/pi-web-access-zh-tw`）
- 補齊 `curator-page.ts` HTML 範本翻譯：搜尋中、審查摘要草稿、等待結果、工作階段已結束等
- 翻譯 `summary-review.ts` 備用摘要文字為繁體中文
- AI 摘要提示詞加入 `Write in Traditional Chinese (zh-TW)` 指令
- 翻譯 aria-labels 與 JS 範本字串內容
- 更新 `apply-zh-TW.mjs` 涵蓋所有遺漏的 HTML 與 JS 範本字串
- 修正 README.md 安裝方式（移除 `npm:` 前綴，改用 `https://` 或 `git:` URL）
- 推送至 GitHub：`CheerioCorner/pi-web-access-zh-tw`

---

## [2026-07-11] ingest | Plannotator 研究 + Notion 同步

- 人類詢問 backnotprop/plannotator 研究需求。
- 來源:GitHub repo + 官網 + pi-extension README。
- 新頁:
  - Entities:[[plannotator]]—— 功能概述、支援 agent 列表、架構說明
  - Sources:[[2026-07-11-plannotator-research]]—— 研究動機與發現摘要
- 另在 Notion 建立「Plannotator Fork 開發規劃」頁面(用於多人協作)
- 發現:repo 無 obsidian/notion 直接整合，但支援 markdown 審閱可間接用於 Obsidian vault

---

## [2026-07-11] ingest | pi-mono × 兩視角,首次進入 wiki

- 完成「AI Agent 框架 / Pi 生態」首次 ingest。
- 來源 1:知乎 王鹏LLM〈下一代Agent架构——Pi Agent Core 设计逻辑深度解析〉(2026-02-10) → 為 `packages/agent/src/` 五支檔的應用碼解析。原始 .md 從 `Clippings/` 搬入 `raw/2026-02-10-pi-agent-core-design.md`。
- 來源 2:ai-chain.tw〈別再被複雜框架綁架:pi-mono 回歸直覺的 TypeScript AI Agent 開發框架〉(2026-05-02,繁中) → 原始 .html 在 `raw/2026-07-11-pi-mono-intro.html`(67KB)。
- 新頁:
  - Entities:[[pi-mono]]、[[pi-agent-core]]、[[mario-zechner]]
  - Concepts:[[late-conversion]]、[[minimal-agent-philosophy]]
  - Sources:[[2026-02-10-pi-agent-core-design]]、[[2026-05-02-pi-mono-framework-tw]]
- 判斷續記(見上對話):
  - 決定「只建 4 個新頁」不一次到位 16 頁,避免「拆太細」之後 lint 頭緣。
  - 下一階段暫先不寫 comparison / 與其他 agent 的細表,A 提供原始材料但人類未指示起個。
- 報廢/移除:
  - 刪 `raw/2026-07-11-zhihu-article.html`(694-byte 的 zse-ck 陽斷效息)。
  - 清掉空的 `Clippings/` 目錄。

## [2026-07-11] setup | 建立 LLM Wiki 知識庫系統

- 建立三層架構:`raw/`、`wiki/`、`AGENTS.md`。
- 建立 `wiki/index.md` 與 `wiki/log.md` 兩個導覽檔。
- 從 karpathy gist `442a6bf555914893e9891c11519de94f` 抽取工作守則。
- 目前尚無任何來源、頁面。等人類放入第一份資料開始 ingest。
