# AGENTS.md — LLM 知識庫工作守則

## Vault 入口

- [[wiki/index|Wiki Index]] — canonical knowledge entry
- [[wiki/topics|Topics / Taxonomy]] — navigation entry
- [[work/current|Current Work]] — current work and references
- [[raw/README|Raw Sources]] — immutable input contract


> 本檔案規範 LLM agent 如何維護這個知識庫。
> 維護者：Cheerio
> 建立日期：2026-07-11
> 最後更新：2026-08-10

## ⚠️ 鐵律

1. **日期必須讀取本機時間** — 每次需要使用日期前，必須先執行 `date` 命令取得正確日期。禁止自己猜測日期！

---

## 1. 架構

```
Obsidian/
├── raw/                    # 原始資料（只讀，不修改）
│   ├── web/                # Browser / Obsidian Web Clipper 原始剪藏
│   ├── youtube/            # YouTube 字幕、逐字稿與影片資訊
│   ├── conversations/      # 原始對話／annotator handoff
│   ├── notion-ingest/      # 從 Notion 花園抓取的頁面內容（反向流程）
│   ├── research/           # 深度研究的原始輸出（Gemini Deep Research 等）
│   ├── assets/             # 原始來源使用的圖片與附件
│   └── README.md           # raw contract
├── projects/               # Project OKF Bundles（跨 session、跨環境）
│   └── <project-id>/       # project-local decisions、discussions、references
├── wiki/                   # Shared cross-project knowledge graph
│   ├── concepts/           # 可跨專案重用的抽象知識（type: concept）
│   ├── entities/           # 人、工具、package、服務與具體實作（type: entity）
│   ├── sources/            # 整理後的外部資料與研究紀錄（type: source）
│   ├── decisions/          # 全域／跨專案已確認的架構與技術選擇
│   ├── discussions/        # 尚未定案的方案與研究問題
│   ├── staging/            # 暫存區（backfill 草稿，TTL 21 天）
│   ├── topics.md           # taxonomy 總入口
│   ├── topics/             # topic landing pages（.md），只放導航，canonical content 連到 collections
│   ├── visualizations/     # 視覺化投影（Mermaid code block 為主，舊 Canvas 保留）
│   ├── audits/             # 分析報告與 migration audit
│   ├── index.md            # 內容索引
│   └── log.md              # 時間日誌（append-only）
├── work/                   # 可追溯工作狀態與歷史事件
│   ├── README.md           # work contract
│   ├── current.md          # 唯一的目前工作清單
│   ├── history/YYYY-MM.md  # 按月分片的完成／決策／活動事件
│   ├── designs/            # 設計文件（需求分析、架構規劃）
│   ├── learning/           # 月度學習紀錄
│   └── synthesis/          # 綜合分析報告
└── AGENTS.md               # 本檔，工作守則
```

**各資料夾用途：**
- `raw/` = 原始資料。永遠只讀；依來源通道分為 `web/`、`youtube/`、`conversations/`、`notion-ingest/`、`research/`，附件集中於 `assets/`。
- `projects/<project-id>/` = Project OKF Bundle；不取代 package repository 或原始碼。
- `wiki/` = canonical knowledge graph。`topics.md` 是 `topics/` 的總目錄（只放連結，不重複列頁面）；`topics/*.md` 才是各 topic 的完整頁面清單；`index.md` 的 Topics 分區只連到 `topics/*.md`，不重複列出每一頁——三處只有一處是真正的全量清單，避免重複維護造成漂移。`visualizations/` 每張地圖須在 `README.md` 註冊並標示 topics。
- `work/` = 唯一的工作狀態系統。`current.md` 管理目前工作，`history/` 保存事件，`designs/` `learning/` `synthesis/` 保存輔助文件。舊工作目錄已完成遷移，不得重新建立。
- `AGENTS.md` = 治理入口，由人類與 LLM 共同演化。

---

## 2. 資料流動規則

