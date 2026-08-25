---
title: OpenCodeReview
type: entity
created: 2026-08-20
updated: 2026-08-20
sources: 3
tags: [code-review, ai-agent, cli-tool, alibaba, open-source, benchmark]
collection: entities
topics: [ai-development-tools, extension-dev]
canonical: entities/open-code-review
---

# OpenCodeReview (OCR)

> 阿里巴巴開源的 AI 代碼審查 CLI 工具，19.3k stars，確定性工程 × Agent 混合架構。

## 一句話

OCR 是一個 AI 驅動的代碼審查工具，讀取 Git diff，透過帶 tool-use 能力的 Agent 將變更檔案送給 LLM，生成行級精度的結構化審查意見。

## 基本資訊

| 項目 | 內容 |
|------|------|
| 來源 | https://github.com/alibaba/open-code-review |
| 官網 | https://open-codereview.ai |
| Stars | 19.3k |
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

- [[wiki/entities/plannotator]] — 我們的視覺化審查工具
- [[wiki/entities/pi-agent-core]] — Pi agent 核心
- [[wiki/concepts/meta-harness]] — Agent 架構
- [[wiki/topics/extension-dev]] — Extension 開發
- [[wiki/entities/codebase-memory-mcp]] — 同品類工具（tree-sitter 知識圖譜），功能更完整

## 標籤

#code-review #ai-agent #cli-tool #alibaba #open-source #benchmark
