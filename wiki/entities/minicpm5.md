---
title: "MiniCPM5 — OpenBMB 的 On-Device Cognitive Core 系列模型"
type: entity
created: 2026-09-12
updated: 2026-09-12
sources: 2
tags: [llm, minicpm5, on-device, agentic, sub-agent, openbmb, tool-calling]
topics: [agent-architecture, agent-infrastructure]
canonical: entities/minicpm5
provenance:
  - kind: raw
    path: raw/youtube/mincpm-v2-intro.md
    url: https://www.youtube.com/watch?v=ox1mW2N9Z_Y
  - kind: raw
    path: raw/youtube/mincpm-v2-benchmark.md
    url: https://www.youtube.com/watch?v=CXvncR7v66o
---

# MiniCPM5

> OpenBMB（清華大學 NLP Lab）推出的輕量級 on-device agentic 模型系列，包含 1B 與 2.5B（標稱 2B）兩種規格。設計貫徹 Karpathy 的 cognitive core 哲學：剝除百科知識、聚焦推理與工具調用。

## 基本規格

| 屬性 | 1B 版 | 2B 版（實際 2.5B） |
|------|-------|-------------------|
| 參數量 | 1B dense | 2.5B dense |
| 架構 | Llama style | Llama style |
| Context Window | 128K | 128K |
| 授權 | Apache 2.0 | Apache 2.0 |
| 訓練資料 | 公開 | 公開 |

## 訓練方法

### 1B 版

三個版本層級：
1. **Base model**：純預訓練，使用 ultra-fine web dataset 與 math pre-training data，資料完全公開 `[05:09]`
2. **SFT**：400B tokens（200B deep-thinking + 200B hybrid）`[05:39]`
3. **Full version**：SFT + RL + on-policy distillation `[06:10]`

### 2B 版升級

- **SFT**：改為 400B tokens 全 deep-thinking（取消 hybrid）`[01:15]`
- **RL2**：Critic-based RL，取代 GRPO，宣稱更穩定 `[01:48]`
- **多 Domain RL**：reasoning / general / agentic 各自專門訓練 `[02:16]`
- **On-policy Distillation**：模型自己 sample trajectories 訓練 `[03:14]`

## Benchmark 表現

### 1B 版

- **Token 效率**：比 Qwen 3.52B reasoning 少用 31 倍 tokens `[07:47]`
- **AA Omniscience**：得分 -1（近乎零幻覺）`[08:10]`
- 在同量級模型中表現突出

### 2B 版

| Benchmark | MiniCPM5-2B | Qwen3.5-4B | 勝出 |
|-----------|:-----------:|:----------:|:----:|
| SWE-Bench Verified | 領先 | — | MiniCPM5 |
| GPQA diamond | 落後 | 較高 | Qwen |
| SWE-Bench Pro | 較低 | ~2 倍 | Qwen |

`[01:00]` `[03:51]` `[06:30]`

GPQA diamond 雖落後 Qwen，但遠超其他 2B 模型 `[03:51]`。

## 工具調用能力

### 1B 版實測

- 單工具呼叫：完全正確 `[14:54]`
- 多步推理：表現良好 `[15:54]`
- 長任務：不穩定，第二個長任務容易失敗 `[16:15]`

### 2B 版實測（Agentech）

- 基礎測試：全過 `[09:29]`
- 進階測試（planning、prompt injection、failure retry）：8/8（80% 通過率）`[10:53]`
- 多工具並行：快速且準確 `[11:58]`
- **核心特性**：「Defer to Tools」——不嘗試用自己的知識回答，主動委派給工具 `[12:32]`

### 推論速度

- 2B 版 GGUF 4-bit：RTX Pro 6000 上 400+ tokens/sec `[07:32]`

## 已知限制

- **長 CoT 容易 loop**：常因 token 限制陷入思考迴圈 `[17:24]`
- **指令遵循不夠精確**：系統提示與命名指令常被忽略 `[13:59]`
- **內容生成偏弱**：無法產出長文、SVG/HTML 品質基礎 `[11:53]`
- **知識類問題不強**：GPQA 等知識 benchmark 落後 Qwen `[06:30]`

## On-Device 應用

- 可運行在手機、瀏覽器、小型 CPU 應用上 `[04:35]`
- MiniCPM desk pet（Electron app）展示 `[10:18]`
- 已有 Rust 實作的 edge home harness `[09:15]`

## 與 Spark-X2.5-4B 的對照

| 維度 | MiniCPM5（1-2.5B） | Spark-X2.5-4B（4.1B） |
|------|-------------------|----------------------|
| 量級 | 1-2.5B dense | 4.1B dense |
| 設計路線 | 瘦身＋專注工具調用、defer to tools | Hybrid attention + 1M context + 全面 benchmark 領先 |
| Agent 能力 | 工具委派成功率高、token 效率極佳 | τ³-bench、MCP-Atlas 等 agent benchmark 領先 |
| Code 能力 | 偏弱 | SWE-Bench Pro 44.4 |
| 適合場景 | Sub-agent、on-device、IoT | 更複雜的 agent 任務、長 context |

兩者代表 agentic 小模型的兩種不同設計哲學：MiniCPM5 走「減法」路線（犧牲知識儲備、專注工具調用），Spark-X2.5 走「全面」路線（hybrid attention、1M context、多領域領先）。非直接競品（量級差 2-4 倍）。

## 相關頁面

- [[wiki/sources/2026-09-12-minicpm5-1b-cognitive-core-review|來源筆記：1B 介紹]]
- [[wiki/sources/2026-09-12-minicpm5-2b-sub-agent-review|來源筆記：2B 評測]]
- [[wiki/concepts/cognitive-core|Cognitive Core]] — Karpathy 提出的「小模型應是認知核心」概念
- [[wiki/entities/spark-x25-4b|Spark-X2.5-4B]] — 同為 agent 導向小模型，但走不同路線
- [[wiki/concepts/local-llm-deployment|Local LLM 部署]] — 地端模型部署策略
- [[wiki/concepts/sub-agents-pattern|Sub-agents]] — 子 agent 委派模式
