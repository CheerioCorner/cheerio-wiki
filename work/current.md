# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [ ] W-2026-08-055 花園流程與 extension 優化（根因修正）🆕 #knowledge #skill #notion
  - next: 確認 Pi 的 hook 能力（Stop hook 或替代方案）+ 確認 mmdc 環境可用性，然後依 P0→P1→P2 順序執行
  - refs: [[.pi/round-table/20260814-095306/synthesis|圓桌會議紀要]]、[[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki/entities/visualmap|visualmap skill]]、[[wiki/entities/page-content|page-content skill]]
  - 重點行動清單：
    - **P0（1–2 天）**：① DB write read-back——所有 Notion 寫入 skill 加反查步驟（0.5–1 天）② URL 存活驗證——page-content 寫入前 curl gate，含 retry + 軟/硬 404 區分（0.5 天）③ page-content 順序改「先查證再寫」+ 允許留白 `[待查證]`（1–2 天）
    - **P1（4–5 天）**：④ visualmap 寫入目標修正——Mermaid 寫入 DB 記錄頁 body，種子頁只放 relation + read-back（1 天）⑤ mmdc render 驗證 + 節點語法規則——Node ID 純英數、Label 加引號（1–2 天，需確認 mmdc 環境）⑥ headless 旗標 + 待審模式——`--mode headless`、`Status: 機器生成待審`、三道護欄（禁止寫入既有 Relation / Origin+run-id 可 rollback / default view 過濾）（2 天）
    - **P2（3–5 天）**：⑦ 共用驗證 harness——內部 SkillCompletionHook（效率層）+ 外部確定性腳本（保證層/promote gate）+ 共用驗證邏輯 + 結構化執行收據 JSON（3–5 天）
  - 共識結論（Claude + Gemini + Copilot 圓桌會議）：
    - 四個驗證關卡：URL 存活 / Mermaid render / DB read-back / 事實來源綁定
    - Headless 策略：正式 DB + `Status: 機器生成待審` + 三道護欄（非隔離 DB）
    - 驗證機制：內外結合雙層架構（內部 hook = advisory / 外部腳本 = gate）
    - 硬失敗 vs 軟失敗：URL 404 要區分真死 vs 暫時性，過度嚴格會誤殺

- [ ] W-2026-08-052 圓桌會議：Claude Design vs GitHub Copilot 可行性分析 🆕 #ai-agent #tools
  - next: 等 Cheer 回顧報告並回饋意見
  - refs: [[.pi/round-table/20260813-183535/synthesis|圓桌會議紀要]]、[[wiki/entities/claude-design|Claude Design]]、[[wiki/concepts/design-md-format|design.md Format]]
  - 狀態：待人類回顧
  - 核心結論：
    - Claude Design 五階段流程經 2026/6 更新後可行性大幅提升（token 問題已修復）
    - Copilot 無原生畫布但 IDE 整合與代碼品質更成熟
    - Cheer 最佳路線：Direct Code Handoff（Claude Design 探索 → DesignSync 雙向同步 → Claude Code 實作 → Copilot 維護）

## 🔴 Phase 1：前置知識（建立 Agent 前必學）

> 目標：理解 Agent 的核心組件，知道要建造什麼。

### 🔥 立即可做（簡單、快速、有價值）

- [ ] W-2026-08-030 安裝並測試 OpenCodeReview（OCR） ⏫ #ai-agent #code-review
  - next: 安裝 OCR、設定 LLM provider、跑一次 `ocr review` 測試
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/sources/2026-08-20-opencode-review-deep-research|Gemini 深度研究]]
  - 預估時間：30 分鐘
  - 為什麼先做：安裝簡單，能立即體驗「確定性工程 × Agent」混合架構

