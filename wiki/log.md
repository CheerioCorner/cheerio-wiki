
# Log

## [2026-08-08] ingest | QwenPaw — YouTube + GitHub README

- 新增 entity：[[wiki/entities/qwenpaw|qwenpaw]]（AgentScope 本地 AI 助理，Agent OS 架構）
- 新增 source：[[wiki/sources/2026-08-08-qwenpaw-youtube-intro|YouTube 介紹影片]]（zh-TW，6:44）
- 新增 source：[[wiki/sources/2026-08-02-qwenpaw-github-readme|GitHub README]]（完整功能與安裝指南）
- 更新 topic：[[wiki/topics/ai-agent|ai-agent]] 加入 qwenpaw 導航
- 更新 index：ai-agent Entities 區塊 + Sources 區塊
- 來源：raw/web + raw/youtube（QE6FVnNcd3Y）

## [2026-08-08] skills-repo | 建立 cheerio-skills 私有 repo

- 盤點自有 skills 並分類（Agent-agnostic / Obsidian-dependent / Pi-specific）
- 建立 `C:/cheerio/pi/cheerio-skills/`，格式符合 `npx skills add` 規範
- 推送到 https://github.com/CheerioCorner/cheerio-skills
- 建立 skills-repo-manager skill 用於後續同步
- 移除 todos skill（已不適用，work/ 系統已取代）

## [2026-08-06] e2e-test | Notion ↔ Obsidian 雙向同步測試完成 ✅

- 測試流程：raw → wiki → Notion → raw（回環測試）
- 驗證項目：wiki-knowledge ingest、knowledge-garden 種子建立、manifest 自動同步、notion-to-raw 抓回
- 結果：全部通過
- 測試 artifacts 已清理（raw、wiki source note、Notion 頁面保留供人工確認）

## [2026-08-06] update | Notion ↔ Obsidian 雙向同步機制 — Manifest 自動同步

- `knowledge-garden` skill 新增 §Manifest 自動同步：每次 Notion 寫入操作後同步更新 `wiki/entities/knowledge-garden.md`
- `notion-to-raw` skill 更新 Step 5：明確定義 manifest 同步格式
- `wiki/discussions/notion-integration-architecture.md`：標記「Garden manifest 的更新時機與方式」為已解決
- 三向資料流完整：raw → wiki（wiki-knowledge）、wiki → Notion（knowledge-garden）、Notion → raw（notion-to-raw）

## [2026-08-08] lint | Provenance 格式修正 + Vibe Coding 連結修復

- **Provenance 格式修正**：所有 source notes 從 YAML nested object 改為 flat 欄位（`provenance_raw` / `provenance_url` / `provenance_session`），兼容 Obsidian Dataview
- 修復 `wiki/sources/2026-08-02-vibe-coding-implications.md` provenance URL（不完整 → 完整）
- 新增 `wiki/concepts/vibe-coding.md` 的 backlinks 到 ai-coding-workflow、minimal-agent-philosophy、defect-metrics、source note
- 更新 `AGENTS.md` 和 `wiki/sources/README.md` 文件，反映新格式
- 所有交叉引用已驗證有效

## [2026-08-08] ingest | Hermes Architecture EXPLAINED: Memory, Context & Gateways

- 來源：`raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/[[../raw/youtube/hermes-architecture-explained|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]]|hermes-architecture-explained]].md`（YouTube n32qq7Kwzh0，40:25）
- 建立：`wiki/sources/2026-08-08-hermes-architecture-explained.md`（source note）
- 更新：`wiki/entities/hermes-agent.md`（sources: 1→2，新增 Agent Loop / Context / Compression / Gateway / Memory / Cron 架構章節）
- 更新：`wiki/topics/skill.md`（hermes-agent 加入 skill topic 導航）
- 更新：`wiki/index.md`（sources 11→12）
- 重點：Hermes 架構深度解析——agent loop、context compression（50% threshold, char/4 estimation）、gateway（async.io + session manager）、三層記憶（markdown + SQLite + external providers）、cron（tick function 每分鐘）

## [2026-08-06] ingest | Pi Architecture Walkthrough（YouTube）

