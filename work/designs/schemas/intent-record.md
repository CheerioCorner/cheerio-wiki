# 意圖紀錄 Schema (IntentRecord)

> W-074 MVP 三份 schema 之一。記錄人類的原始意圖，是主 Cheerio 事後比對的「基準線」。
> 
> 決策依據：W-074 決策 #1（單一對口）、#3（意圖必須寫下來成一級紀錄）

## 欄位定義

```yaml
IntentRecord:
  # === 識別 ===
  intent_id: string          # 格式: "i-YYYYMMDD-NNN"，唯一識別碼
  case_id: string            # 關聯的案件 ID（一案可能有多個意圖）
  
  # === 原始輸入 ===
  raw_input: string          # 人類說的原始文字，原文不改
  input_method: enum         # "text" | "voice" | "image"（未來擴充）
  triggered_by: string       # 觸發來源（例："cheerio:main"、"cheerio:interjection"）
  
  # === 時間 ===
  created_at: ISO-8601       # 意圖建立時間
  updated_at: ISO-8601       # 最後更新時間（人類改口時更新）
  
  # === 翻譯後的意圖 ===
  intent_summary: string     # 主 Cheerio 翻譯的結構化意圖（一句話）
  goal: string               # 明確的目標描述（做什麼、產出什麼）
  constraints: string[]      # 人類提到的限制條件（例："用繁體中文"、"今天內"）
  priority: enum             # "urgent" | "high" | "normal" | "low"
  flexibility: enum          # "strict"（照做）| "advisory"（可以建議更好的方式）
  
  # === 追蹤 ===
  status: enum               # "active" | "superseded" | "withdrawn"
  superseded_by: string?     # 若被新意圖取代，指向新 intent_id
  
  # === 關聯 ===
  related_intents: string[]  # 相關的其他意圖 ID（例：子任務拆分後的關聯）
```

## 設計說明

### 為什麼要有 `raw_input` 和翻譯後的分開存？

決策 #3 要求「意圖必須被寫下來成一級紀錄」。但人類說的話通常是模糊的（「幫我看看那個東西」），主 Cheerio 需要做語意翻譯。分開存是因為：
- `raw_input` 是人類的原話，事後可以回溯「他當時到底說了什麼」
- `goal` 是翻譯後的結構化版本，專家看這個就知道要做什麼
- 如果翻譯有誤，人類可以指出，這時候對比 `raw_input` vs `goal` 就能找出理解落差

### 為什麼 `flexibility` 是意圖層級的？

「可以建議更好的方式」vs「照做就好」會影響專家的行為模式：
- `strict`：專家嚴格執行，不主動建議替代方案
- `advisory`：專家可以提出「我覺得這樣做更好，理由是...」，等人類確認

這跟「插話」機制（決策 #2）是分開的——flexibility 是事前設定，插話是事中介入。

### `priority` 影響什麼？

- `urgent`：可觸發專家分身並行處理
- `high`：主 Cheerio 優先處理
- `normal`：正常排隊
- `low`：可延後或併入其他案子

MVP 只有一個案子一個專家，priority 暫不影響派工邏輯，但欄位先留。
