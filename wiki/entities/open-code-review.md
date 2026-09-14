---
title: OpenCodeReview
type: entity
created: 2026-08-20
updated: 2026-09-14
sources: 4
tags: [code-review, ai-agent, cli-tool, alibaba, open-source, benchmark, security]
collection: entities
topics: [ai-development-tools, extension-dev]
canonical: entities/open-code-review
---

# OpenCodeReview (OCR)

> 阿里巴巴開源的 AI 代碼審查 CLI 工具，24.5k+ stars（2026-09-14 查證，原 19.3k 為 8/20 快照），確定性工程 × Agent 混合架構。

## 一句話

OCR 是一個 AI 驅動的代碼審查工具，讀取 Git diff，透過帶 tool-use 能力的 Agent 將變更檔案送給 LLM，生成行級精度的結構化審查意見。

## 基本資訊

| 項目 | 內容 |
|------|------|
| 來源 | https://github.com/alibaba/open-code-review |
| 官網 | https://open-codereview.ai |
| Stars | 24.5k+（2026-09-14 查證） |
| Forks | 1.3k |
| License | Apache-2.0 |
| 語言 | Go + TypeScript |
| 安裝 | `npm install -g @alibaba-group/open-code-review` |
| 命令 | `ocr review` / `ocr scan` / `ocr delegate` |

## 核心設計：確定性工程 × Agent 混合

這是 OCR 最重要的設計理念，也是它爆紅的主因。

### 確定性工程（強約束）

對「不能出錯」的環節，用工程邏輯而非 LLM：

1. **精準檔案篩選** — 明確哪些檔案需要審查、哪些過濾
2. **智慧檔案打包** — 關聯檔案歸併為同一審查單元（如 `message_en.properties` + `message_zh.properties`）
3. **精細化規則匹配** — 依檔案特性匹配審查規則，從源頭減少噪音
4. **外掛定位與反思** — 獨立的評論定位和反思模組，提升準確性

### Agent（動態決策）

將 Agent 優勢用在真正擅長的地方：

1. **場景化提示詞** — 針對 code review 深度優化
2. **場景化工具集** — 從大量線上數據分析出最佳工具組合

## 為什麼爆紅？

### 1. 解決真實痛點

通用 Agent（如 Claude Code + Skills）做 code review 的問題：
- **覆蓋不全** — 大型 changeset 時 agent 會「偷懶」，選擇性審查
- **位置漂移** — 報告的問題與實際程式碼位置對不上
- **效果不穩定** — 自然語言驅動的 Skills 難以除錯，品質波動大

### 2. 阿里巴巴內部驗證

- 內部官方 AI 代碼審查助手
- 服務數萬開發者
- 識別數百萬個代碼缺陷
- 經過大規模充分驗證

### 3. Benchmark 數據說服力

```
相同模型下 vs Claude Code：
- Precision：顯著更高（誤報更少）
- F1：顯著更高（綜合品質更好）
- Token 消耗：約 1/9（成本大幅降低）
- 速度：更快

代價：Recall 較低（以精準度換低噪音）
```

基於 50 個開源倉庫、200 個真實 PR、10 種語言、80+ 資深工程師驗證。

### 4. 多平台支援

支援 Claude Code、Codex、Cursor、OpenCode 等主流 coding agent，還有 Delegation Mode 讓 agent 自己審查。

### 5. 開源 + Apache 2.0

可以自由 fork 和自定義。

## 使用方式

### 快速開始

```bash
# 安裝
npm install -g @alibaba-group/open-code-review

# 設定 LLM
ocr config provider
ocr config model

# 審查工作區變更
ocr review

# 審查分支差異
ocr review --from main --to feature-branch

# 審查單一 commit
ocr review --commit abc123

# 全檔案掃描（不需要 git history）
ocr scan
ocr scan --path src/
```

### 整合到 Coding Agent

```bash
# Claude Code plugin
# Codex skill
# Cursor skill
# Delegation Mode（不需要 OCR 自己的 LLM）
ocr delegate preview
```

### CI/CD 整合

支援 GitHub Actions、GitLab CI、Gerrit。

## Benchmark 數據

