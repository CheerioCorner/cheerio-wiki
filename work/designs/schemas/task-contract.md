# 任務契約 Schema (TaskContract)

> W-074 MVP 三份 schema 之二。主 Cheerio 根據意圖紀錄產生，派給專家執行。
> 
> 決策依據：W-074 決策 #4（專家角色定義）、#9（四態）、#12（Tier 分級）、#15（調用/觀測契約分離）、#16（model 欄位）、#17（execution_constraint）
> 
> **關鍵決策（8/30）**：驗收標準採混合式——自由文字（LLM 語意比對）+ 規則引擎判定狀態轉換

## 欄位定義

```yaml
TaskContract:
  # === 識別 ===
  contract_id: string        # 格式: "tc-YYYYMMDD-NNN"
  intent_id: string          # 關聯的意圖 ID（必填，一對一）
  case_id: string            # 關聯的案件 ID
  
  # === 專家指派 ===
  expert_role: string        # 專家角色名稱（從註冊表查）
  harness_tier: enum         # "A" | "B" | "C"（可觀測性等級，從角色綁定查）
  model_used: string?        # 預計使用的 model（MVP 階段可留空，由 harness 決定）
  
  # === 任務內容 ===
  task_description: string   # 主 Cheerio 翻譯的具體任務描述
  assumptions: string[]      # 主 Cheerio 依據的假設（例："假設資料來源是 raw/"）
  rejected_alternatives:     # ★ 9/2 新增：被否決的替代方案（對治 context decay）
    - option: string         #   考慮過但沒選的做法
      why_rejected: string   #   為什麼放棄
      consequence: string?   #   放棄後預期的後果（選填）
  deliverables: string[]     # 預期產出物（例："一份 markdown 報告"、"更新 wiki 頁面"）
  deliverable_format: enum   # "text" | "file" | "structured"（結構化資料如 JSON）
  
  # === 驗收（混合式，8/30 拍板）===
  acceptance_criteria: string # 自由文字描述的驗收標準
                              # 例："報告需涵蓋三個章節，每章至少 500 字，引用至少 3 個來源"
                              # 主 Cheerio 在步驟⑤用 LLM 做語意比對
                              # ⚠ 不是 pass/fail 條列，而是語意級的判斷基準
  
  # === 安全與環境 ===
  observable_tier: enum      # "A"（自建，全過程可觀測）| "B"（部分可觀測）| "C"（黑盒）
  execution_constraint: string? # 環境風險限制（MVP 先留空/TBD）
                                # 未來可填："local_only"（只能地端）| "no_network"（斷網）
                                # 對應 wiki/concepts/agent-security-levels 的 L1-L7 分級
  
  # === 資源 ===
  deadline: ISO-8601?        # 截止時間（可選）
  budget_tokens: integer?    # token 預算上限（可選）
  budget_cost_usd: float?    # 成本預算上限（可選）
  
  # === 狀態追蹤 ===
  status: enum               # "pending" | "dispatched" | "in_progress" 
                              # | "awaiting_review" | "completed" | "rejected"
  dispatched_at: ISO-8601?   # 派工時間
  completed_at: ISO-8601?    # 完成時間
  reviewer_decision: enum?   # "approved" | "rejected" | "escalated"
  rejection_reason: string?  # 退回原因（人類或主 Cheerio 填）
```

## 設計說明

### `acceptance_criteria` 為什麼是自由文字？

8/30 決策：混合式驗收。理由：
- 人類說的話本來就模糊（「做一份專業的報告」），拆成 pass/fail 條列會遺漏真實意圖
- 主 Cheerio 在步驟⑤做 LLM 語意比對（「你要的」vs「做出來的」），自由文字更適合
- 步驟⑥的狀態轉換（通過/退回/上呈）仍然是規則引擎判定，不讓 LLM 自由發揮

### `rejected_alternatives` 為什麼要有（9/2 新增，Cheer 拍板）

決策依據：[[wiki/concepts/context-engineering|Context Engineering]] 的 ③ Handoff 對策族 ＋ [[wiki/concepts/context-decay|Context Decay]]。

派工是一次**跨 harness 交接**：主 Cheerio 決定「派給誰、怎麼做」的推論過程，只有結果會進契約，理由留在原本那次對話裡。這正是 context decay 的定義——「做了什麼」傳下去了，「為什麼」沒有。後果是：

- 同一類任務下次派工時，已經被驗證行不通的做法會被重新提一次
- 專家在執行中遇到岔路，無從知道某條路早就被排除過
- 退回重做時，主 Cheerio 自己也想不起當初為何不選 B 方案

**Tier C 黑盒專家的風險最高**：它只拿得到契約文字，拿不到任何脈絡；而 MVP 的零號專家正是 Tier C。

實作上這欄可以留空陣列（多數簡單任務本來就沒有被否決的方案），只有主 Cheerio 真的在多個做法之間做過選擇時才填。成本是一個陣列欄位，但事後補要動歷史資料格式——跟決策 #16 加 `model` 欄位是同一個理由。

### `observable_tier` vs `execution_constraint` 是正交的

決策 #17 的核心：
- `observable_tier`（Tier A/B/C）管「看不看得到專家做事的過程」→ 可觀測性維度
- `execution_constraint` 管「在哪種環境跑、能碰什麼」→ 環境風險維度
- 兩者可以自由組合：Tier A + local_only（最透明最安全）、Tier C + cloud（最不透明最危險）

MVP 只有 Tier C 零號專家 + 單一環境，`execution_constraint` 先留空。

### `harness_tier` vs `observable_tier` 的差別

- `harness_tier`：從專家角色註冊表查，是角色的固有屬性（例：零號專家 = C）
- `observable_tier`：從任務契約指定，是這個具體任務的要求（例：某任務要求 Tier A 才能接）
- 契約的 `observable_tier` 必須 ≤ 專家的 `harness_tier`（不能要求 Tier C 專家做需要 Tier A 觀測的任務）

### `model_used` 為什麼是 nullable？

MVP 只有一位專家、一個 model，不需要在契約中指定。但欄位先留：
- 未來多 model 時，主 Cheerio 可以在契約中指定「這個任務要用 X model」
- 事件流的 `model` 欄位（決策 #16）會記錄實際使用的 model，不一定要跟契約一致