- 來源：`raw/youtube/pi-architecture-walkthrough.md`（488 segments，~39 分鐘，英文）
- 建立：`wiki/sources/2026-08-06-pi-architecture-walkthrough.md`
- 更新：`wiki/entities/pi-agent-core.md`（+agentic loop 三步驟、session tree、compaction、skills 機制，sources 1→2，topics +extension-dev +skill）
- 更新：`wiki/entities/pi-mono.md`（+CLI entry point、extensions events、read-only mode，sources 3→4，topics +extension-dev）
- 更新：`wiki/index.md`（Sources 17→11 計數修正、extension-dev topic 加入 pi-agent-core + pi-mono）
- 重點：session tree 結構、compaction token 計算、skills intercept → markup → read tool 機制

## [2026-08-05] ingest | 批次整理 5 筆 raw 資料入 wiki

- **Omnigent Databricks Blog** → source note 更新 omnigent entity（sources: 1→2）
- **OKF Knowledge Catalog** → source note 更新 okf-open-knowledge-format concept（sources: 1→2）
- **Defect Density & Escape Rate** → source note + new concept `defect-metrics`
- **morphir-dotnet AGENTS.md** → source note + new entity `morphir-dotnet`
- **Vibe Coding Implications** → source note + new concept `vibe-coding`
- 更新：`wiki/index.md`（sources 10→17, concepts 8→10, entities 10→11）
- 建立：5 個 source notes, 1 個 entity, 2 個 concepts
- 維護：omnigent entity、okf-open-knowledge-format concept 來源更新
- 跳過：`raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md`（immutable raw conversation）
- 跳過：`raw/web/2026-08-02-pipackagescoding-agent at main.md`（已在 2026-08-05 ingest）
- 跳過：`raw/web/2026-08-02-Save Plannotator Plans to Obsidian or Bear.md`（已在 2026-08-05 ingest）

## [2026-08-05] skill | plannotator-sync

- 建立 `~/.agents/skills/plannotator-sync/SKILL.md`
- 功能：把 Plannotator 存到 `plannotator/` 的檔案搬到 `raw/conversations/` 並修正 frontmatter
- 觸發詞：sync
- 更新 `wiki/entities/wiki-knowledge.md` 子 skill 列表

## [2026-08-05] ingest | Plannotator Note Storage

- 來源：`raw/web/2026-08-02-Save Plannotator Plans to Obsidian or Bear.md`
- 更新：`wiki/entities/plannotator.md`（Obsidian/Bear/Octarine note 儲存已實現）
- 修正：「與知識花園整合」已從未來計畫移至已實現功能

## [2026-08-05] skill | 安裝 grilling + 釐清 grill-me 依賴

- 發現 `grill-me` 是 thin wrapper，只呼叫 `/grilling`
- `grilling` 是核心追問邏輯，但安裝 `grill-me` 時未自動安裝
- 補安裝：`~/.agents/skills/grilling/SKILL.md`
- 更新：`wiki/entities/mattpocock-skills.md`（補充依賴關係說明）
- 更新：Notion 花園 mattpocock/skills 種子（安裝清單 + 成長計畫）

## [2026-08-05] ingest | Hermes Agent GitHub README

- 來源：`raw/web/2026-08-02-NousResearchhermes-agent The agent that grows with you.md`
- 建立：`wiki/entities/hermes-agent.md`（self-improving agent, learning loop, multi-platform, 7 terminal backends）
- 建立：`wiki/sources/2026-08-05-hermes-agent-github-readme.md`
- 更新：`wiki/index.md`（AI Agent + Skill 雙 topic）
- 重點：Hermes 與 Pi/Tau 最大差異 = 自我學習迴圈（自動建立/改進 skill）+ Honcho user modeling

## [2026-08-05] ingest | Pi GitHub README

- 來源：`raw/web/2026-08-02-pipackagescoding-agent at main.md`
- 修正：repo 從 `badlogic/pi-mono` 改為 `earendil-works/pi`；package 從 5 個改為 4 個（pi-web-ui 已移除）
- 新增：containerization 三方案（Gondolin / Docker / OpenShell）、supply-chain security 策略、session publishing to Hugging Face、MIT license
- 更新：`wiki/entities/pi-mono.md`（repo name、package count、containerization、supply-chain 章節）
- 建立：`wiki/sources/2026-08-05-pi-github-readme.md`
- 更新：`wiki/index.md`

## [2026-08-05] ingest | Tau GitHub README

