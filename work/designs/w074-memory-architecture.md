# W-074 記憶架構設計 — 四層記憶的機制定案

> 補完決策 #3 留下的缺口：Episodic / Artifact 兩層記憶原本標記「待定」，本文定案。
>
> 建立：2026-09-02　｜　狀態：待 Cheer 拍板兩個開放點（見文末）
> 上游：[[wiki/discussions/mem0-vs-decision-ledger-for-w074|Mem0 vs Decision-Ledger 對 W-074 架構的適用性]]、[[wiki/concepts/coala-four-memory-types|CoALA 四種記憶類型]]、[[wiki/concepts/recall-policy-ladder|Recall Policy Ladder]]、[[wiki/concepts/context-engineering|Context Engineering]]

---

## 1. 起點：三個既有結論

1. **記憶按功能分四層**（決策 #3 圖上已有）：Episodic / Semantic / Procedural / Artifact
2. **Semantic 與 Procedural 已配對完成**：Semantic → Mem0 式事實提煉；Procedural → Decision-Ledger。兩者疊加而非二選一
3. **Decision-ledger 是實測最強的取回策略**（Sakana AI，X-Bench long-horizon），勝過 vector RAG，而且更省 token

缺的是第 4 點——Episodic 與 Artifact 用什麼機制，以及這一切在 MVP 要做到哪。

---

## 2. 核心主張：我們的一級紀錄本身就是 Decision-Ledger

這是本文最重要的一句話。Decision-ledger 的資料單元是 `intent → rationale → action → outcome`，而 W-074 為了「驗收」而設計的四份 schema 逐欄對得上：

| Decision-Ledger 概念欄位 | Cheerio 的對應物 |
|---|---|
| **intent**（當初想要什麼） | `IntentRecord.raw_input` ＋ `goal` |
| **rationale**（為什麼這樣做） | `TaskContract.assumptions`（＋建議新增 `rejected_alternatives`） |
| **action**（實際做了什麼） | `EventStream` 從 `contract.dispatched` 到 `expert.*` 的事件序列 |
| **outcome**（結果與判定） | 驗收 verdict ＋ `StateTransition` 的轉換紀錄與理由 |

**推論：Procedural Memory 不需要另外蓋一套系統，它是從一級紀錄投影出來的。** 我們為了治理而留的紀錄，順手就是效果最好的那一階記憶機制——這不是額外成本，是同一份資料的第二種用途。

> 這也回頭解釋了為什麼「主 Cheerio 不背記憶」不會讓系統變笨：它不背，是因為紀錄本身結構化到可以隨時排序取回。

---

## 3. 四層記憶定案表

| 層 | 存什麼 | 存儲機制 | 取回方式 | 維護策略 | MVP |
|---|---|---|---|---|---|
| **Episodic** | 發生過什麼：案件事件流封存 ＋ 每案一份結案摘要 | SQLite `events` 表（append-only）＋ 每案 `summary.md` | 先 `case_id` / 時間範圍精確查，再 FTS5 全文；**不用向量** | retire 不 delete | ✅ 做（事件流本來就要寫） |
| **Semantic** | Cheer 的偏好與背景、已驗證事實 | MVP：純文字 `MEMORY.md`；長大後才換 Mem0 式向量 | 預載入 ＋ FTS5 | override / retire | ⏳ 延後 |
| **Procedural** | 流程、skill、決策軌跡 | **不新建**：一級紀錄的投影 ＋ `SKILL.md` | ranked recall（依相關度排序，不做 gate 判斷） | append-only | ✅ 自然產生 |
| **Artifact** | 產出物：報告、程式碼、檔案 | 檔案系統為真相 ＋ SQLite 索引表 | 中繼資料查詢 ＋ FTS5；**不切塊向量化** | 版本化，`superseded_by` 指新版 | ✅ 只做索引 |

### 3.1 Episodic 為什麼不用向量

三個理由，由強到弱：

1. **查詢形狀不對**：episodic 的實際查詢幾乎都是「哪個案子、什麼時候、誰做的」——這是精確查詢，不是語意相似度
2. **實測支持**：decision-ledger 排名高於 vector RAG，而事件流＋一級紀錄就是 decision-ledger
3. **成本**：向量化每一筆事件的成本會隨事件量線性成長，但回收的價值很低