- [ ] W-2026-08-029 學習系統 + v0.84.0 學習任務 ⏫ #ai-agent #learning
  - next: 嘗試全螢幕模式、測試 AGENTS.override.md、玩 samplingParams
  - refs: [[work/designs/pi-agent-learning-system|學習系統設計]]、[[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 分析]]
  - 預估時間：1 小時
  - 為什麼先做：這些是 Pi 的新功能，動手玩能加深理解

### 📚 核心研究（需要時間，但很重要）

- [ ] W-2026-08-017 研究 harness 架構，開發自己的 AGENT ⏫ #ai-agent
  - next: Pi containerization 方案比較、Tau 三層架構、Hermes 學習迴圈
  - refs: [[wiki/entities/hermes-agent|hermes-agent]]、[[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/tau|tau]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：多天（持續研究）
  - 為什麼重要：這是 Agent 的骨架，決定了整個架構設計

- [ ] W-2026-08-025 研究 AI Agent 網路查詢能力：Extension 機制與 Search 架構 #ai-agent
  - next: 調研 web search / deep research extension 實作方式
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2-3 小時
  - 為什麼重要：Tool 系統是 Agent 的手腳，怎麼擴充功能

- [ ] W-2026-08-022 研究 MCP Server 架構與實作 #ai-agent
  - next: 調研 MCP protocol spec、transport 層、tool/resource/prompt 三大原語
  - refs: [[wiki/entities/pi-mono|pi-mono]]
  - 預估時間：2-3 小時
  - 為什麼重要：MCP 是 Tool 系統的標準協定

- [ ] W-2026-08-033 研究 Hook 機制：不同 IDE / Harness 的實作比較 #ai-agent
  - next: 調研 Claude Code、Cursor、Windsurf、Pi Agent 的 hook 系統
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2-3 小時
  - 為什麼重要：理解 Agent 的事件系統

- [ ] W-2026-08-NEW-001 研究 Session 管理：對話如何持久化與壓縮 #ai-agent
  - next: 調研 Pi、Claude Code、LangChain 的 session storage 機制
  - refs: [[wiki/entities/pi-agent-core|pi-agent-core]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2 小時
  - 為什麼重要：Agent 需要記住對話，這是核心功能

- [ ] W-2026-08-NEW-002 研究 Context 管理：長對話如何處理 #ai-agent
  - next: 調研 compaction、sliding window、summarization 等策略
  - refs: [[wiki/entities/ai-agent-core/token-budget|Token 預算]]、[[wiki/entities/pi-agent-core|pi-agent-core]]
  - 預估時間：2 小時
  - 為什麼重要：Context window 有限，必須有效管理

## 🟡 Phase 2：實作經驗（在現有 Agent 上練功）

> 目標：親手操作，把知識變成經驗。

- [ ] W-2026-08-031 比較實驗：OCR vs code-review skill #ai-agent #code-review
  - next: 找一個有 diff 的專案，分別用兩種工具審查，比較結果
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[wiki/entities/plannotator|Plannotator]]、~/.agents/skills/code-review/
  - blockedBy: [W-2026-08-030]
  - 預估時間：1 小時
  - 為什麼重要：親身體驗不同 code review 方式的差異

- [ ] W-2026-08-032 整合 OCR 到 Code Review 工作流 #ai-agent #code-review
  - next: 設計 OCR → code-review skill → Plannotator 的完整流程
  - refs: [[wiki/entities/open-code-review|OpenCodeReview]]、[[work/designs/pi-agent-learning-system|學習系統]]
  - blockedBy: [W-2026-08-031]
  - 預估時間：1-2 小時
  - 為什麼重要：建立自己的 code review 流程

- [ ] W-2026-08-010 建立 `pi-work-tracker` 並取代 `pi-todo-journal` ⏫ #extension
  - next: 抽象 TaskStore / JournalStore adapters → 測試、CI 與 npm 發布
  - refs: [[projects/pi-work-tracker/index|pi-work-tracker Project Bundle]]、[[work/README|Work System]]
  - 預估時間：多天
  - 為什麼重要：Extension 開發經驗，以後自己的 Agent 也可能需要類似機制

## 🟢 Phase 3：工具整合（提升效率）

> 目標：把好的工具整合到工作流。

- [ ] W-2026-08-028 設定 Pi Agent 使用 GitHub Copilot 訂閱作為 Provider #ai-agent #extension
  - next: 執行 `/login` → 選擇 GitHub Copilot → 完成 OAuth
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/entities/github-copilot|github-copilot]]
  - 預估時間：30 分鐘
  - 為什麼重要：有更多模型可用
  - 備註：有免費 GitHub Copilot 訂閱，可在本機設定

