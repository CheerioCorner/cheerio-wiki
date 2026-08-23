---
title: "How to modernize legacy codebases using AI coding agents"
type: source
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [ai-coding, legacy-code, migration, sandbox, tdd, bdd, code-review, ai-agent]
topics: [agent-coding-practices, coding-agent]
canonical: sources/2026-08-25-google-cloud-legacy-code-ai
provenance:
  - kind: raw
    path: raw/youtube/how-to-modernize-legacy-codebases-ai-coding-agents.md
    url: https://www.youtube.com/watch?v=RKvckuC6Bsk
---

# How to modernize legacy codebases using AI coding agents

> 來源：[Google Cloud Tech YouTube](https://www.youtube.com/watch?v=RKvckuC6Bsk) | 講者：Martin & JK | 時長：8:13 | 語言：English | 字幕：Manual captions

## 核心摘要

Google Cloud 團隊提出用 AI coding agent 現代化 legacy codebase 的三步方法論：**Plan → Execute → Verify**。核心觀點：「寫 code 變便宜了，但建構好的軟體依然昂貴且需要真正的設計。」`[07:29]`

## Step 1: Plan（規劃）

### Sandbox — 一次性隔離環境

應使用容器化技術（如 Docker）建立一次性沙盒，讓 AI 可以安全嘗試、編譯、執行測試，「即使搞砸也不會影響本機配置」`[02:07]`。README 必須詳列 compiler、linter、unit tests 的設定，讓 agent 能自主除錯 `[01:40]`。

兩項附帶好處：① Docker 充當一次性沙盒，一條指令就能還原 `[02:09]`；② 順便產出高品質文件，新人 onboard 更容易 `[02:19]`。

### Planning Mode — 先偵察再動手

不要讓 AI 直接寫 code，先切換到 planning mode 做 recon `[02:31]`。提示詞範例：「explore the codebase and show me what files, APIs, or database tables will break if we push this」`[02:43]`。讓 AI 畫出 blast radius，列出所有受影響的檔案、API 與資料庫資料表 `[02:49]`。

### 對話式討論 — 把 AI 當同事

把 AI 當同事而非自動打字機 `[03:10]`。三種場景的 prompting 策略：

- **新功能**：要求 AI 產生三個架構設計，先自我批判，再選出它偏好的方案 `[03:24]`
- **強迫提問**：逼 AI 反問你，找出你沒想到的需求 `[03:38]`
- **修 Bug**：清楚描述問題＋給具體範例，要求 AI 寫 failing test 驗證它真的理解 `[03:44]`

## Step 2: Execute（執行）

### Context 管理

每個 conversation 只處理單一變更 `[04:22]`。大功能拆子任務，做完一個就產出 summary 傳給下一個。用 markdown checklist 追蹤進度——同時保持人和 agent 都專注在當前任務 `[04:41]`。

### TDD/BDD 領先

用 pro model（如 Claude Sonnet）寫 acceptance criteria 與 tests `[05:03]`。再把執行交給便宜 model（如 Flash）寫實作程式碼 `[05:08]`。測試綠燈後才繼續 `[05:14]`。這可以平行化加速 `[05:18]`。

### Agent Rules — 行為準則

定義 AI 的行為準則，可能是最重要的一項 `[05:24]`。規範你的標準工作流：先寫測試、確認 build 通過、未經許可不 commit `[05:34]`。這樣 AI 在每個工作項上都會自動遵守你的標準 `[05:43]`。

## Step 3: Verify（驗證）— 三層過濾器

「永遠不要讓 AI 直接 shipping 到 production」`[07:12]`。

1. **人工 Code Review**：clean code is for future humans and AIs `[06:05]`
2. **CI/CD AI Review**：另一個 model 審查 PR，原始 agent 為自己的選擇辯護 `[06:13]`
3. **Smoke Tests**：自動部署到 staging，手動點擊驗證行為符合預期 `[06:22]`

## 三大金句

1. 「Document your tools in a README and use planning mode」`[06:40]`
2. 「Write tests first and set agent rules」`[06:49]`
3. 「Build a three-layer filter — never let AI ship directly to production」`[07:08]`

> 「Writing code is cheap now, but building great software is still expensive and requires real design. That loop of planning, testing, and talking with your agent — that's your new superpower.」`[07:29]`

## Cheer 的想法

> 來源：[[raw/conversations/2026-08-25-google-cloud-legacy-code-ai|Cheer 的想法：Google Cloud Legacy Code × AI 方法論]] → [[raw/youtube/how-to-modernize-legacy-codebases-ai-coding-agents|來源逐字稿]]

- 這是每間企業都會面臨的問題：太多舊程式、沒有完整手冊、沒有結構化、沒有自動化測試、沒有 CI/CD
- Docker Compose 是一直想深度研究的方向，但 Docker Desktop 要收費，正在找替代方案
- 充分討論 = Prompt Engineering 的一部分
- TDD 找出受影響系統層面，BDD 驗證 AI 品質——跟他們想法契合，但之前沒有具體步驟

## 開放問題

- Docker Desktop 收費後的免費替代方案（Podman、Rancher Desktop）實際評估
- 三層驗證是否有數據支撐降低多少 defect escape rate
- Agent 自我辯護機制的實際效果——是否真的比單向 AI review 更好？
