---
title: "Legacy Code Modernization with AI — 用 AI 現代化遺留系統的方法論"
type: concept
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [ai-coding, legacy-code, methodology, tdd, bdd, sandbox, code-review]
topics: [agent-coding-practices, coding-agent]
canonical: concepts/legacy-code-modernization
---

> 用 AI coding agent 現代化 legacy codebase 的三步方法論——Plan（sandbox 隔離 + recon 出 blast radius）→ Execute（context 管理 + TDD/BDD 交叉分工）→ Verify（人工 / CI AI / staging smoke test 三層過濾）。是 [[wiki/concepts/ai-coding-workflow|ai-coding-workflow]] 針對「無文件、無測試、無 CI/CD 的既有系統」這個高風險場景的特化版本。

## 核心流程

```
Legacy Codebase → Plan → Execute → Verify → Modernized Code
                    │        │         │
                    │        │         └─ 三層過濾器：人工 Review → AI Review → Smoke Test
                    │        └─ TDD/BDD 領先：Pro model 寫 tests，Flash model 寫 code
                    └─ Sandbox + Recon：Docker 沙盒 + blast radius 分析
```

## 三步方法論

### Step 1: Plan（規劃）

#### Disposable Sandbox — 一次性隔離環境

為 AI 建立容器化隔離環境（Docker / Podman / Rancher Desktop），讓 agent 可以安全嘗試而不影響本機。README 要記錄所有本地設定（compiler、linter、unit tests），讓 agent 能自主除錯 `[01:40]`。

兩項附帶好處：① 一條指令還原乾淨狀態 `[02:09]`；② 順便產出 onboarding 文件 `[02:19]`。

**Docker Desktop 替代方案**（因 Docker Desktop 企業授權收費）：
- **Podman**：相容 Docker API、daemonless、適合 Linux/Windows WSL2
- **Rancher Desktop**：GUI 友善、內建 K3s/Containerd、Windows 好選擇
- **Colima**：macOS 輕量級方案

#### Planning Mode — 先偵察再動手

不要讓 AI 直接寫 code。先用 planning mode 做 recon `[02:31]`：

> 「explore the codebase and show me what files, APIs, or database tables will break if we push this」`[02:43]`

讓 AI 畫出 **blast radius**——列出所有受影響的檔案、API 與資料庫資料表 `[02:49]`。確認清單後才開始修改。

#### 對話式討論 — 把 AI 當同事

把 AI 當同事而非自動打字機 `[03:10]`。三種 prompting 策略：

| 場景 | 策略 | 效果 |
|------|------|------|
| 新功能 | 產生 3 個架構設計 + 自我批判 | 避免 first-idea bias |
| 需求釐清 | 逼 AI 反問你 | 找出你沒想到的 edge cases |
| 修 Bug | 清楚描述 + 範例 + 要求寫 failing test | 確認 AI 真的理解問題 |

### Step 2: Execute（執行）

#### Context 管理 — 單一變更原則

每個 conversation 只處理單一變更 `[04:22]`。大功能拆子任務：
- 做完一個子任務 → 產出 summary → 傳給下一個
- 用 **markdown checklist** 追蹤進度 `[04:41]`
- 避免長時間 multi-hour chat session 導致 agent「見樹不見林」`[04:27]`

#### TDD/BDD 領先 — 雙模型分工

這是跟 [[wiki/concepts/multi-model-cost-routing|multi-model-cost-routing]] 的「Task-based routing」直接對應的具體實踐：

1. **Pro model**（如 Claude Sonnet）：寫 acceptance criteria + tests `[05:03]`
2. **Flash model**（如 Gemini Flash）：寫實作程式碼 `[05:08]`
3. **測試綠燈** → 才繼續 `[05:14]`
4. 可平行化加速 `[05:18]`

關鍵機制：**用測試作為兩個 tier 之間的強制 gate**——Pro model 的輸出（tests）成為 Flash model 的驗收標準，確保便宜 model 不會偏離需求。

#### Agent Rules — 行為準則

定義 AI 的硬性限制 `[05:24]`：
1. 必須先寫測試（TDD Red Light）
2. 必須確認 build 通過
3. 未經許可不得 commit `[05:34]`

### Step 3: Verify（驗證）— 三層過濾器

「永遠不要讓 AI 直接 shipping 到 production」`[07:12]`。

```
AI 寫完 code
    │
    ▼
┌─────────────────────┐
│ 1. 人工 Code Review  │  clean code is for future humans and AIs
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│ 2. CI/CD AI Review   │  另一個 model 審查 PR，原 agent 為自己的選擇辯護
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│ 3. Smoke Tests       │  自動部署到 staging，手動點擊驗證
└─────────────────────┘
```

第 2 層的「agent 自我辯護」是這套方法論的獨特之處：不是被動接受另一 model 的意見，而是讓原始 agent 為自己的設計選擇提出辯護，形成 **雙向對話式審查** `[06:13]`。

## 與既有知識的關係

### 互補

| 既有概念 | 本方法論的補充 |
|---------|--------------|
| [[wiki/concepts/ai-coding-workflow\|ai-coding-workflow]] | 把「Grill with Docs」具體化成 Sandbox + Recon + 對話討論 |
| [[wiki/concepts/multi-model-cost-routing\|multi-model-cost-routing]] | Pro/Flash 分工是「Task-based routing」的第一個具體案例，且引入「測試作為 gate」機制 |
| [[wiki/concepts/ai-code-review\|ai-code-review]] | 新增「agent 自我辯護」的雙向審查角度 |
| [[wiki/concepts/context-decay\|context-decay]] / [[wiki/concepts/context-rot\|context-rot]] | 「單一變更原則」+ markdown checklist 是對抗 context rot 的具體工程對策 |
| [[wiki/concepts/spec-driven-development\|spec-driven-development]] | TDD/BDD 分工提供了從 spec 到 code 之間的自動化驗證橋樑 |

### 衝突

無直接衝突。但注意：`migration` topic 目前語意是「wiki 架構遷移」，跟「legacy codebase 現代化」是同名異義詞，不宜混用。

## 來源

- [[wiki/sources/2026-08-25-google-cloud-legacy-code-ai|Source Note]]
- [[raw/youtube/how-to-modernize-legacy-codebases-ai-coding-agents|原始逐字稿]]

## 相關頁面

- [[wiki/concepts/ai-coding-workflow|AI Coding Workflow]] — 通用版編碼工作流程
- [[wiki/concepts/multi-model-cost-routing|Multi-Model Cost Routing]] — 多模型成本路由
- [[wiki/concepts/ai-code-review|AI Code Review]] — 程式碼審查方法論
- [[wiki/concepts/spec-driven-development|Spec-Driven Development]] — 規格驅動開發
- [[wiki/concepts/context-decay|Context Decay]] — 跨模型脈絡遺失
- [[wiki/concepts/context-rot|Context Rot]] — 長任務記憶腐化