> 未來若出現「我記得幾個月前做過類似的事，但想不起是哪個案子」這種真正的語意查詢，再對**結案摘要**（不是逐筆事件）補向量索引即可——摘要量級小兩個數量級。

### 3.2 Artifact 為什麼不切塊

我們的 artifact 大多是自己產的 markdown 報告與程式碼，**結構完整、標題層級清楚**。傳統 RAG 把它們切成 chunk 做相似度搜尋，會毀掉這個結構。[[wiki/concepts/chunkless-rag|Chunkless RAG]] 的路線更適合：保留文件 tree，讓 agent 靠推理導航。

MVP 的落地版本刻意更保守——**只做「路徑 ＋ 中繼資料 ＋ 全文 FTS5」**，連 Docling 都先不上。理由：MVP 只有一個案子，artifact 數量是個位數，先把索引表的欄位留對比什麼檢索技術都重要。

### 3.3 ArtifactRecord（建議新增的第五份 schema，小）

```yaml
ArtifactRecord:
  artifact_id: string        # "a-YYYYMMDD-NNN"
  case_id: string
  contract_id: string        # 哪份契約產出的
  path: string               # 檔案系統路徑（相對於 vault / workspace root）
  kind: enum                 # "report" | "code" | "data" | "diagram" | "other"
  content_hash: string       # 內容雜湊，用來偵測外部修改
  created_at: ISO-8601
  created_by: string         # actor（哪位專家）
  superseded_by: string?     # 被哪個新版取代（retire 不 delete）
  is_deliverable: bool       # 是否為契約明列的交付物（驗收要看的就是這些）
```

`is_deliverable` 這欄是為了驗收：主 Cheerio 在步驟⑤要比對的是**契約明列的交付物**，不是專家過程中順手產生的暫存檔。沒有這欄，驗收會被中間產物干擾。

---

## 4. 三條記憶紅線

1. **核心不做相似度檢索。** 主 Cheerio 只查一級紀錄與狀態面，走精確查詢（by id）。理由：驗收若因為「檢索沒撈到」而判錯，是最難除錯的一種錯，而且會侵蝕整個治理層的可信度。RAG 只發生在專家內部與長期記憶層。
2. **只 retire 不 delete。** 一級紀錄 append-only；記憶失效用 `superseded_by` / 狀態標記表示。刪掉會讓「當初為什麼這樣做」永久消失，正好製造 [[wiki/concepts/context-decay|Context Decay]]。
3. **記憶是參考資料，不是最高指令。**（原圖上已有，這裡再確認一次）調出來的記憶不能覆蓋當次的意圖紀錄。

---

## 5. 對現有 schema 的影響

| Schema | 改動 | 理由 |
|---|---|---|
| `TaskContract` | **新增 `rejected_alternatives: {option, why_rejected}[]`** | 對治 [[wiki/concepts/context-decay|Context Decay]]。Tier C 黑盒專家本質上就是一次跨模型交接，decay 風險最高；沒有這欄，下一次派同類任務會重踩同一個坑 |
| 新增 `ArtifactRecord` | 見 §3.3 | 驗收要能指名「該看哪個檔案」 |
| `IntentRecord` | 不動 | 已足夠 |
| `EventStream` | 不動 | 已足夠 |

---

## 6. MVP 只做這些

- ✅ 一級紀錄寫進 SQLite（Episodic ＋ Procedural 自然成立）
- ✅ Artifact 索引表（含 `is_deliverable`）
- ⏳ Semantic 長期記憶（MEMORY.md 就夠，不上向量庫）
- ⏳ 結案摘要的向量索引（等案件累積到查不動再說）
- ⏳ Chunkless RAG / Docling（等 artifact 量大再說）

---

## 7. 待 Cheer 拍板

1. **`rejected_alternatives` 現在就加進 TaskContract 嗎？** 建議加——成本是一個陣列欄位，但事後補要動歷史資料格式（跟決策 #16 加 `model` 欄位的理由完全一樣）
2. **ArtifactRecord 要獨立成第五份 schema，還是塞進 TaskContract 的 deliverables？** 建議獨立——artifact 有自己的生命週期（會被新版取代），塞進契約會讓契約變成可變物件，違反一級紀錄不可變的原則

---

## 相關

- [[wiki/concepts/context-engineering|Context Engineering]]（本次一併補上的對策頁）
- [[work/designs/w074-self-improvement-loop|W-074 自我改進迴路設計]]
- `work/designs/schemas/`（四份既有 schema）
