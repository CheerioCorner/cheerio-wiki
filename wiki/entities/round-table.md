---
title: Round Table — 多 AI 圓桌會議
type: entity
created: 2026-08-20
updated: 2026-08-20
sources: 0
tags: [skill, multi-agent, discussion, consensus]
topics: [skill, ai-agent]
canonical: entities/round-table
provenance:
  - kind: project
    path: C:/Users/User/.agents/skills/round-table/SKILL.md
    url: https://github.com/CheerioCorner/cheerio-skills
---

# Round Table — 多 AI 圓桌會議

> Pi 主持的多 AI 討論機制。Pi 不參與討論，派出 subagent 搭配 Claude、Gemini、Copilot 進行序列討論。

## 核心設計

```
Pi (主持人 — 不坐在桌上)
  │
  ├──派出 subagent A ─── model: 使用者指定
  ├──派出 subagent B ─── model: 使用者指定（可選）
  │
  ┌─────────────────────────────────────────────────────┐
  │  Sub A  │  Sub B  │  Claude  │  Gemini  │  Copilot  │
  └─────────────────────────────────────────────────────┘
         每輪由 Pi 決定誰先說
         Round 1 → Round 2 → ... → Round N
         Pi 摺疊 → 會議紀要
```

## 每輪流程（6 步）

| 步驟 | 內容 | 輸出 |
|------|------|------|
| Step 1 | Pi 決定本輪順序 | 排序列表 |
| Step 2 | 依序呼叫每位參與者 | 發言紀錄 |
| Step 3 | 存檔 | `round-N-*.md` |
| Step 4 | 主持人結論 | `round-N-summary.md` |
| Step 5 | 更新論點追蹤表 | `arguments-tracker.md` |
| Step 6 | 檢查結束條件 | 繼續/結束 |

## 主持人結論（每輪）

每輪結束後，Pi 產出結構化摘要：

- **本輪發言摘要**：每位參與者的立場 + 核心論點（表格）
- **本輪新論點**：首次提出的觀點
- **解決的分歧**：本輪達成共識的分歧
- **新增的分歧**：本輪新產生的爭議
- **目前未解決分歧**：所有還在爭論的點
- **共識進度**：已達成 vs 仍待討論

## 論點追蹤表（全程維護）

### 參與者論點歷史

記錄每位參與者在每輪的：
- 輪次、立場（提出/同意/反駁/補充）、論點、回應對象

### 分歧點追蹤

記錄每個分歧點的：
- 提出者、輪次、參與方、狀態（未解決/已解決）

## 量化共識偵測

### 三個指標

| 指標 | 說明 |
|------|------|
| `newArguments` | 本輪新提出的論點數（不重複前輪） |
| `coverageRate` | 本輪回應前輪論點的比例（0-100） |
| `agreementRate` | 同意/補充 vs 反駁/質疑的比例（0-100） |
| `openDisputes` | 未解決的分歧點數量 |

### 結束條件（雙重門檻）

```
共識結束 = 量化條件成立 AND 無未解決分歧
```

**量化觸發條件（滿足任一）：**
1. 連續 2 輪 `newArguments < 2`
2. 連續 2 輪 `coverageRate > 80%` 且 `agreementRate > 70%`
3. 所有參與者都已發言 ≥ 2 次，且最後一輪無新論點

**阻止結束條件（任一成立則強制繼續）：**
- ❌ `openDisputes > 0` 且分歧未收斂（爭議還在）
- ❌ 有新的重要分歧點出現（新增 > 解決）

### 三種結束方式

| 條件 | 判定 |
|------|------|
| `currentRound >= maxRounds` | 強制結束 |
| 量化偵測觸發 且 無未解決分歧 | 共識結束 |
| 人類說「停」 | 介入結束 |

## 輸出結構

```
.pi/round-table/
└── 20260809-143000/
    ├── topic-brief.md              # 議題書
    ├── participants.yaml           # 參與者清單
    ├── arguments-tracker.md        # 論點追蹤表（全程更新）
    ├── round-1-player-a.md         # Round 1 Subagent 發言
    ├── round-1-claude.md           # Round 1 Claude 發言
    ├── round-1-gemini.md           # Round 1 Gemini 發言
    ├── round-1-copilot.md          # Round 1 Copilot 發言
    ├── round-1-summary.md          # Round 1 主持人結論
    ├── round-2-*.md                # Round 2 ...
    ├── round-2-summary.md          # Round 2 主持人結論
    ├── synthesis.md                # 最終會議紀要
    └── meta.yaml                   # 中繼資料
```

## 參與者類型

| 參與者 | 工具 | 模型指定 | 備註 |
|--------|------|----------|------|
| Subagent | `runs.run()` | 使用者指定 | 持久化，可 resume |
| Claude | `claude -p` | `--model` 可選 | Claude Code CLI |
| Gemini | `agy -p` | `--model` 可選 | Antigravity CLI |
| Copilot | `gh copilot -p` | 不支援 | GitHub Copilot CLI |

## Prompt 模板

- **議題書**：Pi 自己產出 topic brief
- **Subagent 開場**：獨立思考者角度
- **後續輪**：回應前面所有人
- **Claude**：深度分析和全面性角度
- **Gemini**：完整展開論述
- **Copilot**：實作面和工程角度
- **Pi 排序**：平衡發言、回應缺口、辯證張力
- **Pi 本輪結論**：結構化 JSON 輸出
- **Pi 論點追蹤更新**：更新追蹤表
- **Pi 共識偵測**：量化分析 + 判斷

## 關聯

- [[wiki/topics/skill|Skill Topic]]
- [[wiki/entities/hermes-agent|hermes-agent]] — learning loop + skills hub
- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — Skill 開放標準