- 來源：`raw/web/2026-08-02-huggingfacetau A Python port of Pi’s minimalist coding agent.md`
- 修正：tau 開發者從「Pi 團隊內部」改為「Hugging Face」
- 新增：三層架構（tau_ai → tau_agent → tau_coding）、設計哲學 6 條、library 用法、安裝選項、MIT license
- 更新：`wiki/entities/tau.md`（sources: 1→2，新增架構/哲學/library 章節）
- 建立：`wiki/sources/2026-08-05-tau-github-readme.md`
- 更新：`wiki/index.md`（AI Agent + Extension Development 雙 topic 標記）

## [2026-08-05] skill | 建立 notion-to-raw skill

- 決策：採用方案 A — 新建獨立 skill（而非擴展 youtube-to-wiki 或 knowledge-garden）
- 理由：YouTube/Notion 抓取機制不同、raw 格式不同、觸發詞不同；knowledge-garden 保持 wiki→Notion 方向
- 建立：`~/.agents/skills/notion-to-raw/SKILL.md`
- 初始版本：固定路徑 Notion → raw → wiki ingest
- **迭代**：重新設計為三條路徑（只是看看 / 寫 raw / deep research）
  - Notion 頁面是起點，不一定每次都要寫進 raw
  - Phase 1（取得內容）→ Phase 2（呈現+問下一步）→ Phase 3（根據選擇執行）
- 完成 W-2026-08-015
- 下一步：W-2026-08-016（測試 Notion 端到端流程）

## [2026-08-03] architecture | Notion 整合架構討論與實作

- 透過 annotator UI 與人類討論 Notion 整合架構
- 確認兩系統分工：Wiki = AI 運作/知識維護，Notion = 人類閱讀/報告/分享
- 建立完整資料流：raw → wiki → Notion → (研究觸發) → raw
- 建立：`wiki/discussions/notion-integration-architecture.md`（架構討論）
- 建立：`wiki/entities/knowledge-garden.md`（花園 manifest/索引）
- 更新：`knowledge-garden` skill（加入 manifest check、notion-cli 引用、觸發詞）
- 更新：`wiki/entities/notionApi.md`（補充完整資料流）
- 更新：`wiki/index.md`（新增頁面連結）
- 決策：保持 notion-cli / knowledge-garden skill 分離，明確引用關係
- 決策：建立 Garden Manifest 機制避免每次都查 Notion API

## [2026-07-22] taxonomy | 新增 Skill topic + 多 topic 關聯機制

- 建立：`wiki/topics/skill.md`（Skill topic 導航頁）
- `mattpocock-skills`、`ai-coding-workflow`、`wiki-knowledge` → frontmatter `topics` 改為多值
- 各 topic 導航頁都列出相關頁面（🛠️ 標記跨 topic 頁面）
- 更新：`wiki/topics.md`、`wiki/index.md`、`wiki/log.md`

## [2026-07-22] ingest | mattpocock/skills — 完整 AI Coding 工作流程教學

- 來源：`raw/youtube/mattpocock-skills.md`（YouTube M6mYodf0dJM，17:16）
- 建立：`wiki/sources/2026-07-22-mattpocock-skills-tutorial.md`（source note）
- 建立：`wiki/entities/mattpocock-skills.md`（entity — Skills repo）
- 建立：`wiki/concepts/ai-coding-workflow.md`（concept — AI 編碼工作流程）
- 更新：`wiki/sources/2026-07-11-mattpocock-skills.md`（加入影片來源連結）
- 更新：`wiki/topics/ai-agent.md`（加入 mattpocock-skills + ai-coding-workflow）
- 更新：`wiki/index.md`（sources 9→10, entities 9→10, concepts 7→8）
- 重點：Matt Pocock 官方教學，展示 grill with docs → spec → tickets → implement → code review 完整流程，context window smart zone ~140k tokens

## [2026-08-03] setup | youtube-to-wiki skill 建立

- 建立：`~/.agents/skills/youtube-to-wiki/SKILL.md`
- 功能：YouTube 字幕抓取（pytubefix + youtube_transcript_api）→ `raw/youtube/` → source note + entity/concept pages → index + log → git push
- 觸發：YouTube URL、YouTube ingest、抓字幕
- 更新：`wiki/entities/wiki-knowledge.md`（加入子 skill 說明）

## [2026-08-03] ingest | Tau: A Python Port of Pi

