# 四態轉換規則 (StateTransitionRules)

> W-074 MVP 三份 schema 之三。定義案件的四種狀態以及誰能觸發哪個轉換。
> 
> 決策依據：W-074 決策 #9（四態狀態機）、#8（衝突三階仲裁）
> 
> **核心原則（8/30）**：狀態轉換 = 100% 確定性邏輯（規則引擎），不需要 LLM 參與

## 狀態定義

```
┌──────────┐    專家開始    ┌──────────┐    專家做完    ┌────────────────┐
│  待辦     │ ───────────→ │  進行中   │ ───────────→ │  待驗收         │
│ pending   │              │ in_progress│              │ awaiting_review │
└──────────┘              └──────────┘              └────────────────┘
                               │    ↑                    │    ↑
                               │    │ 退回               │    │ 退回
                               │    └────────────────────┘    │
                               │                              │
                               │         ┌──────────┐        │
                               │         │  完成     │ ←──────┘
                               │         │ completed │  主 Cheerio 推
                               │         └──────────┘
                               │
                               └──→ 人類可隨時推翻任何狀態
```

## 轉換規則表

| 當前狀態 | 目標狀態 | 觸發者 | 觸發條件 | 說明 |
|---------|---------|--------|---------|------|
| `pending` | `in_progress` | 專家 | 專家收到任務契約後開始執行 | 專家自主觸發 |
| `in_progress` | `pending` | 人類 | 人類插話或取消 | 退回待辦重新排隊 |
| `in_progress` | `awaiting_review` | 專家 | 專家完成任務，標記為待驗收 | 專家自主觸發 |
| `awaiting_review` | `completed` | 主 Cheerio | 主 Cheerio 比對意圖紀錄與產出，判定相符 | **需要 LLM 比對結果作為輸入**，但轉換本身是規則引擎 |
| `awaiting_review` | `pending` | 主 Cheerio | 主 Cheerio 判定不符，退回重做 | 同上，退回原因必須記錄 |
| `awaiting_review` | `escalated` | 主 Cheerio | 主 Cheerio 不確定，上呈人類仲裁 | 人類看完後決定退回或完成 |
| `escalated` | `completed` | 人類 | 人類審閱後確認完成 | 最終仲裁 |
| `escalated` | `pending` | 人類 | 人類審閱後退回重做 | 最終仲裁 |
| `completed` | `pending` | 人類 | 人類推翻已完成的案子（罕見） | 需要記錄推翻理由 |
| `任何` | `withdrawn` | 人類 | 人類取消整個案子 | 終止狀態，不可逆 |

## 觸發者權限矩陣

| 觸發者 | 可觸發的轉換 | 不可觸發的轉換 |
|--------|------------|--------------|
| **專家** | pending→in_progress, in_progress→awaiting_review | 不可自行完成、不可自行退回 |
| **主 Cheerio** | awaiting_review→completed, awaiting_review→pending, awaiting_review→escalated | 不可直接啟動任務（專家自主開始） |
| **人類** | 任何→任何（最高權限） | 無限制 |

## 設計說明

### 為什麼「待驗收」是獨立狀態？

決策 #9：少了待驗收，主 Cheerio 的核心職責就落空。主 Cheerio 的價值是**判斷「人類要的」跟「做出來的」是不是同一件事**，如果沒有待驗收狀態，專家做完就直接完成，主 Cheerio 沒有機會做這件事。

### 步驟⑤→⑥ 的 LLM + 規則引擎邊界

```
⑤ 主 Cheerio 比對意圖紀錄與產出
   │
   │  【LLM】語意比對：acceptance_criteria vs 專家產出
   │  輸出：{ match: boolean, reason: string, confidence: float }
   │
   ▼
⑥ 狀態轉換
   │
   │  【規則引擎】根據⑤的輸入 + 轉換規則表，執行對應轉換
   │  if match == true → completed
   │  if match == false && can_retry → pending
   │  if match == false && !can_retry → escalated
   │
   ▼
  狀態更新，事件流記錄
```

**關鍵**：⑤ 的 LLM 判斷結果只是⑥ 的輸入。⑥ 本身是純規則引擎，不需要再叫 LLM「決定要不要轉換」。

### `can_retry` 的判定邏輯（MVP 簡化版）

MVP 階段的退回邏輯：
- 退回次數 < 3 → 退回 pending，讓專家重做
- 退回次數 ≥ 3 → 升級到 escalated，讓人類決定

這個「3 次」是寫死的規則，不是 LLM 判斷。未來可以改成可配置的 policy。

### 事件流如何記錄每次轉換

每次狀態轉換都會產生一條事件流紀錄（決策 #10）：

```yaml
StateTransitionEvent:
  trace_id: string           # 追蹤 ID
  span_id: string            # 事件 ID
  parent_span_id: string?    # 上級事件（例：專家執行是一整個 span）
  timestamp: ISO-8601        # 轉換時間
  actor: string              # 誰觸發的（"expert:xxx"、"cheerio:main"、"human:cheer"）
  event_type: string         # "state_transition"
  from_state: enum           # 轉換前狀態
  to_state: enum             # 轉換後狀態
  model: string              # 當時使用的 model（決策 #16）
  model_version: string      # model 版本（決策 #16）
  latency_ms: integer        # 從觸發到轉換完成的延遲
  status: enum               # "success" | "error"
  error: string?             # 錯誤訊息（如有）

  # === ★ 9/2 新增：驗收結果結構化（Cheer 拍板）===
  verdict: enum?             # "match" | "mismatch"，步驟⑤ LLM 比對的判定結果
  verdict_reason: string?    # 判定理由（自由文字，給人看）
  verdict_confidence: float? # LLM 比對的信心度 0.0-1.0
  human_override: bool       # 這次轉換是否為人類推翻主 Cheerio 的判定（預設 false）
  overrides_span_id: string? # human_override=true 時，指向被推翻的那次判定事件
  override_kind: enum?       # "misjudged"（主 Cheerio 判錯）| "changed_mind"（人類改變主意）
                             # 先留欄位，MVP 可不填（見設計說明）

  metadata: object           # 其餘額外資訊（不再放 verdict / 退回原因）
```

### 為什麼這幾欄要從 `metadata` 拉出來（9/2 新增，Cheer 拍板）

原本 `verdict`、退回原因、信心度都塞在 `metadata` 的自由文字裡。問題在於**自我改進迴路的兩個指標都要靠它們計算**：

| 指標 | 算法 | 需要的欄位 |
|---|---|---|
| **驗收準確率** | 1 −（`human_override=true` 次數 ÷ 主 Cheerio 判定次數） | `human_override` |
| **一次過率** | 第一次送驗就 `verdict="match"` 的契約數 ÷ 契約總數 | `verdict` |

如果這些值埋在自由文字裡，之後要算指標就得**用 LLM 去讀 metadata**——變成用 LLM 評估 LLM，指標的可信度整個垮掉。結構化之後是純 SQL/程式計算，確定性的。

這也呼應核心迴路的 LLM／規則切分：⑤的判定由 LLM 產生，但**判定結果一旦落地就是結構化資料**，⑥的規則引擎與事後的指標計算都只讀結構化欄位，不再回頭解讀自然語言。

`override_kind` 的用途：人類推翻主 Cheerio 有兩種原因——**它判錯了**，或**我改變主意了**。只有前者代表品質問題；不分開的話，驗收準確率會被「人類改主意的次數」汙染。MVP 階段可以先不填（單一使用者記得住），欄位先留著。

詳見 [[work/designs/w074-self-improvement-loop|W-074 自我改進迴路設計]]。
