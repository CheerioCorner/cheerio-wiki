# AGENTS.md — LLM 知識庫工作守則

> 本檔案規範 LLM agent 如何維護這個知識庫。
> 維護者：Cheerio
> 建立日期：2026-07-11

---

## 1. 架構（三層）

```
Obsidian/
├── raw/                    # 第一層：原始資料（唯讀，不修改）
│   ├── assets/             # Obsidian 下載的本機圖片
│   └── 2026-07-11-xxx.md   # 收集的網頁、文章、論文等
├── wiki/                   # 第二層：LLM 維護的 markdown 筆記
│   ├── index.md            # 內容索引（按類別）
│   ├── log.md              # 時間日誌（append-only）
│   ├── overview.md         # 總覽頁
│   ├── concepts/           # 概念頁
│   ├── entities/           # 實體頁
│   └── sources/            # 來源頁
└── AGENTS.md               # 第三層：本檔，工作守則
```

**邊界不可跨越：**
- `raw/` 永遠只能讀，永遠不修改。資料進來後只在 wiki 裡消化、重組。
- `wiki/` 由 LLM 全權撰寫；人類通常不直接編輯（除非想手動修）。
- `AGENTS.md` 由人類與 LLM 共同演化，任何工作流程改動先更新這裡。

---

## 2. 操作流程

### 2.1 Ingest（吸收新資料）

**觸發條件：** 人類在 `raw/` 放入新檔案並說「處理這個」或「ingest」。

**單筆流程（推薦，可監督）：**
1. 讀完來源（文本一次讀完；有圖片時另外批次讀）。
2. 與人類討論重點，確認要提取什麼知識點。
3. 在 `wiki/` 建立/更新相關頁面（單一來源可能會動到 10–15 頁）：
   - 建立「來源筆記」（1 頁彙整該資料的重點）
   - 更新相關「實體頁」「概念頁」，加入雙向連結
   - 標記新資料是否推翻/補充既有結論
4. 更新 `wiki/index.md`（加入新頁或更新摘要）。
5. 在 `wiki/log.md` 附加一條 ingest 紀錄。

**批次流程：** 相同步驟，但一次處理多筆、較少互動。適合不重要的積累型資料。

### 2.2 Query（查詢）

**觸發條件：** 人類對 wiki 提出問題。

1. 先讀 `wiki/index.md` 找出相關頁面。
2. 讀那些頁面（必要時追溯其連結）。
3. 給出有引用的回答，並標明來源頁面。
4. **重要：好的回答可以回填成新頁面**（比較表、新分析、新發現）。人類說「把這個存到 wiki」就建檔 + 更新 index + 寫 log。

### 2.3 Lint（健康檢查）

**觸發條件：** 人類說「lint wiki」，或定期（例如每 10 次 ingest 後）。

1. 掃 `wiki/` 找：
   - 頁面間互相矛盾
   - 過時主張被新資料推翻卻沒標記
   - 孤立頁面（沒有 inbound 連結）
   - 出現多次但沒有自己頁面的概念
   - 缺漏的交叉引用
2. 提出「該修什麼、該查什麼、該補什麼資料」的清單。
3. 人類確認後開始修改。

---

## 3. Wiki 頁面規範

### 3.1 檔名
- 全小寫、英文或中文皆可，以一致性為原則。
- 實體頁用名詞（`gpt-4.md`、`張三.md`）。
- 概念頁用主題（`attention.md`、`rlhf.md`）。
- 來源筆記用日期+標題（`2026-07-11-podcast-name.md`）。

### 3.2 Frontmatter（YAML，給 Obsidian Dataview 用）

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

### 3.3 內容結構（建議）

- **第一段：** 一句話總結這個頁面是什麼。
- **主體：** 章節式整理事實、概念、爭議。
- **「來源」區塊：** 列出哪些 raw 檔案支持哪些論點（用 `[[wikilink]]`）。
- **「相關頁面」區塊：** `[[page-a]]`、`[[page-b]]`。

