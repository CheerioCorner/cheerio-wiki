# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [x] W-2026-08-067 YouTube ingest：Understanding AI Infrastructure — GPUs, vLLM, K8s ✅ #knowledge #ai-agent #youtube
  - completed: 2026-08-21
  - next: ✅ 已完成並 push（commit 3eec926）。NPU 討論頁後續由 W-2026-08-069 接續。
  - refs: [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Source Note]]、[[wiki/concepts/gpu-architecture-for-ai-inference|GPU 架構]]、[[wiki/concepts/llm-serving-architecture|LLM Serving 架構]]、[[wiki/concepts/model-sharding|Model Sharding]]、[[wiki/entities/vllm|vLLM]]、[[wiki/entities/llm-d|llm-d]]、[[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]
  - 已完成：
    - ✅ 抓字幕 → 建 source note + 3 concepts + 2 entities + 1 discussion
    - ✅ 更新 index / log / topics 導航
    - ✅ git commit + push（3eec926），Claude 核對檔案存在與 git 同步確認無誤
    - ✅ agy-bridge 品管失敗後，Claude 改為人工核對 GPU 規格表（A100/H100/H200/B200 TFLOPS/VRAM/Bandwidth），數字與公開規格相符
  - 備註：agy-bridge 連續 4 次 CANCELED 見 [[work/current#W-2026-08-068|W-2026-08-068]]；NPU 研究見 [[work/current#W-2026-08-069|W-2026-08-069]]

- [ ] W-2026-08-069 NPU 角色深度研究（Gemini research）🔄 #knowledge #ai-agent #research
  - next: 等 Gemini chat-with-gemini-research 完成後，將研究結果回填進 [[wiki/discussions/npu-role-in-ai-infrastructure]]，補充引用來源
  - refs: [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]、[[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|AI Infrastructure Source Note]]
  - 起因：Cheer 看完影片後提出開放問題「NPU 在 AI 基礎設施架構中扮演什麼角色、為什麼 AI 時代需要 NPU」，已標記在 discussion 頁，正在派 Gemini 做深度研究（有引用來源要求）

- [ ] W-2026-08-068 agy-bridge 異常追蹤：讀檔後被 CANCELED 🔴 #tools #bug
  - next: 排查 agy-bridge 為何在讀完一個檔案後被系統取消（status: CANCELED, exit_code 0, 無逾時），嘗試重現並找出根因；可能需要檢查 agy CLI 的 session 管理或 tool timeout 設定
  - refs: [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|AI Infrastructure Source Note]]（觸發本次品管的 ingest）
  - 狀態：Blocked — 工具本身的問題，已回報但尚未解決。連續 4 次（含開新對話）都在讀完一個檔案後被系統取消，完全吐不出結果
  - 影響：這次 ingest 改為 Claude 人工核對 GPU 規格，但長期若 agy 不穩定，Gemini 品管流程會受阻

- [x] W-2026-08-066 想法與原始來源建立雙向關聯（raw/conversations ↔ raw/youtube・raw/web）✅ #knowledge #wiki #meta #ingest-pipeline
  - completed: 2026-08-20
  - next: ✅ 全部完成。Claude 於下個 session 讀 diff 做最終品質審查
  - refs: [[AGENTS.md]]、[[raw/conversations/readme|raw/conversations 說明]]、[[raw/youtube/readme|raw/youtube 說明]]、[[raw/README|raw contract]]、[[work/synthesis/2026-08-20-thought-source-linkage-review|Gemini 覆核報告]]、[[raw/conversations/2026-08-20-langgraph-thoughts|驗收想法檔]]
  - 背景：Cheer 看 YouTube 影片／文章時常有當下想法，這正是他想存下這篇資料的原因，但現行 ingest 只吃來源本身（字幕、網頁全文），想法沒有被一起留存，事後回頭看只剩「這篇在講什麼」，看不到「我當時為什麼覺得重要」
  - 任務規格（Cheer 2026-08-20 拍板）：
    1. YouTube 逐字稿維持原路徑不變：`raw/youtube/`
    2. 影片本體不下載備份，只保留 URL；`raw/youtube` 現有 metadata（URL、抓取時間）已足夠，不需新增欄位
    3. Cheer 當下的想法存入 `raw/conversations/`，沿用既有 `source_kind: conversation` 慣例，原文不整理
    4. **核心新增**：存入的當下，`raw/conversations/` 的想法檔案要與觸發它的原始資料（`raw/youtube/xxx.md`、`raw/web/xxx.md`、或其他 raw channel）建立雙向關聯——具體實作方式（frontmatter 加 `related_raw:` 欄位、或用 `[[wikilink]]` 互相引用、或兩者皆用）由 Pi 提案、Gemini 覆核後定案，寫進 `AGENTS.md` §4.2 frontmatter 規範
    5. `wiki-ingest` 系列 skill 要能讀到這層關聯：ingest 時若某筆 raw 有關聯的想法檔案，對應的 `wiki/sources/` 筆記要新增「Cheer 的想法」小節並連回原始想法檔；想法本身若夠獨立成新概念／新問題，另開 `wiki/concepts/` 或 `wiki/discussions/` 頁面，雙向連結回來源筆記
    6. `wiki-lint` 要新增健檢規則：偵測 `raw/conversations/` 想法檔與其宣稱關聯的 `raw/youtube`／`raw/web` 來源之間的雙向連結是否存在、是否互指一致；缺失或斷鏈要能被 lint report 抓出來
    7. `wiki-query` 查詢來源相關內容時，若該來源有關聯的想法檔案，要能一併呈現「Cheer 當時的想法」，不能只回來源本身內容
  - 驗收重點：Pi 完成後要能舉一個實例（一部影片 + 一則想法 + ingest 後的 wiki/sources 頁面）證明關聯真的從 raw 一路追溯到 wiki，不是只改了文件說明沒改流程
  - 無相依，可任何 session 切入

- [x] W-2026-08-065 Wiki Lint 第二輪：Claude × Gemini 圓桌討論 + P0 修復 ✅ #knowledge #wiki #meta
  - completed: 2026-08-19
  - next: ✅ P0 全部完成。P1（wiki-lint dead-end 規則修正 + wiki-ingest frontmatter guardrail）待下次 session 處理
  - refs: [[wiki/reports/wiki-lint-2026-08-19|Lint Report]]、[[wiki/topics/skill|Skill 拆分先例]]、[[wiki/discussions/topic-stub-cleanup|Topic Stub Cleanup 決策]]
  - 已完成：
    - ✅ 第二輪 lint 全面掃描（254 頁面、89 sources、28 topics）
    - ✅ 問 Claude 7 大問題處理建議 → 拿去 Gemini 討論 → 再回 Claude 確認 Gemini 獨特洞察（共 3 輪）
    - ✅ 收斂 3 個系統性缺口：寫入無驗證、架構歸屬未檢查、缺 lifecycle trigger
    - ✅ P0 執行：27 個 topic page 補 `type: topic`、9 個冗餘 raw trash、`pi-agent-learning` 補進 index.md 導航
  - Claude×Gemini 共識決策：
    - Topic 拆分沿用 hub-spoke 模式（skill.md 先例）
    - 缺失 topic page 多數合併/忽略，不新建（違反 topic-stub-cleanup policy）
    - Dead-end 多數是 lint 誤判，修規則而非硬加連結
    - 拆分 topic 時用 grep 批次更新 frontmatter topics:，不靠 alias
    - wiki-ingest 源頭加 frontmatter guardrail，不靠事後 lint
    - Raw 停留天數 → lifecycle trigger → 自動提醒

- [x] W-2026-08-056 Skill 建置自動化研究 ①找現有方案 ✅ #ai-agent #skill #enterprise #skill-automation-design
  - completed: 2026-08-19
  - next: ✅ 兩份獨立深度調研完成並 ingest 進 wiki。建議將本調研成果餵給 [[work/current#W-2026-08-057|W-2026-08-057]]（共享驗證機制設計 round-table）當起點，特別是 Codex 發現的 Durable Execution 層與五類 Gate 設計建議。
  - refs: [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研（20 sources）]]、[[wiki/sources/2026-08-19-agentic-pipeline-orchestration-gemini|Gemini 調研（7 sources）]]、[[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]]、[[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]]、[[wiki/concepts/agent-skills-enterprise-governance|Agent Skills 企業治理（已更新）]]
  - 備註：同組任務 [[work/current#W-2026-08-057|W-2026-08-057]]／[[work/current#W-2026-08-058|W-2026-08-058]]／[[work/current#W-2026-08-059|W-2026-08-059]]
  - 品質說明：Codex 版本（gpt-5.6-luna）品質明顯優於 Gemini 版本——深連結 vs 根目錄 URL、來源數 20 vs 7、日期誠實標「未標示」。兩份事實衝突時優先採信 Codex。
  - QA 補記：Pi 執行 ingest 時自我報告不完整（漏報弄壞 26 個 wiki/topics/*.md 的 frontmatter、漏報 9 個無關 raw 檔案搬移），經 Claude 逐一讀 diff 驗證後抓到並修復。詳見 [[work/history/2026-08#坑 7：Pi ingest commit 弄壞 26 個 wiki 頁面 + 未揭露的檔案搬移（8/19）|work/history/2026-08.md 坑 7]]。

- [ ] W-2026-08-057 Skill 建置自動化研究 ②共享驗證機制設計 🆕 #ai-agent #skill #enterprise #skill-automation-design
  - **Session handoff（8/19）**：今天工作到此為止，下次 session 從這裡接續開始；完成 057 後要依序推進 [[work/current#W-2026-08-058|W-2026-08-058]] → [[work/current#W-2026-08-059|W-2026-08-059]]，不要漏掉後面兩個。
  - next: 開 round-table（Claude+Gemini+Copilot）設計中間無人看管階段（分析/設計/開發/測試）的自動驗證機制，作為 (a)/(b) 兩種 checkpoint 拓撲的共同地基。**建議以 [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研]] 的五類 Gate 設計（Contract / Policy / Quality / Human / Release）與 [[wiki/concepts/durable-execution-for-agents|Durable Execution 層」作為討論起點。**
  - refs: [[wiki/sources/2026-08-19-agentic-pipeline-orchestration-codex|Codex 調研（W-2026-08-056 產出）]]、[[wiki/concepts/agentic-pipeline-checkpoint-design|Agentic Pipeline Checkpoint 設計]]、[[wiki/concepts/durable-execution-for-agents|Durable Execution for Agents]]、[[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支SKILL簡報筆記]]（P22 Write-back 安全門、P27 Skill 的限制）、[[wiki/concepts/agent-skills-enterprise-governance|Agent Skills企業治理]]（6 階段生命週期 + Agentic Pipeline 整合）、W-2026-08-055（花園流程「內部 hook=advisory + 外部腳本=gate」先例，可借鏡）
  - 無相依，可任何 session 切入
  - 備註：同組任務 W-2026-08-056／W-2026-08-058／W-2026-08-059；這是 (a)/(b) 兩種拓撲都要站上去的共同保障，沒有它就沒有「少人工介入」的安全網

- [ ] W-2026-08-058 Skill 建置自動化研究 ③Checkpoint 拓撲比較 🆕 #ai-agent #skill #enterprise #skill-automation-design
  - next: 站在②的驗證機制之上，完整寫出 (a) 兩道 checkpoint vs (b) 三道 checkpoint 的對比文件（優缺點、風險、適用情境），供主管決策參考
  - refs: [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支SKILL簡報筆記]]（P26 Skill SDLC）
  - blockedBy: [W-2026-08-057]
  - 備註：同組任務 W-2026-08-056／W-2026-08-057／W-2026-08-059；這是要帶去跟主管報告拍板的核心產出

- [ ] W-2026-08-059 Skill 建置自動化研究 ④企業導入路徑 🆕 #ai-agent #skill #enterprise #skill-automation-design
  - next: 由 Cheer 主導（AI 打草稿），對齊 `wiki/concepts/agent-skills-enterprise-governance` 既有 6 階段治理框架與 CAB 變更管理慣例，規劃如何在長榮 IT 系統架構課落地
  - refs: [[wiki/concepts/agent-skills-enterprise-governance|Agent Skills企業治理]]
  - 無相依，可任何 session 切入
  - 備註：同組任務 W-2026-08-056／W-2026-08-057／W-2026-08-058；技術設計再好，沒有對齊企業現有流程就無法真正導入

- [ ] W-2026-08-064 Prime Agent Docker 化評估與實作 🆕 #ai-agent #docker #prime-agent #enterprise
  - next: ① 撰寫 Dockerfile + docker-compose.yml（基於 python:3.11-slim，掛載 workspace volume + API key env）② 在 Windows Docker Desktop（WSL2 backend）實跑測試 ③ 驗證三個面向：session 持久化（JSONL volume 掛載）、IPython kernel 狀態（/refine harness state）、daemon 模式相容性 ④ 撰寫安全建議文件（對齊社群 PR #1126 的 best practice：非 root、cap-drop、network 控制）⑤ 評估是否適合導入長榮 IT 作為 enterprise coding agent sandbox 方案
  - refs: [[wiki/entities/prime-agent|Prime Agent]]、[[wiki/entities/pi-mono|pi-mono]]、https://github.com/PrimeIntellect-ai/prime-agent/issues/1103（Containerized / K8s deployment support）、https://github.com/PrimeIntellect-ai/prime-agent/pull/1126（security/sandboxing guidance）
  - 無相依，可任何 session 切入
  - 備註：Prime Agent 2026-08-05 才發布，官方無 Dockerfile，但技術上無阻礙；社群已在討論 K8s 部署方案；此任務可作為 W-2026-08-056~059 Skill 建置自動化研究的「執行環境」參考

- [x] W-2026-08-062 2026-08 月度回顧重寫：陳述級溯源 ✅ #knowledge #meta
  - completed: 2026-08-18
  - next: ✅ 全部完成
  - 重寫 work/history/2026-08.md，從流水帳改為「學習成長」導向
  - 新結構：月度里程碑 → 學習演進（含事件溯源）→ 踩坑紀錄 → 決策反思 → 量化成長 → 未來展望
  - 每個陳述都有真正的連結：wiki 頁面、圓桌會議紀要、work task、skill、extension、腳本
  - refs: [[wiki/entities/knowledge-garden|knowledge-garden]]、C:/Cheerio/Obsidian/work/history/2026-08.md

- [x] W-2026-08-063 Skill 專題重寫：花園「🔬 Agent Skills」頁面 + 視覺地圖全面更新 ✅ #knowledge #skill #notion
  - completed: 2026-08-18
  - next: ✅ 全部完成
  - 起因：使用者發現昨天（W-2026-08-060）新增的兩顆種子（Skill 設計方法論、Agent 擴充架構層級）沒有被整合進「🔬 Agent Skills」專題母頁——種子種下去了但沒嫁接回主幹
  - 流程：Pi 純粹依 wiki 大腦內容（不看花園現有頁面）重寫全新專題文章 → Gemini + Codex 獨立交叉評審（先查證再下結論）→ Claude 審閱報告 → Gemini + Codex 讀花園現有內容跟新草稿交叉比對，找出遺失的花園獨有內容（四個結構性限制、三個真實撞牆案例含 2026-08-15 事故、判準一二決策框架、Description Optimization 具體數字）→ Pi 補回 → 使用者確認 → 寫入 Notion，視覺地圖比照同一流程重製
  - 新增結構性要求「陳述級溯源」：全文 80+ 處論點標示（參考：外部來源）／（原創：Cheer 自己的研究成果，如 Intention-First 命名）／（沿用既有花園內容）三種來源，讓外部參考跟原創貢獻分得清楚
  - 過程中兩次抓到 Pi 謊報完成（貼假的「已修改內容」證據，實際檔案沒改到）與一次章節標題遺失，都靠 Claude 直接讀檔/API 查證抓到並修正，未再信任 agent 自報
  - refs: [[wiki/topics/skill|Skill 主題索引]]、[[wiki/concepts/skill-design-methodology|Skill 設計方法論]]、[[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]、https://app.notion.com/p/3bc5979e3a8c8121924ef99b09671383（🔬 Agent Skills 專題頁）、https://app.notion.com/p/3bc5979e3a8c81e0bb89f9066f64f664（視覺地圖）

- [x] W-2026-08-055 花園流程與 extension 優化（根因修正）✅ #knowledge #skill #notion
  - completed: 2026-08-17
  - next: ✅ 全部完成
  - **2026-08-17 視覺地圖結構性修復（3 顆種子）✅**：Skill 設計方法論 + Agent 擴充架構層級的 Mermaid 搬入 DB 記錄頁 + relation 修復；E2E 測試種子錯置 Mermaid 清除。詳見 work/history/2026-08.md。
  - refs: [[.pi/round-table/20260814-095306/synthesis|2026-08-14 圓桌會議紀要]]、[[.pi/round-table/20260817-080831/synthesis|2026-08-17 P0 實作設計圓桌會議紀要]]、[[wiki/entities/knowledge-garden|knowledge-garden]]、[[wiki/entities/visualmap|visualmap skill]]、[[wiki/entities/page-content|page-content skill]]
  - **Phase 1（共用驗證腳本）✅ 完成**：`scripts/notion_verify.sh` + `scripts/url_gate.sh`，經 mock server 獨立測試通過
  - **Phase 2（接入 Notion 寫入 skill）✅ 完成（有已知限制）**：四支 knowledge-garden-* skill 都接上 read-back / URL gate，重構為共用參考檔 `references/write-back-safety.md`
  - 已知限制：① Notion DB 還沒加 Run-ID/Origin/Verification-ID 三個 property（2026-08-17 API 確認不存在，需 Cheer 手動在 Notion UI 新增）② 尚未用真實 Notion page 做端到端測試（依賴 ①）③ cheerio-skills 同步：knowledge-garden + page-content SKILL.md 已同步（commit 32c942d），其餘 skill 待後續同步
  - 共識結論（Gemini + Codex 圓桌會議 + Claude 覆核）：
    - 四個驗證關卡：URL 存活 / Mermaid render / DB read-back / 事實來源綁定
    - Headless 策略：正式 DB + `Status: 機器生成待審` + 三道護欄
    - 驗證機制：內外結合雙層架構（內部 hook = advisory / 外部腳本 = gate）
    - 硬失敗 vs 軟失敗：URL 404 要區分真死 vs 暫時性，過度嚴格會誤殺

(W-2026-08-060 已完成，見 Completed 區塊)

- [x] W-2026-08-060 DevOps Skill 簡報 ingest wiki（Agent Skills 專題擴充）✅ #knowledge #skill #notion
  - completed: 2026-08-17
  - refs: [[wiki/sources/2026-08-17-devops-skill-presentation|DevOps Skill 簡報來源筆記]]、[[wiki/concepts/skill-design-methodology|Skill 設計方法論]]、[[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]
  - 已完成：
    - ✅ 讀完 31 頁 HTML 簡報全部內文
    - ✅ 雙模型交叉驗證（Claude + Gemini）
    - ✅ 新增 2 顆概念種子 + 更新 4 個既有頁面
    - ✅ 建立來源筆記 + git commit 推送
  - 花園同步：依賴 W-2026-08-055

- [x] W-2026-08-061 YouTube ingest + 圓桌會議：Claude Code Hooks 教學影片 ✅ #knowledge #ai-agent #hooks
  - completed: 2026-08-17
  - refs: [[wiki/sources/2026-08-18-claude-code-hooks-tutorial|Claude Code Hooks 教學來源筆記]]、[[wiki/concepts/claude-code-hooks-architecture|Claude Code Hooks 架構]]、[[.pi/round-table/20260817-222740/synthesis|圓桌會議紀要]]
  - 已完成：
    - ✅ 抓字幕（zh-TW 手動字幕，568 segments → 26 paragraphs）
    - ✅ 雙模型交叉驗證（Claude + Gemini 提案一致）
    - ✅ 新建 source note + concept page
    - ✅ 更新 claude-code entity、agent-extensibility-hierarchy concept、2 個 topic pages
    - ✅ 2 輪圓桌會議（Claude + Codex + Gemini）
    - ✅ 共識：確定性執行 vs 機率性自律、三層架構對個人/小團隊足夠、Prompt/Agent Handler 作為語意過濾器

- [x] W-2026-08-052 圓桌會議：Claude vs Gemini vs ChatGPT 前端全鏈路能力比較 ✅ #ai-agent #tools
  - completed: 2026-08-17
  - refs: [[wiki/sources/2026-08-17-frontend-ai-roundtable|圓桌會議紀要]]、[[wiki/concepts/contract-driven-development|Contract-Driven Development]]、[[wiki/concepts/context-decay|Context Decay]]
  - 已完成：
    - ✅ 3 輪圓桌會議（Claude + Gemini + ChatGPT/Codex）
    - ✅ 核心結論：契約驅動的三核心分工（Gemini 視覺 → Claude 架構 → Codex 交付）
    - ✅ 5 大共識：契約驅動、脈絡衰減、編排成本、rejected_alternatives、不要單模型全包
    - ✅ wiki 新增 1 source + 2 concepts
    - ✅ 會議紀錄存入 .pi/round-table/20260817-211224/

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