### 2.1 什麼進 raw/？
| 類型 | 範例 | 觸發方式 |
|------|------|---------|
| 網頁文章 | 部落格、新聞、知乎回答 | Web Clipper 或 agent 存入 `raw/web/` |
| URL 連結 | 你貼給我的 URL | agent 用 web_fetch 抓取後存入 |
| YouTube | 影片字幕/逐字稿 | `youtube-to-wiki` skill 抓字幕存入 `raw/youtube/` |
| PDF/論文 | 學術論文、技術報告 | `wiki-pdf` skill（markitdown + pymupdf）存入 `raw/web/` |
| Notion 頁面 | 花園種子要深入研究 | `knowledge-garden-to-raw` skill 抓取存入 `raw/notion-ingest/` |
| 深度研究 | Gemini Deep Research 輸出 | `chat-with-gemini-research` skill 存入 `raw/research/` |
| 原始文字 | 你直接貼的一段文字 | agent 存入適當的 raw source channel |

**關鍵：未經整理的原始資料。**

### 2.2 什麼進 wiki/？
| 類型 | 範例 | 觸發方式 |
|------|------|---------|
| 討論後的洞見 | 「我覺得這個概念可以存下來」 | 說「存到 wiki」 |
| 整理後的知識 | ingest 後的結構化筆記 | 自動（ingest 流程） |
| 比較分析 | A vs B 的對比表 | 說「把這個比較存到 wiki」 |
| 實作紀錄 | 某個技術的使用經驗 | 說「記一下我學到的」 |

**關鍵：已經討論過、有質量的內容。**

### 2.3 什麼進 Notion 花園？
| 類型 | 範例 |
|------|------|
| 重要學習 | 從外部資源學到的關鍵知識 |
| 技術決策 | 專案中的重要技術選擇 |
| 工具/方法 | 新發現的工具或方法論 |
| 里程碑 | 專案的重要進展 |

**關鍵：值得跟別人分享的精華，也是自己要能快速找到的速查手冊。**

### 2.4 什麼進 work/？
| 類型 | 範例 |
|------|------|
| 目前工作 | 要做什麼、正在做什麼、下一步 |
| 完成事件 | 做過哪些事、結果是什麼 |
| 決策事件 | 討論後確認了什麼 |
| 可追溯關係 | 對應的 raw、project、wiki references |

**關鍵：只記錄 AI 未來需要追溯的工作、討論與處理結果；不建立個人生活日誌。**

### 2.5 什麼進 projects/？
| 類型 | 範例 |
|------|------|
| 正在建造的專案 | Pi Web/Desktop |
| 已發布的 extension | pi-plannotator-auto |
| 需要維護的東西 | pi-web-access-zh-tw |
| Side project | 個人開發項目 |

**關鍵：有明確邊界、需要追蹤進度的東西。**

---

## 3. 操作流程

### Git 同步（跨機器協作）

知識庫託管於 GitHub：`https://github.com/CheerioCorner/cheerio-wiki`（私有 repo）

- **操作前** → 先 `git pull` 確保拿到最新版
- **操作後** → `git add -A && git commit -m "..." && git push` 同步回 GitHub

在 Ingest 和 Lint 流程結束時，自動執行 git commit + push。

### 3.1 Ingest（吸收新資料）

**觸發條件：** 人類在 `raw/` 放入新檔案並說「處理這個」或「ingest」，或 Lint 稽核出未消化的 raw 檔案時自動觸發。全程不需要人類確認——人類的角色只有把資料放進 `raw/`，之後全交給 AI。

