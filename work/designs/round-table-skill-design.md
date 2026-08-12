# Round Table — 多 AI 圓桌會議 Skill 設計

> 建立日期：2026-08-09
> 更新：2026-08-09（v2 — subagent 參與者架構）
> 狀態：設計完成，待實作

---

## 1. 概述

**圓桌會議**是一個讓多個 AI 模型針對同一議題進行序列討論的 skill。
Pi Agent 擔任**主持人**（不坐在桌上），派出 subagent 作為一方參與者，
搭配 Gemini 和 Copilot 進行多方討論。使用者可以指定 subagent 的數量和模型。

```
Pi (主持人 — 不參與討論)
  │
  ├── spawns subagent A (model: 使用者指定)
  ├── spawns subagent B (model: 使用者指定)  ← 可選
  │
  │  圓桌上的參與者：
  │  ┌───────────────────────────────────────────────┐
  │  │ Sub A │ Sub B │ ... │ Gemini │ Copilot       │
  │  └───────────────────────────────────────────────┘
  │
  │  Round 1: Pi 決定順序 → 依序發言
  │  Round 2: Pi 根據前一輪決定順序 → 依序發言
  │  Round N: ...直到結束
  │
  └── Pi 摺疊 → 會議紀要
```

---

## 2. 角色

| 角色 | 數量 | 來源 | 模型 | 說明 |
|------|------|------|------|------|
| **主持人** | 1 | Pi Agent | — | 固定。開場、排序、傳遞、摘要、判定結束 |
| **Subagent 參與者** | 0-N | Pi 派出 | 使用者指定 | 每個 subagent 是一個獨立 participant |
| **Gemini** | 1 | agy CLI | agy 預設 | 固定參與者 |
| **Copilot** | 1 | gh copilot | gh copilot 預設 | 固定參與者 |

### 使用者輸入格式

```yaml
topic: "要討論的議題"
context: "背景資料路徑"          # 選填
subagents:                      # 選填，預設 0 個（只有 Gemini + Copilot）
  - model: anthropic/claude-sonnet-4
  - model: gemini-3.1-pro-high
  # 可以加更多...
maxRounds: 3                    # 選填，預設 3
```

**範例：**
- `subagents: []` → 圓桌：Gemini + Copilot（二人對談）
- `subagents: [{model: anthropic/claude-sonnet-4}]` → 圓桌：Claude + Gemini + Copilot（三人）
- `subagents: [{model: claude}, {model: gemini-pro}]` → 圓桌：Claude + Gemini-pro + Gemini + Copilot（四方）

---

## 3. 模型選擇

### Subagent 參與者

使用者在 skill 參數中指定 `model`，Pi 用 `subagent` 工具的 `model` 參數派出：

```javascript
runs.run('player-A', {
  agent: 'worker',
  model: 'anthropic/claude-sonnet-4',  // 使用者指定
  task: '你是圓桌會議的參與者...'
})
```

### Gemini

使用 agy CLI，可透過 `--model` 指定（但預設用 agy 預設即可）。

### Copilot

使用 gh copilot CLI，目前**無法指定模型**（取決於 GitHub Copilot 訂閱）。

---

## 4. 發言順序：Pi 指定

**每輪的順序由 Pi 根據前一輪的發言動態決定。**

### 為什麼不固定順序？

- 固定順序讓後發者 always 有「回應優勢」，不公平
- 動態順序更像真實圓桌討論
- Pi 可以根據「誰還沒被回應過」或「誰的觀點需要被挑戰」來排序

### Pi 排序的依據

1. **平衡發言**：誰最少發言，誰先說
2. **回應缺口**：誰的觀點還沒被回應
3. **辯證張力**：誰的立場跟前一位最不同，適合接續
4. **新觀點催化**：如果所有人都在重複，換一個很少說話的先

### 實作方式

每輪結束後，Pi 分析本輪所有人的發言，產出下一輪的順序：