- 來源：`raw/youtube/tau-python-port-of-pi.md`（YouTube qo1QNxWcm28，25:03）
- 建立：`wiki/sources/2026-08-03-tau-python-port-of-pi.md`（source note）
- 建立：`wiki/entities/tau.md`（entity — Pi 的 Python port）
- 更新：`wiki/entities/pi-mono.md`（加入「跨語言移植」章節 + Tau 連結）
- 更新：`wiki/index.md`（sources 8→9, entities 8→9, tau 加入 Topics）
- 重點：Tau 與 Pi 架構完全相同（session tree, skills, extensions），TUI 用 Textual，extension events API 跨語言相容

## [2026-08-03] lint | visualizations broken links + topic frontmatter

- 修正 4 處 `[[wiki/visualizations]]` broken link → `[[wiki/visualizations/README]]`（wiki/index.md、wiki/topics.md、wiki/topics/README.md、wiki/topics/knowledge-mgmt.md）
- 為 4 個 topic pages 補上 frontmatter：ai-agent、extension-dev、knowledge-mgmt、meta-systems
- 全域 lint 結果：0 broken links（除上述已修復）、0 orphan pages、所有 canonical pages frontmatter 完整

## [2026-08-03] migration | Obsidian vault structure v2

- 透過 annotator UI 確認 `AGENTS.md` ↔ `wiki/index.md` 少量入口 linking。
- 建立 `work/current.md` 與按月分片的 `work/history/YYYY-MM.md`，以 references 取代 todos/journal 的多重 active workflow。
- raw 改採 `web/`、`youtube/`、`conversations/` 來源通道，保留共用 `assets/`。
- Web Clipper note destination 改為 `raw/web/`；template 不宣稱能指定圖片目錄，無法指定時由 ingest normalize。
- topics 改採 `wiki/topics.md` 與 `wiki/topics/*.md` landing pages；Canvas 統一集中於 `wiki/visualizations/`。

## [2026-08-02] update | Notion 整合修復與 skill 更新

- 連接 MCP `notionApi` 伺服器，驗證讀取功能正常。
- 共享 Notion 花園頁面給 CheerioPi integration，解決 404 問題。
- 更新 `knowledge-garden` skill：加入前置條件、MCP 讀取 workflow、錯誤處理指引。
- 更新 `entities/notionApi.md`：補充 CheerioPi integration 資訊、MCP + ntn CLI 完整 workflow。
- 決策：讀用 MCP，寫用 `ntn` CLI。

## [2026-08-02] lint | Obsidian structure cleanup

- 移除已退場的 `wiki/projects/` legacy project documentation；canonical project 入口統一為 root `projects/<project-id>/index.md`。
- 移除 `wiki/topics/` 下 15 個 compatibility stubs，改為四個 topic domain navigation pages；Canvas 保留於 `topics/ai-agent/`。
- 舊工作紀錄已遷移至 `work/history/`；`todos/` 與 `journal/` 已刪除，不再維護第二套工作或日誌系統。
- 更新 `AGENTS.md`、`work/README.md`、`wiki/index.md` 與相關 skills，明確以 work/ 作為唯一工作系統。

## [2026-08-02] record | Wiki × OKF migration complete work record

- 建立完整工作紀錄：[[sources/2026-08-02-wiki-okf-migration-complete|Wiki × OKF migration complete]]。
- 紀錄 audit、collections、canonical links、provenance、decisions、discussions、三個 Project Bundles、Git history 與保留邊界。
- 同步完成舊工作資料至 `work/history/2026-08.md`；本階段標記完成，後續工作改由 `work/current.md` 管理。

## [2026-08-02] promotion | Confirmed architecture decisions and open discussions

- 將已確認的 Wiki × OKF 架構方向整理至 `wiki/decisions/`：Project Bundle boundary、Shared Wiki collection model、raw conversation promotion workflow、package knowledge boundary。
- 將尚未定案內容整理至 `wiki/discussions/`：YouTube pipeline timing、canonicalization／semantic linking、topic stub cleanup。
- 所有正式頁面以 provenance 回指 immutable raw handoff；未建立 future concept pages。
- 本次 promotion 不修改 `raw/`、`PLAN.md` 或 package source of truth。

## [2026-08-02] migration | Wiki collections + Project OKF Bundle pilot

