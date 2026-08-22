# Current Work

> 唯一的工作狀態入口。完成事件寫入 `work/history/YYYY-MM.md`。
> 每個工作項目都要有穩定 ID、下一步與至少一個 reference。

## In progress

- [ ] W-2026-08-074 個人 AI 助理架構願景：最小規格 + 垂直切片 🆕 #ai-agent #architecture
  - next: 圓桌會議（Claude+Gemini+Codex）已於 2026-08-22 完成第一輪並收斂共識（見 refs 會議紀要），下一步是實際動筆產出**最小規格**（不是完整規格文件）：① 核心=控制平面（無狀態、管流程/政策/派工/log，不管內容）vs Plugin=能力平面（標準插頭介面）的邊界文件 ② log schema 先定 MUST 欄位（trace_id/span_id/parent_span_id/timestamp/actor/event_type/status/latency_ms/error，不含敏感內容）即可，SHOULD 欄位（完整 input/output payload/token_usage/cost_estimate）待遮罩規則定案後再開 ③ orchestrator 派工邏輯直接把 round-table 既有角色分工（Claude=架構/Gemini=研究/Codex=工程/Pi=本機自動化）正式化，不重新設計 ④ stateless+記憶檢索直接把 work-tracker 現有「session 啟動讀 work/current.md」模式當雛形擴充，記憶分 Episodic/Semantic/Procedural/Artifact 四類 ⑤ 多 harness 調用包成統一 Agent Provider Adapter，記錄 provider/harness/model/輸入輸出reference/成本/驗證結果。**最小規格出爐後立刻做一條垂直切片**——優先選搜尋能力，跑通一次「輸入→派工→Tool呼叫→驗證→Log→回覆」的完整路徑，驗證規格可行再擴大，不要 5 大維度全部寫到完整才動手
  - **訂正（8/22）**：[[work/current#W-2026-08-017|W-017]]／[[work/current#W-2026-08-025|W-025]]／[[work/current#W-2026-08-022|W-022]]／[[work/current#W-2026-08-033|W-033]]／W-2026-08-NEW-001／W-2026-08-NEW-002 這 6 個項目**目前都還沒開始做**（next 都只是「調研 XXX」的待辦，不是已完成的研究產出）。W-074 不是拿它們的成果來整合，而是先產出架構規格，讓這 6 個原本各自零散、範圍模糊的研究待辦，之後有明確的規格可以對齊、重新框定範圍（部分可能因此被合併或縮小，不用再各自從零摸索方向）
  - refs: [[raw/conversations/2026-08-22-personal-ai-assistant-architecture-vision|Cheer 架構願景原始想法]]、[[.pi/round-table/20260822-161304/synthesis|2026-08-22 圓桌會議紀要]]、[[work/current#W-2026-08-017|W-017]]、[[work/current#W-2026-08-025|W-025]]、[[work/current#W-2026-08-022|W-022]]、[[work/current#W-2026-08-033|W-033]]
  - 起因：2026-08-22 討論工作優先順序時，Cheer 提出完整的個人 AI 助理架構願景（核心/plugin 分離、自我成長觀測、單一對口統籌、stateless+記憶檢索、多 harness 外部 agent 調用），並點名「搜尋能力」是當前最迫切的 plugin（Pi 無 Deep Research、無法跟 Gemini 交互、Gemini 品質下降）
  - 無相依，可跟 W-072 平行進行（三方圓桌共識：不必等 W-072/W-057 做完才開始）

- [x] W-2026-08-076 建立 cheerio-roadmap skill：地鐵路線圖工作進度視覺化 ✅ #ai-agent #visualization #skill
  - completed: 2026-08-22
  - next: ✅ 已完成並推上 cheerio-skills repo。之後只要問工作進度/更新 work/current.md 就會自動重繪，不需要額外動作
  - refs: [Cheerio 路線圖 Artifact](https://claude.ai/code/artifact/d9d83769-d2f7-4a44-b660-bebe3838f30c)、`C:\Users\User\.claude\skills\cheerio-roadmap\SKILL.md`、[[work/current#W-2026-08-075|W-075]]（未來 DevOps 版重用同一套設計系統）
  - 起因：跟 Cheer 討論完 W-074/W-057/W-072 排序後，Cheer 希望有「鐵路線」風格的視覺化呈現整體 roadmap，並要求以後每次問工作進度或更新 work 都要自動看到
  - 已完成：
    - ✅ 地鐵路線圖風格 Artifact：4 條軌道（知識花園/基礎設施/企業 Skill 自動化/個人 AI 助理），站點依狀態分四種樣式 + 獨立決策菱形，今天虛線標實際日期
    - ✅ 依 Cheer 逐輪回饋迭代：修正過大的黑色箭頭 marker（改用 `markerUnits="userSpaceOnUse"`、每軌道各自顏色）→ 加大字體＋固定原生寬度＋zoom 工具列（解決字太小看不清楚）→ 點站點改成可拖移、指向該站點的浮動 popover（`position:fixed`，不怕被頁面捲動吃掉）→ popover 加「Read more →」導到下方對應卡片並閃爍高亮 → 閃爍高亮加上背景色（原本只有邊框不夠明顯）
    - ✅ 建立 `cheerio-roadmap` skill 並接進 `work-tracker` 的啟動流程與非啟動時的觸發規則；`state.json` 存 Artifact 連結供每次更新同一個網址；存進我自己的記憶（feedback + reference 兩則）確保未來 session 記得
    - ✅ 資料驅動重構：原本 SVG 站點／彈出卡片 `DETAILS`／下方卡片區是三份手寫重複資料，容易漏改導致不同步。派背景 agent 在複本上重構成單一 `ROADMAP` 資料物件驅動三處渲染，完工後用腳本讓瀏覽器**真的依序點過全部 22 個站點的 Read More**驗證每個都導到正確卡片，確認無誤才覆蓋正式版並重新發布
    - ✅ 推上 `CheerioCorner/cheerio-skills` GitHub repo（commit `64eff50` 建立 skill、`608f5da` 資料驅動重構），公司電腦如果也有 `C:/Cheerio` vault 可用 `npx skills add CheerioCorner/cheerio-skills@cheerio-roadmap -g -a pi` 拉取
  - 備註：公司端工作追蹤是另一套系統（Azure DevOps，工作追蹤+工時統分），本 skill 只管 Cheerio 個人知識系統；未來重用同一套設計系統給公司端的規劃記在 [[work/current#W-2026-08-075|W-075]]
  - **⚠️ 事後訂正（8/22 同日）**：完成當下把重繪流程設計錯了——直接覆寫要同步進共用 `cheerio-skills` repo 的 `references/template.html`，導致 Cheer 真實工作內容（含洩漏雇主名稱）被推上 GitHub 兩次。另一 session（CheerCopilot）發現並修正架構，拆成「共用通用範本」（skill 資料夾內，不含真實資料）與「本機真實輸出」（`Obsidian/work/roadmap/cheerio-roadmap.html`，不進版控）兩份檔案；Claude 核對確認安全後推上 sanitize commit（`5232bbe`）。**git 歷史裡舊 commit（`64eff50`/`608f5da`）仍含洩漏內容，是否要 rewrite history 徹底清掉待 Cheer 決定。**詳見 work/history 同日事件

- [ ] W-2026-08-073 IBM Skill 最佳實踐影片心得補寫進花園《Skill》專題頁 🆕 #knowledge #skill #notion #youtube
  - next: Cheer 先看措辭草稿再決定要不要寫。若要寫，依既有流程先開圓桌會議（Claude+Gemini），寫入 Notion 後再雙模型覆核品質。定位角度：Topic 2/3 的空白是「組織治理層」（現有：怎麼審查才能部署）之外缺「個人習慣層」（使用者動筆寫/要用一支 skill 前自己該負的責任），建議用這個角度切入補寫，不要逐條加句子。
  - refs: https://app.notion.com/p/Agent-Skills-3bc5979e3a8c8121924ef99b09671383（🔬 Agent Skills 專題頁，補寫目標）、https://youtu.be/qYNs80FKIVc?si=ONtRQtqXU73YXi8K（來源影片）、[[raw/notion-ingest/2026-09-01-notion-agent-skills-fulltext-raw|Notion 頁面全文本機備份]]、[[wiki/sources/2026-09-01-ibm-youtube-skill-best-practices-supplement-draft|本機 wiki 草稿（非最終目標，可能重寫或棄用）]]
  - 已完成的前置比對（Pi 比對 + Claude 逐字核對驗證，結論可信）：
    - Topic 1（觸發精準度）：Notion 頁面已完整涵蓋（Description 撰寫技巧、eval queries 迭代流程、Model/User-invoked 分類），**不需要補**
    - Topic 2（SKILL 專業知識要來自實戰、不能是 AI 生成；500行/5000字建議）：頁面**沒有**這個論點，真空白——唯一提到「1500行」是講 Pi Agent 核心大小，語境不同
    - Topic 3（Script 要具體化不要讓AI亂猜；使用前掃描避免惡意軟體；使用前理解資源存取權限）：頁面第六章只有「組織部署前審查」（8步審查清單/5級風險評估），缺「個人使用者每次要用一支 skill 前」的習慣層面，真空白
  - 狀態：2026-08-21 暫停（Cheer 太累），2026-08-22 接續

- [ ] W-2026-08-072 AI 模型/Host 可用性登記表 🆕 #tools #ai-agent #mcp
  - next: 明天直接開始實作。先觀察各家 CLI 的真實限流錯誤訊息格式，再開發 `lib/availability.mjs` 共用模組 + 四個 bridge 的前置檢查邏輯
  - refs: [[work/current#W-2026-08-068|W-2026-08-068]]（agy-bridge 問題觸發本設計）
  - 起因：2026-08-21 處理 agy-bridge CANCELED 問題時，Cheer 聯想到更通用的問題——AI 模型/服務都有 Token 額度或限流上限，當某個 model/host 額度用盡時，我們應該記錄「什麼時候解封」，讓之後呼叫這些工具的 AI（Claude、Pi 等）可以提前知道「現在這個不能用」，不用白白浪費呼叫
  - **設計定案（8/21 Cheer 討論拍板，直接照做）**：
    - **共用登記表**：`Claude/mcp-bridges/state/availability.json`，JSON 格式記錄每個 model/host 的封鎖狀態（blocked_until / reason / confidence / recorded_by / recorded_at）
    - **三種記錄來源**（由 `confidence` 欄位區分）：
      - `exact`：supplier 錯誤訊息有明確解封時間（retry-after header、rate limit reset）。照時間走，時間到自動解封
      - `estimated`：限流但沒給明確時間。預設封鎖 1 小時；1 小時後允許探針重試一次——成功就 `clearBlocked`，失敗就延長再封 1 小時（成本低，每小時最多浪費一次真實請求）
      - `human-reported`：人類主動告知（查面板知道確切時間）。完全不做探針，當權威資訊封鎖到底。不需額外工具——使用者跟任何 AI 口頭講「某 model 被鎖到幾點」，AI 手動編輯 JSON 寫入
    - **實作範圍**（四個 bridge 都要改：agy / pi / codex / copilot）：
      1. `lib/availability.mjs` 共用模組：`checkAvailability(name)` / `recordBlocked(name, opts)` / `clearBlocked(name)`
      2. 四個 bridge 的工具呼叫 CLI **之前**先 `checkAvailability()`：封鎖中→回傳清楚錯誤（不 spawn CLI）；`estimated` 已過 1 小時→允許探針
      3. 四個 bridge 呼叫 CLI **之後**偵測限流訊號（429、quota exceeded、rate limit 等）→ `recordBlocked()`（有明確時間用 `exact`，沒有用 `estimated`）
    - **待確認細節**（明天實作時觀察）：各家 CLI 實際限流錯誤訊息格式、`estimated` 預設 1 小時是否合理（先用固定值，看實際情況調整）
  - **明天接續實作，不需重新設計**

- [x] W-2026-08-067 YouTube ingest：Understanding AI Infrastructure — GPUs, vLLM, K8s ✅ #knowledge #ai-agent #youtube
  - completed: 2026-08-21
  - next: ✅ 已完成並 push（commit 3eec926）。NPU 討論頁後續由 W-2026-08-069 接續。
  - refs: [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Source Note]]、[[wiki/concepts/gpu-architecture-for-ai-inference|GPU 架構]]、[[wiki/concepts/llm-serving-architecture|LLM Serving 架構]]、[[wiki/concepts/model-sharding|Model Sharding]]、[[wiki/entities/vllm|vLLM]]、[[wiki/entities/llm-d|llm-d]]、[[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]
  - 已完成：
    - ✅ 抓字幕 → 建 source note + 3 concepts + 2 entities + 1 discussion
    - ✅ 更新 index / log / topics 導航
    - ✅ git commit + push（3eec926），Claude 核對檔案存在與 git 同步確認無誤
    - ✅ agy-bridge 品管失敗後，Claude 改為人工核對 GPU 規格表（A100/H100/H200/B200 TFLOPS/VRAM/Bandwidth），數字與公開規格相符
  - 備註：agy-bridge 連續 4 次 CANCELED 見 [[work/current#W-2026-08-068|W-2026-08-068]]；NPU 研究見 [[work/current#W-2026-08-069|W-2026-08-069]]；引用驗證流程問題見 [[work/current#W-2026-08-070|W-2026-08-070]]

- [ ] W-2026-08-069 NPU 角色深度研究（Gemini research）🔄 #knowledge #ai-agent #research
  - next: 等 Gemini chat-with-gemini-research 完成後，將研究結果回填進 [[wiki/discussions/npu-role-in-ai-infrastructure]]，補充引用來源
  - refs: [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]、[[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|AI Infrastructure Source Note]]
  - 起因：Cheer 看完影片後提出開放問題「NPU 在 AI 基礎設施架構中扮演什麼角色、為什麼 AI 時代需要 NPU」，已標記在 discussion 頁，正在派 Gemini 做深度研究（有引用來源要求）



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
  - **圓桌共識（8/22）**：已卡 3 天，Codex 明確主張「立即 timebox 解 blocker，不能再用等更多研究延長」——今天直接列出卡住的具體假設，逐項指定決策人與截止時間，先接受最小方案，非必要爭議記成後續決策待議即可，不要再展開。見 [[.pi/round-table/20260822-161304/synthesis|會議紀要]]
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
  - refs: [[wiki/entities/pi-mono|pi-mono]]、[[wiki/concepts/meta-harness|meta-harness]]、[[work/current#W-2026-08-074|W-074]]（架構規格出爐後，本項目即為 Plugin (a) 搜尋能力的落地任務）
  - 預估時間：2-3 小時
  - 為什麼重要：Tool 系統是 Agent 的手腳，怎麼擴充功能。2026-08-22 Cheer 點名這是目前最迫切的 plugin（Pi 無 Deep Research、無法跟 Gemini 交互、Gemini 品質下降）

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

## Blocked

## Backlog

- [ ] W-2026-08-075 未來：抽出「地鐵路線圖」設計系統給公司 Azure DevOps Analysis Report skill 重用 🆕 #ai-agent #devops #visualization
  - next: 等公司端 Azure DevOps work item/工時 API 可接（或決定用哪種方式讀取）之後，新建 `devops-analysis-report` skill，資料源換成 Azure DevOps，但 [[cheerio-roadmap 設計系統|C:\Users\User\.claude\skills\cheerio-roadmap\SKILL.md]] 裡的 CSS/JS（地鐵路線圖 SVG、可拖移浮動 popover、Read more 導頁高亮、zoom 工具列）直接搬過去重用，不重新設計
  - refs: [[work/current#W-2026-08-074|W-074]]（同一批討論脈絡）、C:\Users\User\.claude\skills\cheerio-roadmap\SKILL.md
  - 起因：2026-08-22 Cheer 看完 Cheerio 路線圖後指出，公司端工作追蹤是另一套系統（之前討論過的 Azure DevOps Skill，工作追蹤+工時統分），跟這裡的 work/current.md 是不同資料源，但視覺設計值得原封不動搬過去，只是 skill 名稱跟資料源要換
  - 無相依，可任何 session 切入（但實際上要等 Azure DevOps 那端條件成熟才會真的動手）

## Completed

- [x] W-2026-08-078 cheerio-skills git 歷史清除：拿掉舊 commit 裡洩漏的真實工作資料 ✅ #tools #bug #security
  - completed: 2026-08-22
  - next: ✅ 已完成。之後同步進共用 repo 前會先親自核對內容（見 [[verify-shared-repo-content-before-push]]），不會再犯
  - refs: [[work/current#W-2026-08-076|W-2026-08-076]]（事件本身）、[[verify-shared-repo-content-before-push]]
  - 已完成：
    - ✅ fetch 確認遠端沒有其他 session 的新 commit，避免 force-push 蓋掉別人的工作
    - ✅ `git reset --soft` 到洩漏前的 parent commit，用當下已乾淨的 tree 重新 commit 成單一 commit（`a923048`），取代原本 3 個 commit（`64eff50`／`608f5da`／`5232bbe`）
    - ✅ push 前 diff 新舊 tree 確認內容完全一致（只有歷史被改寫，內容沒變），grep 確認新 commit 內容零洩漏
    - ✅ `git push --force-with-lease` 推上 GitHub，push 後重新 fetch + `git ls-remote` 核對遠端 `master` 只剩乾淨的新 commit，舊 SHA 在遠端已不可達
    - ✅ 清掉本機的備份 tag、跑 `git gc --prune=now` 讓舊 commit 在本機也徹底不可達
  - 備註：GitHub 後台可能仍短暫保留已不可達的物件（一般 force-push 後的標準行為，非 100% 立即從 GitHub 基礎設施抹除），但一般瀏覽/clone/API 已經看不到洩漏內容

- [x] W-2026-08-077 Mem0 深度研究：跟其他系統比較／生產限制／Decision-Ledger 適用性／授權定價 ✅ #knowledge #ai-agent #research
  - completed: 2026-08-22
  - next: ✅ 已完成並 ingest 進 wiki。生產限制角度仍是知識缺口，之後有真實來源時可回頭補
  - refs: [[wiki/sources/2026-08-22-mem0-deep-research-comparison|Mem0 深度研究 source note]]、[[wiki/discussions/mem0-vs-decision-ledger-for-w074|Mem0 vs Decision-Ledger 適用性]]、[[wiki/entities/mem0|Mem0 entity 頁面]]、`raw/research/2026-08-22-mem0-deep-research-verified.md`（查證版）、`raw/research/2026-08-22-mem0-deep-research-raw.md`（Gemini 未查證原始輸出，僅供對照）
  - 起因：W-070 修好後的第一次實測。Cheer 指定研究 Mem0 補既有 YouTube ingest 沒涵蓋的角度，並要求「Gemini 查完 Claude 過濾品質再讓 Pi 寫進大腦」的分工流程
  - 已完成：
    - ✅ Gemini（agy）深度研究四角度：跟 Zep/MemGPT/LangMem 比較、生產環境限制、對 W-074 decision-ledger 記憶架構的適用性、授權定價
    - ✅ Claude 用新修好的 W-070 流程逐一 WebFetch 查證 10 個 Tier 1/2 來源：2 個完全正確、3 個真實頁面但文不對題（已找到真正來源替換：Zep 論文、Graph Memory 移除官方頁、LangMem repo）、4 個完全捏造（404 或文不對題，來源移除、陳述降級 [UNVERIFIED]）
    - ✅ 關鍵發現：Gemini 原始報告中「Mem0 vs Decision-Ledger」整個論點唯一引用的兩個來源（zenml.io、vktr.com）都是捏造的 404 網址；改用本機既有的 Sakana AI 來源筆記支撐同一論點，結論本身站得住腳
    - ✅ 發現並修正 Gemini 捏造的「Growth $79/月」定價方案（實際只有 Hobby/Starter/Pro/Enterprise 四個）
    - ✅ Pi 依查證版報告寫入 wiki：新建 1 個 source note + 1 個 discussion 頁面，更新 entity 頁面（mem0.md）、topic 頁面、index.md、log.md
    - ✅ Claude 逐檔讀取核對 Pi 自報的 6 項改動全部屬實；額外抓到 Pi 在比較表自行加了一列未查證的「授權」比較（Zep/MemGPT/LangMem 各自授權），事後用 WebFetch 補查證，內容正確但已加註來源說明
  - 備註：這是 W-070 新流程的首次實戰驗證——證實「格式檢查防不住捏造」的診斷正確，光靠 skill 指示 Gemini 小心不夠，一定要呼叫端親自查

- [x] W-2026-08-070 chat-with-gemini-research 引用驗證流程修復 ✅ #skill #tools #bug
  - completed: 2026-08-22
  - next: ✅ 已修復（Claude 直接修）。下次執行 chat-with-gemini-research 時要照新流程實測：Gemini 產出後由呼叫端親自 WebFetch 查證每個 Tier 1/2 來源
  - refs: `C:\Users\User\.claude\skills\chat-with-gemini-research\SKILL.md`（Step 4 改動）、[[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]（commit 3910322，觸發本次問題的原始事件）、[[work/current#W-2026-08-069|W-2026-08-069]]
  - 已完成：
    - ✅ Step 4 新增強制 URL 實查步驟：呼叫端用 WebFetch 對每個 Tier 1/2 來源實際發送請求，確認可訪問且內容與被引用陳述相符；查證失敗降級 [UNVERIFIED] 並移出正式來源列表
    - ✅ Raw Research frontmatter 新增 `citations_verified` / `verified_by` 欄位，標明報告引用已實查
    - ✅ 品質檢查清單新增「URL 實查」小節，明確寫「呼叫端親自做，不能省略」，防止查證責任又被委派回 Gemini
  - 備註：尚未實跑驗證新流程（下一個研究任務即 Mem0 深度研究會是第一次實測）

- [x] W-2026-08-068 agy-bridge 異常追蹤：headless 模式讀檔被 CANCELED/ERROR ✅ #tools #bug
  - completed: 2026-08-21
  - next: ✅ 已修復（Codex 修、Claude 驗證）
  - refs: [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|AI Infrastructure Source Note]]、[[work/current#W-2026-08-070|W-2026-08-070]]、[[work/current#W-2026-08-071|W-2026-08-071]]
  - 已完成：
    - ✅ Codex 修復 `buildAgyArgs()` 預設加上 `--sandbox` 和 `--dangerously-skip-permissions`（除非呼叫端明確傳 false）
    - ✅ Claude 逐項查證：讀檔確認改動、node --check 語法、5 次真實 agy.exe 測試全 SUCCESS（exitCode 0、無 CANCELED/ERROR）
    - ⚠️ MCP server 是長駐進程，需 session 重啟/MCP 重新連線才會載入新版程式碼
    - ⚠️ `C:/Cheerio` 非 git repo，修改直接存在磁碟上，無需 commit

- [x] W-2026-08-071 agy-bridge headless 權限修復方案評估與實作 ✅ #tools #bug
  - completed: 2026-08-21
  - next: ✅ 已修復（Codex 修、Claude 驗證）
  - refs: [[work/current#W-2026-08-068|W-2026-08-068]]、`Claude/mcp-bridges/lib/agy.mjs`、`Claude/mcp-bridges/src/agy-bridge.mjs`
  - 已完成：
    - ✅ Codex 修改兩個檔案：`buildAgyArgs()` 預設加 sandbox + dangerously-skip-permissions；`ask_agy` description 更新；`runAgy()` 的 `stderr` 截斷到 2000 字元寫入 audit log（之前完全沒用到）
    - ✅ Claude 逐項查證：讀檔 → node --check → 5 次 agy.exe 實測全 SUCCESS
    - ✅ Codex 第一次因 Claude cwd 設錯導致 sandbox 擋寫入、誠實回報失敗；修正後第二次順利完成（非 Codex 過錯）
    - ⚠️ MCP server 長駐進程需重啟才載入新版；本 repo 非 git repo 無需 commit
  - **後續修復（8/21 同日，commit + push 到 cheerio-mcp-bridges）**：
    - 背景：四個 MCP bridge（agy/pi/codex/copilot）的 `*_BRIDGE_CWD` 環境變數都有寫死的個人路徑 fallback（例如 `process.env.AGY_BRIDGE_CWD || "C:/Cheerio"`，`pi.mjs` 甚至是過時的 `"C:/Cheerio/pi"`）；`*_BRIDGE_ENTRY` 也有同樣問題。同事用不同 Windows 使用者名稱的電腦時，寫死路徑完全不對且不會報錯——會悄悄跑錯目錄。
    - ✅ commit `410156e`：`lib/run.mjs` 新增共用 `requireEnv(name)` helper，沒設環境變數就拋出清楚錯誤訊息（講明要去 `.mcp.json` 設定）。四個 bridge 的 `*_CWD` 全部改用 helper，拿掉寫死個人路徑 fallback
    - ✅ commit `5b74fa8`：`*_ENTRY` 同樣改用 requireEnv（原考慮退回 PATH 自動偵測，但 `pi` 是 `node <路徑>` 方式呼叫、`copilot` 預設 `.cmd` 在 `shell:false` 下無法裸指令解析，與其每個工具各寫一套偵測邏輯，乾脆一律要求明確設定、沒設就報錯）
    - ✅ 5 個修改過的檔案全部過 `node --check` 語法檢查；沒設環境變數會拋出清楚錯誤、設了正常運作；每次改完都重新用真實 agy.exe 跑 3 次讀檔驗證全 SUCCESS，確認沒把先前修好的 CANCELED/ERROR 改回去
    - 影響：`.mcp.json` 已有正確設定，本次修改對現行運作無影響（no-op），純防呆——以後複製設定給同事或漏設變數時會立刻在 MCP 連線狀態看到明確錯誤

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
