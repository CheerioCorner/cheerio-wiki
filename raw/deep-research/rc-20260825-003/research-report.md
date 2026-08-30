# Deep Research 報告 — rc-20260825-003

- 研究主題：Multi-agent orchestration 設計模式深度研究：常駐背景（persistent background）、語義狀態偵測（semantic state detection）、原子化 prompt（atomic prompt）三大模式的實作原理、工具比較（Herdr / Pi / Claude Code / Copilot SDK / LangGraph）及對 Cheerio 助理的應用建議
- Notebook：https://notebooklm.google.com/notebook/2e0b821d-a22b-458c-86b6-7ebeb1f9d26f
- 產生時間：2026-08-30T10:30:53.989Z
- 品質過濾：74 → 65 筆（移除 2 筆）
- 來源分類/重新命名：65 / 65 筆成功

## 研究結果

### 1. 常駐背景（persistent background）模式如何實作？哪些工具支援？對 agent 容錯有什麼幫助？

將**常駐背景（Persistent Background）**、**語義狀態偵測（Semantic State Detection）**與**原子化 Prompt（Atomic Prompt）**三大編排模式落地於 **Cheerio 助理**的多 Agent 協同設計，能徹底解決傳統爬蟲助理常見的「脆弱 CSS 選擇器失效（Selector Drift）」[15]、「遭遇 CAPTCHA 阻擋陷入死循環」[11, 53] 以及「長工期爬取中斷前功盡棄」[58] 等痛點。

以下為 Cheerio 助理多 Agent 協作架構的具體應用與設計建議：

---

### 一、 基於「原子化 Prompt 與物理隔離」的並行解析與代碼生成

Cheerio 最大的優勢在於**極高的 HTML 解析速度與輕量級記憶體佔用** [14, 17, 54]，但隨著網站結構的複雜化，讓單一 Agent 處理整條爬取管道容易造成 Prompt 膨脹與競態衝突。

*   **原子化職責拆解（Micro-Scrapers Task Allocation）**：
    將協作架構拆解為三個高度原子化的 Agent 角色，彼此不共享對話歷史（Context Isolation）以節省 Token [9, 22]：
    1.  **結構分析師（Structure Scout）**：負責獲取目標網頁 HTML [45, 60]，利用 LLM 語義解析（LLM-Driven HTML Parsing）定位目標數據的 DOM 結構或 JSON-LD 區塊 [15, 45]，輸出簡潔的數據欄位對照清單。
    2.  **腳本工程師（Cheerio Writer）**：接收結構分析師的原子 Prompt 指令，**專注且唯一地編寫並測試單一網頁的 Cheerio 提取代碼** [14, 52]。
    3.  **管道整合器（Pipeline Merger）**：負責管理任務進度 [59]。
*   **Git Worktree 與檔案鎖雙重隔離機制**：
    當需要同時爬取或分析多個不同版面結構的子頁面時，主協調器（Orchestrator）應效仿 **Herdr 的 Worktrees 模式** [50]，在臨時的隔離 Git Worktree 或獨立的 scratch 工作區中分派複數個「腳本工程師」Agent [50]。在寫入最終的 `scrapers.js` 設定檔時，必須引用**檔案寫入鎖（File Locking）** [3, 10]，確保代碼生成時不會產生 Race Condition。

---

### 二、 基於「語義狀態偵測」的動態「輕/重型爬蟲切換」與防死循環策略

Cheerio 助理在面對動態渲染（SPA）或設有嚴格 Cloudflare 防禦、CAPTCHA 的網站時極易觸發 Blocked [1, 54]。系統必須具備敏銳的狀態偵測與避障機制。

*   **建立「阻礙（Blocked）」與「無效動作」狀態偵測鏈**：
    實作 **TSR（任務狀態表示法）** 的思想 [64]。當 Cheerio 助理下載網頁後，外部的狀態更新器（State Updater）立即解析 HTML 狀態碼或 DOM 樹 [11, 64]。
    *   **Blocked 偵測**：若 HTML 內含有 `cloudflare`、`security checkpoint`、`429 Too Many Requests` 或 `captcha` 等特定關鍵字 [11, 53]，或畫面截圖比對後判定動作無效 [64]，立即向 EventBus 拋出 `state: blocked` 信號 [47]，中斷當前的 Cheerio 提取。
*   **動態降級與路由（Fallback to Heavy Crawler）**：
    一旦 Cheerio 偵測到 `blocked` 狀態，Orchestrator 應暫停該 Cheerio 任務，並利用 **Stagehand v4** [1] 或 **Playwright** [54] 等重量級瀏覽器 Agent 作為備用路徑（Fallback Route），接管該 PTY 終端或工作流 [26, 47]，待通過驗證碼、獲取靜態 HTML 後，再將數據快照（Snapshot）返還給輕量級的 Cheerio 助理進行後續的高速 DOM 遍歷與清洗 [14, 45]，實現「**輕重配合，極速容錯**」。
*   **Compaction 門檻防範記憶體崩潰**：
    Cheerio 下載的原始 HTML 文本動輒數萬行，直接塞入 LLM 進行語義分析會迅速壓迫 Context Window 臨界線 [9, 15]。助理必須配置如 Claude Code 的 **Compaction Gates 機制** [9]：當累積導入的網頁源碼 Token 達到限制的 75% 時，非同步觸發壓縮過濾 Agent [9, 22]，將 HTML 徹底精簡為只剩有價值的 CSS 樹和 metadata（如 `cheerio.load(html)` 後僅保留特定的 article/div 節點） [14, 18]，之後才允許下一個 Crawl 節點的 Spawning [9]。

---

### 三、 基於「常駐背景與 Checkpointing」的長工期非同步爬取與中斷重啟

當面對需要爬取成千上萬個網址（例如電商全站目錄或新聞封存庫）的長工期任務時，網路波動或 API 限制是不可避免的常態 [11, 58]。

*   **利用圖狀態機 Checkpointer 實作斷點續爬（Checkpoint & Resume）**：
    切忌將爬蟲任務設計為單一的線性 Script。建議採用 **LangGraph 狀態圖** 搭配 **`AsyncPostgresSaver`** 的架構 [36, 37]。
    *   將每一筆待爬取的 URL 與其對應的 Cheerio 提取代碼視為圖中的一個原子節點 [37]。
    *   每成功爬取並儲存一個頁面，系統在背景自動向數據庫提交一次 Checkpoint（包括 `thread_id`、`scraped_urls`、`extracted_json`） [35, 36]。
    *   即使背景進程因 API 逾時、設備斷電而中斷，重新啟動後，Agent 能讀取資料庫狀態並直接從未爬取的 URL 列表繼續推進 [37]，**保證節點執行的冪等性（Idempotency），避免耗費 Token 重複爬取已完成網頁** [37]。
*   **Herdr 終端託管下的「人機協同審核（HITL）」**：
    將 Cheerio 助理部署在 **Herdr 背景 Daemon 伺服器**中 [26]。當助理在背景連續解析出錯（例如因網站改版導致 Cheerio Selector 解析出的 JSON 欄位全為 `null` [15, 52]），助理會自動將狀態標記為 `blocked` 並掛起（Pause）進程 [26, 47]。此時，人類工程師可以透過 `herdr --remote` 連線接管該 Pane [28, 29]，直接在終端內對 Cheerio 提取器進行手動代碼修正（Hot-fix），確認無誤後輸入指令，背景 Agent 隨即無縫還原（Resume）並繼續執行後續數百個頁面的非同步爬取 [26, 47]。

---

🧭 這套將常駐背景、狀態偵測與原子化 Prompt 融入 Cheerio 助理的架構，能最大程度發揮 Cheerio 的效能優勢並克服其靜態解析的侷限性。**您是否希望我為您將這個架構設計撰寫成一份結構完整的「企業級多 Agent 容錯爬蟲系統設計報告（Tailored Report）」並儲存在您的 Studio 面板中，以便您後續開發參考呢？**

### 2. 語義狀態偵測（semantic state detection）如何實現 blocked/working/done 的精準判斷？各工具的偵測策略差異？

在多 Agent 編排（Orchestration）中，**語義狀態偵測（Semantic State Detection）**是避免長工期（Long-Horizon）任務因網路波動、API 限制或環境延遲而崩潰，進而實現「高容錯、非阻塞、自主協同」的核心技術。

以下深度解析此模式如何精準判定 **`blocked`（受阻）**、**`working`（執行中）**、**`done`（完成）**，以及主流工具的偵測策略與實作差異。

---

### 一、 語義狀態偵測的四種實作機制

為了解決大語言模型（LLM）無狀態的缺陷，避免 Agent 在任務進度中產生**目標漂移（Goal Drift）**、**進度幻覺（Progress Hallucination）**或**面臨延遲網頁時盲目重複無效動作（Stale-screen repetition）**，技術界主要透過以下四種機制實現狀態判斷 [1, 2]：

#### 1. 視覺與狀態解耦比對模式（Task-State Representation, TSR）
此模式將「持久任務狀態」與「瞬時畫面觀測」徹底解耦，在 Agent 外圍包覆一個 **State Updater（狀態更新器）** [3, 4]。
*   **精準判定實作**：在每一次動作前後（\\(o_{t-1}\\) 與 \\(o_t\\)），Updater 會藉由一筆 Prompted MLLM 呼叫進行比對 [4-6]。
    *   **Working/Done 判定**：更新器維護一個結構化的 JSON 進度追蹤器（Progress Tracker），包含`任務分解`、`已完成里程碑`、`當前子目標`、`剩餘需求`等欄位，藉此防止進度幻覺或提早結束 [7, 8]。
    *   **Blocked 判定**：若環境畫面未更新，更新器比對截圖後會將 `action_effective` 判定為 `false`，並在 `next_action_focus` 欄位寫入引導（如要求刷新、驗證狀態），**主動阻斷 Agent 陷入死循環** [8-10]。