**流程：**
1. 讀完來源（文本一次讀完；有圖片時另外批次讀）。
2. 查詢既有知識（避免重複）：讀 `wiki/index.md` 找相關頁面。
3. **雙模型交叉驗證（取代人類確認，Pi 主持不投票）**：
   - Pi（執行 ingest 的 agent）**不自己提案**，只當主持人：分別呼叫 `chat-with-claude` 與 `chat-with-gemini` skill，讓兩個獨立參與者對同一份 raw 各自產出結構化提案（要建立/更新哪些頁面、分類、topics）
   - **Round 1（盲判）**：只比對關鍵欄位——目標頁面（新建或併入哪一頁）、`type` 分類、`topics`、是否推翻既有結論。措辭/章節順序/次要 tag 不同不算分歧
   - 關鍵欄位一致 → 採用，進入步驟 4
   - 關鍵欄位不一致 → **Round 2（覆核）**：把對方的提案互相展示，各自決定是否修改立場
   - Round 2 後仍不一致 → 呼叫 `chat-with-copilot` skill 當第三票，多數決；分歧與裁決記錄進 `wiki/log.md`
   - 三方仍無共識（罕見）→ **仍然寫入**（不可因為沒有共識就放棄這筆資料），標記 frontmatter `confidence: draft`，列入下次 Lint 報告的「🤝 AI 已自動處理」區塊供人類選讀
   - **輪數上限：2 輪**（Round 1 + Round 2），第三票不算額外一輪，避免無止盡討論
4. 在正確的 canonical collection 建立／更新相關頁面（單一來源可能會動到多頁）：
   - 建立「來源筆記」（`wiki/sources/`，1 頁彙整該資料的重點）**，⚠️ 必須在 frontmatter 加入 `provenance_raw` 或 `provenance_url`**（格式見 §4.2）
   - 可重用抽象放入 `wiki/concepts/`
   - 具體人／工具／package 放入 `wiki/entities/`
   - 尚未定案內容放入 `wiki/discussions/`；已確認的全域選擇放入 `wiki/decisions/`
   - **⚠️ Topic pages 必須同步更新**：每當新增或更新 entity/concept/source，必須同時更新對應的 `wiki/topics/*.md` 導航頁（讀取新頁面 frontmatter 的 `topics: [...]`，在每個相關 topic page 的 Entities、Concepts 或 Sources 列表加入新頁面，跨 topic 頁面用 🛠️ 標記）
   - `wiki/topics.md` 與 `wiki/topics/*.md` 只放導航內容；不得在 topic 目錄建立內容副本或 compatibility stub
   - 標記新資料是否推翻／補充既有結論
   - **不篩選、零遺漏**：每個 raw 檔案都必須產出至少一個 wiki 頁面，或在既有頁面明確記錄「已檢視、併入 XXX，理由：...」；不允許 AI 判斷「沒有意義」就無痕跡跳過
5. 重新產生 `wiki/index.md`（自動全量重建，不是手動加一行；見 §5.1）。
6. 在 `wiki/log.md` 附加一條 ingest 紀錄；若步驟 3 有仲裁，一併記錄分歧與裁決理由。
7. **git push** 同步回 GitHub。

**批次流程：** 相同步驟，但一次處理多筆。步驟 3 的雙模型交叉驗證仍然執行（這是唯一的品質防線，不可因批次而跳過），只是不需要人類在場互動。

### 3.2 Query（查詢）

**觸發條件：** 人類對 wiki 提出問題。

若問題涉及「之前做過什麼」「上次進度」或既有決策，才讀取相關 `work/history/YYYY-MM.md`；一般 query 不在啟動時預先載入 history。

1. 先讀 `wiki/index.md` 找出相關頁面；啟動時只讀 `work/current.md`，history 僅在需要追溯過去時讀取。
2. 讀那些頁面（必要時追溯其連結）。
3. 給出有引用的回答，並標明來源頁面。這一步是即時互動，單一模型即可，不需要共識機制。
4. **不確定時要說**：如果對回答沒把握，明確告訴人類「我對這個回答不確定」並說明原因——這是給人類的溝通訊號，不是回填門檻（回填品質改用共識判斷，見 §3.5，不再用自評信心分數）。
5. **全自動回填**：任何有新意的洞察一律走 §3.5 的回填流程，不需要人類說「存到 wiki」才會動作；人類仍可隨時主動說「把這個存到 wiki」立即觸發。

### 3.3 Lint（健康檢查）

**觸發條件：** 人類說「lint wiki」，或定期（例如每 10 次 ingest 後）。

執行 `wiki-lint` skill（`~/.agents/skills/wiki-lint/SKILL.md`），該 skill 定義完整檢查清單與輸出格式。流程概要：

