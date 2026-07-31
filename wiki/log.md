# Log

> Append-only 時間日誌。每條以前綴開頭，方便 grep：
> `grep "^## \[" log.md | head -10`

## [2026-07-31] ingest | NPM 發佈流程設定完成

- 人類要求：將 pi-plannotator-auto 發佈到 NPM
- 完成內容：
  - 設定 package.json（repository、bugs、homepage、publishConfig 等）
  - 建立 GitHub Actions CI/CD（ci.yml + publish.yml）
  - 設定 Branch Protection（PR + CI 驗證）
  - 建立 NPM Organization（cheeriocorner）
  - 產生有 bypass 2FA 權限的 NPM Token
  - 設定 GitHub Secret：NPM_TOKEN
  - 成功發佈套件到 NPM：@cheeriocorner/pi-plannotator-auto
- 建立文件：
  - [[npm-publishing-workflow]] — 完整的 NPM 發佈流程文件
  - [[2026-07-31-npm-publishing-setup]] — 今日工作記錄
- 更新：[[index]] — 加入新頁面
- 學到的知識：
  - NPM Scoped Packages 需要 Organization
  - NPM 2FA 對發佈有嚴格要求
  - GitHub Branch Protection 可以防止直接 push
  - GitHub Actions 需要 workflow 權限
- 下一步：記錄到 Notion 知識花園

---

## [2026-07-30] ingest | Plannotator 知識重整 + 種進花園

- 人類要求：整理 Plannotator 相關知識，種進知識花園
- 背景：我們已開發 pi-plannotator-auto extension，有實際使用經驗
- 重整內容：
  - 更新 Entities:[[plannotator]] — 加入「我們的實驗」章節：
    - pi-plannotator-auto 功能與技術細節
    - 實驗觀察（Shared Event API、Pi 整合、授權）
    - 未來可能的發展（與知識花園整合、多人協作、AI 自動摘要）
  - 更新 Sources:[[2026-07-11-plannotator-research]] — 加入「後續發展」章節：
    - pi-plannotator-auto 開發過程
    - 實驗結論
  - 更新 [[index]] — plannotator 條目加入自訂 extension 說明
- 下一步：在 Notion 知識花園種下 Plannotator 樹苗

---

## [2026-07-30] ingest | Omnigent 知識重整 + 研究專題定位

- 人類要求：整理 Omnigent 相關知識，重新定位研究方向
- 背景：昨天實驗發現 Omnigent 目前不支援 Windows，只支援 Linux/Mac
- 重整內容：
  - 更新 Entities:[[omnigent]] — 加入「實驗觀察」章節（平台相容性表格、Windows 不相容結論）
  - 更新 Concepts:[[meta-harness]] — 加入「我們的研究定位」章節，明確：
    - meta-harness = 研究專題
    - Omnigent = 進入這個領域的一棵樹苗（🌱 種子期）
    - 列出未來可能收集的樹苗
    - 列出 4 個研究方向
  - 更新 [[index]] — omnigent 條目加入 Windows 警告，meta-harness 標記為研究專題
- 下一步：在 Notion 知識花園種下 Omnigent 樹苗

---

## [2026-07-30] dev | pi-plannotator-auto 發佈到 GitHub

- 人類要求：確認 auto-annotate.ts 的安裝來源
- 發現：auto-annotate.ts 從本地 package `C:/Cheerio/pi/packages/pi-plannotator-auto` 載入（透過 settings.json 的 local path 設定）
- 完成事項：
  - 建立 git repo 並 push 到 https://github.com/CheerioCorner/pi-plannotator-auto
  - 更新 README.md 移除 npm 安裝方式，改為 git 安裝
  - 移除 settings.json 中的本地路徑設定
  - 執行 `pi install git:github.com/CheerioCorner/pi-plannotator-auto` 完成安裝
- 安裝位置：`~/.pi/agent/git/github.com/CheerioCorner/pi-plannotator-auto/`

---

## [2026-07-11] dev | Pi 專案 ToDo 系統 + 知識花園維運 skill