#### 2. 底層虛擬終端（PTY）緩衝區匹配
針對本身不具備主動報告機制或無法修改源碼的 CLI 助理（例如 Claude Code、Codex 等），平台層透過讀取終端文字來推估 [11, 12]。
*   **精準判定實作**：
    *   **Working 判定**：讀取虛擬終端（PTY）底部的螢幕快照緩衝區（非 Scrolled Viewport），並比對 OSC 進度序列或終端標題（OSC 0/2）來推估活動狀態 [13, 14]。
    *   **Blocked 判定**：**採用極為嚴格的匹配策略**。系統讀取 TOML 格式的偵測規則文件（Screen Manifests），只有當螢幕底部的 ANSI 文本完全匹配已知的審批、問題或許可 UI（例如 Claude Code 的交互式輸入提示）時，才會判定為 `blocked`；無規則匹配時，預設 fallback 為 `idle`，以防發送盲目 keystroke 造成破壞 [13, 15, 16]。
    *   **Done 判定**：當背景工作執行完畢回到 PTY Shell 提示符時，標記為 `done`，直到使用者聚焦該 Tab/Pane，才回到 `idle` [17]。

#### 3. 程序級生命週期鉤子與主動自我宣告（Self-Reporting Hooks）
最精準、最無誤差的判定，是由 Agent 程序直接在內部的生命週期節點主動申報 [18, 19]。
*   **精準判定實作**：
    *   **Working/Idle 判定**：程式碼在關鍵節點（Lifecycle Hooks）上寫入宣告。例如：開始呼叫 API 或執行工具（`agent_start`）時向 Socket 拋出 `working`；回到等待輸入狀態（`agent_settled`）時拋出 `idle` [18]。
    *   **Blocked 判定**：當程序內部調用 `ask_user` 等阻礙組件時，EventBus 立即攔截此事件，並向外部協調器拋出 `report-agent --state blocked` 主動申報 [18]。

#### 4. 圖形編排狀態機與資料庫 Checkpointing
這是不涉及終端外殼、純邏輯與程式級的狀態管理 [20, 21]。
*   **精準判定實作**：
    *   **Working/Done 判定**：將狀態（State）定義為圖形節點執行的轉移邊（Edges），當 State 流傳播至 `END` 節點即判定為 Done [22, 23]。
    *   **Blocked 判定**：在圖編譯時配置 **Interrupts（中斷點）** [24]。當執行流到達中斷邊時，圖形執行緒自動掛起，將 Graph State 序列化寫入持久化資料庫（如 PostgreSQL），執行緒轉為 Sleep 釋放運算資源，直到人類更新 State 或發送 Resume 信號 [20, 25-27]。

---

### 二、 主流工具的偵測策略差異

這五款工具在狀態偵測的架構與定位上存在著根本性的差異：

#### 1. Herdr
*   **策略：終端級 Screen Inference 與 Status Authority Chain** [12, 19]
*   **偵測實作**：優先使用 installed hooks（如 Pi, OMP 的 Hook 報告）[19]。針對無完整 Hook 支援的 CLI，則藉由 PTY 底部的 recent buffer 快照，對照 TOML 格式的 `agent-detection` 規則文件進行啟發式匹配 [13, 16]。當遇到未知 prompts 時，Herdr 會因「Blocked 判定刻意從嚴」而 fallback 至 idle，主動防止 Orchestrator 進行盲目且破壞性的 keystroke 寫入 [15]。其 `explain` 輸出可提供極具細節的規則匹配與 matched 證據 [28]。

#### 2. Pi (pi-herdr-agents)
*   **策略：程序內部主動自我宣告與微觀生命週期追蹤** [18, 29]
*   **偵測實作**：Pi 本身不推估 PTY 像素，而是利用其 `pi-herdr-agents` 擴充套件，在 `agent_start` 拋出 `working`，`agent_settled` 拋出 `idle`，並在調用 `ask_user` 時主動通報 `blocked` [18]。
*   主會話協調器會渲染出一個極為精緻的 **Subagents 狀態監控 Widget**，實時追蹤並投射 `starting`、`active`、`blocked`、`waiting`、`interrupted`、`stalled`、`running`、`finalizing` 八種微觀生命週期 [29-31]。它還設有 Watchdog 機制，在 Pane 消失或失聯時，將其判定為 `stalled` 並向主會話發送 steer 警報 [29, 32]。

