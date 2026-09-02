# W-074 自我改進迴路設計 — 從可觀測性到實際會變好

> 補完決策 #14 留下的缺口：原本只寫了「完整可觀測性是自我成長的前提」，沒有寫前提成立之後迴路怎麼跑。
>
> 建立：2026-09-02　｜　狀態：待 Cheer 拍板三個開放點（見文末）
> 上游：[[wiki/concepts/continual-harness|Continual Harness]]、[[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill]]、[[wiki/concepts/meta-harness|Meta-Harness]]

---

## 1. 我們的不對稱優勢：eval 資料是免費的

一般 agent 想做自我改進，最貴的一步是**做評測集**——要有人去標「這個回答好不好」。

Cheerio 不用。因為決策 #1 就規定主 Cheerio 每個案子都要做一次「意圖 vs 產出」的比對，**每跑一次閉環就自動產生一筆帶標籤的評測資料**：

```
(意圖紀錄, 交付物, verdict, 判定理由)   ← 步驟⑤ 的副產品
```

更關鍵的是決策 #9 的第三條：**只有人類能推翻主 Cheerio 的判定**。人類每一次推翻，就是一筆**黃金標籤**——它告訴我們主 Cheerio 判錯了，而且錯在哪個方向（該過沒過 / 不該過卻過了）。

> 一句話：別人要花錢買的東西，我們為了治理已經在生產了。這是自建 harness 真正的複利點，比「拿得到 trace」更值錢。

## 2. 兩個指標（都能從一級紀錄直接算，不必埋點）

| 指標 | 算法 | 看什麼 |
|---|---|---|
| **驗收準確率** | 1 −（人類推翻次數 ÷ 主 Cheerio 判定次數） | 主 Cheerio 的核心職責做得好不好 |
| **一次過率** | 第一次送驗就判「相符」的契約數 ÷ 契約總數 | 意圖翻譯 ＋ 派工品質（前段做得好，後段就不用退） |

兩個指標指向不同的病：**驗收準確率低 = 判官有問題**（步驟⑤的 prompt 或比對基準不好）；**一次過率低 = 派工有問題**（步驟②③的意圖翻譯或專家選擇不好）。分開看才知道要修哪裡。

第三個之後可以加：**退回輪數分布**（同一份契約被退回幾次才過），用來抓「反覆磨但磨不出來」的案型。

## 3. 五階迴路

```
① Trace ──→ ② Eval ──→ ③ Diagnose ──→ ④ Gate ──→ ⑤ Release
   事件流      算指標        歸因          驗證        版本化發布
  （已有）    （批次）    （四選一）    （不退步）    （寫回事件流）
```

### ① Trace — 已經有了
事件流（決策 #10）。這一階 MVP 就會完成，不需額外設計。

### ② Eval — 從一級紀錄產生評測集
觸發時機：每 N 個案子結案後（建議 N=10，MVP 之後才啟用）。產出：兩個指標的當期值 ＋ 失敗案例清單。

### ③ Diagnose — 歸因到「四個可改物件」之一
[[wiki/concepts/continual-harness|Continual Harness]] 把 harness 狀態形式化為 **H = (ρ, G, K, M)**——prompt、sub-agents、skills、memory。對到我們：

| Continual Harness | Cheerio 的可改物件 | 誰在改 |
|---|---|---|
| ρ prompt | 主 Cheerio 的三個 prompt（翻意圖／生契約／驗收比對） | 自我改進迴路 |
| G sub-agents | 專家註冊表（新增／退役專家） | 決策 #11 已有治理流程 |
| K skills | 專家的 SKILL 內容 | 自我改進迴路 |
| M memory | 記憶的取回策略參數 | 自我改進迴路 |

每個失敗案例必須歸到其中一格。**歸不到就不改**——這條防止「感覺怪怪的所以調一下 prompt」這種無法驗證的修改。

### ④ Gate — 改動要通過驗證才准上
借 [[wiki/concepts/skillopt-meta-skill|SkillOpt]] 的三個機制：