### 3.4 交叉引用

- 一律用 Obsidian 的 `[[wikilink]]` 風格（雙中括號）。
- 提到任何重要概念/實體時，**也要建立連結**。這是 wiki 能編譯一次就永久受用的關鍵。
- 連結斷掉的頁面（`[[foo]]` 指向不存在檔案）是 lint 的目標之一。

---

## 4. 索引與日誌

### 4.1 `wiki/index.md`（內容索引）

- 按類別分區：Entities / Concepts / Sources / Comparisons / Synthesis。
- 每頁一行：`[標題](頁面) — 一句話摘要 —（可選）更新日期、來源數`。
- 每次 ingest / lint 後更新。
- 中等規模（~100 來源、數百頁）僅靠 index.md 就夠，不需搜尋引擎。

### 4.2 `wiki/log.md`（時間日誌）

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

## 5. 與人類的協作原則

- **不主動刪人類寫的東西**。要改前先問。
- **不擅改 raw/**。永遠是只讀。
- **關鍵判斷先問**：這個來源要不要建獨立頁？這條結論要不要標記為「暫定」？要併入舊頁還是另開新頁？
- **保持精簡**：wiki 是工具不是目的，不要水文。
- **不預設立場**：發現矛盾就明標出來，不要掩飾。

---

## 6. 搜尋引擎

規模小（目前階段）不需要。如果之後 wiki 膨脹：

- 搜尋引擎：[qmd](https://github.com/tobi/qmd)（BM25 + 向量 + reranking，本地）。
- Obsidian 插件：Graph View、DATAVIEW、Marp（投影片）、Web Clipper（剪網頁到 raw/）。

---

## 7. Obsidian Web Clipper 設定

最頻繁的入場動作是「瀏覽器點一點，腦中想到『這個可以存』」。以下是約定 SOP：

### 7.1 設定（僅一次）
- 在 Obsidian Web Clipper 中設定預設值：
  - **Format**：Markdown
  - **Destination**：資料夾路徑填 `raw/`（而不是 vault root 或 `Clippings/`）
  - **Filename 模板**：`YYYY-MM-DD-<標題>`（避免空白、連字符、遵 AGENTS.md §3.1）
  - 「**Download all images to vault**」項目打勾，實體路徑設為 `raw/assets/`。

### 7.2 活動 SOP
- 看到文章 → 點 Clipper icon → 確認 Format/Destination/Filename 沒被改變 → 送出。
- 回到 agent 這邊說一聲「好了」並帶上 URL 或該文章檔名，agent 就能定位。
- 如果檔案出現在 `Clippings/`、`Clippings2/` 之類其他位置（那是你手動改過 destination）→ agent 必須主動搬進 `raw/` 並重新命名為日期+標題格式。

### 7.3 網站限制提醒
- 如果該文章的 HTML 會被 zse-ck / Cloudflare / JS-challenge 之類的身分驗證阻擋（例如 zhuanlan.zhihu.com），**直接 curl 抓不到**——瀏覽器點一點才能截取，需要走 Clipper 路徑，不可濫用。
- 人類要進到該 URL **必須實際在瀏覽器裡跨過該挑戰**才能取得原文。

---

## 8. 圖片處理

- 圖片放 `raw/assets/`，由 Obsidian Web Clipper 或手動下載後複製過來。
- 在 markdown 中以相對路徑引用：`![[raw/assets/image.png]]`。
- agent 讀取時**先讀文字**，再分批讀相關圖片（LLM 無法在同一次讀取 markdown 中的內嵌圖片）。

---

## 9. 架構靈感

本系統基於 [Andrej Karpathy 的 LLM Knowledge Base 概念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。

核心理念：
- **不需要向量資料庫**：個人規模下，簡單的 wiki + index 就夠用。
- **LLM 即編譯器**：原始資料進來，結構化 wiki 出來。
- **回饋循環**：query 答案可以存回 wiki，讓知識持續累積。