1. `git pull` 取得最新版。
2. 依 skill 定義的檢查項掃描 `wiki/`，包含遺漏稽核（每個 `raw/` 檔案是否都有對應 wiki 頁面或明確排除紀錄）。
3. 能自動處理的問題（矛盾仲裁、topics 分裂、Staging 逾時晉升、index.md 重建、觸發遺漏 raw 的 Ingest）直接執行，不等人類。
4. 產出結構化報告（🔴需要人類判斷的極少數事項 / 🤝AI 已自動處理的紀錄 / 📊統計），人類選讀即可，不阻塞。
5. `git push` 同步。

### 3.4 Projects（專案維護）

**觸發條件：** 開始新專案、或專案有重大進展時。

1. 在 root `projects/<project-id>/` 建立 Project OKF Bundle。
2. 至少建立 `index.md`（project identity、邊界、目標、入口）。
3. 可選：`decisions/`、`discussions/`、`references.md`、`log.md`。
4. 使用 GitHub repository URL 作為跨環境 canonical reference。
5. 不複製 package source code、完整 PLAN 或 package `docs/`；更新 `wiki/index.md` 的 Projects 區塊。
6. 專案 canonical 入口一律是 `projects/<project-id>/index.md`。舊的 `wiki/projects/` 已完成退場，不再建立或更新 legacy project README。

### 3.5 Backfill（回填機制）

**觸發條件：** Query 產出候選洞察，或 Ingest 雙模型交叉驗證後標記為 `draft` 的條目。

1. **查重優先**：比對候選洞察與既有 wiki / Staging 內容是否高度重疊。
   - 高度重疊 → 不新增頁面，只把既有頁面 frontmatter 的 `reinforcement` 計數 +1（見 §5a 半衰期管理），流程結束。
   - 是新內容 → 進入步驟 2。
2. **共識判斷（取代自評信心分數，Pi 主持不投票）**：Query 已產出的回答視為第一個參與者的提案，呼叫 `chat-with-gemini` 讓第二個獨立參與者對同一個洞察給出判斷。
   - 兩者一致認同 → `status: auto_verified`
   - 不一致 → 互相展示一次對方判斷（Round 2）；仍不一致才呼叫 `chat-with-copilot` 當第三票，多數決 → `status: verified_by_arbitration`
   - 三方仍無共識 → 仍然寫入，`status: draft`（不廢棄，只降低信任層級）
   - 輪數上限：2 輪 + 第三票
3. 寫入 Staging Buffer（`wiki/staging/`）：附帶 metadata（backfill_id, query, answer, status, consensus_result, arbiter, reinforcement, created_at, ttl）。
4. TTL：21 天。**逾時不刪除**——自動晉升為正式知識，保留 `status: draft` 標記（避免資料因為沒人來確認就悄悄消失，違反零遺漏原則）。
5. 若在 TTL 內被同一主題的後續 Query 或 Ingest 再次印證，`reinforcement` 計數累加，可提前晉升為 `auto_verified`。
6. 更新 index.md 和 log.md。

### 3.6 Garden Sync（花園同步）

**觸發條件：** 花園有新種子成熟（🌱 → 🌳）或研究專題有新發現。

1. 偵測 Notion 種子狀態變化。
2. 自動抓取 Notion 頁面內容，存入 `raw/notion-ingest/`。
3. 觸發 Ingest 流程（§3.1）。
4. 更新 `wiki/entities/knowledge-garden.md`（本地 manifest）。

**種子成熟判定條件：**
- 種子標籤從 `#seed` 變更為 `#mature`
- 通過 `wiki-lint` 基本格式校驗
- 備註字數 > 100 或連結數 > 3
- 符合上述條件後，自動複製至 `raw/notion-ingest/` 觸發 Ingest 流程

---

## 4. Wiki 頁面規範

### 4.1 檔名
- 全小寫、英文或中文皆可，以一致性為原則。
- Canonical content page 用穩定主題名（`pi-mono.md`、`meta-harness.md`）。
- 來源筆記若是時間型研究／工作紀錄，使用日期+標題（`2026-07-11-podcast-name.md`）。穩定型來源可不加日期，但建立後不因規則改名。