```
Pi 分析：Subagent A 的觀點沒被 Copilot 回應 → 下一輪 Copilot 先說
Pi 分析：Gemini 提出了新論點但沒人回應 → 下一輪 Subagent A 先回應 Gemini
```

---

## 5. 完整流程

### Phase 0: 準備

1. Pi 讀取 skill 參數
2. Pi 產出**議題書**（Topic Brief）
3. Pi 派出 subagents（每個 subagent 收到：議題書 + 你的角色說明）

### Phase 1: 討論（序列輪轉）

每輪的流程：

```
┌─ Pi 決定本輪順序 ─────────────────────────────┐
│  依據：前一輪的發言分析                          │
│  產出：[Sub A, Gemini, Copilot, Sub B]          │
└─────────────────────────────────────────────────┘
                      │
                      ▼
┌─ 參與者 1 發言 ────────────────────────────────┐
│  Prompt：議題 + 所有前一輪發言                    │
│  執行方式取決於參與者類型：                        │
│    - Subagent: runs.run() with model            │
│    - Gemini: agy CLI                            │
│    - Copilot: gh copilot CLI                    │
│  結果存到 round-N-player-1.md                   │
└─────────────────────────────────────────────────┘
                      │
                      ▼
┌─ 參與者 2 發言 ────────────────────────────────┐
│  Prompt：議題 + 前面所有人的發言（含參與者 1）     │
│  結果存到 round-N-player-2.md                   │
└─────────────────────────────────────────────────┘
                      │
                      ▼
                    ...（依序到最後一位）
```

### Phase 2: 結束判定

| 條件 | 判定方式 | 預設 |
|------|---------|------|
| **最大輪數** | `currentRound >= maxRounds` | `maxRounds: 3` |
| **共識偵測** | Pi 分析是否無新論點 | Pi 主動判斷 |
| **人類介入** | 人類說「停」 | 隨時可觸發 |

### Phase 3: 摺疊（Synthesis）

Pi 產出會議紀要：

```markdown
# 圓桌會議紀要：[議題]
> 日期 | 參與者（含模型）| 輪數

## 共識
1. ...

## 分歧
1. **[議題]** — 各方立場...

## 未解問題
1. ...

## 建議下一步
1. ...

## 完整討論紀錄
- Round 1: [Sub A](...) → [Gemini](...) → [Copilot](...) → [Sub B](...)
- ...
```

---

## 6. 輸出路徑

```
.pi/round-table/
└── 20260809-143000/
    ├── topic-brief.md              # 議題書
    ├── participants.yaml           # 參與者清單（含模型）
    ├── round-1-sub-a.md            # Round 1 Subagent A
    ├── round-1-gemini.md           # Round 1 Gemini
    ├── round-1-copilot.md          # Round 1 Copilot
    ├── round-1-sub-b.md            # Round 1 Subagent B（如有）
    ├── round-2-*.md                # Round 2 ...
    ├── synthesis.md                # 會議紀要
    └── meta.yaml                   # 中繼資料
```

同步到 `work/history/YYYY-MM.md`（摘要 + refs）。

---

## 7. Subagent 實作細節

### 派出 Subagent

```javascript
// Pi 派出 subagent 參與者
const player = await runs.run('player-' + id, {
  agent: 'worker',
  model: specifiedModel,  // 使用者指定的模型
  task: `
    你是圓桌會議的參與者。

    ## 議題
    ${topicBrief}

    ## 目前討論紀錄
    ${allPreviousStatements}

    ## 你的任務
    發表你的觀點。可以同意、反駁、補充前面任何人的發言。
    回覆你的完整觀點（不要只說「同意」，要展開論述）。
  `
})
```

### Subagent 的限制

- Subagent 只能用 Pi 的工具（read、bash、grep 等）
- Subagent 不能直接呼叫 `agy` 或 `gh copilot`（它們是 CLI）
- Subagent 的「觀點」來自它自己的 model，不需要呼叫外部工具

