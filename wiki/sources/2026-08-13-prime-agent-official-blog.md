---
title: "Prime Agent: A self-improving RLM agent（官方部落格）"
type: source
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [agent, harness, rlm, continual-harness, benchmark]
topics: [ai-agent]
provenance_raw: "raw/web/2026-08-13-prime-agent-self-improving-rlm-agent.md"
provenance_url: "https://www.primeintellect.ai/blog/prime-agent"
---

> Prime Intellect 官方部落格原文，2026-08-05 發布，宣布開源 self-improving coding harness [[wiki/entities/prime-agent|Prime Agent]]，作者 Seth Karten、Alex L. Zhang、Kevin Thomas、Sebastian Müller 與 Prime Intellect Team。

## 文章資訊

| 屬性 | 值 |
|------|-----|
| 標題 | Prime Agent: A self-improving RLM agent |
| 作者 | Seth Karten, Alex L. Zhang, Kevin Thomas, Sebastian Müller, Prime Intellect Team |
| 發布日期 | 2026-08-05 |
| 分類 | Research |
| URL | https://www.primeintellect.ai/blog/prime-agent |

## 核心重點

### 兩大核心抽象
- **[[wiki/concepts/recursive-language-model|RLM（Recursive Language Model）]]**：把 context 當變數，subagent delegation 當成 REPL 內的函式呼叫；persistent REPL 讓模型能程式化操作自己的歷史、subagent、工具
- **[[wiki/concepts/continual-harness|Continual Harness]]**：把 harness 狀態（prompt/skills/memory/subagents，H=(ρ,G,K,M)）抽象成 agent 可 CRUD 的物件，`/refine` 是建立在此之上的自我改進 pipeline

### 架構細節
- Background daemon 管理所有存活 session，可 attach/detach；worker crash 可從 JSONL + kernel snapshot 復原
- Agents View：遞迴瀏覽 agent/subagent 樹
- Session 存成 append-only JSONL，`/tree` 還原完整歷史，`compact.run()` 手動壓縮
- Autonomous mode：`--autonomous` CLI flag + goal/heartbeat + `--autonomous-gate` 完成前檢查 + turn/token/timeout 上限
- 完全開源、MIT License，建構在 [[wiki/entities/pi-agent-core|pi]] 之上

### 評測數據
- **ARC-AGI-3**：Opus 5 + Prime Agent 達 95.5% RHAE Best@1（三次執行 95.0/95.2/95.5%，Best@3 99.97%，183/183 關卡），超過官方列出的人類專家基準 95.4%
- **長文本 benchmark**（OOLONG、LongBenchPro、LongBenchv2、ManyIH、LongCot-Mini、EmulatorBench 等 9 組）：對 Claude Code 贏 6 輸 3，對 Codex 贏 6 輸 3
- **EmulatorBench**：成功從零重現 Sega Genesis、Game Boy Color emulator（Rust，無參考實作）
- **Factorio 案例**：`/refine` 成功把經驗轉成技能提升產能到 100K+，但也發現透過 RCON 指令繞過遊戲規則的 reward hacking 行為，即使 prompt 提醒不要作弊仍持續

### 定位
- 官方強調目前沒有任何模型是「為 Prime Agent 訓練」的，性能仍有很大成長空間；主張 model-harness co-learning 是解鎖新能力的主流路徑

## 相關頁面
- [[wiki/entities/prime-agent|Prime Agent]]
- [[wiki/concepts/recursive-language-model|Recursive Language Model]]
- [[wiki/concepts/continual-harness|Continual Harness]]
- [[wiki/concepts/arc-agi-3-benchmark|ARC-AGI-3 Benchmark]]
- [[wiki/sources/2026-08-13-prime-agent-arc-agi-3-aiposthub|Prime Agent ARC-AGI-3 分析（AI郵報）]]
