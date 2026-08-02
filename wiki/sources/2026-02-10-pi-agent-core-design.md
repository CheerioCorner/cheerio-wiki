---
title: 下一代Agent架构——Pi Agent Core 设计逻辑深度解析
type: source
created: 2026-07-11
updated: 2026-07-11
sources: 1
tags:
  - pi
  - agent
  - coding-agent
  - zechner
  - source-zh
collection: sources
provenance_raw: obsidian://open?vault=Obsidian&file=raw%2Fweb%2F2026-02-10-pi-agent-core-design
provenance_url: https://zhuanlan.zhihu.com/p/2004665077618458930
---

一篇知乎文章,對 `badlogic/pi-mono/packages/agent`(又稱 **Pi Agent Core**)做程式碼級解剖。發布於 2026-02-10,作者署為「王鹏LLM」,286 人贊同。

> 這裡做為來源頁收存。目的是:反映從這篇文章推導出的主張、以及被這篇文章影響、我們接著在 wiki 內拆解的 entity / concept / comparison 頁。

## URL 與原始檔
- 原文:[zhuanlan.zhihu.com/p/2004665077618458930](https://zhuanlan.zhihu.com/p/2004665077618458930)
- 本知識庫內的 clean markdown:`raw/[[../../raw/web/[[../../raw/web/[[../../raw/web/[[../../raw/web/[[../../raw/web/[[../../raw/web/[[../../raw/web/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]]|2026-02-10-pi-agent-core-design]]|2026-02-10-pi-agent-core-design]]|2026-02-10-pi-agent-core-design]]|2026-02-10-pi-agent-core-design]]|2026-02-10-pi-agent-core-design]]|2026-02-10-pi-agent-core-design]].md`(由 Obsidian Web Clipper 收下後搬入)。
- 圖片(原表示架構圖、迴圈圖、事件生命週期、Proxy stream 對比圖):目前仍以 Zhihu 圖床 URL 形式存在 markdown 內,**未下載**——若 lint 需要可逐批拉。

## 作者核心主張(一句話)
> 「自主 agent 就是 LLM + tools + 一個迴圈。」(Mario Zechner)
>
> 並且:前沿模型經 RL 已足夠理解「coding agent 是什麼」,**不需要 10000-token 系統提示詞、不需要堆疊工具** — Pi 的系統提示 + 工具定義加起來 **< 1000 token**、只有 **4 個內建工具**(read / write / edit / bash)。
>
> Pi 在 Terminal-Bench 2.0 上以 Claude Opus 4.5 進入排行榜前列,與 Codex、Cursor、Windsurf 等擁有複雜工具鏈的 Agent 競爭並未落下風。

## 文章結構(7 章)
1. **反直覺立場**:跟主流「加法」反向
2. **5 檔架構**:`pi-agent-core` 全套 1,500 行 / 5 個檔
3. **型別系統**(types.ts):
   - `AgentMessage` = LLM Message ∪ 自訂 Message(Declaration Merging)
   - **最晚轉換**(late conversion):只在邊界過濾
   - `AgentLoopConfig`:可插拔行為注入
   - `AgentEvent`:Agent / Turn / Message-Tool 三層 11 種事件
4. **核心迴圈**(agent-loop.ts):
   - 兩個入口:`agentLoop`(新訊息) vs `agentLoopContinue`(重試)
   - 雙層 while:`外層由 FollowUp 驅動`,`內層由 ToolCall + Steering 驅動`
   - Steering 中斷細節:剩餘工具全部記為「Skipped due to queued user message」,讓 LLM 下一輪看得懂
   - 流式應答:partial message **就地更新** context,Ui 同步看見
5. **Agent 類**(agent.ts):
   - steering mode:`"all"` vs `"one-at-a-time"`(為什麼需要 one-at-a-time?)
   - API 五件:`prompt` / `continue` / `steer` / `followUp` / `abort`
   - 錯誤恢復:`catch` 內組裝完整 `AssistantMessage` 帶 `stopReason: "error"`,讓 `continue()` 從錯誤重試
6. **Proxy Stream**(proxy.ts):瀏覽器 ↔ 代理伺服器之間的 **delta-only 重建**,不傳 `partial`
7. **設計哲學**:明確不做清單(Plan Mode / MCP / Sub-Agent / maxSteps / 權限檢查),以及五項核心設計原則(極簡 / 可觀測 / 可介入 / 最晚轉換 / 自我進化)

## 五項核心設計原則(原圖列表)
- **極簡主義**: < 1000 token 系統提示 / 4 個核心工具 / 5 個源檔
- **可觀測性**: 三層事件生命週期 / 工具執行可見
- **可干預性**: Steering 中斷 / FollowUp 排隊 / Abort 取消
- **最晚轉換**: 內部用 AgentMessage,LLM 邊界轉換 / 自訂訊息不洩漏
- **自我進化**: 透過 bash 自我呼叫 / 執行時動態擴充 / 不依賴內建 Skills

## 給開發者的「何時借鏡」表(原文 7.3)
- ✅ 高可觀測性 → 三層事件
- ✅ coding/CLI agent → 4 工具 + bash 極簡
- ✅ 跨 provider 會話遷移 → `convertToLlm` 最晚轉換
- ✅ 使用者中途介入 → Steering/FollowUp 雙 queue
- ❌ 複雜的多 agent 編排 → 相悖
- ❌ 嚴格安全沙箱 → 明確選了 YOLO

## 推導出的 wiki 頁(由這篇衍生)
- Entities:[[wiki/entities/pi-agent-core]]、[[wiki/entities/mario-zechner]]
- Concepts: `steering-followup`、`minimal-agent-loop`、`observability-layer`、`yolo-by-default`（均為尚未建立的 future concepts）
- Comparisons: `coding-agent-comparison`（Claude Code / Codex / Pi 五維表，尚未建立）

## 相關頁面
- [[wiki/sources/2026-05-02-pi-mono-framework-tw]] — 同一作者族、但從整個 monorepo 角度看的姊妹篇
- [[wiki/entities/pi-mono]]
- [[wiki/concepts/minimal-agent-philosophy]]
