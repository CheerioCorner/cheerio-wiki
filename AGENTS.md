# AGENTS.md — LLM 知識庫工作守則

> 本檔案規範 LLM agent 如何維護這個知識庫。
> 維護者：Cheerio
> 建立日期：2026-07-11
> 最後更新：2026-08-02

---

## 1. 架構

```
Obsidian/
├── raw/                    # 原始資料（只讀，不修改）
│   ├── assets/             # Obsidian 下載的本機圖片
│   └── 2026-07-11-xxx.md   # 收集的網頁、文章、論文等
├── wiki/                   # 所有筆記（LLM 維護）
│   ├── topics/             # 主題式分類
│   │   ├── ai-agent/       # AI Agent 相關
│   │   ├── extension-dev/  # Extension 開發
│   │   ├── meta-systems/   # 元系統
│   │   └── knowledge-mgmt/ # 知識管理
│   ├── projects/           # 專案維護（README、進度、決策紀錄）
│   ├── sources/            # 來源筆記（AI 整理過的摘要）
│   ├── index.md            # 內容索引（按主題分類）
│   └── log.md              # 時間日誌（append-only）
├── todos/                  # 任務系統
│   ├── current.md          # 目前進行中（1-3 個任務）
│   ├── backlog.md          # 待辦清單（按優先級排列）
│   ├── done/               # 已完成（按日期歸檔）
│   └── archive/            # 舊的已完成項目（超過 30 天）
├── journal/                # 日記系統
│   ├── daily/              # 每日日記（YYYY-MM-DD.md）
│   └── templates/          # 日記模板
└── AGENTS.md               # 本檔，工作守則
```

**各資料夾用途：**
- `raw/` = 原始資料（網頁剪藏、PDF、外部文章）。只讀不寫。
- `wiki/topics/` = 結構化筆記，按主題分類。LLM 維護。
- `wiki/projects/` = 專案維護，追蹤所有建造中的東西。
- `wiki/sources/` = 來源筆記，AI 整理過的摘要。
- `todos/` = 任務系統，管理所有待辦和進行中任務。
- `journal/` = 日記系統。用 Obsidian Daily Notes + Calendar 外掛管理。
- `AGENTS.md` = 工作守則。由人類與 LLM 共同演化。

---

## 2. 資料流動規則

### 2.1 什麼進 raw/？
| 類型 | 範例 | 觸發方式 |
|------|------|---------|
| 網頁文章 | 部落格、新聞、知乎回答 | Web Clipper 或 agent 存入 |
| URL 連結 | 你貼給我的 URL | agent 用 web_fetch 抓取後存入 |
| YouTube | 影片字幕/逐字稿 | 字幕抓取工具（待研究） |
| PDF/論文 | 學術論文、技術報告 | 手動放入 raw/ |
| 原始文字 | 你直接貼的一段文字 | agent 存入 raw/ |

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

### 2.4 什麼進日記？
| 類型 | 範例 |
|------|------|
| 今日完成 | 做了哪些事 |
| 學到的 | 今天的洞見 |
| 心情反思 | 對工作的感受 |
| 明日計畫 | 明天要做什麼 |

**關鍵：日常記錄，不需要結構化。**

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

**觸發條件：** 人類在 `raw/` 放入新檔案並說「處理這個」或「ingest」。

**單筆流程（推薦，可監督）：**
1. 讀完來源（文本一次讀完；有圖片時另外批次讀）。
2. 與人類討論重點，確認要提取什麼知識點。
3. 在 `wiki/topics/` 建立/更新相關頁面（單一來源可能會動到多頁）：
   - 建立「來源筆記」（`wiki/sources/`，1 頁彙整該資料的重點）
   - 更新相關主題頁面，加入雙向連結
   - 標記新資料是否推翻/補充既有結論
4. 更新 `wiki/index.md`（加入新頁或更新摘要）。
5. 在 `wiki/log.md` 附加一條 ingest 紀錄。
6. **git push** 同步回 GitHub。

**批次流程：** 相同步驟，但一次處理多筆、較少互動。適合不重要的積累型資料。

### 3.2 Query（查詢）

**觸發條件：** 人類對 wiki 提出問題。

1. 先讀 `wiki/index.md` 找出相關頁面。
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
2. 提出「該修什麼、該查什麼、該補什麼資料」的清單。
3. 人類確認後開始修改。
4. **git push** 同步回 GitHub。

### 3.4 Projects（專案維護）

**觸發條件：** 開始新專案、或專案有重大進展時。

1. 在 `wiki/projects/` 建立子資料夾。
2. 至少建立 `README.md`（專案簡介、目標、進度）。
3. 可選：`decisions.md`（技術決策）、`issues.md`（問題紀錄）。
4. 更新 `wiki/index.md` 的 Projects 區塊。

---

## 4. Wiki 頁面規範

### 4.1 檔名
- 全小寫、英文或中文皆可，以一致性為原則。
- 主題頁用主題名（`pi-mono.md`、`meta-harness.md`）。
- 來源筆記用日期+標題（`2026-07-11-podcast-name.md`）。

### 4.2 Frontmatter（YAML，給 Obsidian Dataview 用）

每頁開頭要有：

```yaml
---
title: 頁面標題
type: entity | concept | source | comparison | synthesis
created: 2026-07-11
updated: 2026-07-11
sources: 3   # 引用過幾個 raw 來源
tags: [topic-a, topic-b]
---
```