#### 3. Claude Code (Anthropic)
*   **策略：對話日誌監控與 Compaction Gates** [33-35]
*   **偵測實作**：系統背景解析 `~/.claude/projects/` 下的 JSONL 對話歷史日誌中的 Token 計數 [36, 37]。當 Token 佔用率達到 75% 時啟動 compaction 警告，80% 時執行 compaction（自動壓縮歷史 tool output 提取為摘要） [34, 35, 37]。**一旦達到 85% 臨界線，Hook 會強制硬性阻斷（Block）所有 subagent 的 spawning**，以確保不會因為 context window 爆滿而發生崩潰 [35, 37]。
*   在 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` 平行協作中，Teammates 透過各自的 `~/.claude/teams/{team}/inboxes/{agent}.json` 信箱發信，領取任務時採用 file-locking 進行阻斷防止 race condition [38, 39]。

#### 4. GitHub Copilot SDK
*   **策略：協定級連線事件監聽與 On-Demand Elicitation** [40, 41]
*   **偵測實作**：使用 JSON-RPC 協定。在 streaming 模式下實時監聽 `assistant.message_delta` 與 `assistant.reasoning_delta` 事件以定義 Active 狀態 [40]。
*   當需要人類介入時，SDK 會觸發 `OnUserInputRequest` 或是調用 `UI` 模組的 `Confirm`、`Select`、`Input`、`Elicitation`（帶 Schema 驗證）交互式 UI schema [41, 42]。這會將 Session 轉入等待狀態。同時，`OnPostToolUseFailure` 鉤子能截獲工具失敗，並在背景向 Agent 注入糾錯引導 context [43]。其 Infinite Sessions 也在背景設有 80%/95% 的 compaction 監控與事件回報 [44]。

#### 5. LangGraph (LangChain)
*   **策略：有向無環圖（DAG）狀態轉移與資料庫 Checkpoint** [20, 21, 27]
*   **偵測實作**：純邏輯狀態機判定。將 status 解耦為圖節點執行的轉移邊，done 狀態即是指狀態流傳播至 `END` 節點 [98, END]。
*   若遇到預先定義的 `interrupt`（中斷點）[24]，執行流與執行緒將會凍結掛起（Sleep），並自動將當前 State 快照序列化寫入 `SqliteSaver` 或 `PostgresSaver` 中（在 Vercel 等 Serverless 部署中，本地 SQLite 寫入 ephemeral 的 `/tmp` 會流失記憶，推薦使用 `AsyncPostgresSaver` 解決） [45, 46]。此時資源完全釋放，直到外界提供 input 或者是 Resume/Time Travel 觸發，系統能從 Checkpoint 瞬時恢復運行 [20, 25-27]。

---
🧭 語義狀態偵測是多 Agent 能夠長工期運行的心臟。既然您對狀態偵測與這五大工具的實作細節有了如此深入的掌握，**是否想了解如何將這些機制與「原子化 prompt（atomic prompt）」機制整合，以設計出具備高容錯與並行開發能力的 Cheerio 網路數據抓取助理工作流呢？**

**引用來源：**
- [1] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [2] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [3] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [4] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [5] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [6] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [7] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [8] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [9] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [10] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [11] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [12] [[Developers Digest 媒體] 比較分析 - Agent 執行期環境對決：Herdr vs Pi vs Tmux](https://www.developersdigest.tech/blog/herdr-vs-pi-vs-tmux-agent-harness-compared)
- [13] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [14] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [15] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [16] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [17] [[Herdr 官方] 實作指南 - 使用 Herdr 進行 Agent 自動化工作流](https://herdr.dev/docs/agent-automation/)
- [18] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [19] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [20] [[MachineLearningMastery 媒體] 技術分析 - AI Agent 記憶與狀態的五大架構模式](https://machinelearningmastery.com/5-architectural-patterns-for-persistent-memory-and-state-in-ai-agents/)
- [21] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [22] [[LangChain 官方] 實作指南 - 使用 LangGraph 構建自訂 RAG AI Agent](https://docs.langchain.com/oss/javascript/langgraph/agentic-rag)
- [23] [[Spaceout 媒體] 技術分析 - 探討 LangChain、LangGraph 與常見 Agentic AI 設計模式](https://spaceout.pl/langchain-langgraph-and-agentic-ai-patterns/)
- [24] [[LangChain 官方] 架構文件 - LangGraph 中的狀態持久化與記憶機制](https://docs.langchain.com/oss/python/langgraph/persistence)
- [25] [[MachineLearningMastery 媒體] 技術分析 - AI Agent 記憶與狀態的五大架構模式](https://machinelearningmastery.com/5-architectural-patterns-for-persistent-memory-and-state-in-ai-agents/)
- [26] [[LangChain 官方] 架構文件 - LangGraph 中的狀態持久化與記憶機制](https://docs.langchain.com/oss/python/langgraph/persistence)
- [27] [[LangChain 官方] 架構文件 - LangGraph Checkpoints 模組與狀態持久化 API](https://reference.langchain.com/python/langgraph/checkpoints)
- [28] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [29] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [30] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [31] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [32] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [33] [[Anthropic 官方] 實作指南 - 為 Claude Code 擴充自訂 Skills 的實作方法](https://code.claude.com/docs/en/skills)
- [34] [[Anthropic 官方] 架構文件 - Claude Code 終端 Agent 的底層運作原理](https://code.claude.com/docs/en/how-claude-code-works)
- [35] [[Tech Blog 媒體] 架構文件 - 確定性 AI 協調：面向自主開發的平台架構](https://www.praetorian.com/blog/deterministic-ai-orchestration-a-platform-architecture-for-autonomous-development/)
- [36] [[Anthropic 官方] 架構文件 - Claude Code 終端 Agent 的底層運作原理](https://code.claude.com/docs/en/how-claude-code-works)
- [37] [[Tech Blog 媒體] 架構文件 - 確定性 AI 協調：面向自主開發的平台架構](https://www.praetorian.com/blog/deterministic-ai-orchestration-a-platform-architecture-for-autonomous-development/)
- [38] [[Anthropic 官方] 實作指南 - 如何協同與編排多個 Claude Code 終端會話](https://code.claude.com/docs/en/agent-teams)
- [39] [[Anthropic 官方] 實作指南 - 如何協同與編排多個 Claude Code 終端會話](https://code.claude.com/docs/en/agent-teams)
- [40] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [41] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [42] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [43] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [44] [[GitHub 官方] 架構文件 - GitHub Copilot SDK 與 CLI 相容性規格說明](https://docs.github.com/en/copilot/how-tos/copilot-sdk/troubleshooting/compatibility)
- [45] [[Reddit 社群] 案例研究 - 解決 LangGraph + SQLite Checkpointer 在 Vercel 部署後的記憶流失問題](https://www.reddit.com/r/AI_Agents/comments/1vfasz4/langgraph_sqlite_checkpointer_loses_memory_after/)
- [46] [[LangChain 官方] 架構文件 - LangGraph Checkpoints 模組與狀態持久化 API](https://reference.langchain.com/python/langgraph/checkpoints)

### 3. 原子化 prompt（atomic prompt）如何避免競態條件？哪些工具已有此機制？

**原子化 Prompt（Atomic Prompt）** 是一種將複雜、長工期的任務，拆解為最簡潔、自持且一次僅專注於「單一特定任務或概念」的提示詞指令設計 [1]。

在多 Agent 協同編排系統中，主協調器（Orchestrator）會將高階總體目標分解為一系列在時間上或邏輯上有順序的、針對個別專屬 Agent 的原子化子指令或子目標 [2-5]。透過這種高度專注的任務拆解，系統再結合特定的**鎖定與隔離機制**，能從根本上消除多 Agent 平行協作時所產生的**競態條件（Race Conditions）**。

---

### 一、 原子化 Prompt 如何避免競態條件？

原子化 Prompt 主要透過以下四種具體機制與執行期（Runtime）的控制配合來消除競態條件：

#### 1. 任務認領鎖定（Task Claiming Locks）
*   **原理與痛點**：當多個並行的 Agent 同時在共享的任務清單（Task List）中尋找並嘗試認領（Claim）未完成的子任務時，如果缺乏並行控制，極易發生兩個 Agent 同時去爭奪、認領並重複執行相同任務的衝突 [2, 6]。
*   **避免方式**：系統在認領流程中採用**檔案鎖（File Locking）**機制 [6, 7]。當某個 Agent 開始嘗試認領某項任務時，會先獲取該任務的檔案排他鎖，同一時間僅允許一個 Agent 鎖定並修改任務狀態為 In Progress，其餘 Agent 則會被阻擋並自動去尋找其他未被認領的任務，從而避免重複派工與資源浪費 [6, 7]。

#### 2. 分散式原始碼檔案鎖（Distributed File Locking）
*   **原理與痛點**：當多個 Specialized Developer Agent 同時根據各自收到的原子 Prompt 去編輯同一個代碼庫時，如果多個 Agent 同時寫入相同的檔案（例如 `auth.ts`），後寫入的變更會覆蓋掉先前的修改，造成嚴重的程式碼競態衝突 [7]。
*   **避免方式**：在執行 Edit/Write 工具時，系統會強制實施**檔案鎖定**（如 `.claude/locks/{agent}.lock`）[7]。任何寫入型 Agent 在鎖釋放前都會處於等待或阻塞狀態，確保對同一個原始碼檔案的寫入具有絕對的排他性 [7]。

#### 3. 環境與工作區實體隔離（Git Worktrees）
*   **原理與痛點**：即使在程式碼層面有檔案鎖，若多個並行的 Agent 在同一個工作目錄下執行依賴安裝、編譯建置與測試，依然會因為共用同一個 `node_modules` 或暫存目錄而產生嚴重的環境競態與干擾 [8, 9]。
*   **避免方式**：系統為每個執行原子 Prompt 的子 Agent **分配一個獨立且隔離的 Git Worktree 運作空間** [8, 10, 11]。各 Agent 在各自獨立的 checkout 目錄中進行本地測試、編譯與修改，任務完成後再統一進行 Git Commit 與 Merge，物理性地根除了環境競態衝突 [8, 9]。

#### 4. 原子化狀態更新與 API 控制
*   **原理與痛點**：在協調器向背景發送指令並等待其響應的過程中，若發送指令與啟動監聽事件之間存在時間差，可能會因為狀態流轉過快而導致事件丟失（Desynchronization） [12, 13]。另外，多個 Agent 同時寫入全局狀態（如進程控制塊）時也容易造成數據不一致 [2, 7]。
*   **避免方式**：更新全域進度（如 `MANIFEST.yaml`）時採用**原子化寫入操作（Atomic Operations）**，確保狀態變更「要麼完全成功，要麼完全不發生」 [2, 7]。同時在 API 呼叫層面，將 Prompt 發送與 Waiting 監聽封裝在單次**原子呼叫（Atomic Call）**中 [12, 13]。

---

### 二、 哪些主流工具已有此機制？

這幾種技術已在當前主流的 Agent 執行期環境和編排框架中得到實作：

#### 1. Claude Code (Anthropic)
*   **任務認領檔案鎖**：在啟動實驗性 Agent Teams（`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`）的協作會話中，**任務認領內建了 File Locking 機制**，能完全杜絕多個 Teammates 搶佔同個 Task 的 Race Condition [6]。
*   **信箱寫入保護**：Teammates 之間的非同步通訊透過寫入對方的 JSON 格式信箱（`~/.claude/teams/{team-name}/inboxes/{agent-name}.json`）完成，系統在寫入時會驗證每筆訊息格式並確保檔案寫入成功，避免同時寫信時的衝突 [14]。
*   **子 Agent 上下文隔離**：支持透過 `context: fork` 將原子 Prompt 發送至乾淨、無共享歷史的隔離會話中運作，避免 Context 污染 [15]。

#### 2. Praetorian Development Platform (薄 Agent 內核開發平台)
*   **分散式原始碼檔案鎖**：在其 16 階段的多 Agent 協作工作流中，當多個 Specialized Developer 進行平行代碼變更時，強制在沙盒內採用 **`.claude/locks/{agent}.lock` 鎖文件**，確保在同一時間只有一個 Worker 擁有該檔案的寫入權 [7]。
*   **Process Control Block 狀態持久化**：使用持久化於磁碟的 `MANIFEST.yaml` 來記錄目前的任務階段與狀態，並在巢狀反饋循環中保障狀態寫入的原子性，即使會話崩潰也能無縫恢復 [7]。

#### 3. Herdr & Pi (pi-herdr-agents 整合套件)
*   **Git Worktree 託管隔離**：結合 Pi 與 Herdr 的 `pi-herdr-agents` 支援透過 `worktree` 參數同時 **Spawn 複數個平行子 Agent**（如 Scouts, Workers, Reviewers）[10, 11, 16]。Herdr 會自動為每個原子任務檢出一個獨立且隔離的 Git Worktree 工作區 [8, 10, 11]，並在完成後自動回收，物理性阻斷並行衝突。
*   **API 原子化控制**：在 Herdr 0.7.5+ 中，`agent prompt <target> <text> --wait` 被設計為**原子化呼叫（Atomic Call）**，與 `agent wait <target> --until <status>` 一同保證指令發送與狀態監聽不因時間差而掛起 [12, 13]。

#### 4. LangGraph (LangChain)
*   **節點原子化與狀態隔離**：將複雜的 Orchestration 邏輯切分為極度原子化的 functions（Nodes），並透過圖形（StateGraph）與 Thread ID 將每一次會話狀態嚴格隔離，Subgraph 亦擁有獨立命名空間，防範記憶體污染 [17-21]。
*   **Pending Writes 保護**：多 Node 平行執行時，若其中一個 Node 失敗，其 Checkpointer（如 PostgresSaver 或 SqliteSaver）會**安全保留其餘成功 Node 的寫入結果（Pending writes）** [22, 23]。當 Resume 重啟時，Agent 可以直接跳過已完成的原子任務繼續執行（需保證節點具冪等性），規避了重複執行與競態條件的風險 [22, 24]。

#### 5. MA-VLA (多手臂視覺-語言-動作模型)
*   **原子動作分配（Atomic Action Assignment）**：由 VLM planner 將高階協作指令分解為 temporally ordered 的**原子 Prompts（如 grasp object, hold bowl）** [3, 5, 25]，並分配給各自獨立的機械手臂執行 [3]，從模型底層進行明確的分工協調，有效提升任務泛化能力 [3, 26]。

**引用來源：**
- [1] [[Reddit 社群] 技術分析 - AI Agent 核心概念與技術術語高層次定義](https://www.reddit.com/user/maltelandwehr/comments/1kdmi8v/simple_ai_definitions/)
- [2] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [3] [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)
- [4] [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)
- [5] [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)
- [6] [[Anthropic 官方] 實作指南 - 如何協同與編排多個 Claude Code 終端會話](https://code.claude.com/docs/en/agent-teams)
- [7] [[Tech Blog 媒體] 架構文件 - 確定性 AI 協調：面向自主開發的平台架構](https://www.praetorian.com/blog/deterministic-ai-orchestration-a-platform-architecture-for-autonomous-development/)
- [8] [[Reddit 社群] 案例研究 - 基於 Herdr 與獨立 Git 工作區的多 Agent 協調並行開發高階工作流](https://www.reddit.com/r/ClaudeWorkflows/comments/1vpdryi/workflow_advanced_multiagent_orchestration_with/)
- [9] [[Reddit 社群] 案例研究 - 基於 Herdr 與獨立 Git 工作區的多 Agent 協調並行開發高階工作流](https://www.reddit.com/r/ClaudeWorkflows/comments/1vpdryi/workflow_advanced_multiagent_orchestration_with/)
- [10] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [11] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [12] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [13] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [14] [[Anthropic 官方] 實作指南 - 如何協同與編排多個 Claude Code 終端會話](https://code.claude.com/docs/en/agent-teams)
- [15] [[Anthropic 官方] 實作指南 - 為 Claude Code 擴充自訂 Skills 的實作方法](https://code.claude.com/docs/en/skills)
- [16] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [17] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [18] [[LangChain 官方] 實作指南 - 使用 LangGraph 構建自訂 RAG AI Agent](https://docs.langchain.com/oss/javascript/langgraph/agentic-rag)
- [19] [[LangChain 官方] 實作指南 - 使用 LangGraph 構建自訂 RAG AI Agent](https://docs.langchain.com/oss/javascript/langgraph/agentic-rag)
- [20] [[LangChain 官方] 架構文件 - LangGraph 中的狀態持久化與記憶機制](https://docs.langchain.com/oss/python/langgraph/persistence)
- [21] [[LangChain 官方] 架構文件 - LangGraph 中的狀態持久化與記憶機制](https://docs.langchain.com/oss/python/langgraph/persistence)
- [22] [[LangChain 官方] 架構文件 - LangGraph Checkpoints 模組與狀態持久化 API](https://reference.langchain.com/python/langgraph/checkpoints)
- [23] [[LangChain 官方] 架構文件 - LangGraph Postgres 儲存器持久化配置](https://reference.langchain.com/python/langgraph.store.postgres)
- [24] [[MachineLearningMastery 媒體] 技術分析 - AI Agent 記憶與狀態的五大架構模式](https://machinelearningmastery.com/5-architectural-patterns-for-persistent-memory-and-state-in-ai-agents/)
- [25] [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)
- [26] [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)

### 4. 這三個模式在主流工具（Herdr / Pi / Claude Code / Copilot SDK / LangGraph）中的實現程度比較？

這三個模式在 **Herdr**、**Pi**、**Claude Code**、**GitHub Copilot SDK** 與 **LangGraph** 五款主流工具中的實現程度與技術細節對比如下：

---

### 一、 常駐背景（Persistent Background）模式

此模式旨在解決長工期任務中 LLM 無狀態的缺陷，實現「長生命週期、非阻塞、自主運行」[1, 2]。

*   **Herdr**：**實現程度：極高**
    *   **技術實作**：Herdr 是專為 AI 代理設計的終端多路複用器 [3, 4]。它採用 Rust 編寫的背景伺服器 `herdr-server` 直接擁有並託管底層虛擬終端進程（PTY）[5-7]。
    *   **容錯表現**：當使用者關閉電腦或 SSH 中斷後，背景 Agent 仍持續在伺服器端推進任務 [5, 7, 8]；系統重啟後能自動還原工作區與 Pane 佈局並恢復會話 [5, 7]。
*   **Pi (搭配 pi-herdr-agents)**：**實現程度：高**
    *   **技術實作**：Pi 核心本身拒絕成為平台，倡導 "No background bash. Use tmux" [3]。但透過與 `@andrewjacop/pi-herdr` 整合 [9, 10]，Pi 能以非阻塞方式將子任務 Spawning 到 Herdr 的各個 PTY 獨立 Pane 中 [10, 11]，主會話 Acknowledge 後即可繼續在前景工作 [12]。
*   **Claude Code (Anthropic)**：**實現程度：高**
    *   **技術實作**：支援將自訂 Skill 設定為 `context: fork` 與 `background: true` [13, 14]，這會孵化獨立於主線程 Context 限制的背景子 Agent（Teammate）[13, 14]。在平行協作（Agent Teams）中，Teammates 在本地 `~/.claude/teams/` 下擁有專屬信箱 [15]。
*   **Copilot SDK**：**實現程度：中高**
    *   **技術實作**：預設啟用「**Infinite Sessions**」，自動將 Checkpoints 與狀態持久化在 `~/.copilot/session-state/` 工作區 [16, 17]。支援 Fleet Mode（平行子 Agent 執行）[16, 17]。
*   **LangGraph**：**實現程度：中（框架級持久化，不託管背景）**
    *   **技術實作**：圖的狀態在每個節點執行完畢後會透過 Checkpointer 自動寫入持久化層 [18-20]。
    *   **容錯表現**：在 Serverless（如 Vercel）環境部署中，本地 SQLite 寫入 ephemeral 的 `/tmp` 會流失記憶 [21]；生產環境中必須使用 `AsyncPostgresSaver`（結合 Neon 或 Supabase Postgres）來維持跨部署的狀態持久 [21-23]。

---

### 二、 語義狀態偵測（Semantic State Detection）模式

此模式用於精準判定 Agent 目前處於 `working`、`blocked`、`done`/`idle` 的哪一種狀態，以引導控制流 [24, 25]。

*   **Herdr**：**實現程度：極高**
    *   **技術實作**：採用「狀態權威鏈（Status Authority Chain）」[26, 27]。若 Agent 安裝了 Hooks（如 Pi），則直接採用 Hook 報告為權威 [26, 28]；若無 Hooks（如 Claude Code、Codex），則讀取 PTY 底部的 ANSI 螢幕快照緩衝區（非 Scrolled Viewport），比對 TOML 格式的 `agent-detection` 規則文件進行啟發式匹配 [27, 29]。
    *   **嚴格 Blocked 判定**：只有當螢幕底部的 ANSI 文本完全匹配已知的審批、問題或許可 UI 時才標記 blocked [30]；其餘 fallback 至 idle，主動防止 Orchestrator 進行盲目且破壞性的 keystroke 寫入 [30]。
*   **Pi (搭配 pi-herdr-agents)**：**實現程度：高**
    *   **技術實作**：Pi 不依賴螢幕推估，而是利用內置的 lifecycle hooks 執行主動自我宣告 [31]。在 `agent_start` 拋出 `working`，`agent_settled` 拋出 `idle`，並在調用 `ask_user` 時主動通報 `blocked` [31]。
    *   **視覺化監控**：父 Agent 會在終端渲染出一個極為精緻的 **Subagents 狀態監控 Widget**，實時追蹤並投射 `starting`、`active`、`blocked`、`waiting`、`interrupted`、`stalled` 等微觀生命週期 [32-34]。設有 Watchdog 機制，在子進程失聯時將狀態標記為 `stalled` 並向主會話發送 steer 警報 [34, 35]。
*   **Claude Code**：**實現程度：中**
    *   **技術實作**：主要透過解析 `~/.claude/projects/` 下的 JSONL 對話歷史日誌中的 Token 計數來監控狀態 [36, 37]。
    *   **Blocked 觸發**：當 Token 佔用率達到 75% 時啟動 compaction 警告 [37]，**一旦達到 85% 臨界線，Hook 會強制硬性阻斷（Block）所有 subagent 的 spawning**，以確保不會因為 context window 爆滿而發生崩潰 [37]。
*   **Copilot SDK**：**實現程度：中高**
    *   **技術實作**：透過 JSON-RPC 的 `streaming: true` [17]，實時監控 `delta` 資訊以定義 Active 狀態 [38]。
    *   **Blocked 觸發**：當需要人類介入時，SDK 會觸發 `onPermissionRequest` 或 `onUserInputRequest`（即 `ask_user`）[17, 39]；其 `onPostToolUseFailure` 鉤子能截獲工具失敗並在背景向 Agent 注入糾錯引導 [17, 40]。
*   **LangGraph**：**實現程度：極高（邏輯級）**
    *   **技術實作**：純圖形狀態機判定 [41, 42]。
    *   **Blocked 觸發**：透過在圖編譯時配置特定的**中斷點（Interrupts/Breakpoints）** [20, 43, 44]。當執行流到達中斷邊時，圖形執行緒自動掛起，將 Graph State 序列化寫入持久化資料庫 [18, 45]，執行緒轉為 Sleep 釋放全部運算資源，直到外界提供 input 或者是 Resume 觸發 [18, 45]。

---

### 三、 原子化 Prompt（Atomic Prompt）模式

此模式將複雜的長工期任務拆解為自持且一次僅專注於單一概念的指令（Atomic Sub-instructions），並配合鎖定或隔離機制，避免多 Agent 並行時產生競態條件（Race Conditions）[46, 47]。

*   **Herdr**：**實現程度：高**
    *   **技術實作**：在 0.7.5+ 中，`agent prompt --wait` 被設計為**原子化呼叫（Atomic Call）**，避免了發送 prompt 與等待狀態監聽之間因時間差導致狀態流失 [48-50]。
    *   **環境隔離**：Herdr 提供 Tier 4 的 **Git Worktrees 隔離機制** [12, 51]，能為並行運行的各個原子任務單獨 checkout 出乾淨、獨立的工作目錄，物理性地根除了並行測試、編譯與檔案寫入時的競態衝突 [12, 52]。
*   **Pi (搭配 pi-herdr-agents)**：**實現程度：高**
    *   **技術實作**：提供 `herdr_delegate` 複合工具，在一筆呼叫中原子化封裝「spawn → send prompt → wait → harvest」[31, 49, 53]。
    *   **中斷容錯**：其工作流控制器（`herdr_workflow`）擁有強大的 **Cancel 終止契約** [54, 55]：取消會搶佔全局終端閘門 [56]，同步向 PTY 發送 close 並輪詢 PTY 是否退出 [56]，確認超時則進行安全鎖定（fail-closed），拒絕向父 Agent 返還未驗證的髒數據 [56, 57]。
*   **Claude Code**：**實現程度：中高**
    *   **技術實作**：
        1.  **Skills 兩層漸進式載入模式** [14]：Skills 的 YAML frontmatter（L1 metadata）預設載入 [13, 58]，而完整的 markdown 運作細節（L2 instructions）只有在 matching 觸發或主動呼叫時才 lazy-load [13, 58]，極大地減少了系統 prompt 膨脹與 token 浪費。
        2.  **檔案鎖避障**：在 Teammates 平行寫作或透過 mailbox 通訊時，系統會強制執行 **`~/.claude/locks/{agent}.lock` 檔案鎖** [15]，防止並行執行時將同個原始碼檔案寫壞 [15]。
*   **Copilot SDK**：**實現程度：中**
    *   **技術實作**：SDK 提供極其強大的 SystemMessage 控制權 [17, 39]。建立會話時，可配置 `Mode: "customize"` [39, 59]，對 system prompt 的十二個核心切片進行細顆粒度操作 [39, 59]。例如，可對 `SectionTone` 進行 `replace`，對 `SectionCodeChangeRules` 進行 `remove`，並對 `SectionGuidelines` 進行 `append` [59]。這使得在上層架構中動態重構原子 Prompt 變得十分高效。
*   **LangGraph**：**實現程度：極高（邏輯級）**
    *   **技術實作**：
        1.  **節點高度原子化**：邏輯被切分為極度原子化的 functions（Nodes）[42, 60]。
        2.  **保留 Pending Writes**：多 Node 並行執行時，若其中一個 Node 失敗，其 Checkpointer 會**安全保留其餘已成功 Node 的寫入結果（Pending writes）** [61]。這允許 Agent 在重啟（Resume）時直接跳過已成功的節點推進，只要保證 side-effecting 節點的冪等性，就能優雅地防止重複執行與競態條件的風險 [61, 62]。

---

🧭 這五大主流工具在三大模式上的實現策略各有千秋。既然您對各工具的編排架構有了如此深入的掌握，**是否想將這些常駐背景、語義狀態偵測與隔離 Worktree 等高容錯機制，具體應用到 Cheerio 網頁數據抓取助理的多 Agent 協作架構中呢？**

**引用來源：**
- [1] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [2] [[Mem0 官方] 產品規格 - 具備持久化記憶的 AI Agent 平台](https://mem0.ai/blog/ai-agent-platforms-with-persistent-memory)
- [3] [[Developers Digest 媒體] 比較分析 - Agent 執行期環境對決：Herdr vs Pi vs Tmux](https://www.developersdigest.tech/blog/herdr-vs-pi-vs-tmux-agent-harness-compared)
- [4] [[Herdr 官方] 產品規格 - 基於 Rust 實現的終端多 Agent 協調多路複用器](https://www.coddykit.com/pages/blog-detail?id=512884&slug=herdr-the-rust-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-one-term)
- [5] [[Developers Digest 媒體] 比較分析 - Agent 執行期環境對決：Herdr vs Pi vs Tmux](https://www.developersdigest.tech/blog/herdr-vs-pi-vs-tmux-agent-harness-compared)
- [6] [[Herdr 官方] 產品規格 - 基於 Rust 實現的終端多 Agent 協調多路複用器](https://www.coddykit.com/pages/blog-detail?id=512884&slug=herdr-the-rust-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-one-term)
- [7] [[Herdr 官方] 架構文件 - Herdr Agent 執行期技術架構與原理](https://herdr.dev/)
- [8] [[Herdr 官方] 產品規格 - 基於 Rust 實現的終端多 Agent 協調多路複用器](https://www.coddykit.com/pages/blog-detail?id=512884&slug=herdr-the-rust-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-one-term)
- [9] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [10] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [11] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [12] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [13] [[Anthropic 官方] 實作指南 - 為 Claude Code 擴充自訂 Skills 的實作方法](https://code.claude.com/docs/en/skills)
- [14] [[Anthropic 官方] 架構文件 - Claude Code 終端 Agent 的底層運作原理](https://code.claude.com/docs/en/how-claude-code-works)
- [15] [[Anthropic 官方] 實作指南 - 如何協同與編排多個 Claude Code 終端會話](https://code.claude.com/docs/en/agent-teams)
- [16] [[GitHub 官方] 架構文件 - GitHub Copilot SDK 與 CLI 相容性規格說明](https://docs.github.com/en/copilot/how-tos/copilot-sdk/troubleshooting/compatibility)
- [17] [[GitHub 官方] 架構文件 - GitHub Copilot SDK 與 CLI 相容性規格說明](https://docs.github.com/en/copilot/how-tos/copilot-sdk/troubleshooting/compatibility)
- [18] [[MachineLearningMastery 媒體] 技術分析 - AI Agent 記憶與狀態的五大架構模式](https://machinelearningmastery.com/5-architectural-patterns-for-persistent-memory-and-state-in-ai-agents/)
- [19] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [20] [[LangChain 官方] 架構文件 - LangGraph 中的狀態持久化與記憶機制](https://docs.langchain.com/oss/python/langgraph/persistence)
- [21] [[Reddit 社群] 案例研究 - 解決 LangGraph + SQLite Checkpointer 在 Vercel 部署後的記憶流失問題](https://www.reddit.com/r/AI_Agents/comments/1vfasz4/langgraph_sqlite_checkpointer_loses_memory_after/)
- [22] [[Reddit 社群] 案例研究 - 解決 LangGraph + SQLite Checkpointer 在 Vercel 部署後的記憶流失問題](https://www.reddit.com/r/AI_Agents/comments/1vfasz4/langgraph_sqlite_checkpointer_loses_memory_after/)
- [23] [[LangChain 官方] 架構文件 - LangGraph Checkpoints 模組與狀態持久化 API](https://reference.langchain.com/python/langgraph/checkpoints)
- [24] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [25] [[Herdr 官方] 架構文件 - Herdr Agent 執行期技術架構與原理](https://herdr.dev/)
- [26] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [27] [[Developers Digest 媒體] 比較分析 - Agent 執行期環境對決：Herdr vs Pi vs Tmux](https://www.developersdigest.tech/blog/herdr-vs-pi-vs-tmux-agent-harness-compared)
- [28] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [29] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [30] [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [31] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [32] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [33] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [34] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [35] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [36] [[Anthropic 官方] 實作指南 - 為 Claude Code 擴充自訂 Skills 的實作方法](https://code.claude.com/docs/en/skills)
- [37] [[Tech Blog 媒體] 架構文件 - 確定性 AI 協調：面向自主開發的平台架構](https://www.praetorian.com/blog/deterministic-ai-orchestration-a-platform-architecture-for-autonomous-development/)
- [38] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [39] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [40] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [41] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [42] [[Spaceout 媒體] 技術分析 - 探討 LangChain、LangGraph 與常見 Agentic AI 設計模式](https://spaceout.pl/langchain-langgraph-and-agentic-ai-patterns/)
- [43] [[Spaceout 媒體] 技術分析 - 探討 LangChain、LangGraph 與常見 Agentic AI 設計模式](https://spaceout.pl/langchain-langgraph-and-agentic-ai-patterns/)
- [44] [[LangChain 官方] 架構文件 - LangGraph 中的狀態持久化與記憶機制](https://docs.langchain.com/oss/python/langgraph/persistence)
- [45] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [46] [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)
- [47] [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)
- [48] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [49] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [50] [[Herdr 官方] 實作指南 - 使用 Herdr 進行 Agent 自動化工作流](https://herdr.dev/docs/agent-automation/)
- [51] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [52] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [53] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [54] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [55] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [56] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [57] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [58] [[Anthropic 官方] 架構文件 - Claude API 中的 Agent Skills 設計與規範](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [59] [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [60] [[LangChain 官方] 實作指南 - 使用 LangGraph 構建自訂 RAG AI Agent](https://docs.langchain.com/oss/javascript/langgraph/agentic-rag)
- [61] [[LangChain 官方] 架構文件 - LangGraph Checkpoints 模組與狀態持久化 API](https://reference.langchain.com/python/langgraph/checkpoints)
- [62] [[MachineLearningMastery 媒體] 技術分析 - AI Agent 記憶與狀態的五大架構模式](https://machinelearningmastery.com/5-architectural-patterns-for-persistent-memory-and-state-in-ai-agents/)

### 5. 對 Cheerio 助理的多 agent 協作設計有什麼具體應用建議？

將**常駐背景（Persistent Background）**、**語義狀態偵測（Semantic State Detection）**與**原子化 Prompt（Atomic Prompt）**三大編排模式，具體應用於 **Cheerio 網頁數據抓取助理**的多 Agent 協作設計中，能夠根本性地解決傳統網頁爬蟲面臨的**選擇器易碎（Selector Fragility）**、**反爬鎖定（Anti-bot blocks）**與**長工期中斷前功盡棄**等生產環境痛點 [1, 2]。

以下為 Cheerio 助理的多 Agent 協作架構設計與具體應用建議：

---

### 一、 微型 Agent 與實體限制分工（Thin-Agent & Specialized Roles）

傳統的單體式爬蟲常將網頁下載、DOM 解析、資料清洗與代碼寫入邏輯耦合在一個龐大的 Agent 中，極易因 Prompt 膨脹、Context Window 爆滿或 A/B 測試變更而全面崩潰 [3, 4]。
建議採用 **Thin-Agent / Fat-Platform** 模式 [4]，限制個別 Agent 的能力邊界，將抓取管道拆解為五個高度原子化的 Specialized Roles（專屬角色），從實體限制上防止 Planner 與 Executor 發生衝突 [4, 5]：

1.  **領班協調器（Orchestrator / Foreman）**
    *   **職責**：駐留在主會話（Kernel Mode）中 [4]。負責解析目標網頁的 Sitemap、URL 隊列或分頁規律 [6, 7]，將待爬子任務寫入共享的 `MANIFEST.yaml` 狀態清單，並分派中階原子 Prompt 給子 Agent 執行 [2, 4]。
    *   **限制（Coordinator 限制）**：**不擁有代碼寫入或 CLI 執行權限**，只能進行任務分派，杜絕代碼修改時的混亂 [4]。
2.  **網頁獲取者（Fetcher Agent）**
    *   **職責**：專注執行 HTTP 網路獲取 [6]。
    *   **策略**：首選以輕量、高速的靜態 HTTP 請求下載網頁原始碼 [6, 8]。若偵測到客戶端動態渲染（如 HTML 僅含 skeleton 骨架或空標籤） [9]，則**自動升級（Escalate）**調用重量級無頭瀏覽器（如 Playwright 或 Stagehand v4）獲取 Hydrated DOM [9, 10]，兼顧效能與靈活度。
3.  **DOM 淨化器（Distiller Agent）**
    *   **職責**：在將 HTML 送入 LLM 前進行 DOM 剪枝 [11]。利用底層 Cheerio 快速解析的特性 [12, 13]，主動剝離 `<script>`, `<style>`, `<svg>`, `<iframe>` 及廣告等高噪聲節點 [11]，將 HTML 壓縮為乾淨的 Markdown 格式，防止 Context 溢出 [4]。
    *   **無障礙標籤注入（Accessibility Injection）**：**在淨化過程中，主動將 HTML 的 ARIA Labels（無障礙標籤）抽離並注入 Markdown 中** [1]。這能為後續的 LLM 提供最直接的導航語義，使資料提取準確度顯著上升 [1]。
4.  **語義提取器（Extractor Agent）**
    *   **職責**：進行 **LLM-Driven 無選擇器語義解析** [1]。
    *   **策略**：不依賴脆弱的 `.price` 或 `.title` CSS Class Name [1]，而是直接閱讀 Distiller 提供的乾淨 Markdown，透過空間相鄰性與語義特徵（如：「貨幣格式且鄰近購物車按鈕的數值即是價格」）進行推理提取 [1, 14]。
    *   **限制（Executor 限制）**：配合 **Zod / Pydantic V2** 進行 Schema 強類型驗證 [9, 15]，將 LLM 提取的字串自動脅迫轉換為 clean float 或 ISO 日期格式 [9, 15]。**實體限制不允許 Spawn 任何子 Agent** [4]。
5.  **自癒工程師（Self-Healing Agent）**
    *   **職責**：負責維護**「靜態快取選擇器」**，在 Cheerio 助理遭遇提取異常時進行代碼自癒（Self-Healing） [1, 4]。

---

### 二、 基於「任務狀態表示法（TSR）」的精準狀態偵測與斷點續爬

在數千個 URL 的長工期（Long-Horizon）抓取任務中，若對網頁錯誤或 CAPTCHA 阻擋缺乏狀態感知，Agent 極易陷入重複請求的 token 燒毀死循環 [14, 16]。

1.  **任務狀態（Persistent Task State）與瞬時觀測（Transient Observation）徹底解耦**
    *   實作 **TSR 框架** [14]，外置狀態更新器（State Updater）。
    *   在 Orchestrator 中維護一個獨立的結構化 JSON `Progress Tracker`，記錄：`Global Goal`（抓取 10 頁數據）、`Milestones Completed`（Page 1-4 已完成）、`Current Subgoal`（Page 5 正在獲取）、`Remaining Requirements` [14]。這能防止 Agent 產生進度幻覺（Progress Hallucination）或提早終止任務 [14]。
2.  **精準偵測 Blocked 狀態以防止封鎖升級**
    *   Fetcher 下載 HTML 後，必須先經由驗證保護（Validation Guard） [17]。若 HTML 內文匹配到已知反爬 Class 或 CAPTCHA 關鍵字（如 SmartCaptcha、CheckboxCaptcha 或 429 狀態碼） [17, 18]，**嚴禁 Agent 盲目重試** [2]。
    *   系統立即發送 `report-agent --state blocked` 主動向 Orchestrator 申報 `Blocked` [19, 20]，並暫存當前 HTML 快照 [19]。
3.  **利用 `caller_ping` 實作人機協同（HITL）與時間旅行**
    *   當爬蟲 Agent 被標記為 `Blocked` 時，子會話呼叫 `caller_ping({message: "encounter_captcha"})` 安全退出並暫停（Pause） [19, 21, 22]。
    *   這會將 Graph State 序列化寫入持久化資料庫（推薦使用 `AsyncPostgresSaver` 或 Redis，防止 Serverless 部署時 SQLite ephemeral 的記憶流失） [23-25]，徹底釋放運算資源 [2, 3]。
    *   待人類工程師手動調整 Proxy、解開驗證碼或提供輸入後，Orchestrator 觸發 `subagent_resume` [19, 21] 還原 Snapshot，並**承接 Checkpoint 斷點續爬，無需重新開始** [2, 26]。

---

### 三、 基於 Git Worktrees 的物理隔離與原子化 Prompt 防範競態

當有多個 Specialized Developer Agent 同時針對不同版面結構的網站（如電商、新聞、論壇）編寫 Cheerio 抓取代碼時，如果共用同一個目錄，極易在並行測試、套件安裝和代碼寫入時產生嚴重的競態條件與寫入覆蓋 [4, 27]。

1.  **Git Worktrees 物理隔離平行派發**
    *   主協調器在分派任務時，應利用 Herdr 的 **Git Worktrees 託管機制** [19, 28]，為每個並行任務單獨 Checkout 出乾淨、獨立的工作目錄（如 `~/.copilot/session-state/{id}/`）與 Git 分支 [15, 19]。
    *   各子 Agent 在各自隔離、互不影響的目錄下獨立安裝依賴包、撰寫 Cheerio 腳本並跑測試 [27]，從物理上根除並行衝突 [4, 27]。
2.  **分佈式檔案寫入鎖（Distributed File Locking）**
    *   當並行的 Agent 完成任務並將 Cheerio 腳本合併回主項目的 `scrapers.js` 配置檔時，系統在執行代碼寫入工具時會強制執行 **`.claude/locks/{agent}.lock` 檔案鎖** [4]。
    *   同時間僅允許一個 Agent 擁有寫入權，其餘 Agent 處於等待狀態，確保設定檔寫入具有排他性 [4]。
3.  **Fail-Closed 安全鎖定與終止契約**
    *   主協調器在平行 Spawning 子 Agent 時（如 `herdr_delegate`） [19, 29]，使用強大且具搶佔性的 **Cancel 終止契約** [19]。
    *   當手動取消或子任務超時，系統會同步向 PTY 終端發送 close，並持續輪詢直到捕捉到進程退出（Exit Code） [19]。若退出確認超時則進行安全鎖定（Fail-Closed），拒絕向主會話返還未驗證的髒數據，保障狀態原子性 [19, 30]。

---

### 四、 自癒混合模式（Self-Healing Hybrid Routing）的經濟學優化

雖然 LLM-Driven 語義解析非常強大，但每次調用都會耗費 API Token [2]。為達到效能與成本的經濟學最佳平衡，建議實施自癒混合模式：

```
                    [下載 HTML 原始碼]
                           │
                           ▼
             [執行 Syntactic Fast Path]
            (使用快取的 Cheerio 靜態選擇器)
                           │
                 ┌─────────┴─────────┐
         (提取成功)│                 │(提取失敗/Null)
                 ▼                 ▼
           [寫入資料庫]     [觸發 Blocked / Failed]
                                   │
                                   ▼
                       [轉入 Inference Core]
                     (調用 LLM 語義提取數據)
                                   │
                                   ▼
                       [Self-Healing Agent]
                     (反向推導新 CSS Selector)
                                   │
                                   ▼
                         [更新選擇器 Cache]
                    (下次抓取回歸 $0 快速路徑)
