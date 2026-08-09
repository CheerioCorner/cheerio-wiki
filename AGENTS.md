# AGENTS.md — LLM 知識庫工作守則

## Vault 入口

- [[wiki/index|Wiki Index]] — canonical knowledge entry
- [[wiki/topics|Topics / Taxonomy]] — navigation entry
- [[work/current|Current Work]] — current work and references
- [[raw/README|Raw Sources]] — immutable input contract


> 本檔案規範 LLM agent 如何維護這個知識庫。
> 維護者：Cheerio
> 建立日期：2026-07-11
> 最後更新：2026-08-05

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
│   ├── assets/             # 原始來源使用的圖片與附件
│   └── README.md            # raw contract
├── projects/               # Project OKF Bundles（跨 session、跨環境）
│   └── <project-id>/       # project-local decisions、discussions、references
├── wiki/                   # Shared cross-project knowledge graph
│   ├── concepts/           # 可跨專案重用的抽象知識
│   ├── entities/           # 人、工具、package、服務與具體實作
│   ├── sources/            # 整理後的外部資料與研究紀錄
│   ├── decisions/          # 全域／跨專案已確認的架構與技術選擇
│   ├── discussions/        # 尚未定案的方案與研究問題
│   ├── topics.md           # taxonomy 總入口
│   ├── topics/             # topic landing pages（.md）
│   ├── visualizations/     # 所有 Canvas 視覺化投影
│   ├── audits/             # 分析報告與 migration audit
│   ├── index.md            # 內容索引
│   └── log.md              # 時間日誌（append-only）
├── work/                   # 可追溯工作狀態與歷史事件
│   ├── README.md            # work contract
│   ├── current.md          # 唯一的目前工作清單
│   └── history/YYYY-MM.md  # 按月分片的完成／決策／活動事件
└── AGENTS.md               # 本檔，工作守則
```

**各資料夾用途：**
- `raw/` = 原始資料與對話。永遠只讀；依來源通道分為 `web/`、`youtube/`、`conversations/`，附件集中於 `assets/`。原始 annotator feedback 使用 `type: raw-conversation`、`immutable: true`。
- `projects/<project-id>/` = 跨 session、跨本地環境的 Project OKF Bundle；不取代 package repository、原始碼或 package 內的 `docs/`。
- `wiki/concepts/` = 可跨專案重用的抽象知識，`type: concept`。
- `wiki/entities/` = 人、工具、package、服務與具體實作，`type: entity`。
- `wiki/sources/` = 整理後的外部資料，`type: source`；raw 仍是原始 source of truth。
- `wiki/decisions/` = 全域或跨專案已確認的決策；project-local decisions 留在 Project Bundle。
- `wiki/discussions/` = 尚未定案的討論；確認後才提升為 decision 或 concept。
- `wiki/topics.md` 與 `wiki/topics/*.md` = 導航與 taxonomy；只放 topic 導航頁，canonical content 優先連到五個 collections。
- `wiki/visualizations/` = 視覺化投影；**新格式**：直接在 Notion 寫入 Mermaid code block（不需要轉換為圖片）；舊有 Canvas 檔案仍保留。每張視覺地圖必須在 `visualizations/README.md` 註冊並標示所屬 topics；對應 topic page 的 `## Visualizations` 區塊須列出相關視覺地圖。
- `work/` = 唯一的工作狀態與可追溯事件系統；`README.md` 定義 contract，`current.md` 管理目前工作，`history/YYYY-MM.md` 保存完成、決策與重要處理結果。舊工作目錄已完成遷移並刪除，不得重新建立。
- `AGENTS.md` = 工作守則，由人類與 LLM 共同演化；它是治理入口，不要求每個內容頁反向連結。

---

## 2. 資料流動規則

### 2.1 什麼進 raw/？
| 類型 | 範例 | 觸發方式 |
|------|------|---------|
| 網頁文章 | 部落格、新聞、知乎回答 | Web Clipper 或 agent 存入 `raw/web/` |
| URL 連結 | 你貼給我的 URL | agent 用 web_fetch 抓取後存入 |
| YouTube | 影片字幕/逐字稿 | 字幕抓取工具（待研究）存入 `raw/youtube/` |
| PDF/論文 | 學術論文、技術報告 | `markitdown` 轉 Markdown + `pymupdf` 提取圖片 → `raw/web/`（用 `pdf-to-wiki` skill） |
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

**觸發條件：** 人類在 `raw/` 放入新檔案並說「處理這個」或「ingest」。若 ingest 產生明確 follow-up 或完成事件，依 `work/README.md` 更新 work。

**單筆流程（推薦，可監督）：**
1. 讀完來源（文本一次讀完；有圖片時另外批次讀）。
2. 與人類討論重點，確認要提取什麼知識點。
3. 在正確的 canonical collection 建立／更新相關頁面（單一來源可能會動到多頁）：
   - 建立「來源筆記」（`wiki/sources/`，1 頁彙整該資料的重點）**，⚠️ 必須在 frontmatter 加入 `provenance` 指向 raw 檔案或外部來源**（格式見 §4.2）
   - 可重用抽象放入 `wiki/concepts/`
   - 具體人／工具／package 放入 `wiki/entities/`
   - 尚未定案內容放入 `wiki/discussions/`；已確認的全域選擇放入 `wiki/decisions/`
   - **⚠️ Topic pages 必須同步更新**：每當新增或更新 entity/concept/source，必須同時更新對應的 `wiki/topics/*.md` 導航頁（讀取新頁面 frontmatter 的 `topics: [...]`，在每個相關 topic page 的 Entities、Concepts 或 Sources 列表加入新頁面，跨 topic 頁面用 🛠️ 標記）
   - `wiki/topics.md` 與 `wiki/topics/*.md` 只放導航內容；不得在 topic 目錄建立內容副本或 compatibility stub
   - 標記新資料是否推翻／補充既有結論
4. 更新 `wiki/index.md`（加入新頁或更新摘要）。
5. 在 `wiki/log.md` 附加一條 ingest 紀錄。
6. **git push** 同步回 GitHub。

**批次流程：** 相同步驟，但一次處理多筆、較少互動。適合不重要的積累型資料。

### 3.2 Query（查詢）

**觸發條件：** 人類對 wiki 提出問題。

若問題涉及「之前做過什麼」「上次進度」或既有決策，才讀取相關 `work/history/YYYY-MM.md`；一般 query 不在啟動時預先載入 history。

1. 先讀 `wiki/index.md` 找出相關頁面；啟動時只讀 `work/current.md`，history 僅在需要追溯過去時讀取。
2. 讀那些頁面（必要時追溯其連結）。
3. 給出有引用的回答，並標明來源頁面。
4. **重要：好的回答可以回填成新頁面**（比較表、新分析、新發現）。人類說「把這個存到 wiki」就建檔 + 更新 index + 寫 log。

### 3.3 Lint（健康檢查）

**觸發條件：** 人類說「lint wiki」，或定期（例如每 10 次 ingest 後）。

1. 掃 `wiki/` 找：
   - 頁面間互相矛盾
   - 過時主張被新資料推翻卻沒標記
   - 孤立頁面（沒有 inbound 連結）
   - 出現多次但沒有自己頁面的概念
   - 缺漏的交叉引用
   - **Source note provenance 缺漏**：`wiki/sources/` 下的頁面應有 `provenance_raw` 或 `provenance_url` 指向 raw 檔案或外部 URL；指向不存在檔案的 provenance 須標記
   - **Topic page 遺漏**：每個 entity/concept/source frontmatter 的 `topics: [...]` 都應在對應 `wiki/topics/*.md` 的 Entities、Concepts 或 Sources 列表中出現；反之，topic page 列出的頁面都應存在
   - **視覺地圖遺漏**：`wiki/visualizations/` 下的視覺地圖都應在 `visualizations/README.md` 註冊並標示 topics；對應 topic page 的 `## Visualizations` 區塊須列出相關視覺地圖
2. 提出「該修什麼、該查什麼、該補什麼資料」的清單。
3. 人類確認後開始修改。
4. **git push** 同步回 GitHub。

### 3.4 Projects（專案維護）

**觸發條件：** 開始新專案、或專案有重大進展時。

1. 在 root `projects/<project-id>/` 建立 Project OKF Bundle。
2. 至少建立 `index.md`（project identity、邊界、目標、入口）。
3. 可選：`decisions/`、`discussions/`、`references.md`、`log.md`。
4. 使用 GitHub repository URL 作為跨環境 canonical reference。
5. 不複製 package source code、完整 PLAN 或 package `docs/`；更新 `wiki/index.md` 的 Projects 區塊。
6. 專案 canonical 入口一律是 `projects/<project-id>/index.md`。舊的 `wiki/projects/` 已完成退場，不再建立或更新 legacy project README。

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

---

## 5. 索引與工作歷史

#### 5.1 `wiki/index.md`（內容索引）

- 按 taxonomy 分區：Topics（AI Agent / Extension Dev / Meta Systems / Knowledge Mgmt / Skill）/ Collections / Projects / Sources。
- 每頁一行：`[[collection/path|標題]] — 一句話摘要`。
- **多 topic 頁面**：跨 topic 頁面在所有相關 topic 區塊都列出，用 🛠️ 標記。
- 每次 ingest / lint 後更新。
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

## 6. 與人類的協作原則

- **不主動刪人類寫的東西**。要改前先問。
- **不擅改 raw/**。永遠是只讀。
- **關鍵判斷先問**：這個來源要不要建獨立頁？這條結論要不要標記為「暫定」？要併入舊頁還是另開新頁？
- **保持精簡**：wiki 是工具不是目的，不要水文。
- **不預設立場**：發現矛盾就明標出來，不要掩飾。
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

## 9. 閉環優化系統

### 核心循環：收集 → 消化 → 實踐 → 反思

```
① 收集 ──→ ② 消化 ──→ ③ 實踐 ──→ ④ 反思 ──→ 回到①
 │           │           │           │
 ▼           ▼           ▼           ▼
raw/剪藏    wiki/頁面    做專案      lint/巡檢
花園種子    index整理    skill用     日記回顧
TODO收集    log記錄      TDD/CR      過時清理
```

### 各階段做法

**① 收集（每天/每次對話）**
- 浮現的新想法 → 加入 `work/current.md`，並建立至少一個 reference；不得重新建立舊工作目錄
- 看到好文章 → Web Clipper 到 `raw/`
- 好的對話洞見 → 說「存到 wiki」
- 新工具/方法 → 說「這概念值得記下來」

**② 消化（每週 1-2 次）**
- 執行 Ingest 處理 `raw/` 積壓
- 更新 `wiki/index.md` 和 `wiki/log.md`
- Notion 花園灌溉

**③ 實踐（每天）**
- 從 `work/current.md` 拉任務做
- 有實際進展就追加 `work/history/YYYY-MM.md`，並連結 raw、project 或 wiki；沒有可追溯結果的對話不必記錄
- 用 `tdd`、`code-review`、`grill-me` 等 skill 跑完整流程
- 每次實踐後問：「這個學到什麼可以存到 wiki？」

**④ 反思（每週末）**
- Lint wiki：找矛盾、孤頁、過時資訊
- 花園巡檢：哪些種子停滯了？
- 日記回顧：本週學了什麼？
- 檢查 `work/current.md`：重新排列工作優先級與 references
- 優化 skill：哪個不好用？用 `skill-creator` 改

---

## 10. 架構靈感

本系統基於：

1. **[Andrej Karpathy 的 LLM Knowledge Base 概念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)**
   - 核心理念：不需要向量資料庫，個人規模下 wiki + index 就夠用

2. **[Google Cloud OKF (Open Knowledge Format)](https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf)**
   - 核心理念：Markdown + YAML frontmatter 表示知識，人類和 AI 都能讀
   - 進階式揭露（Progressive Disclosure）：index → topics → entities
   - Graph-shaped, not tree-shaped：概念之間互相連結形成語義網路
   - Trust tier + Provenance：內建信任機制和來源追蹤

核心理念：
- **不需要向量資料庫**：個人規模下，簡單的 wiki + index 就夠用。
- **LLM 即編譯器**：原始資料進來，結構化 wiki 出來。
- **回饋循環**：query 答案可以存回 wiki，讓知識持續累積。
- **閉環優化**：收集→消化→實踐→反思，不斷自我改進。
- **Progressive Disclosure**：先看 overview，再深入特定主題。
- **Graph is knowledge**：graph view 應該只顯示知識內容，不被 structural files 污染。
