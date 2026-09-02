# 事件流 Schema (EventStream)

> W-074 MVP 的觀測基礎。記錄整個系統的每一個動作，是「自我成長」的原料。
> 
> 決策依據：W-074 決策 #10（事件流 vs 狀態面分離）、#14（可觀測性是自我成長前提）、#16（model 欄位）

## 欄位定義

```yaml
Event:
  # === 識別（8/22 定的 MUST 欄位）===
  trace_id: string           # 追蹤 ID，同一個案子的所有事件共享
  span_id: string            # 事件 ID，唯一
  parent_span_id: string?    # 上級事件 ID（形成事件樹）
  
  # === 時間 ===
  timestamp: ISO-8601        # 事件發生時間
  
  # === 誰做了什麼 ===
  actor: string              # 觸發者（"human:cheer"、"cheerio:main"、"expert:xxx"）
  event_type: string         # 事件類型（見下方事件類型表）
  
  # === 結果 ===
  status: enum               # "success" | "error" | "timeout"
  latency_ms: integer        # 事件耗時（毫秒）
  error: string?             # 錯誤訊息（status=error 時必填）
  
  # === 8/30 新增：model 資訊（決策 #16）===
  model: string              # 執行此事件時使用的 model（例："qwen-3.8-27b"、"claude-sonnet-5"）
  model_version: string      # model 版本（例："fp8"、"2026-08-26"）
  
  # === 內容 ===
  payload: object?           # 事件特定的額外資料（結構依 event_type 不同）
```

## 事件類型表

| event_type | 觸發時機 | payload 內容 |
|-----------|---------|-------------|
| `intent.created` | 人類輸入意圖 | `{ raw_input, intent_summary }` |
| `intent.superseded` | 意圖被取代 | `{ old_intent_id, new_intent_id }` |
| `contract.created` | 主 Cheerio 產生任務契約 | `{ contract_id, expert_role }` |
| `contract.dispatched` | 任務派給專家 | `{ contract_id, harness_tier }` |
| `expert.started` | 專家開始執行 | `{ expert_id, model }` |
| `expert.progress` | 專家執行中的進度更新 | `{ progress_pct, current_step }` |
| `expert.tool_call` | 專家呼叫工具 | `{ tool_name, input, output, latency_ms }` |
| `expert.completed` | 專家完成任務 | `{ deliverables }` |
| `state.transition` | 狀態轉換 | `{ from_state, to_state, trigger, verdict?, verdict_reason?, verdict_confidence?, human_override, overrides_span_id?, override_kind? }`（★ 9/2 起驗收結果為結構化欄位，見 `state-transition-rules.md`） |
| `review.started` | 主 Cheerio 開始比對 | `{ contract_id }` |
| `review.result` | 主 Cheerio 比對結果 | `{ verdict, verdict_reason, verdict_confidence }`（★ 9/2 欄位名對齊 `state.transition`，原 `match/reason/confidence` 廢止） |
| `case.completed` | 案子完成 | `{ final_status }` |
| `case.escalated` | 上呈人類 | `{ reason }` |

## 設計說明

### 事件流 vs 狀態面（決策 #10）

| 面向 | 事件流 | 狀態面 |
|------|--------|--------|
| 本質 | 流水帳（每一筆動作） | 結論（當前狀態快照） |
| 用途 | 互動模式的即時投影、事後回溯、自我成長分析 | 秘書查進度、主 Cheerio 決策依據 |
| 資料量 | 大（每個 tool call 都記） | 小（只記最新狀態） |
| 更新頻率 | 每個事件即時寫入 | 狀態轉換時更新 |

### 為什麼 `model` 是必填？

決策 #16 的精神：事件流是「一級紀錄」，之後要換 model、要跨 model 比較，沒有這欄位就無法回溯。加一個字串欄位成本幾乎是零，但事後補要動歷史資料格式。

### `trace_id` 的生成規則

- 同一個案子的所有事件共享同一個 `trace_id`
- 格式：跟 `case_id` 相同（`c-YYYYMMDD-NNN`）
- 讓你可以用一個 trace_id 拉出整個案子的完整事件鏈

### `parent_span_id` 形成事件樹

```
trace: c-20260830-001
├── span: e001 (cheerio:main, intent.created)         [root]
├── span: e002 (cheerio:main, contract.created)       [parent: e001]
├── span: e003 (expert:zero, expert.started)           [parent: e002]
│   ├── span: e004 (expert:zero, expert.tool_call)     [parent: e003]
│   ├── span: e005 (expert:zero, expert.tool_call)     [parent: e003]
│   └── span: e006 (expert:zero, expert.completed)     [parent: e003]
├── span: e007 (cheerio:main, review.started)          [parent: e002]
├── span: e008 (cheerio:main, review.result)           [parent: e007]
└── span: e009 (cheerio:main, state.transition)        [parent: e002]
```

這棵樹讓你可以：
- 拉出「一個案子從頭到尾的所有事件」
- 分析「專家執行花了多少時間」（e003 的 duration）
- 分析「主 Cheerio 比對花了多少時間」（e007 的 duration）
- 未來做自我成長分析時，看哪個環節最耗時、最容易出錯