| 指標 | OCR | Claude Code | 說明 |
|------|-----|-------------|------|
| Precision | ⬆️ 高 | 較低 | OCR 誤報更少 |
| Recall | 較低 | ⬆️ 高 | Claude Code 找到更多問題 |
| F1 | ⬆️ 高 | 較低 | OCR 綜合品質更好 |
| Token | ~1/9 | 基準 | OCR 成本大幅降低 |
| 時間 | ⬆️ 快 | 較慢 | OCR 更快 |

**設計取捨**：以精準度換低噪音（少誤報 > 多找問題）

## 與我們現有工具的比較

### 我們有什麼

| 工具 | 用途 | 位置 |
|------|------|------|
| `code-review` skill | 雙軸審查（Standards + Spec） | `~/.agents/skills/code-review/` |
| `plannotator-review` | 瀏覽器 UI 標註審查 | Pi extension |
| `plannotator` | 視覺化 plan/code/HTML review | 多平台支援 |

### 詳細比較

| 面向 | OpenCodeReview | code-review skill | Plannotator |
|------|----------------|-------------------|-------------|
| **定位** | 專業 code review CLI | 雙軸 review skill | 視覺化標註工具 |
| **架構** | 確定性工程 + Agent | 純 Agent（parallel sub-agents） | 瀏覽器 UI + Event API |
| **輸出格式** | 行級結構化評論 | 兩份獨立報告 | 視覺化標註 |
| **覆蓋範圍** | 全 diff + 全檔案掃描 | diff only | diff / plan / HTML |
| **規則系統** | 精細化規則匹配 | Fowler code smells | 無（自由標註） |
| **Benchmark** | 有（50 repos, 200 PRs） | 無 | 無 |
| **Token 效率** | ⭐⭐⭐（~1/9） | ⭐（parallel agents 較耗） | ⭐⭐（UI 本身不耗 token） |
| **人類介入** | CLI 輸出 | Agent 報告 | 瀏覽器標註 + feedback |
| **CI/CD** | ✅ GitHub Actions/GitLab | ❌ 需手動 | ❌ 需手動 |
| **安裝** | npm global | Pi skill | Pi extension |

### 核心差異

#### 1. 架構哲學不同

```
OpenCodeReview：工程約束 + Agent 決策
  → 確定性保證覆蓋率和位置準確性
  → Agent 只負責需要判斷的部分

code-review skill：純 Agent 驅動
  → 兩個 parallel sub-agents 分別審查
  → 靈活但依賴 LLM 品質

Plannotator：人類主導的視覺化審查
  → Agent 準備內容，人類標註 feedback
  → 強調人機協作
```

#### 2. 使用場景不同

```
OpenCodeReview：
  → CI/CD 自動審查
  → PR review 自動化
  → 大型 changeset 的系統性審查
  → 團隊代碼品質管控

code-review skill：
  → 開發過程中的即時 review
  → 需要同時考量 Standards 和 Spec
  → 個人開發的工作流

Plannotator：
  → 需要人類判斷的審查
  → Plan review（方案審查）
  → 需要標註 feedback 給 agent
  → 視覺化溝通
```

#### 3. 互補性

```
它們不是替代關係，而是互補：

1. OCR 做自動化初審（快速、低成本、高精準）
2. code-review skill 做深度雙軸審查（Standards + Spec）
3. Plannotator 做人類標註和 feedback（需要人類判斷時）
```

## 兩種執行模式：誰付 LLM 的錢（2026-09-14 補充）

必須先設定 LLM provider/model 才能審查，**除非**用 Delegation Mode。

| 模式 | 誰呼叫 LLM | 成本 |
|------|-----------|------|
| 預設（`ocr review`） | OCR 自己 | 需 `ocr config provider` 設 API key（Anthropic/OpenAI/Gemini/DeepSeek/Bedrock/多家中國區廠商/自訂 OpenAI 相容端點含本機模型），每次審查是獨立 LLM 費用 |
| Delegation Mode（`ocr delegate preview` + `ocr delegate rule`） | 你既有的 coding agent（Claude Code/Codex/Cursor 等） | 不需設定 LLM，吃既有訂閱額度，零額外成本 |

Delegation Mode 下 OCR 只做確定性的檔案篩選與規則解析，實際審查判斷交給主 agent 自己的 LLM。

## 資安