- 建立 shared collection skeleton：`wiki/concepts/`、`wiki/entities/`、`wiki/decisions/`、`wiki/discussions/`。
- 將 15 個 `topics/` 頁面建立 canonical copies，並保留原 topic pages 作為暫時 taxonomy compatibility stubs。
- 修正 `wiki/projects/README.md` 的兩個 escaped aliases。
- 建立 `projects/pi-plannotator-auto/` Project OKF Bundle pilot；package repository、package `docs/` 與 Obsidian bundle 的 source-of-truth 邊界維持分離。
- 將現行 index、project README 與正文 links 指向 collection canonical pages；未建立 unresolved future concepts。
- 依據：[[audits/2026-08-02-wiki-collection-topic-link-audit|2026-08-02 Wiki collection/topic/link audit]]。
- 完成後續規範對齊：root `projects/`、五個 shared collections、topic taxonomy、GitHub canonical references 與 system local date/time 規則。
- Source collection provenance pass：7 個 source notes 保留 `sources: N` 相容欄位，並補上 `collection: sources` 與逐頁 provenance。
- 建立 `projects/pi-todo-journal/` Project OKF Bundle，保留 package repository、package `docs/` 與 Obsidian bundle 的邊界。
- 建立 `projects/pi-web-access-zh-tw/` Project OKF Bundle，分開記錄自有 repository、upstream repository 與 maintenance discussions。

> Append-only 時間日誌。每條以前綴開頭，方便 grep：
> `grep "^## \[" log.md | head -10`

## [2026-08-02] decision | pi-todo-journal 知識邊界與未來自有 harness 相容性

- 確認不重寫 `pi-todo-journal` Phase 0–5 核心 phases；只在 `PLAN.md` 補充 knowledge boundary 與未來 optional explicit references。
- `pi-todo-journal` 的責任維持在 session 狀態、明確任務、Journal、checkpoint 與使用者提供的 references；不負責外部知識 ingest、概念統一、Wiki taxonomy 或語意 linking。
- Project OKF Bundle 與 package 內 `docs/` 分工：前者是 Obsidian 的跨 session project knowledge，後者是隨 package 發布的開發／維運 bundle；兩者不互相依賴，也不形成第二份 package source of truth。
- 未來自有 Agent harness 可以消費相同的純 Markdown／JSON 資料與抽象 store contract；本次不提前把自有 harness 實作或依賴加入 package。
- 依據：[[projects/pi-todo-journal/README|pi-todo-journal]]、package `PLAN.md`、`raw/conversations/2026-08-02-wiki-okf-youtube-session-handoff.md`。

## [2026-08-01] dev | pi-todo-journal Phase 1 完成

- 人類確認 package 名稱為 `pi-todo-journal`，並要求先保存完整計畫，再進行階段性開發。
- 建立 `C:/Cheerio/pi/packages/pi-todo-journal/PLAN.md`，記錄產品原則、Phase 0–5、驗收條件與非目標。
- Phase 1 完成，包含：
  - package skeleton（manifest、README、LICENSE、TypeScript 設定）
  - extension entrypoint（session_start、agent_settled、session_shutdown hooks）
  - 純函式核心（checkpoint、config、date、dry-run、file-store、journal-store、todo-markdown、todo-store、types）
  - commands（`/session-close`、`/session-close --dry-run`、`/todo-sync`、`/task-done`）
  - OKF Knowledge Bundle（AGENTS.md、docs/index.md、architecture、tools、dependencies、log、references）
  - 8 個 unit test + 1 個 smoke test，全部通過
  - npm typecheck 通過
  - 日期策略改用 `Asia/Taipei`
- 建立專案頁：[[projects/pi-todo-journal/README|pi-todo-journal]]，並更新 Projects/index。
- 下一步：Phase 2 — 改善 confirm UI、task start/pause/note 與 session 摘要。

## [2026-08-02] cleanup | 知識庫大掃除 + Todos 系統建立

- 變更內容：
  - 建立 `todos/` 任務系統（current.md、backlog.md、done/、archive/）
  - 建立 `todos` skill（`~/.agents/skills/todos/SKILL.md`）
  - 更新 `wiki-knowledge` skill 反映最新架構 + frontmatter 檢查
  - 修復 15+ 個斷裂的 Wikilinks（清理不存在的頁面引用）
  - 修復 Frontmatter 格式不一致（okf-open-knowledge-format.md、2026-08-01-okf-extension-development.md）
  - 更新 `index.md` 移除不存在的引用、更新統計數字
  - 更新 Pi 專案 `AGENTS.md` 指向 Obsidian todos 系統
  - 遷移舊 ToDo archive 到 `todos/archive/`
  - 修正 raw/ 檔名格式（空格改連字符）
