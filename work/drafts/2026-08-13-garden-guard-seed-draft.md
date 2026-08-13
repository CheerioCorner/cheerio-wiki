# 草稿：花園巡檢事故與 runtime gate 設計（不發布）

> 這是 Notion 知識花園種子的草稿內容，依據 `knowledge-garden-page-content` Phase 0/1 產出。
> **停在 Phase 3 之前**——等 Cheerio 確認後才決定是否種入 Notion。

---

## 這是什麼？

這不是一顆「技術工具」種子，而是一顆「學習案例」種子——記錄「Agent 執行自己的流程時，規則寫得很清楚但還是跳過了」這個模式，以及我們找到的解方。

核心發現：**純文字 SKILL.md 規則不可靠**——LLM agent 有心智捷徑傾向，會把「Properties 填完」誤認為「任務完成」，即使規則明確要求「必須呼叫 `ntn pages update --content`」。解方是分層：二元失誤（做了/沒做）用 runtime extension 確定性 gate 硬擋，品質判斷（寫得好不好）交給 LLM + 人類。

## 客觀事實

| 面向 | 發現 |
|------|------|
| 事故觸發 | 2026-08-13 花園巡檢，5 顆新種子 Notion body content 全空白 |
| 根本原因 | 把「Properties 填完」誤認為「種子種好了」——Notion DB 記錄和頁面 body content 是兩次 API 呼叫 |
| 巡檢失敗原因 | 只查 Database Properties，從未 fetch 任何頁面 body content |
| 圓桌會議共識 | 純文字規則不可靠 → 分層：確定性 gate + LLM 品質判斷 |
| 技術解法 | `tool_call` event 攔截（`beforeToolCall` hook），可在 bash 執行前 block |
| 修正範圍 | 3 個 SKILL.md 段落 + 1 個新 extension（garden-guard.ts）+ 1 篇 wiki source note |

## 我的觀點

### 來源的做法 vs 我的做法

| 面向 | 一般做法 | 我們的做法 |
|------|---------|-----------|
| 品質控制 | 寫更嚴格的規則 | 規則 + runtime gate 分層 |
| Extension 類型 | 工具型（registerTool） | 攔截型（tool_call 事件） |
| 錯誤處理 | fail-closed（擋住） | fail-open + 事後巡檢補防 |
| 版本控制 | loose-file | loose-file → 建議包成 git repo |

### 關鍵洞察

1. **「規則寫得清楚」≠「Agent 會遵循」**——這是 agent 設計的基本認知失調
2. **二元判斷適合用 extension，品質判斷適合用 LLM**——不要把兩種東西混在同一層
3. **Fail-open 是合理的工程權衡**——gate 的目的是防「無意間」不是防「刻意繞過」，擋住合法操作的代價 > 放行後事後補救
4. **Extension 攔截比外部腳本更可靠**——在 runtime 層攔截不需要 Agent「記得」去呼叫

## 下一步

- [ ] garden-guard.ts 從 loose-file 升級為 git repo（✅ 已完成：`CheerioCorner/garden-guard`）
- [ ] 評估是否將此模式泛化為「任何 Agent 流程的 gate 設計」通用概念
- [ ] 建立第三層防線：每週自動巡檢比對 Properties 更新時間 vs body content 字數

## 連結

- 事故報告：[[wiki/sources/2026-08-13-garden-guard-incident|花園巡檢事故全記錄]]
- Extension 安裝：[[wiki/concepts/agent-extension-installation|Agent Extension 安裝位置與方式]]
- 圓桌會議：[[.pi/round-table/20260813-223356/synthesis|圓桌會議紀要]]