### 4.3 內容結構（建議）

- **第一段：** 一句話總結這個頁面是什麼。
- **主體：** 章節式整理事實、概念、爭議。
- **「來源」區塊：** 列出哪些 raw 檔案支持哪些論點（用 `[[wikilink]]`）。
- **「相關頁面」區塊：** `[[page-a]]`、`[[page-b]]`。

### 4.4 交叉引用

- 一律用 Obsidian 的 `[[wikilink]]` 風格（雙中括號）。
- 提到任何重要概念/實體時，**也要建立連結**。這是 wiki 能編譯一次就永久受用的關鍵。
- 連結斷掉的頁面（`[[foo]]` 指向不存在檔案）是 lint 的目標之一。
- **新規則**：使用完整路徑連結，例如 `[[topics/ai-agent/pi-mono|pi-mono]]`，確保連結在任何位置都能正確解析。

---

## 5. 索引與日誌

### 5.1 `wiki/index.md`（內容索引）

- 按主題分區：Topics（AI Agent / Extension Dev / Meta Systems / Knowledge Mgmt）/ Projects / Sources。
- 每頁一行：`[[link|標題]] — 一句話摘要`。
- 每次 ingest / lint 後更新。
- 中等規模（~100 來源、數百頁）僅靠 index.md 就夠，不需搜尋引擎。

### 5.2 `wiki/log.md`（時間日誌）

- Append-only，不刪舊紀錄。
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
- **日期一律使用臺北時間（Asia/Taipei）**：建立日記、done 檔案、frontmatter 日期等所有 `YYYY-MM-DD` 欄位時，必須以臺北時區計算當日日期，不可直接使用 UTC 或系統本地時間。避免因時差導致日期跳天。

---

## 7. 搜尋引擎

規模小（目前階段）不需要。如果之後 wiki 膨脹：

- 搜尋引擎：[qmd](https://github.com/tobi/qmd)（BM25 + 向量 + reranking，本地）。
- Obsidian 插件：Graph View、DATAVIEW、Marp（投影片）、Web Clipper（剪網頁到 raw/）。

---

## 8. Obsidian Web Clipper 設定

最頻繁的入場動作是「瀏覽器點一點，腦中想到『這個可以存』」。以下是約定 SOP：

### 8.1 設定（僅一次）
- 在 Obsidian Web Clipper 中設定預設值：
  - **Format**：Markdown
  - **Destination**：資料夾路徑填 `raw/`（而不是 vault root 或 `Clippings/`）
  - **Filename 模板**：`{{date|date:"YYYY-MM-DD"}}-{{title}}`（避免空白、連字符、遵 §4.1）
  - 「**Download all images to vault**」項目打勾，實體路徑設為 `raw/assets/`。

### 8.2 活動 SOP
- 看到文章 → 點 Clipper icon → 確認 Format/Destination/Filename 沒被改變 → 送出。
- 回到 agent 這邊說一聲「好了」並帶上 URL 或該文章檔名，agent 就能定位。
- 如果檔案出現在 `Clippings/`、`Clippings2/` 之類其他位置 → agent 必須主動搬進 `raw/` 並重新命名為日期+標題格式。

### 8.3 網站限制提醒
- 如果該文章的 HTML 會被 zse-ck / Cloudflare / JS-challenge 之類的身分驗證阻擋（例如 zhuanlan.zhihu.com），**直接 curl 抓不到**——瀏覽器點一點才能截取，需要走 Clipper 路徑，不可濫用。

---

## 9. 圖片處理

- 圖片放 `raw/assets/`，由 Obsidian Web Clipper 或手動下載後複製過來。
- 在 markdown 中以相對路徑引用：`![[raw/assets/image.png]]`。
- agent 讀取時**先讀文字**，再分批讀相關圖片（LLM 無法在同一次讀取 markdown 中的內嵌圖片）。

---

## 10. 閉環優化系統

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
- 浮現的新想法 → 加入 `ToDo/current.md` Backlog
- 看到好文章 → Web Clipper 到 `raw/`
- 好的對話洞見 → 說「存到 wiki」
- 新工具/方法 → 說「這概念值得記下來」

**② 消化（每週 1-2 次）**
- 執行 Ingest 處理 `raw/` 積壓
- 更新 `wiki/index.md` 和 `wiki/log.md`
- Notion 花園灌溉

**③ 實踐（每天）**
- 從 Backlog 拉任務做
- 做了就要記 —— 日記 + wiki 雙記
- 用 `tdd`、`code-review`、`grill-me` 等 skill 跑完整流程
- 每次實踐後問：「這個學到什麼可以存到 wiki？」

**④ 反思（每週末）**
- Lint wiki：找矛盾、孤頁、過時資訊
- 花園巡檢：哪些種子停滯了？
- 日記回顧：本週學了什麼？
- 更新 Backlog：重新排列優先級
- 優化 skill：哪個不好用？用 `skill-creator` 改

---

## 11. 架構靈感

本系統基於 [Andrej Karpathy 的 LLM Knowledge Base 概念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。

核心理念：
- **不需要向量資料庫**：個人規模下，簡單的 wiki + index 就夠用。
- **LLM 即編譯器**：原始資料進來，結構化 wiki 出來。
- **回饋循環**：query 答案可以存回 wiki，讓知識持續累積。
- **閉環優化**：收集→消化→實踐→反思，不斷自我改進。