`ASSURANCE_CASE.md`（廠商自評，非第三方稽核，但精確到檔名/行號）用 OWASP Top 10/CWE Top 25 逐條檢核：對外只呼叫 `git`（硬編碼子命令，不經 shell，防 command injection）、API key 只讀環境變數不落 log、`pathutil.WithinBase()` 驗證檔案路徑防 traversal、本機 viewer 有 host allowlist 防 DNS rebinding。agent 有一個 shell 工具，但只執行使用者自己設定的腳本，不是 LLM 可任意生成指令執行。

**已發布並修復的真實 CVE**：`GHSA-wwg6-qfxw-xffj`（CVSS 5.3 medium）——舊版本裡 `.opencodereview/rule.json` 可指定絕對路徑或 symlink 當規則來源，未驗證是否仍在 repo 內，攻擊者能讓 `ocr` 讀出機器上任意 512KB 以內的 `.md/.txt/.markdown` 檔並送進 LLM prompt（純讀檔外送，非程式碼執行）。2026-09-10 發布，`v1.11.1` 修復。**風險前提是審查一個不完全信任的 repo**（自架 CI checkout 未信任分支，或本機 clone 陌生 repo）；只審自己信任的程式碼不受影響。

**Telemetry/隱私**：OpenTelemetry 整合，預設關閉；開啟後只輸出聚合指標（時長/token 數/模型名/狀態），官方保證「never attaches prompt content to spans or events」，目的地是使用者自己配置的 OTLP endpoint，不回傳 Alibaba。官方 FAQ 原話：「OCR sends your diffs (and optional read-tool snippets) to whatever LLM endpoint you configured. Nothing else leaves your machine.」

## Token / 成本槓桿（2026-09-14 補充）

1/9 token 消耗的 benchmark 資料集公開於 [Hugging Face](https://huggingface.co/datasets/Alibaba-Aone/aacr-bench)（可獨立查證，非片面宣稱）。成本可調旋鈕：`--effort low/medium/high`（1/2/3 審查輪數）、plan phase 觸發門檻（單檔 ≥50 行或合計 ≥100 行，調高可省錢）、`MAX_TOOL_REQUEST_TIMES`（預設 100 輪上限）。零成本探路指令：`ocr review --preview`（純本機篩檔不呼叫 LLM）、`ocr delegate preview`。

## 我們可以怎麼用

### 立即可做

1. **安裝 OCR** — 在 Pi 中設定 `ocr` 命令
2. **用 OCR 做初步審查** — 在 code review 工作流中加入 OCR
3. **比較結果** — 同一個 PR 分別用 OCR 和 code-review skill，比較差異

### 整合到工作流

```
Git commit / PR
    ↓
OCR 自動審查（低成本、快速）
    ↓
code-review skill 深度審查（Standards + Spec）
    ↓
Plannotator 標註（需要人類判斷時）
    ↓
修復 → 再次 OCR 確認
```

### 延伸研究

1. **OCR 的規則系統** — 如何自訂審查規則
2. **Delegation Mode** — 讓 Pi agent 執行 OCR 的審查
3. **MCP 整合** — OCR 的 MCP server 如何擴展
4. **Benchmark 方法論** — 如何建立自己的 code review benchmark

## 相關頁面

- [[wiki/sources/2026-09-14-opencode-review-primary-source-verification|2026-09-14 一手查證]] — 資安/Delegation Mode/Token 成本的查證來源
- [[wiki/sources/2026-08-20-opencode-review-deep-research|2026-08-20 Gemini 深度研究]] — 架構與社群爆紅原因，部分細節已被上方查證取代
- [[wiki/entities/plannotator]] — 我們的視覺化審查工具
- [[wiki/entities/pi-agent-core]] — Pi agent 核心
- [[wiki/concepts/meta-harness]] — Agent 架構
- [[wiki/topics/extension-dev]] — Extension 開發
- [[wiki/entities/codebase-memory-mcp]] — 不是同品類、零重疊（本頁 2026-09-14 訂正）：OCR 是**審查**變更好不好（review），codebase-memory-mcp 是把整個 codebase 建成知識圖譜供**理解**結構查詢（誰呼叫誰、架構全貌），兩者互補而非替代——該頁自己也是這樣定位兩者關係

## 標籤

#code-review #ai-agent #cli-tool #alibaba #open-source #benchmark