- **文字學習率預算**：一次只准改一個物件、改動幅度有上限
- **Bounded Edit**：只允許 add / delete / replace，不允許整份重寫
- **Validation Gate**：改動後在**歷史案例重放**上不得退步（保留集，不能只看被改進的那幾個案例）

### ⑤ Release — 版本化並寫回事件流
每次發布記下：改了哪個物件、為什麼改、指標前後。**這條紀錄本身也是一級紀錄**——之後要回答「為什麼主 Cheerio 現在這樣判」，得查得到。

## 4. 紅線：什麼可以改、什麼不能改

| | 物件 | 理由 |
|---|---|---|
| ✅ 可改 | 主 Cheerio 三個 prompt、專家 SKILL、policy 參數、專家註冊表 | 都是「怎麼做」的層次 |
| ❌ **不可改** | 四態狀態機與轉換權限（決策 #9） | 改了就沒有治理 |
| ❌ **不可改** | 一級紀錄 schema 的必填欄位 | 改了歷史資料無法比較，指標失去意義 |
| ❌ **不可改** | 驗收基準的來源（一律是意圖紀錄） | **最重要的一條** |

最後一條要說白：**讓 agent 能改自己的驗收標準，等於沒有驗收。** 這是 reward hacking 最典型的入口——系統會發現「把標準放寬」比「把事情做好」容易得多。[[wiki/concepts/continual-harness|Continual Harness]] 那頁的標籤本身就列著 `reward-hacking`，這不是我們想像出來的風險。

> 對照：那張外部 harness 架構圖把 Gate → Release 畫成純技術流程。我們多的是「哪些東西不准進這個流程」，這是治理層才有的東西。

## 5. 治理：沿用決策 #11 的分流

決策 #11 已經規定主 Cheerio 新增專家時「有重大影響 → 停下來等人類仲裁；沒有 → 先做、事後報告，兩者都留理由」。自我改進沿用同一條，並把「重大影響」定義清楚：

- **重大（要等人類）**：動到 policy、動到專家註冊表（新增／退役專家）、改動導致任一指標下降
- **非重大（先做後報）**：只改 prompt 措辭、只改 SKILL 內文，且 Gate 全過

## 6. MVP 做到哪

**MVP 只做①，但要確保②算得出來。** 具體就是一件事：**把驗收結果變成可查詢欄位，而不是塞在 metadata 自由文字裡。**

建議 `StateTransitionEvent` 新增：

```yaml
  verdict: enum?             # "match" | "mismatch"（步驟⑤的判定結果）
  verdict_reason: string?    # 判定理由（自由文字，給人看）
  verdict_confidence: float? # LLM 比對的信心度
  human_override: bool       # 這次轉換是否為人類推翻主 Cheerio（黃金標籤）
  overrides_span_id: string? # 被推翻的那次判定
```

現有 schema 把這些放在 `metadata: object` 的自由文字裡。**兩個指標都需要它們是結構化欄位**，否則之後要算指標得靠 LLM 去讀 metadata——那就變成用 LLM 評估 LLM，可信度整個垮掉。這正是決策 #16 加 `model` 欄位的同一個理由：現在加幾乎零成本，事後補要動歷史資料。

其餘②③④⑤全部延後，等 MVP 六條驗收條件全過再說。

## 7. 待 Cheer 拍板

1. **`verdict` 那五個欄位現在就升成結構化欄位嗎？** 建議要——這是本文唯一影響 MVP 的改動
2. **Eval 的觸發頻率**：每 N 案（建議 N=10）還是固定週期？MVP 期間案件量少，建議按案數
3. **人類推翻算不算「主 Cheerio 失敗」？** 建議算，而且刻意讓這個數字難看——它是唯一誠實的品質訊號。但要留一個 `override_kind` 區分「判錯」與「我改變主意了」，否則指標會被人類改主意的次數汙染

---

## 相關

- [[work/designs/w074-memory-architecture|W-074 記憶架構設計]]
- [[wiki/concepts/context-engineering|Context Engineering]]
- `work/designs/schemas/state-transition-rules.md`、`work/designs/schemas/event-stream.md`