### 4.2 Frontmatter（YAML，給 Obsidian Dataview 用）

每個 canonical content page 開頭要有；`README.md`、`index.md`、`log.md` 與 topic 導航頁可作為結構性例外：

```yaml
---
title: 頁面標題
type: entity | concept | source | comparison | synthesis | decision | discussion | audit | project-bundle
created: 2026-07-11
updated: 2026-07-11
sources: 3   # 引用過幾個 raw 來源
tags: [topic-a, topic-b]
topics: [skill, ai-agent]  # 頁面所屬的 topics（可多值陣列）
canonical: entities/name    # 可選，canonical path
provenance_raw: "raw/example.md"   # source 類型必填；指向 raw 檔案
provenance_url: https://...         # source 類型必填；指向外部 URL
provenance_session: "description"  # 選填；對話 session 來源
---
```

**Source note provenance 強制規則：** `wiki/sources/` 下的每個頁面**必須**有 `provenance_raw` 或 `provenance_url` frontmatter（至少一筆）。YouTube 來源的 raw transcript 也應記錄在 `provenance_raw` 中（例如 `provenance_raw: "raw/youtube/video-slug.md"`）。

**多 topic 關聯**：一個頁面可同時屬於多個 topic。在 topic 導航頁中，跨 topic 頁面用 🛠️ 標記。例如：
```markdown
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Agent Skills 集合 🛠️
```
在 `wiki/index.md` 底部加說明：`> 🛠️ = 同時屬於多個 topic（見各頁 frontmatter topics）`

### 4.3 內容結構（建議）

- **第一段：** 一句話總結這個頁面是什麼。
- **主體：** 章節式整理事實、概念、爭議。
- **「來源」區塊：** 列出哪些 raw 檔案支持哪些論點（用 `[[wikilink]]`）。
- **「相關頁面」區塊：** `[[page-a]]`、`[[page-b]]`。

### 4.4 交叉引用

- 一律用 Obsidian 的 `[[wikilink]]` 風格（雙中括號）。
- 提到任何重要概念/實體時，**也要建立連結**。這是 wiki 能編譯一次就永久受用的關鍵。
- 連結斷掉的頁面（`[[foo]]` 指向不存在檔案）是 lint 的目標之一。
- 使用 vault-root 完整路徑連結，例如 `[[wiki/entities/pi-mono|pi-mono]]`、`[[wiki/concepts/okf-open-knowledge-format|OKF]]`，確保連結在任何位置都能正確解析。
- `[[basename]]` 只適合唯一 target；當 compatibility stub 或 Canvas 造成歧義時，改用完整路徑。
- Audit／歷史 log 中的 link 要與正文 link 分開統計；不要因已刪除歷史頁而重建頁面。

### 4.5 雙向連結規範

- **所有新增的 concept 頁面**必須至少包含 2 個以上的雙向連結（`[[target-concept]]`）
- **所有新增的 entity 頁面**必須至少包含 1 個 inbound link（被其他頁面連結）
- **交叉引用**：提到相關概念時，必須建立連結，讓 Obsidian 關聯檢視圖能正確繪製
- **來源連結**：wiki 頁面必須連結到對應的 source note（`[[wiki/sources/...]]`）

---

## 5. 索引與工作歷史

#### 5.1 `wiki/index.md`（內容索引）