- 決策理由：
  - Tasks 統一在 Obsidian 管理，避免多頭馬車
  - 獨立 todos skill 方便未來 UI 整合
  - Frontmatter 強制規範防止格式不一致
- 相關頁面：
  - `todos/README.md` — 任務系統格式規範
  - `todos/current.md` — 目前進行中
  - `todos/backlog.md` — 待辦清單

## [2026-08-01] setup | Wiki 結構重構 + 系統全面更新

- 變更內容：
  - Wiki 結構從 `concepts/` + `entities/` 重構為 `topics/` 主題式分類
  - 建立 4 個主題資料夾：`ai-agent/`、`extension-dev/`、`meta-systems/`、`knowledge-mgmt/`
  - 移動 15 個頁面到對應主題資料夾
  - `overview.md` 改名為 `guide.md`
  - 更新 `index.md` 反映新結構
  - 更新 `AGENTS.md` 加入資料流動規則、閉環優化系統、projects/ 維護流程
  - 更新 `wiki-knowledge` skill 反映新架構
  - 更新 `快速開始.md` 反映新結構
  - 建立 `wiki/projects/` 專案維護資料夾
  - 回填 7 個日期的日記（2026-07-11 ~ 2026-08-01）
- 決策理由：
  - 主題式比類型式更直覺，隨著頁面增多更容易找到東西
  - `guide.md` 比 `overview.md` 更明確表示用途
  - 閉環優化系統讓知識管理可以自我驅動
- 相關頁面：
  - [[index|Wiki index]] — 更新後的索引
  - [[guide]] — 使用指南
  - [[快速開始]] — 新手入門

## [2026-08-01] ingest | OKF Extension 開發實作 + 自動化 Publish 流程

- 人類要求：學習 OKF，並在 pi-plannotator-auto 上實作
- 來源：https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf
- 完成內容：
  - 建立 OKF Knowledge Bundle（docs/ 資料夾）
  - 建立 AGENTS.md 和 Pi Skill
  - 自動化 publish 流程（合併 PR 後自動建立 tag + publish）
  - 發佈 v1.0.3 到 npm
- 建立文件：
  - [[okf-open-knowledge-format]] — OKF 完整教學，用 pi-plannotator-auto 作為案例
  - [[sources/2026-08-01-okf-extension-development]] — 今日工作記錄
- 更新：[[index|Wiki index]] — 加入新頁面
- 學到的知識：
  - **OKF 核心：** Markdown + YAML frontmatter 表示知識，人類和 AI 都能讀
  - **適用場景：** Extension 文件、AI 知識庫、團隊知識管理
  - **不適用：** 即時資料、複雜查詢、敏感資料
  - **自動化流程：** 合併 PR → 自動建立 tag + publish
  - **Pi Skill 機制：** 當任務匹配 description 時自動載入規則
- 更新 Notion 知識花園：已種下 OKF 樹苗
- 下一步：在其他 extension 專案中應用 OKF

---

## [2026-07-31] ingest | Package Publishing 流程建立

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
  - [[npm-publishing-workflow]] — Package Publishing 統一觀點文件
  - [[sources/2026-07-31-npm-publishing-setup]] — 今日工作記錄
- 更新：[[index|Wiki index]] — 加入新頁面
- 學到的知識：
  - **核心觀點：** 不管叫什麼名字（Package、Extension、Marketplace），發佈到市場的流程本質上是一樣的
  - 差異只在平台、市場和來源不同
  - 相同的概念：Git 版本控制、Tag 觸發、CI/CD 自動化、Token 認證
  - NPM Scoped Packages 需要 Organization
  - NPM 2FA 對發佈有嚴格要求
  - GitHub Branch Protection 可以防止直接 push
  - GitHub Actions 需要 workflow 權限
- 更新 Notion 知識花園：已種下 NPM Publishing Workflow 樹苗
- 下一步：探索其他發佈平台（NuGet、PyPI 等）

---

## [2026-07-31] ingest | Agent Extension 安裝位置記錄