- [ ] W-2026-08-008 用 `grill-me` 跑一次完整需求追問（Pi Web） #skills
  - next: grilling 已安裝，可執行
  - refs: [[projects/pi-web-access-zh-tw/index|Pi Web project context]]
  - 預估時間：1 小時
  - 為什麼重要：確認 Pi Web 專案需求

## ⚪ Phase 4：延伸研究（有空再做）

> 目標：深入了解，為未來做準備。

- [ ] W-2026-08-023 研究 MCP Registry 與企業級管理 #ai-agent
  - next: 調研 MCP registry 方案、server 發現與註冊機制、版本管控
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]
  - 預估時間：2 小時
  - 為什麼可以等：企業級管理，個人建造不需要

## Backlog

（目前無其他待辦任務）

## Completed

- [x] W-2026-08-055 round-table + chat-with-codex skill 建立 ✅ #skills #ai-agent
  - completed: 2026-08-15
  - refs: [[wiki/sources/2026-08-15-round-table-pi-agent-v084-learning|圓桌會議紀要]]
  - 已完成：
    - ✅ 建立 chat-with-codex skill（codex exec 整合）
    - ✅ 更新 round-table skill 加入 Codex 為預設參與者
    - ✅ 舉行圓桌會議：Pi Agent v0.84.x 更新中值得學習的設計模式（Claude + Copilot）
    - ✅ 建立 wiki source note + 更新 index/log + work/history
    - ✅ 同步 cheerio-skills repo

- [x] W-2026-08-054 花園視覺地圖 Mermaid 全面驗證與修復 ✅ #knowledge #notion
  - completed: 2026-08-14
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]
  - 已完成：
    - ✅ 用 mmdc 實跑驗證全部 21 張種子地圖語法，修好 1 張壞掉的
    - ✅ 把 Omnigent（3 張）與 OpenCodeReview（3 張）從 ASCII-art 轉為真正 Mermaid

- [x] W-2026-08-053 花園技術類半成品補強（第三批）✅ #knowledge #notion
  - completed: 2026-08-14
  - 已完成：
    - ✅ 全部 6 顆技術類種子完成補強與 Gemini 證據型評審
    - ✅ 🎯 花園 22 顆種子全部處理完畢

- [x] W-2026-08-051 Ingest：Prime Agent 官方發布 + AI郵報 ARC-AGI-3 分析 ✅ #knowledge #ai-agent
  - completed: 2026-08-13
  - 已完成：
    - ✅ 存入 2 篇 raw/web 文章全文
    - ✅ 新建 2 個 concept 頁面 + 2 個 source notes
    - ✅ 大幅擴充 prime-agent entity

- [x] W-2026-08-050 知識系統架構 v3 修正：雙模型共識取代人類確認 ✅ #knowledge #meta #notion
  - completed: 2026-08-12
  - 已完成：
    - ✅ 推翻 v2.0 的人類確認機制，改用雙模型交叉驗證
    - ✅ 修改 AGENTS.md 全面改寫
    - ✅ 知識花園 4 個 skill 全面調整

- [x] W-2026-08-049 Wiki 大整理：Redis + CodeReview + Agentic AI + Knowledge Management ✅ #knowledge #wiki
  - completed: 2026-08-10
  - 已完成：
    - ✅ 新增 15 個 wiki 頁面，ingest 12 個 raw 檔案