```

*   **Syntactic Fast Path（高速靜態路徑）**：Cheerio 的執行成本接近 **\$0**，且速度極快 [6, 13]。系統預設使用先前快取的 CSS 選擇器進行抓取 [11]。
*   **Inference Core（語義提取路徑）**：當網頁改版、CSS 異動導致 Cheerio 解析出的 JSON 欄位出現 `null`、`empty` 或型別不符時 [1]，系統立即中斷，並將任務路由至 Extractor Agent 進行高成本的 LLM 語義數據提取（成本約 **\$0.01**） [1]。
*   **選擇器自癒（Self-Healing）**：成功提取數據後，**Self-Healing Agent 會根據目前最新的 DOM 結構，重新反向推導出一套全新的、基於 stable 屬性（如 `data-*`）的 CSS 選擇器** [4]。這套新選擇器會自動更新並寫回本地快取，使未來的 10,000 次請求重新回到 \$0 的高速靜態路徑，達到極高的系統韌性 [1, 4]。

---

### 五、 離線記憶精煉與系統自我演進（Offline Refinement & Self-Annealing）

1.  **休眠期非同步記憶精煉（Sleep-Time Asynchronous Memory Refinement）**
    *   在白天的密集抓取任務結束後，系統於深夜進入 Idle 閒置期 [2]。
    *   啟動一個專門的 **Memory Refinement Agent** [2]，異步檢索整天的抓取日誌、429 頻率限制、代理封鎖與選擇器漂移失敗等軌跡（Episodic Event Logs） [3]。
    *   Refinement Agent 負責在背景總結失敗規律，並自動將其提煉為 actionable wisdom（例如：「Target-B 網站每秒 Request 限制為 5 次」），自動調整該網站的調度延遲（Delay）至 2000ms，寫入持久化記憶層（如 Mem0 平台） [2, 31]，實現越用越聰明的正向循環 [2, 3]。
2.  **自我修正與 Meta-Agent（Self-Annealing Workflow）**
    *   當 Cheerio 助理在 Implementation → Review 循環中連續失敗超過 3 次 [4]，系統會觸發 **Self-Annealing 工作流**，喚醒一個 **Meta-Agent（元代理）** 獲取 `.claude/` 目錄的編輯權 [4]。
    *   Meta-Agent 讀取會話日誌 [4]，識別出 Agent 寫入 buggy Cheerio 代碼的「思維路徑」（如：「我以為這是簡單的 typo 所以跳過了測試」） [4]。
    *   它會主動修改 `SKILL.md`，將其寫入 explicit **"Anti-Pattern"（反模式）** 提醒，並更新 `.claude/config/orchestration-limits.yaml` 中的限制引導，實現 prompt 的自我淬火硬化 [4]。

---
🧭 這套整合了常駐背景、TSR 狀態更新器、Git Worktrees 與自癒混合路徑的 Cheerio 多 Agent 協作設計，能最大程度發揮 Cheerio 的超高速優勢，同時藉由 LLM 賦予系統鋼鐵般的容錯自癒能力。**您是否希望我為您將此架構具體撰寫成一份包含代碼框架與 MANIFEST.yaml 設定規格的「企業級多 Agent 容錯爬蟲系統設計報告（Tailored Report）」並儲存在您的 Studio 面板中，以便您後續開發參考呢？**

**引用來源：**
- [1] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [2] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [3] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [4] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [5] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [6] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [7] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [8] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [9] [[MachineLearningMastery 媒體] 技術分析 - AI Agent 記憶與狀態的五大架構模式](https://machinelearningmastery.com/5-architectural-patterns-for-persistent-memory-and-state-in-ai-agents/)
- [10] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [11] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [12] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [13] [[Apify 官方] 技術分析 - 2025年 Cheerio 網頁數據抓取最新技術與優化方法](https://blog.apify.com/web-scraping-with-cheerio/)
- [14] [[Mem0 官方] 產品規格 - 具備持久化記憶的 AI Agent 平台](https://mem0.ai/blog/ai-agent-platforms-with-persistent-memory)
- [15] [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [16] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [17] [[ScrapingBee 媒體] 實作指南 - 使用 Python 與 Node.js 爬取 Yandex 搜尋結果](https://www.scrapingbee.com/blog/how-to-scrape-yandex-search-results/)
- [18] [[ScrapingBee 媒體] 實作指南 - 使用 Python 與 Node.js 爬取 Yandex 搜尋結果](https://www.scrapingbee.com/blog/how-to-scrape-yandex-search-results/)
- [19] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [20] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [21] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [22] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [23] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [24] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [25] [[LangChain 官方] 架構文件 - LangGraph Postgres 儲存器持久化配置](https://reference.langchain.com/python/langgraph.store.postgres)
- [26] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [27] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [28] [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [29] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [30] [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [31] [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)

## 已移除的來源（品質過濾）

- Claude Code by Anthropic | AI Coding Agent, Terminal, IDE — 此頁面為 Anthropic 官方的產品功能介紹首頁與定價宣傳頁，包含產品功能特點、用戶評價、收費標準（每月 200 美元）及常見問答（FAQ），缺乏具體實作程式碼或深度的技術架構細節，屬於廣告/宣傳/行銷頁面。
- Cheerio AI Agent Sandbox - ScrapeNinja — 此頁面為 ScrapeNinja 針對其 AI 提取工具所設計的行銷推廣頁，僅以高層次的概念術語（如 Slice & Dice, Iterate & Refine, Self-Validate）介紹產品優勢並引導用戶連結至外部沙盒，並未提供具體實作細節或程式碼，屬於無具體實作細節的推廣頁。

## 參考來源清單

- [[4Geeks Academy 媒體] 產品規格 - Browserbase Stagehand v4 瀏覽器 Agent SDK 解析](https://4geeks.com/en/blog/ai-tools/que-es-stagehand)
- [[AnswerAgentAI 官方] 產品規格 - AnswerAgent 中的 Cheerio 網頁爬蟲配置](https://answeragent.ai/docs/sidekick-studio/chatflows/document-loaders/cheerio-web-scraper)
- [[Anthropic 官方] 實作指南 - 在 Claude Code 中使用 Skills 擴充 Agent 能力](https://code.claude.com/docs/en/agent-sdk/skills)
- [[Anthropic 官方] 實作指南 - 如何協同與編排多個 Claude Code 終端會話](https://code.claude.com/docs/en/agent-teams)
- [[Anthropic 官方] 實作指南 - 快速上手：在 API 中啟用 Agent Skills 擴充功能](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/quickstart)
- [[Anthropic 官方] 實作指南 - 為 Claude Code 擴充自訂 Skills 的實作方法](https://code.claude.com/docs/en/skills)
- [[Anthropic 官方] 架構文件 - Claude API Skill 擴充機制與呼叫規範](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/claude-api-skill)
- [[Anthropic 官方] 架構文件 - Claude API 中的 Agent Skills 設計與規範](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [[Anthropic 官方] 架構文件 - Claude Code 終端 Agent 的底層運作原理](https://code.claude.com/docs/en/how-claude-code-works)
- [[Anthropic 官方] 架構文件 - 企業級 Claude 應用中的 Skills 權限與整合架構](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/enterprise)
- [[Apify 官方] 技術分析 - 2025年 Cheerio 網頁數據抓取最新技術與優化方法](https://blog.apify.com/web-scraping-with-cheerio/)
- [[Apify 官方] 產品規格 - Cheerio Scraper 網頁 HTML 爬取工具](https://apify.com/apify/cheerio-scraper)
- [[Check Point Research 媒體] 安全研究 - 漏洞分析：利用 LangGraph Checkpointer 實現遠端程式碼執行](https://research.checkpoint.com/2026/from-sqli-to-rce-exploiting-langgraphs-checkpointer/)
- [[Cheerio 官方] 產品規格 - Cheerio 輕量級快速 HTML/XML 解析程式庫](https://github.com/cheeriojs/cheerio)
- [[DEV Community 媒體] 技術分析 - 選擇器的終結：基於 LLM 的 HTML 語義解析新時代](https://dev.to/deepak_mishra_35863517037/the-end-of-selectors-llm-driven-html-parsing-28b2)
- [[Deno 官方] 實作指南 - 使用 TypeScript 在 Deno 中建立自訂 RAG AI Agent](https://deno.com/blog/build-custom-rag-ai-agent)
- [[Developers Digest 媒體] 比較分析 - Agent 執行期環境對決：Herdr vs Pi vs Tmux](https://www.developersdigest.tech/blog/herdr-vs-pi-vs-tmux-agent-harness-compared)
- [[FlowiseAI 官方] 產品規格 - Flowise 中的 Cheerio 網頁爬蟲節點設計](https://docs.flowiseai.com/integrations/langchain/document-loaders/cheerio-web-scraper)
- [[GitHub 官方] 架構文件 - GitHub Copilot SDK for Go 開發者指引與規格](https://github.com/github/copilot-sdk/blob/main/go/README.md)
- [[GitHub 官方] 架構文件 - GitHub Copilot SDK 與 CLI 相容性規格說明](https://docs.github.com/en/copilot/how-tos/copilot-sdk/troubleshooting/compatibility)
- [[GitHub 官方] 架構文件 - nodetool Agent 管理與調度機制規範](https://github.com/nodetool-ai/nodetool/blob/main/AGENTS.md)
- [[GitHub 官方] 產品規格 - Copilot SDK 跨平台 Agent 整合開發套件](https://github.com/github/copilot-sdk)
- [[GitHub 官方] 產品規格 - pi-herdr-agents 封裝與調用依賴規格](https://pi.dev/packages/pi-herdr-agents)
- [[GitHub 社群] 案例研究 - Awesome Claude Fable 5 高階 Prompt 精選庫](https://github.com/thenicolas1894/awesome-claude-fable-5-prompt-vault)
- [[Herdr 官方] 實作指南 - 使用 Herdr 進行 Agent 自動化工作流](https://herdr.dev/docs/agent-automation/)
- [[Herdr 官方] 架構文件 - Herdr Agent 執行期技術架構與原理](https://herdr.dev/)
- [[Herdr 官方] 架構文件 - Herdr 中的多 Agent 管理與執行期環境](https://herdr.dev/docs/agents/)
- [[Herdr 官方] 架構文件 - Herdr 多 Agent 多路複用器完整技術手冊](https://herdr.dev/docs/)
- [[Herdr 官方] 產品規格 - 基於 Rust 實現的終端多 Agent 協調多路複用器](https://www.coddykit.com/pages/blog-detail?id=512884&slug=herdr-the-rust-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-one-term)
- [[IPRoyal 官方] 實作指南 - 給初學者的 Cheerio 與 Node.js 網頁爬蟲快速入門](https://iproyal.com/blog/web-scraping-cheerio-nodejs/)
- [[Incogniton 媒體] 實作指南 - Cheerio 與 User-Agent 網頁爬蟲入門教學](https://api-docs.incogniton.com/how-to-guides/beginners-guide-to-scraping)
- [[LangChain 官方] 實作指南 - LangGraph 層級式多 Agent 團隊協調範例](https://github.com/langchain-ai/langgraphjs/blob/main/examples/multi_agent/hierarchical_agent_teams.ipynb?ref=blog.langchain.com)
- [[LangChain 官方] 實作指南 - 使用 LangGraph 構建自訂 RAG AI Agent](https://docs.langchain.com/oss/javascript/langgraph/agentic-rag)
- [[LangChain 官方] 架構文件 - LangChain 中的網頁瀏覽器工具與節點整合](https://docs.langchain.com/oss/javascript/integrations/tools/webbrowser)
- [[LangChain 官方] 架構文件 - LangGraph Checkpoints 模組與狀態持久化 API](https://reference.langchain.com/python/langgraph/checkpoints)
- [[LangChain 官方] 架構文件 - LangGraph Postgres 儲存器持久化配置](https://reference.langchain.com/python/langgraph.store.postgres)
- [[LangChain 官方] 架構文件 - LangGraph 中的狀態持久化與記憶機制](https://docs.langchain.com/oss/python/langgraph/persistence)
- [[MachineLearningMastery 媒體] 技術分析 - AI Agent 記憶與狀態的五大架構模式](https://machinelearningmastery.com/5-architectural-patterns-for-persistent-memory-and-state-in-ai-agents/)
- [[Mem0 官方] 產品規格 - 具備持久化記憶的 AI Agent 平台](https://mem0.ai/blog/ai-agent-platforms-with-persistent-memory)
- [[Microsoft 官方] 實作指南 - 在 Copilot Studio 中實作多 Agent 協調架構](https://techcommunity.microsoft.com/blog/azure-ai-foundry-blog/implementing-multi%E2%80%91agent-orchestration-in-microsoft-copilot-studio/4494717)
- [[Microsoft 官方] 技術分析 - Copilot Studio 多 Agent 協調模式與最佳實踐](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/multi-agent-patterns)
- [[Microsoft 官方] 架構文件 - 企業級多 Agent 協調模式設計指南](https://learn.microsoft.com/en-us/agents/architecture/multi-agent-patterns)
- [[Microsoft 社群] 實作指南 - 結合 Agent Framework、GitHub Copilot CLI 與 Squad 構建 Agent 團隊](https://devblogs.microsoft.com/agent-framework/building-agent-teams-with-agent-framework-github-copilot-cli-and-squad/)
- [[Microsoft 社群] 案例研究 - 結合 GitHub Copilot SDK 與 Foundry Local 的 Agent 程式碼自動修復](https://techcommunity.microsoft.com/blog/educatordeveloperblog/agentic-code-fixing-with-github-copilot-sdk-and-foundry-local/4493967)
- [[Olostep 官方] 實作指南 - 使用 Cheerio 在 Node.js 中進行高效網頁爬取的現代指南](https://www.olostep.com/blog/web-scraping-cheerio)
- [[OrbitScraper 官方] 實作指南 - 使用 Node.js API 爬取 Google 搜尋結果](https://orbitscraper.com/blog/scrape-google-results-node-js)
- [[Pi coding agent 官方] 產品規格 - andrewjacop/pi-herdr 整合套件規格](https://pi.dev/packages/@andrewjacop/pi-herdr?name=web)
- [[Reddit 社群] 技術分析 - AI Agent 核心概念與技術術語高層次定義](https://www.reddit.com/user/maltelandwehr/comments/1kdmi8v/simple_ai_definitions/)
- [[Reddit 社群] 案例研究 - Gem Team：基於 Copilot SDK 的多 Agent 協調工作流實作分享](https://www.reddit.com/r/GithubCopilot/comments/1r2oht8/i_built_gem_team_multiagent_orchestration/)
- [[Reddit 社群] 案例研究 - 基於 Herdr 與獨立 Git 工作區的多 Agent 協調並行開發高階工作流](https://www.reddit.com/r/ClaudeWorkflows/comments/1vpdryi/workflow_advanced_multiagent_orchestration_with/)
- [[Reddit 社群] 案例研究 - 解決 LangGraph + SQLite Checkpointer 在 Vercel 部署後的記憶流失問題](https://www.reddit.com/r/AI_Agents/comments/1vfasz4/langgraph_sqlite_checkpointer_loses_memory_after/)
- [[ScrapeOps 媒體] 實作指南 - 使用 Node.js 與 Cheerio 進行 HTML 網頁解析](https://scrapeops.io/nodejs-web-scraping-playbook/nodejs-cheerio-guide/)
- [[ScrapingBee 媒體] 實作指南 - 使用 Python 與 Node.js 爬取 Yandex 搜尋結果](https://www.scrapingbee.com/blog/how-to-scrape-yandex-search-results/)
- [[Scrappey 官方] 實作指南 - Node.js 網頁爬蟲技術：Cheerio 與 Playwright 實戰比較](https://scrappey.com/qa/web-scraping-languages/web-scraping-with-nodejs)
- [[Shift Asia 媒體] 比較分析 - 企業級 Agent 框架大比拼：Nous Hermes Agent vs. OpenClaw](https://shiftasia.com/column/nous-hermes-agent-vs-openclaw-architectural-deep-dive/)
- [[Spaceout 媒體] 技術分析 - 探討 LangChain、LangGraph 與常見 Agentic AI 設計模式](https://spaceout.pl/langchain-langgraph-and-agentic-ai-patterns/)
- [[Tech Blog 媒體] 實作指南 - 結合 RAG 與向量搜尋建立具持久記憶的 AI Agent](https://www.mindstudio.ai/blog/ai-agent-persistent-memory-rag-vector-search)
- [[Tech Blog 媒體] 技術分析 - 2026年長工期 AI Agent 的七大狀態持久化策略](https://www.indium.tech/7-state-persistence-strategies-ai-agents-2026/)
- [[Tech Blog 媒體] 架構文件 - 確定性 AI 協調：面向自主開發的平台架構](https://www.praetorian.com/blog/deterministic-ai-orchestration-a-platform-architecture-for-autonomous-development/)
- [[Twilio 官方] 實作指南 - 使用 Node.js 與 Cheerio 進行 HTML 網頁下載與解析](https://www.twilio.com/en-us/blog/developers/tutorials/building-blocks/web-scraping-and-parsing-html-with-node-js-and-cheerio)
- [[arXiv 學術] 學術論文 - MA-VLA：用於協同與任務泛化的多手臂視覺-語言-動作模型](https://arxiv.org/html/2608.25864v1)
- [[arXiv 學術] 學術論文 - PanelTR：基於多 Agent 科學研討的零樣本表格推理框架](https://arxiv.org/html/2508.06110v1)
- [[arXiv 學術] 學術論文 - 深入剖析 Claude Code：當前與未來 AI Agent 系統之設計空間](https://arxiv.org/abs/2604.14228)
- [[arXiv 學術] 學術論文 - 長工期行動端 GUI Agent 的任務狀態表示法](https://arxiv.org/html/2607.00502v1)
- [[htek.dev 媒體] 技術分析 - GitHub Copilot SDK：將 Agent 整合至各類應用的技術探討](https://htek.dev/articles/github-copilot-sdk-agents-for-every-app)