- 人類要求：記錄 Pi 安裝 extension 的不同方式和位置
- 背景：從 GitHub 安裝和從 NPM 安裝的 extension 儲存位置不同
- 建立文件：
  - [[agent-extension-installation]] — Agent Extension 安裝位置與方式
  - 記錄了 Pi 的兩種安裝來源（git/ vs npm/）
  - 比較了不同 Agent 框架的做法（Claude Code、Cursor、Continue、Aider）
- 更新：[[index|Wiki index]] — 加入新頁面
- 學到的知識：
  - Pi 用目錄結構區分安裝來源（`git/` vs `npm/`）
  - 不同 Agent 有不同的安裝和儲存方式
  - 記錄這些差異有助於理解 Agent 的運作機制

---

## [2026-07-30] ingest | Plannotator 知識重整 + 種進花園

- 人類要求：整理 Plannotator 相關知識，種進知識花園
- 背景：我們已開發 pi-plannotator-auto extension，有實際使用經驗
- 重整內容：
  - 更新 Entities:[[plannotator]] — 加入「我們的實驗」章節：
    - pi-plannotator-auto 功能與技術細節
    - 實驗觀察（Shared Event API、Pi 整合、授權）
    - 未來可能的發展（與知識花園整合、多人協作、AI 自動摘要）
  - 更新 Sources:[[sources/2026-07-11-plannotator-research]] — 加入「後續發展」章節：
    - pi-plannotator-auto 開發過程
    - 實驗結論
  - 更新 [[index|Wiki index]] — plannotator 條目加入自訂 extension 說明
- 下一步：在 Notion 知識花園種下 Plannotator 樹苗

---

## [2026-07-30] ingest | Omnigent 知識重整 + 研究專題定位

- 人類要求：整理 Omnigent 相關知識，重新定位研究方向
- 背景：昨天實驗發現 Omnigent 目前不支援 Windows，只支援 Linux/Mac
- 重整內容：
  - 更新 Entities:[[entities/omnigent|omnigent]] — 加入「實驗觀察」章節（平台相容性表格、Windows 不相容結論）
  - 更新 Concepts:[[meta-harness]] — 加入「我們的研究定位」章節，明確：
    - meta-harness = 研究專題
    - Omnigent = 進入這個領域的一棵樹苗（🌱 種子期）
    - 列出未來可能收集的樹苗
    - 列出 4 個研究方向
  - 更新 [[index|Wiki index]] — omnigent 條目加入 Windows 警告，meta-harness 標記為研究專題
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
- 新頁：Sources:[[sources/2026-07-11-mattpocock-skills]]
- 更新頁：[[index|Wiki index]] — 來源 5→6，頁面 15→16
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
  - Sources:[[sources/2026-07-18-pi-resource-inventory]]
  - Entities:[[pi-web-access-zh-tw]]、[[wiki-knowledge]]、[[notionApi]]
- 更新頁：[[index|Wiki index]] — 來源 4→5，頁面 11→15，新增 3 實體頁

---

## [2026-07-18] ingest | Pi 專案/工作區模型

- 人類提問：「Pi 裡的 project 難道一定要對應只有一個實體路徑嗎？」
- 來源：Pi 官方文檔（README.md、settings.md、docs/）、pi-coding-agent 原始碼檢視
- 發現：Pi 的「專案」概念綁定 `cwd` + `.pi/` 設定作用域，**不強制綁定單一實體路徑**；Session、Trust、Config 均可跨專案解耦。
- 新頁：
  - Concepts:[[pi-project-workspace-model]]
- 更新頁：
  - [[index|Wiki index]] — 總頁面數 11 → 12，新增概念頁連結
- 更新 index 計數：頁面 11 → 12

---

## [2026-07-13] ingest | Omnigent — Databricks 開源 meta-harness

- 人類提供 Databricks 部落格文章
- 來源：https://www.databricks.com/blog/introducing-omnigent-meta-harness-combine-control-and-share-your-agents
- 發現：Omnigent 是定義 B（業界版 meta-harness）的具體開源實作，由 Databricks 開發
- 新頁：
  - raw:[[../raw/web/2026-07-13-omnigent-meta-harness-databricks]]
  - Entities:[[entities/omnigent|omnigent]]
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
- 更新 [[index|Wiki index]] 與 [[overview]] 加入新頁面連結
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
  - Sources:[[sources/2026-07-11-plannotator-research]]—— 研究動機與發現摘要
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
  - Sources:[[sources/2026-02-10-pi-agent-core-design]]、[[sources/2026-05-02-pi-mono-framework-tw]]
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