- [x] W-2026-08-048 知識系統架構改進：4 輪圓桌會議 + 花園更新 ✅ #knowledge #meta #notion
  - completed: 2026-08-10
  - 已完成：
    - ✅ 4 輪圓桌會議（Pi + Gemini + Copilot）確立架構
    - ✅ 核心架構：三個操作 + 一個機制 + 一個回流

- [x] W-2026-08-047 知識系統健檢機制全面盤點 + AGENTS.md 整理 ✅ #knowledge #meta
  - completed: 2026-08-14
  - 已完成：
    - ✅ 三大健檢機制盤點
    - ✅ AGENTS.md 品質掃描與修復
    - ✅ garden-guard.ts extension 建立

- [x] W-2026-08-044 建立「圓桌會議」skill ✅ #skills #ai-agent
  - completed: 2026-08-11
  - 已完成：
    - ✅ 設計文件 v2 + 建立 SKILL.md
    - ✅ 新增 Claude 為預設參與者
    - ✅ 新增量化共識偵測機制

- [x] W-2026-08-043 Content 設計 Redesign ✅ #knowledge #skill
  - completed: 2026-08-10
  - 已完成：
    - ✅ 重寫 page-content skill（四層骨架+主觀現實+roadmap）

- [x] W-2026-08-042 知識花園 Skill 架構重構 ✅ #knowledge #skill
  - completed: 2026-08-10
  - 已完成：
    - ✅ 建立 seed_schema.yaml + 重構 skills

- [x] W-2026-08-038 Notion 整合設計全面重構 ✅ #knowledge #notion
  - completed: 2026-08-09
  - 已完成：
    - ✅ 5 個 Phase 全部完成

- [x] W-2026-08-039 Notion 頁面內容逐一手動調整 ✅ #knowledge #notion
  - completed: 2026-08-09
  - 已完成：
    - ✅ 7 筆種子頁面全部更新

- [x] W-2026-08-037 知識花園加強：Relation 關聯 + 視覺地圖 + 改名 ✅ #knowledge #notion
  - completed: 2026-08-09
  - 已完成：
    - ✅ 建立種子 ↔ 專題雙向 Relation 關聯

- [x] W-2026-08-041 YouTube ingest：LangGraph in 10 Minutes ✅ #knowledge #ai-agent
  - completed: 2026-08-08
  - 已完成：
    - ✅ 新增 wiki：source note + entity + concept

- [x] W-2026-08-039 PDF 處理流程建立 ✅ #knowledge #tools
  - completed: 2026-08-08
  - 已完成：
    - ✅ 建立完整 PDF → Markdown → wiki 流程

- [x] W-2026-08-040 cheerio-skills 同步 ✅ #skills
  - completed: 2026-08-08
  - 已完成：
    - ✅ 同步 7 個新 skills + 2 個更新

- [x] W-2026-08-036 Notion「任何當下 → AI相關」頁面 ingest ✅ #knowledge #notion
  - completed: 2026-08-07
  - 已完成：
    - ✅ 新增 wiki 2 entities

- [x] W-2026-08-034 YouTube ingest：Wow 頻道 5支 AI Agent 前沿研究 ✅ #knowledge #ai-agent
  - completed: 2026-08-06
  - 已完成：
    - ✅ 新增 wiki：1 source note、5 entities、4 concepts

- [x] W-2026-08-021 建立知識花園導覽 Database ✅ #knowledge #notion
  - completed: 2026-08-06
  - 已完成：
    - ✅ 建立 Database + 6 顆種子 + 5 個專題

## Work record contract

- 任務狀態只在本檔維護。
- 完成、決策、重要討論與處理結果追加至 `work/history/YYYY-MM.md`。
- 每個 history event 必須包含 `refs:`，至少指向 raw conversation、project 或 wiki 其中之一。
- 沒有形成工作進展或可追溯結果的對話，不需要建立事件。