### Gemini / Copilot 的呼叫方式

```bash
# Gemini
agy -p "${prompt}" --output-format stream-json > .pi/round-table/${id}/round-${n}-gemini.log 2>&1

# Copilot
gh copilot -p "${prompt}" --allow-tool 'shell(echo)' > .pi/round-table/${id}/round-${n}-copilot.log 2>&1
```

---

## 8. Prompt 模板

### 議題書 Prompt（Pi → 自己）

```
你是一個圓桌會議主持人。請針對以下議題，產出一份議題書。

議題：{topic}
背景：{context}

議題書格式：
1. 議題定義（一句話）
2. 背景脈絡（3-5 句）
3. 討論目標（要回答什麼？要決定什麼？）
4. 期望產出
```

### 參與者 Prompt（通用）

```
你是圓桌會議的參與者：{participant_name}。

## 議題
{topic_brief}

## 目前討論紀錄
{all_previous_statements}

## 你的任務
發表你的觀點。回應前面所有人的發言，可以同意、反駁、補充或提出新觀點。
請完整展開你的論述。
```

### Pi 排序 Prompt（Pi → 自己）

```
你是圓桌會議主持人。根據上一輪所有人的發言，決定下一輪的發言順序。

上一輪發言：
{round_statements}

排序依據：
1. 平衡發言次數
2. 回應缺口
3. 辯證張力
4. 新觀點催化

請產出下一輪的參與者順序列表。
```

---

## 9. 使用範例

### 範例 1：技術決策（三人）

```
圓桌會議：我们该用 MongoDB 还是 PostgreSQL？

subagents:
  - model: anthropic/claude-sonnet-4

maxRounds: 3
```

桌上：Claude (Sub) + Gemini + Copilot

### 範例 2：架構審查（四方）

```
圓桌會議：目前的 Notion ↔ Obsidian 雙向同步架构有什么问题？

subagents:
  - model: anthropic/claude-sonnet-4
  - model: gemini-3.1-pro-high

maxRounds: 4
```

桌上：Claude (Sub) + Gemini-pro (Sub) + Gemini + Copilot

### 範例 3：純二人對談（無 subagent）

```
圓桌會議：AI Agent 的 self-evolution 有哪些可行方案？

maxRounds: 3
```

桌上：只有 Gemini + Copilot

---

## 10. 與現有 Skill 的關係

| 現有 Skill | 關係 |
|-----------|------|
| **chat-with-copilot** | 圓桌會議呼叫 copilot skill 的 CLI 流程 |
| **chat-with-gemini** | 圓桌會議呼叫 gy skill 的 CLI 流程 |
| **pi-subagents** | 圓桌會議用 runs.run() 派出 subagent 參與者 |
| **workflow-patterns → multi-perspective** | 類似概念但不同。multi-perspective 是 Pi 內部 subagent 並行分析；圓桌是外部 AI 序列討論 |
| **grilling** | 類似壓力測試但只對人類；圓桌是 AI 之間的討論 |

---

## 11. 開放問題

- [ ] Subagent 失敗（model API timeout）時，要跳過繼續還是中止？
- [ ] 需要設定每個人的 timeout 嗎？
- [ ] 如果使用者派出 0 個 subagent（只有 Gemini + Copilot），流程是否簡化？
- [ ] Gemini 和 Copilot 的 prompt 要不要有角色差異（例如 Copilot 偏實作、Gemini 偏分析）？

---

## 12. 實作步驟

1. **建立 SKILL.md** — 觸發詞、參數、流程
2. **建立 prompt 模板** — 議題書、參與者、排序
3. **實作 Phase 0-1** — 派出 subagent + 呼叫 CLI
4. **測試三人圓桌** — 1 個 subagent + Gemini + Copilot
5. **實作 Phase 2-3** — 結束判定 + 摺疊
6. **測試四方圓桌** — 2 個 subagent + Gemini + Copilot
7. **優化 prompt** — 根據測試結果調整
