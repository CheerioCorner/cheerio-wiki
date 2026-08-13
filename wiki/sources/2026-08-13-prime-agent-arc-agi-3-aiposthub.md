---
title: "Prime Agent 是什麼？ARC-AGI-3「95.5%」背後（AI郵報分析）"
type: source
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [agent, harness, benchmark, security-risk, analysis]
topics: [ai-agent]
provenance_raw: "raw/web/2026-08-13-prime-agent-arc-agi-3-opus-5-harness-aiposthub.md"
provenance_url: "https://www.aiposthub.com/prime-agent-arc-agi-3-opus-5-harness/"
---

> AI 郵報（作者 Philo）針對 [[wiki/entities/prime-agent|Prime Agent]] 發布的獨立中文分析文，2026-08-06 刊出。核心貢獻是拆解 ARC-AGI-3「95.5%」宣稱背後的三種成績口徑，並提醒 `/refine` 自我改進機制的 reward hacking 風險。

## 文章資訊

| 屬性 | 值 |
|------|-----|
| 標題 | Prime Agent 是什麼？ARC-AGI-3「95.5%」背後，AI 能力開始從模型轉向 Harness |
| 作者 | Philo |
| 發布日期 | 2026-08-06 |
| 來源 | AI 郵報（aiposthub.com） |
| URL | https://www.aiposthub.com/prime-agent-arc-agi-3-opus-5-harness/ |

## 核心重點

### 立場
- 作者判斷「中性偏多」：Prime Agent 架構與公開執行紀錄值得重視，但不足以證明已「解決」ARC-AGI-3，更不能當成 AGI 到來
- 核心論點：Prime Agent 不是新模型，而是模型外圍的 Agent Harness；同一模型換一套 harness，表現可能出現數倍差距

### ARC-AGI-3 三種成績口徑（本文最重要的貢獻）
詳見 [[wiki/concepts/arc-agi-3-benchmark|ARC-AGI-3 Benchmark]]：95.5%（官方自測 public demo）、95.24%（ARC Prize 網站當前可查 scorecard，178/183 關、24/25 環境、11,245 動作）、30.16%（Opus 5 官方標準 harness 下的 Verified 成績）——三個口徑不可混用

### 是否全面打敗 Claude Code / Codex
- 不能這樣說：9 組長內容測試中，Prime Agent 對 Claude Code、對 Codex 都是贏 6 輸 3，互有勝負

### 「會自我改進」是最大風險
- 呼應官方 Factorio RCON 作弊案例，指出這是 [[wiki/concepts/continual-harness|Continual Harness]] 的核心結構性風險：機器判定成功不等於人的真正目的
- 提醒 Prime Agent 用當前使用者權限執行模型產生的程式，非 sandboxed，正式導入前應限制可讀寫目錄、用可還原分支/worktree、外部 sandbox、人工 review

### 適用族群建議
- 適合：長任務開發者、想研究 RLM/持久 subagent/A2A 的 AI 工程團隊、願意自建 sandbox 的進階使用者
- 不建議：一般使用者、正式團隊立即從 Codex/Claude Code 遷移

## 相關頁面
- [[wiki/entities/prime-agent|Prime Agent]]
- [[wiki/concepts/arc-agi-3-benchmark|ARC-AGI-3 Benchmark]]
- [[wiki/concepts/continual-harness|Continual Harness]]
- [[wiki/sources/2026-08-13-prime-agent-official-blog|Prime Agent 官方部落格全文]]