- 按 taxonomy 分區：Topics / Collections / Projects / Sources。
- **Topics 分區只連結到 `wiki/topics/*.md`，不重複列出每一頁**（那份全量清單的唯一正本在 topics 頁面本身）；Collections / Projects / Sources 才是全量條列。
- 每頁一行：`[[collection/path|標題]] — 一句話摘要`。
- **多 topic 頁面**：跨 topic 頁面在所有相關 topic 區塊都列出，用 🛠️ 標記（適用於 Collections 等全量列表區塊，以及 topics/*.md 本身）。
- **每次 ingest / lint 後自動全量重建**（掃描 `wiki/` 底下所有頁面重新產生，不是手動加一行），確保不會因為忘記更新而漏頁。
- 中等規模（~100 來源、數百頁）僅靠 index.md 就夠，不需搜尋引擎。

#### 5.2 `wiki/log.md`（知識庫變更日誌）

- Append-only，不刪舊紀錄；工作完成、決策與處理事件另寫入 `work/history/YYYY-MM.md`，不混入 wiki log。
- 每條統一前綴方便 grep：
  ```
  ## [2026-07-11] ingest | <標題>
  ## [2026-07-11] query  | <問題>
  ## [2026-07-11] lint   | <結論摘要>
  ```
- 最新的一條放在最上面。
- 看最近活動：`grep "^## \[" wiki/log.md | head -10`

---

## 5a. 知識演化協定

### 權責分工
- 人類：把資料放進 `raw/`、在 Notion 花園整理與探索、（可選）閱讀 Lint 報告裡的自動處理紀錄
- LLM：summarizing、cross-referencing、filing、bookkeeping、topics 分裂決策、回填品質把關、矛盾仲裁、遺漏稽核——這些一律不等人類確認

### 品質把關機制（雙模型共識，Pi 主持不投票，取代人類確認與自評信心分數）
- Pi 是主持人，不提案、不投票，只負責比對與裁決流程；實際提案由兩個獨立參與者產生（Ingest 用 Claude+Gemini；Backfill 用 Query 原始回答+Gemini）
- **分歧判斷標準**：只比對關鍵結構化欄位（目標頁面、type、topics、是否推翻既有結論），不比對措辭；關鍵欄位一致就視為達成共識
- Round 1 一致 → `auto_verified`；不一致 → Round 2（互相展示對方提案，各自覆核）；仍不一致 → `chat-with-copilot` 第三票多數決 → `verified_by_arbitration`
- **輪數上限：2 輪 + 第三票**，不開放式討論
- 三方仍無共識 → 照樣寫入並標記 `draft`
- **核心原則：分歧只降低信任層級，不會阻止資料進入知識庫**（零遺漏優先於零錯誤）

### Staging Buffer
- 位置：`wiki/staging/`
- 語意：不是「等人類批准」，是「等共識或後續印證」
- TTL：21 天，逾時**自動晉升**為正式知識（保留 `draft` 標記），不刪除

### decisions/ 與 discussions/ 的語意（因應共識機制調整）
- `decisions/`：雙模型（或三方）**已達成共識**的結論——不再代表「人類已確認」，而是「AI 共識已收斂」
- `discussions/`：共識仲裁後**仍無法收斂**的分歧——保留兩種觀點並存，標記為待深入，不強行選邊
- 這兩個資料夾仍然必要：它們是系統對自己信心程度的誠實訊號，比全部塞進同一個 wiki 頁面更容易看出「這是穩固的知識」還是「這是還在打架的假說」

### 半衰期管理
- 快訊類：7 天
- 技術文件：180 天
- 歷史常識：3650 天
- 公式：`Score = BaseScore × e^(-λt) + Reinforcement`
- `Reinforcement`：每被一次獨立 Query 或 Ingest 印證同一結論，計數 +0.1，用來讓被反覆驗證的知識分數回升，不會被單純的時間衰減判定為過時

### 同步機制
- 大腦 → 花園：單向同步（wiki → Notion）
- 花園 → 大腦：單向回流（觸發條件：種子成熟、研究專題新發現、人類主動）

### Topics 分裂
- 全自動：LLM 偵測到某個 topic 下頁面過多時，用 `round-table` skill（claude + gemini + copilot）討論分裂方案並執行，結果記錄在 Lint 報告供選讀

---

## 6. 與人類的協作原則

- **不主動刪人類寫的東西**。要改前先問（人類手寫的內容仍需要問；AI 自己產生的知識走 §5a 的共識機制，不需要問）。
- **不擅改 raw/**。永遠是只讀（Lint 的 raw 冗餘清理例外：只能 `mv` 到 `raw/.trash/`，可逆、git 有歷史，不算破壞只讀原則）。
- **關鍵判斷走共識，不問人類**：這個來源要不要建獨立頁？這條結論要不要標記為「暫定」？要併入舊頁還是另開新頁？——一律由 §3.1/§3.5 的雙模型交叉驗證決定，理由記錄於 log，人類只在事後選讀。
- **保持精簡**：wiki 是工具不是目的，不要水文。
- **不預設立場**：發現矛盾先嘗試共識仲裁；仍無法收斂就明標兩種觀點並存（歸入 `discussions/`），不要掩飾也不要擅自選邊。
- **日期使用執行環境的 system local date/time**：建立日記、done 檔案、frontmatter 日期等 `YYYY-MM-DD` 欄位時，使用當前執行環境的本地日期；不要將 `Asia/Taipei` 當作現行固定規則。歷史文件中的舊日期策略只保留其歷史事實，後續更新才採現行規則。

---

## 7. 搜尋引擎

規模小（目前階段）不需要。如果之後 wiki 膨脹：

- 搜尋引擎：[qmd](https://github.com/tobi/qmd)（BM25 + 向量 + reranking，本地）。
- Obsidian 插件：Graph View、DATAVIEW、Marp（投影片）、Web Clipper（剪網頁到 `raw/web/`）。

---

## 8. Obsidian Web Clipper 設定

最頻繁的入場動作是「瀏覽器點一點，腦中想到『這個可以存』」。以下是約定 SOP：

### 8.1 設定（僅一次）
- 在 Obsidian Web Clipper 中設定預設值：
  - **Format**：Markdown
  - **Destination**：資料夾路徑填 `raw/web/`（而不是 vault root 或 `Clippings/`）
  - **Filename 模板**：`{{date|date:"YYYY-MM-DD"}}-{{title}}`（避免空白、連字符、遵 §4.1）
  - Template 不宣稱能指定圖片目錄；若 Clipper 實際設定可指定，使用 `raw/assets/`，否則由 ingest normalize 搬移並修正引用。

### 8.2 活動 SOP
- 看到文章 → 點 Clipper icon → 確認 Format/Destination/Filename 沒被改變 → 送出。
- 回到 agent 這邊說一聲「好了」並帶上 URL 或該文章檔名，agent 就能定位。
- 如果檔案出現在 `Clippings/`、`Clippings2/` 之類其他位置 → agent 必須主動搬進 `raw/` 並重新命名為日期+標題格式。

### 8.3 網站限制提醒
- 如果該文章的 HTML 會被 zse-ck / Cloudflare / JS-challenge 之類的身分驗證阻擋（例如 zhuanlan.zhihu.com），**直接 curl 抓不到**——瀏覽器點一點才能截取，需要走 Clipper 路徑，不可濫用。

---

## 9. 閉環優化：收集 → 消化 → 實踐 → 反思

```
① 收集 ──→ ② 消化 ──→ ③ 實踐 ──→ ④ 反思 ──→ 回到①
 │           │           │           │
 ▼           ▼           ▼           ▼
raw/剪藏    wiki/頁面    做專案      lint/巡檢
花園種子    index整理    skill用     花園巡檢
TODO收集    log記錄      TDD/CR      過時清理
```

各階段詳細流程見 §3（Ingest / Query / Lint / Projects）。核心節奏：收集與實踐每天進行，消化每週 1-2 次，反思每週末。

---

## 10. 架構靈感

本系統基於：

1. **[Andrej Karpathy 的 LLM Knowledge Base 概念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)** — 不需要向量資料庫，個人規模下 wiki + index 就夠用。LLM 即編譯器，原始資料進來、結構化 wiki 出來。query 答案回填 wiki，知識持續累積。

2. **[Google Cloud OKF (Open Knowledge Format)](https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf)** — Markdown + YAML frontmatter 表示知識。進階式揭露（Progressive Disclosure：index → topics → entities）、Graph-shaped（概念互相連結形成語義網路）、Trust tier + Provenance（信任機制與來源追蹤）。