- 建立 `C:/Cheerio/pi/ToDo/` 資料夾結構（current.md / done / archive）
- 記錄目前待辦清單：Pi Web/Desktop 專案、skills 實際驗證、知識花園灌溉
- 建立 `C:/Cheerio/pi/AGENTS.md` 專案工作守則
- 更新全域 `~/.pi/agent/AGENTS.md` 加入啟動檢查規則
- 建立 `knowledge-garden` skill（`~/.agents/skills/knowledge-garden/SKILL.md`）
- 修正 Notion 圖示規則：icon 和 title 必須分開
- 移動知識花園到 workspace 頂層 + 視覺地圖放到樹苗子頁

---

## [2026-07-11] ingest | mattpocock/skills — 工程實踐 Skills 集合

- 人類要求：研究 mattpocock/skills GitHub repo 並安裝建議的 skills
- 來源：https://github.com/mattpocock/skills
- 發現：
  - Matt Pocock（Total TypeScript 作者）維護的 coding agent skills 集合
  - 核心理念：用工程方法做 real engineering，不是 vibe coding
  - 共 22 個 active skills（Engineering 17 + Productivity 5）+ 9 個 in-progress + 4 個 misc + 2 個 personal
  - 設計為小而可組合、模型無關、強調 TDD/code review/領域建模/架構設計
  - User-invoked 負責編排，Model-invoked 持有可重用紀律
- 安裝了 7 個建議 skills 到 `~/.agents/skills/`：
  - setup-matt-pocock-skills、grill-me、to-spec、to-tickets、tdd、code-review、improve-codebase-architecture
- 新頁：Sources:[[2026-07-11-mattpocock-skills]]
- 更新頁：[[index]] — 來源 5→6，頁面 15→16
- 維護設計：wiki 頁面使用表格追蹤每個 skill 的安裝狀態和上游狀態，方便未來比對更新

---

## [2026-07-18] ingest | Pi 資源完整盤點（套件、Skills、MCP、Obsidian Wiki、專案目錄）

- 人類要求：「檢視我們現在擁有的所有資源」
- 來源：掃描 `C:/Cheerio/pi/`、`C:/Users/User/.pi/agent/`、`C:/Users/User/.agents/skills/`、`C:/Cheerio/pi-web-access-zh-tw/`、`C:/Cheerio/Obsidian/`
- 發現：
  - Pi 全域設定：OpenRouter + Nemotron-3-Ultra + thinking:high + 12 個已安裝套件
  - 本地套件：pi-web-access-zh-tw (v0.13.0-zh-tw.1) 已安裝啟用，含 Extension + Skill:librarian
  - 全域 Skills：wiki-knowledge、notion-cli、skill-creator、find-skills (4 個)
  - Obsidian Wiki：三層架構 raw/wiki/AGENTS.md，4 來源、11 頁面、Git 同步到私有 repo
  - MCP：Notion API (lazy connect，14 工具，排除 9 寫入類)
  - 當前工作目錄：C:/Cheerio/pi/ (含 PLAN.md、notion-reorg/ 等)
- 新頁：
  - Sources:[[2026-07-18-pi-resource-inventory]]
  - Entities:[[pi-web-access-zh-tw]]、[[wiki-knowledge]]、[[notionApi]]
- 更新頁：[[index]] — 來源 4→5，頁面 11→15，新增 3 實體頁

---

## [2026-07-18] ingest | Pi 專案/工作區模型

- 人類提問：「Pi 裡的 project 難道一定要對應只有一個實體路徑嗎？」
- 來源：Pi 官方文檔（README.md、settings.md、docs/）、pi-coding-agent 原始碼檢視
- 發現：Pi 的「專案」概念綁定 `cwd` + `.pi/` 設定作用域，**不強制綁定單一實體路徑**；Session、Trust、Config 均可跨專案解耦。
- 新頁：
  - Concepts:[[pi-project-workspace-model]]
- 更新頁：
  - [[index]] — 總頁面數 11 → 12，新增概念頁連結
- 更新 index 計數：頁面 11 → 12

---

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
