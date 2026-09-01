# Deep Research 報告 — rc-20260831-001

- 研究主題：現有 AI Agent Harness/Framework 全貌盤點：以四大廠牌路線（OpenAI Codex、DeepSeek Harness、Vercel AI SDK、Microsoft AutoGen/Semantic Kernel）為核心，並涵蓋 Python 與 TypeScript 兩大語言生態圈的主流 Agent Framework（如 LangChain/LangGraph、CrewAI、OpenAI Agents SDK、smolagents、Pydantic AI、Mastra、LangChain.js 等），重點比較各框架的架構設計與可觀測性能力（能否取得完整過程與工具呼叫紀錄、記憶機制設計），以及多 Agent 協作能力（角色分工、狀態機/工作流編排方式）。此研究是 W-074（Cheerio 個人 AI 助理架構，傾向自建 harness）開工前的業界現況盤點，用於評估與對照，非決定要採用哪一個框架。
- Notebook：https://notebooklm.google.com/notebook/15f04046-8dd2-479a-8a08-2766dd1aebbb
- 產生時間：2026-08-31T07:34:16.184Z
- 原始批次（98 筆匯入）：品質過濾 98 → 90 筆（移除 8 筆）→ 蒸餾必要性 90 → 84 筆（移除 5 筆無貢獻來源）→ 來源分類/重新命名 76 / 76 筆成功
- **補救批次（2026-08-31 追加）**：第 1 節（OpenAI Codex）與第 4 節（Microsoft AutoGen/Semantic Kernel）原答案因 `query_answers.js` 問題分派 bug 而錯位（分別誤答成 Python／TypeScript 生態圈內容），已重新查詢修正。Microsoft 沿用既有 8 筆來源純重新查詢；Codex 因 notebook 內原本零筆相關來源，另補跑一次窄範圍 deep 研究，新匯入 93 筆來源（未套用品質過濾/蒸餾/重新命名，直接採用）
- **目前總來源數：177 筆**（84 原始 + 93 補研究）

## 研究結果

### 1. OpenAI Codex CLI/Agent 的架構設計是什麼？提供哪些可觀測性機制（工具呼叫紀錄、推理過程、記憶存取）？是否支援多 Agent 協作？

### 1. OpenAI Codex CLI/Agent 的架構設計

**OpenAI Codex** 在其產品矩陣中不僅代表底層模型，更演進為一套完整的**代理產品套件（Product Suite）**，涵蓋了 **Codex CLI（終端機 CLI 工具，為開源項目）、Codex Cloud（託管雲端服務）、IDE 插件及桌面應用程式** `[1-3]`。

其底層架構與設計邏輯如下：

*   **固定代理迴圈（Fixed Loop）**：與 DeepSeek Harness 強調「萬物皆插件」、允許動態抽換底層 Loop 的微內核架構不同，Codex 提供的是由官方精心工程化（Engineered）的**固定代理迴圈（Agent Loop）**，旨在保證跨介面（終端、IDE、雲端、桌面）體驗的一致性與確定性 `[4]`。
*   **展開型 ReAct 迴圈（Unrolling the Loop）**：其核心運轉流程基於典型的 `Observe`（讀取檔案、搜尋代碼、檢查狀態） \\(\rightarrow\\) `Think`（推理下一步、規劃行為） \\(\rightarrow\\) `Act`（執行工具如編輯、執行、搜尋等）之**展開型 ReAct 迴圈** `[5]`。
*   **執行與沙箱環境（Sandbox）**：任務運作於**雲端隔離的 Docker 容器沙箱環境**中，並會預先載入使用者的程式碼庫（Repository），確保工具調用與代碼執行與本機主機物理隔離 `[6, 7]`。
*   **底層模型驅動**：標準編程任務由專門針對 Context 壓縮優化的 **GPT-5.2 Codex**（SWE-bench Pro 達 56.4%）驅動，而長時程操作（Long-running operations）則採用 **GPT-5.1-Codex-Max** `[6, 7]`。
*   **標準協議整合**：藉由將 CLI 暴露為 **MCP（Model Context Protocol）** 伺服器，並使用 **OpenAI Agents SDK** 進行編排，開發者可將其無縫整合至具備確定性且可稽核的軟體交付流水線中 `[7]`。

---

### 2. 可觀測性機制（Observability）

OpenAI Codex 依賴 **OpenAI Agents SDK** 的底層遙測能力，提供極為細緻、端到端的白盒化可稽核機制：

*   **官方 Traces 儀表板**：所有 LLM 生成、工具呼叫、交接（Handoffs）、防護欄（Guardrails）與計時資訊，都會實時記錄並呈現於 **OpenAI 官方開發者 Traces 儀表板**上，便於開發與生產環境的除錯 `[8]`。
*   **OpenTelemetry 標準導出**：內建符合 **OTel GenAI 語意規範**的追蹤 `[9]`。開發者可透過標準 OTel API 或 OpenInference 儀器化工具（Instrumentor），將 Trace 數據導出至自建的觀測平台（如 **OpenObserve、Braintrust、Datadog** 等） `[9-11]`。
*   **工具呼叫紀錄與敏感資訊去重**：詳盡捕捉工具呼叫的輸入參數、Zod/Pydantic 驗證 Schema、耗時與 Token。若有安全性或 PII 隱私考量，可設定 `trace_include_sensitive_data=False` 限制 Span 僅記錄結構，自動去識別化 Prompt 與參數內容 `[12]`。
*   **推理思維鏈追蹤（Reasoning Spans）**：針對新一代推理模型，SDK 提供 **`ReasoningItem` / `RunReasoningItem` 等原生追蹤類型** `[13]`，能將模型的背景思考鏈（Thinking/Reasoning Spans）與決策路徑視覺化，幫助開發者精確診斷「模型在第幾步發生決策偏離」 `[14]`。
*   **協作項目事件化（Item Events）**：在 2026 年 1 月發布的 **Codex v0.85.0** 中，引入了 **app-server v2**，協作工具調用會被發射為**項目事件（Item Events）**，這讓實時的多代理協作渲染成為可能 `[7]`。

---

### 3. 記憶與上下文管理（Memory & Sessions）

*   **原生上下文壓縮（Native Compaction）**：由於長時程編程任務容易產生龐大的 Context 堆疊，GPT-5.2 Codex 具備專門訓練的**原生壓縮能力** `[15]`。它能自動對使用者與 Agent 的對話軌跡（Trajectory）和工具結果進行遞歸/層級摘要壓縮，在釋放 Context Token 的同時維持時間序列的語意關聯 `[7, 15]`。
*   **短期會話管理（Sessions）**：藉由 SDK 的 `Session` 接口（預置 `SQLiteSession`、`SQLAlchemySession`、或客製化的 `Oracle-backed Session`），自動在多次 `Runner.run()` 之間維持 raw 對話歷史的持久化與載入 `[16-18]`。
*   **短期與長期記憶分離（Separation of Concerns）**：
    1.  **會話歷史（Session items）**僅用於 short-term 歷史重播與 Continuity 復原 `[18]`。
    2.  **長期持久化知識**則與對話流分開，必須透過顯式的**長期記憶工具**（例如 `save_research_finding` 連接 Oracle AI Agent Memory）將 durable 發現、事實與決定獨立保存，實現跨 Session、跨線程的長期習得事實調用 `[17-19]`。

---

### 4. 多 Agent 協作支援

OpenAI Codex 完美支持多代理協作，並與 OpenAI Agents SDK 的協作 Primitives 深度融合，支援以下兩種核心協作模式：

*   **去中心化對等動態交接（Peer-to-Peer Handoffs）**：
    *   交接在 LLM 眼中就是一個名為 **`transfer_to_<agent_name>`** 的普通工具（Handoff Tool） `[20, 21]`。這將協作語意與 Tool-calling 統一，交由 LLM 在運行中自主決定何時轉移控制權 `[21]`。
    *   當 A Agent 調用交接工具時，SDK Runner 會暫停 A、加載 B 的 System instructions，並**攜帶當前會話歷史（Session History）無縫移轉給 B** 接管對答 `[22, 23]`。
    *   **安全防禦（Hop Counter）**：為避免 Agent 之間因邏輯衝突陷入 A \\(\rightarrow\\) B \\(\rightarrow\\) A 的「交接漂移（Handoff Drift）」無限循環，Runner 內建了**跳數限制器（Hop Counter）**，交接跳數超標即安全熔斷 `[24, 25]`。
*   **經理委託模式（Agent-as-a-Tool）**：
    *   透過 **`agent.as_tool()`** 將另一個 Codex Specialist 封裝為 Tool 供父 Agent 呼叫 `[26, 27]`。
    *   與 Handoff 不同，子代理**不接管 conversation 控制權**，其運作於獨立的 message history 邊界中；子代理執行完畢後僅將 Zod/Pydantic 驗證後的**強型別結構化結果返還給 Manager Agent**，控制權回歸 Manager，完美實現 Context 與權限隔離 `[28, 29]`。
*   **動態子代理生成（`spawn_agent`）**：
    在 Codex v0.85.0 中，`spawn_agent` 函數已支援傳入 **Agent 角色預設（Role Preset）**，使父 Agent 在運行時能根據任務需求，動態派生、配置並控制不同的子 Agent 完成更細緻的軟體工程分工 `[7]`。
*   **跨框架集成（Sub-agent connector）**：
    由於多代理協作接口高度工具化，Codex 本身也能被包裝為**其他 Harness 框架（例如 DeepSeek Harness）下的子 Agent 插件**，在外部大工作流中負責特定的編程子任務 `[30]`。

---
⚙️ **接下來，您可能想：**
評估 DeepSeek Harness 的 Cordis 插件化微內核架構，看它在「一切皆插件」的動態抽換設計上，與 OpenAI 這種固定 Loop、強調 vendor-engineered 連貫體驗的設計有何不同？

**引用來源：**
- [1] [[Cloudsway] 架構設計 - DeepSeek Harness 代理運行時概念解析](https://www.cloudsway.ai/resources/deepseek-harness-explained-what-is-an-agent-harness?id=21)
- [2] [Agents SDK | OpenAI API](https://developers.openai.com/api/docs/guides/agents)
- [3] [[OpenAI 官方] 官方文件 - Agents SDK 核心功能與開發指南](https://developers.openai.com/api/docs/guides/agents)
- [4] [[Cloudsway] 架構設計 - DeepSeek Harness 代理運行時概念解析](https://www.cloudsway.ai/resources/deepseek-harness-explained-what-is-an-agent-harness?id=21)
- [5] [How Coding Agents Work: Inside the Agentic Loop - Dr. TMA Pai Endowment Chair - ITIS](https://blog.ecitis.org/coding-agents-explained/)
- [6] [How Coding Agents Work: Inside the Agentic Loop - Dr. TMA Pai Endowment Chair - ITIS](https://blog.ecitis.org/coding-agents-explained/)
- [7] [How Coding Agents Work: Inside the Agentic Loop - Dr. TMA Pai Endowment Chair - ITIS](https://blog.ecitis.org/coding-agents-explained/)
- [8] [Tracing - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/tracing/)
- [9] [Instrument the OpenAI Agents SDK with OpenTelemetry (2026) - OpenObserve](https://openobserve.ai/blog/instrument-openai-agents-sdk-opentelemetry/)
- [10] [Instrument the OpenAI Agents SDK with OpenTelemetry (2026) - OpenObserve](https://openobserve.ai/blog/instrument-openai-agents-sdk-opentelemetry/)
- [11] [OpenAI Agents SDK - Braintrust](https://www.braintrust.dev/docs/integrations/agent-frameworks/openai-agents-sdk)
- [12] [Instrument the OpenAI Agents SDK with OpenTelemetry (2026) - OpenObserve](https://openobserve.ai/blog/instrument-openai-agents-sdk-opentelemetry/)
- [13] [Handoffs | OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-js/guides/handoffs/)
- [14] [[Sid Bharath] 技術部落格 - OpenAI Agents SDK 生態與核心機制解析](https://sidbharath.com/blog/openai-agents-sdk/)
- [15] [How Coding Agents Work: Inside the Agentic Loop - Dr. TMA Pai Endowment Chair - ITIS](https://blog.ecitis.org/coding-agents-explained/)
- [16] [OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-python/agents/)
- [17] [Enabling Long-Term Agent Memory with Oracle AI Agent Memory - OpenAI Developers](https://developers.openai.com/cookbook/examples/vector_databases/oracle_db/deep_research_openai_agents)
- [18] [Enabling Long-Term Agent Memory with Oracle AI Agent Memory - OpenAI Developers](https://developers.openai.com/cookbook/examples/vector_databases/oracle_db/deep_research_openai_agents)
- [19] [Enabling Long-Term Agent Memory with Oracle AI Agent Memory - OpenAI Developers](https://developers.openai.com/cookbook/examples/vector_databases/oracle_db/deep_research_openai_agents)
- [20] [[OpenAI 官方] 官方文件 - Agents SDK 的代理交接機制與控制權轉移](https://openai.github.io/openai-agents-python/handoffs/)
- [21] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [22] [Multi-Agent Systems Don't Fail at Reasoning. They Fail at Handoff. | Chanl Blog](https://www.channel.tel/blog/handoff-is-the-new-prompt)
- [23] [Swarm vs. Supervisor: Multi-Agent Architecture Guide - Augment Code](https://www.augmentcode.com/guides/swarm-vs-supervisor)
- [24] [OpenAI Swarm Multi-Agent Framework in 2026: What It Is, How It Works, and How to Use It | Lexogrine Blog](https://lexogrine.com/blog/openai-swarm-multi-agent-framework-2026)
- [25] [[Lexogrine] 教學指南 - 2026 年 OpenAI Swarm 輕量代理框架實戰手冊](https://lexogrine.com/blog/openai-swarm-multi-agent-framework-2026)
- [26] [Sandbox Agents | OpenAI API](https://developers.openai.com/api/docs/guides/agents/sandboxes)
- [27] [SandboxAgent - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/ref/sandbox/sandbox_agent/)
- [28] [Openai-agents handoff implementation - API](https://community.openai.com/t/openai-agents-handoff-implementation/1370797)
- [29] [SandboxAgent - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/ref/sandbox/sandbox_agent/)
- [30] [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)

### 2. DeepSeek 官方 Harness（https://deepseek.com/harness/en/）的架構設計、可觀測性能力、多 Agent 協作支援為何？

### 1. 架構設計：元框架 Cordis 與「一切皆插件」

DeepSeek 官方 Harness（簡稱 **`dsh`**，於 2026 年 8 月發布開發者預覽版 v0.1 `[1-4]`）在設計上採取了極具開創性的**「一切皆插件（Everything is a plugin）」**架構 `[2, 3, 5, 6]`。

* **無特權核心（No Privileged Core）**：與功能固定的傳統 Coding Agent 相比，`dsh` 的底層不包含任何特權核心，其模型適配器（Model Adapters）、工具註冊表、會話日誌、執行迴圈、沙箱、安全審核、遙測、甚至是使用者介面，全部模組化為**可隨時在執行時進行動態載入、移除和抽換的插件（Plugins）** `[2, 3, 7-10]`。
* **底層核心：Cordis 響應式微內核**：此架構由微內核元框架 **Cordis** 所驅動 `[5, 11-14]`，其設計理念源於學術論文《*A Programming Paradigm for Spatiotemporal Composability*》（時空可組合性的編程範式）`[3, 5, 12, 15, 16]`。Cordis 在 Runtime 層面定義了時空兩個維度的動態組合語意 `[17, 18]`：
  * **時間維度：可逆效應（Revertible Effects）**：插件載入時產生的任何副作用（如註冊命令、事件監聽、Socket 連線）都帶有其對應的「逆向操作（Inverse）」，由系統 Runtime 自動追蹤，並在插件卸載時自動清理，不殘留任何幽靈監聽器 `[17, 19]`。
  * **空間維度：響應式共效應（Reactive Coeffects）**：插件會宣告其對系統環境的依賴（如特定的資料庫、模型或外部服務）`[18, 19]`。Runtime 會持續監看依賴狀態，在依賴就緒前不激活該插件，並在依賴變更時動態重配 `[18, 19]`。
* **四種運行模式（Runtime Modes）**：藉由加載不同的預設插件組合，支援四種模式 `[3, 20-22]`：
  1. **Standard Mode（標準模式）**：搭載檔案編輯、Shell 存取、檔案與網頁搜尋、計劃、目標、子代理與工作流，屬於完整的自律型 Coding Agent 狀態 `[20, 22]`。
  2. **Code Mode（程式化模式 / PTC - Programmatic Tool Calling）**：允許模型直接生成 TypeScript 程式碼，並在單次執行的 TypeScript 程式中批量調用多個工具，大幅減少與 LLM 的往返交互回合 `[3, 20-22]`。
  3. **Minimal Mode（極簡模式）**：僅保留 persistent bash 與 `str_replace_editor` 兩個工具。這是 DeepSeek 官方用來進行模型基準測試（Benchmark）的乾淨執行環境 `[3, 20-22]`。
  4. **Creative Mode（創造者模式）**：允許 Agent 檢查當前的執行時（Runtime）、在記憶體中動態測試 Cordis 插件，並自主編排、組裝出全新的預設模式 `[3, 20-23]`。

---

### 2. 可觀測性能力（Observability）

DeepSeek Harness 將可觀測性視為系統核心，提供了一套高度透明、可回溯且具備安全審查的除錯機制 `[3, 24, 25]`：

* **單一追加會話日誌（Append-Only Session Log）**：一切模型所見——包括系統提示詞、思考鏈（Reasoning / CoT）、工具呼叫細節與回傳值、子代理排程狀態及每次上下文注入，全部會被實時寫入單一事件流日誌中 `[3, 24, 26]`。
* **軌跡檢視圖（Trajectory View）與指標追蹤**：在內建的本機 Web UI 中，開發者可以透過 Trajectory 視圖精確查看每一步的決策 `[3, 26-28]`。系統亦會實時曝露傳統框架隱藏的執行指標，如每秒 Token 數、**快取命中率（Cache Hit Rate）**、回合數、模型時間、工具時間、首字輸出時間（TTFT）與解碼時間等 `[3, 24, 27, 29]`。
* **可回溯、分叉與重放（Resume, Fork & Replay）**：由於日誌本質上是單一事件流，當 Agent 任務失敗或偏離軌道時，開發者可以**重放（Replay）**任務歷史，或是在歷史中的特定節點直接進行**分叉（Fork）**生成新分支 `[25, 26, 30]`。這使得比較不同 Loop 插件、工具或排程算法在相同對話上下文下的表現變得非常簡單 `[25]`。
* **安全與審查管道（Tool Pipeline）**：每一次工具調用（無論是 ReAct 還是 PTC 模式）都必須通過一個嚴格的擴展管道：**Hooks（鉤子攔截） \\(\rightarrow\\) Approvals（人工授權門禁） \\(\rightarrow\\) Permission Checks（權限控制，如唯讀或 dangerous-skip-permissions） \\(\rightarrow\\) Sandbox（沙箱隔離） \\(\rightarrow\\) Timeout（超時限制） \\(\rightarrow\\) 執行後結果重寫與日誌寫入** `[3, 14, 31-33]`。

---

### 3. 多 Agent 協作支援（Multi-Agent Collaboration）

DeepSeek Harness 擁有一套原生且深度模組化的多代理編排系統，並與其「一切皆插件」的哲學高度結合 `[3, 34]`：

* **編排原語（Spawn & Fork Primitives）**：
  * **`Spawn`**：主 Agent 可動態派生（Spawn）出子 Agent，並為其分配一個全新的對話上下文 `[34]`。
  * **`Fork`**：主 Agent 可通過 Fork 原語將自己當前的會話歷史完整傳遞給子 Agent，使其承接當前進度繼續執行 `[34]`。
* **動態與程序化編排（Workflows & Ralph Mode）**：
  * 當面對複雜任務時，模型可以使用其工作流工具動態編寫 JavaScript 程式碼，並調用 `parallel()`（並行）與 `pipeline()`（管道化）等函數，在運行時自主建立臨時的動態任務有向無環圖（DAG）`[3, 34]`。
  * 支援 **`Ralph Mode`**，允許多個新 Agent 輪流（Round-by-Round）地接替推動任務完成 `[3, 34]`。
* **協作模式的定位（Hierarchical Orchestration）**：DSH 的多代理協作本質上屬於 **Hierarchical Supervisor-Worker（層級式監督者-執行者）** 模式，並與並行管道、Ralph Loop 進行混合：由 Parent Agent 負責高階的任務分解、指派與結果驗證，Child Agents 則負責執行具體工作 `[35]`。
* **跨框架連接器（Harness-as-a-Sub-Agent）**：這是 DSH 的一大獨特優勢：由於多代理的協作接口高度插件化，開發者可以將**其他外部廠牌的 Coding Agent（如 Claude Code、OpenAI Codex，或相容 ACP 協議的 Agent）封裝為 DSH 的子 Agent 插件** `[36-40]`。這代表 DeepSeek Harness 可以坐在最上層擔任「總指揮/專案經理」，在特定步驟將子任務委託給 Claude Code 或 Codex 執行，並將回傳的結構化結果整合回主工作流中 `[37, 38]`。
* **當前侷限性（非 Swarm）**：儘管其多代理設計在架構上非常靈活，但目前**尚未實現真正的 Swarm 協作** `[35, 36]`。任務的決策與分發權限依然高度集中在 Parent Agent，Agent 之間仍無法進行去中心化的自主談判、競爭、動態對等交接或協商 `[35, 36]`。

---
💡 **接下來，您可能想：**
比較 Pydantic AI (搭配其 Coder/Researcher 官方 Harness 與 pydantic-graph) 在架構設計上與 DeepSeek Harness 的 Cordis 插件設計有何異同？

**引用來源：**
- [1] [[DataCamp] 教學指南 - 開源 DeepSeek Harness 代理環境部署與實作](https://www.datacamp.com/tutorial/deepseek-harness)
- [2] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [3] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [4] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [5] [[Cloudsway] 架構設計 - DeepSeek Harness 代理運行時概念解析](https://www.cloudsway.ai/resources/deepseek-harness-explained-what-is-an-agent-harness?id=21)
- [6] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [7] [[Helmcode] 學術論文分析 - DeepSeek Harness 的底層 Cordis 框架設計](https://helmcode.com/posts/cordis-paper-spatiotemporal-composability)
- [8] [[Cloudsway] 架構設計 - DeepSeek Harness 代理運行時概念解析](https://www.cloudsway.ai/resources/deepseek-harness-explained-what-is-an-agent-harness?id=21)
- [9] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [10] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [11] [[Helmcode] 學術論文分析 - DeepSeek Harness 的底層 Cordis 框架設計](https://helmcode.com/posts/cordis-paper-spatiotemporal-composability)
- [12] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [13] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [14] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [15] [[Helmcode] 學術論文分析 - DeepSeek Harness 的底層 Cordis 框架設計](https://helmcode.com/posts/cordis-paper-spatiotemporal-composability)
- [16] [[DeepSeek 開源] 開源專案 README - Harness 插件化代理運行時架構](https://github.com/deepseek-ai/deepseek-harness)
- [17] [[Helmcode] 學術論文分析 - DeepSeek Harness 的底層 Cordis 框架設計](https://helmcode.com/posts/cordis-paper-spatiotemporal-composability)
- [18] [[Helmcode] 學術論文分析 - DeepSeek Harness 的底層 Cordis 框架設計](https://helmcode.com/posts/cordis-paper-spatiotemporal-composability)
- [19] [[Helmcode] 學術論文分析 - DeepSeek Harness 的底層 Cordis 框架設計](https://helmcode.com/posts/cordis-paper-spatiotemporal-composability)
- [20] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [21] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [22] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [23] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [24] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [25] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [26] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [27] [[DataCamp] 教學指南 - 開源 DeepSeek Harness 代理環境部署與實作](https://www.datacamp.com/tutorial/deepseek-harness)
- [28] [[DataCamp] 教學指南 - 開源 DeepSeek Harness 代理環境部署與實作](https://www.datacamp.com/tutorial/deepseek-harness)
- [29] [[DataCamp] 教學指南 - 開源 DeepSeek Harness 代理環境部署與實作](https://www.datacamp.com/tutorial/deepseek-harness)
- [30] [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [31] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [32] [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [33] [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [34] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [35] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [36] [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [37] [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [38] [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [39] [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [40] [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)

### 3. Vercel AI SDK 的 Agent 架構設計、可觀測性能力（tracing/logging）、多 Agent 協作支援（Agent 交接、工作流編排）為何？

在盤點 Vercel 的 AI Agent 布局時，必須首先釐清一個在業界與開發者社群中極常被混淆的關鍵分工：**Vercel AI SDK 絕不等於 Vercel Eve** [1]。

*   **Vercel AI SDK** 是一個輕量級、非意見導向（Unopinionated）的**模型與串流 UI 整合層**，負責處理模型調用、串流輸出以及無狀態的單次 Request 工具調用 [1-3]。
*   **Vercel Eve**（於 2026 年 6 月 17 日在 Vercel Ship London 大會發布開源 Beta 版）則是一個**高階、基於檔案系統的「耐用（Durable）」型 Agent 開發框架**，提供狀態持久化、沙箱、通道、多代理協作及生產級基礎設施 [1, 4-6]。

以下為 Vercel 陣營（AI SDK 與 Eve）在架構設計、可觀測性及多 Agent 協作支援上的詳細剖析：

---

### 1. Agent 架構設計 (Architecture Design)

#### A. Vercel AI SDK：無狀態、輕量級的 ReAct Primitives
在 AI SDK 6 中，**「Agent」被定義為一個 Type（類型），而非 Class（類別）**，底層並不存在一個沉重的背景 Runtime 在進行調度 [4]。
*   **核心抽象（ToolLoopAgent）**：AI SDK 藉由 `ToolLoopAgent` 類別來實作標準的 ReAct 迴圈（Reasoning \\(\rightarrow\\) Act \\(\rightarrow\\) Observation）[7, 8]。開發者只需定義 Model、Zod 語法描述的 Tools、以及自訂的終止條件（例如 `stopWhen: stepCountIs(20)` 限制單次運轉不超過 20 步），即可在單次 Request 內自動執行多步工具調用 [3, 4, 7]。
*   **模型路由與控制（AI Gateway）**：透過 **Vercel AI Gateway**，Agent 程式碼得以使用單一 API 呼叫跨越 OpenAI、Anthropic、Google 等上百種模型 [3, 9, 10]。其內建自動容錯移轉（Failover）、限額與預算設定（Budgets） [3, 9, 10]。

#### B. Vercel Eve：檔案系統即 Agent (Agents-as-Directories)
Vercel Eve 的核心哲學是**「約定優於配置（Convention over Configuration）」** [11, 12]。它將 Agent 的所有原語對應到專案目錄的實體檔案中，並在建置時自動編譯：
*   **專案結構（Anatomy）** [13-15]：
    ```bash
    agent/
    ├── instructions.md      # 系統提示詞（System Prompt），採 Markdown 格式
    ├── agent.ts             # 運行時 Model 配置與定義（defineAgent）
    ├── tools/               # 每個工具獨立成單一 TypeScript 檔案（defineTool）
    ├── skills/              # 模組化的背景知識庫（Markdown Wiki）
    ├── subagents/           # 嵌套的子專案結構，用於多 Agent 角色
    ├── channels/            # 配置與 UI 通道（Slack, Discord 等）的對接
    └── connections/         # MCP 伺服器或外部 API 的連線定義
    ```
*   **Manifest 編譯機制**：在開發或部署（`vercel deploy`）時，Eve 的編譯器會自動遍歷並驗證該目錄，**自動將 Tools、Skills、Connections 編譯成一張 Manifest 清單並對接模型**，開發者完全不需要撰寫手動 Import 或註冊工具的 Boilerplate 程式碼 [12, 16]。
*   **動態知識載入 (Skills)**：將模組化的背景知識以 Markdown 格式放在 `skills/` 下，Eve 會在對話時自動進行 RAG 語意检索，僅載入與當前對話高度相關的 Skills，有效控制 Context Window 的 Token 消耗 [16-18]。
*   **耐用運行時（Durable Runtime）**：Eve 解決了 Serverless 部署中 long-running 任務容易因實例重啟或代碼部署而遺失記憶的痛點 [5, 19]。它**基於 Vercel Workflow 技術**，會將 Agent 的每一步執行、工具輸出記錄為事件日誌（Event Log），並透過**確定性重播（Deterministic Replay）**在 Cold Start 或超時重啟後無縫回復狀態 [5, 20, 21]。
*   **安全沙箱（Isolated Sandbox）**：當 Agent 需要寫入並執行 LLM 自主產生的程式碼或 Bash 命令時，Eve 會在 Vercel 平台上自動為其拉起一個**硬體隔離的 Firecracker microVM 容器**（本機開發時則使用 Docker） [5, 10, 22]。沙箱與主應用程式物理隔離，確保惡意代碼不會破壞生產系統 [5, 10]。

---

### 2. 可觀測性能力 (Observability, Tracing & Logging)

Vercel 陣營提供了從「底層標準追蹤」到「平台級即開即用儀表板」的完整可觀測性：

*   **OpenTelemetry（OTel）原生整合**：
    *   **Vercel AI SDK** 原生整合 OTel 規範，對 `ai.streamText`、`ai.generateText` 以及 `ai.toolCall` 等操作均有定義清晰的 Span [23-25]。
    *   **Eve 的 Trace 樹**：追蹤層級從頂層的 `ai.eve.turn` 一路向下追蹤至內部的模型與工具調用 [24]。
    *   **外接導出（OTel Exporters）**：開發者只需配置 `agent/instrumentation.ts`，Trace 數據即可無縫匯出至 **Braintrust（透過 Marketplace 集成）、LangSmith、Langfuse、Arize Phoenix**、Honeycomb 或 Datadog 等任何 OTel 相容後端 [24, 26]。
*   **Vercel Eve 專屬 Agent Runs 儀表板**：
    如果 Agent 部署於 Vercel，平台會**自動啟用「Agent Runs」專屬面板**，完全不需手動配置追蹤代碼 [1]。
    *   它提供**層級 Trace 樹狀圖**（呈現 Timing、Reasoning 與 Tool 呼叫內容） [1, 27]。
    *   實時曝露每輪對話的推理思維、Tool 呼叫參數/回傳 JSON [1, 27]。
    *   精確統計並區分 Input Token、Output Token 以及**快取 Token（Cached Tokens）**的成本與耗時 [1]。
*   **評估框架（Evals）**：
    Eve 內建了類似於 pytest 的**本機與 CI/CD 自動化評估管道**，允許團隊在發布前針對固定資料集（Dataset）批次執行「LLM-as-a-judge」或工具調用準確度（Tool-calling accuracy）測試，防止 Agent 的非確定性輸出產生退化（Regression） [24, 27]。

---

### 3. 多 Agent 協作支援 (Multi-Agent Collaboration)

#### A. Vercel AI SDK 的協作模式：分離式編排 (Orchestration Decoupling)
在 AI SDK 內部，**多 Agent 協作目前「並非」原生支援的內建功能** [28, 29]。
*   **分離式編排架構**：Vercel 官方與社群推薦的生產實踐為「分層架構」——**讓 AI SDK 專注於 LLM 的呼叫與 UI 串流（Streaming useChat/streamText）**，而多代理的「任務分解、工作流有向無環圖 (DAG) 編排、跨 Thread 狀態共享」則交由**外部編排器（如 open-multi-agent 框架、LangGraph 或自建狀態機）**處理 [30-32]。

#### B. Vercel Eve 的協作模式：層級子代理與耐用審批
Vercel Eve 將多 Agent 的組織與協作規範化，大幅降低了手寫狀態機的複雜度 [16, 17]：
*   **層級子代理模式（Hierarchical Subagents Pattern）**：
    在 Eve 中，只要在 `agent/subagents/` 下建立子資料夾，即可定義一個**完全嵌套、擁有獨立 `instructions.md` 與專屬 Tools 的 Subagent** [17, 18]。
    *   **上下文隔離**：子代理僅能存取父代理傳遞給它的 Scope Context，無法越權讀取或干擾父代理的全域狀態 [17, 18]。
    *   **編排控制**：父代理（通常擔任 Triage / Supervisor 角色）可以調用子代理作為一個「高階工具（Orchestration Tool）」，待子代理執行完畢後，返回結構化數據給父代理 [16-18]。
*   **人類協同審批門禁（Human-in-the-loop Approval Gates）**：
    當 Agent 協作需要觸及敏感、高風險的「寫入（Write）」或 State-changing 操作（例如 SQL 異動、發送退款）時，Eve 提供了原生的暫停與繼續機制 [16, 33]：
    *   **工具層級審批（Deferred Tools）**：在 TypeScript 定義中，只要在工具宣告加入 `approval: always()` [16, 33]。
    *   **無消耗暫停**：當 Agent 運行到此步驟時，**底層 Workflow 會將該執行執行個體掛起（Suspend）並凍結其狀態，此時完全不消耗任何 compute/CPU 資源** [22, 27]。
    *   **通道引發（Channel Trigger）**：Eve 會將包含「批准/拒絕」按鈕的卡片發送至 Slack 或 Discord 等外部通道 [16, 22, 33]。一旦人類點擊「核准」，通道會發送 Callback 至 HTTP Endpoint，Workflow 即利用 event log 重新載入，並從**剛才中斷的工具調用步驟直接 Resume 繼續運轉**，不需從頭開始重跑整段對話 [5, 22, 27]。

---
⚙️ **接下來，您可能想：**
評估如何將 Vercel AI SDK 的 `ToolLoopAgent` 無狀態迴圈 [8]，與 Vercel Workflows 的 `durable status` 進行手動結合 [34]，以此作為您 Cheerio 個人 AI 助理（W-074，傾向自建 harness）低耦合、高掌控力的底層狀態機設計？

**引用來源：**
- [1] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [2] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [3] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [4] [[The Road To Enterprise] 架構設計 - 企業級 Agent 框架選型必要性評估](https://theroadtoenterprise.com/blog/do-you-need-an-agent-framework)
- [5] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [6] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [7] [[The Road To Enterprise] 架構設計 - 企業級 Agent 框架選型必要性評估](https://theroadtoenterprise.com/blog/do-you-need-an-agent-framework)
- [8] [[Vercel 官方] 概念指南 - 開發者的 LLM Agent 核心概念與要素](https://vercel.com/i/llm-agent)
- [9] [[Vercel 官方] 設計指南 - 問題導向的代理型 AI 應用構建](https://vercel.com/i/building-agentic-ai-applications-with-a-problem-first-approach)
- [10] [[Vercel 官方] 概念指南 - 開發者的 LLM Agent 核心概念與要素](https://vercel.com/i/llm-agent)
- [11] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [12] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [13] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [14] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [15] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [16] [[GitHub 筆記] 技術筆記 - Vercel Eve 輕量級代理框架語法解析](https://github.com/coleam00/cole-medin-knowledge-base/blob/main/entities/tools/eve.md)
- [17] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [18] [[GitHub 筆記] 技術筆記 - Vercel Eve 輕量級代理框架語法解析](https://github.com/coleam00/cole-medin-knowledge-base/blob/main/entities/tools/eve.md)
- [19] [[The Road To Enterprise] 架構設計 - 企業級 Agent 框架選型必要性評估](https://theroadtoenterprise.com/blog/do-you-need-an-agent-framework)
- [20] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [21] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [22] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [23] [[Vercel 官方] 設計指南 - 問題導向的代理型 AI 應用構建](https://vercel.com/i/building-agentic-ai-applications-with-a-problem-first-approach)
- [24] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [25] [[Vercel 官方] 概念指南 - 開發者的 LLM Agent 核心概念與要素](https://vercel.com/i/llm-agent)
- [26] [[Vercel 官方] 技術部落格 - 生產環境 AI Agent 評估框架](https://vercel.com/i/ai-agent-evaluation-frameworks-production)
- [27] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [28] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [29] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [30] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [31] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [32] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [33] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [34] [[Vercel 官方] 設計指南 - 問題導向的代理型 AI 應用構建](https://vercel.com/i/building-agentic-ai-applications-with-a-problem-first-approach)

### 4. Microsoft 陣營（AutoGen、Semantic Kernel）的架構設計、可觀測性能力、多 Agent 協作模式（角色分工、群聊/工作流編排）為何？兩者定位差異是什麼？

在微軟（Microsoft）陣營的 AI Agent 生態中，**Semantic Kernel (SK)** 與 **AutoGen**（以及 2026 年最新演進的 **Microsoft Agent Framework (MAF)** 與社群分支 **AG2**）代表了兩種截然不同的技術哲學與架構設計 [1-4]。

以下針對兩者的定位差異、底層架構設計、可觀測性機制，以及多 Agent 協作模式進行深度盤點：

---

### 一、 兩者定位差異與 2026 年最新演進

*   **Semantic Kernel (SK) —— 企業級、Plugin 導向的開發者 SDK**
    *   **定位**：將大語言模型（LLM）作為**「編排器（Orchestrator）」**，用來動態調用企業既有的傳統代碼、API 與資料庫（Plugins）[5]。
    *   **特點**：強調強型別、可預測性、合規安全與企業級整合，原生支援 **.NET、Python 與 Java** [5, 6]。
*   **AutoGen —— 對話與研發導向的多代理（Multi-Agent）框架**
    *   **定位**：定位於**「對話導向（Conversation-first）」**，強調讓多個具備不同 Persona（角色設定）的 Agent 透過非結構化的對話與動態互動來共同解決複雜任務 [7-9]。
    *   **特點**：主要針對 **Python 生態系**，極度適合需要高度動態、探索性（R&D）以及辯論/評審（Adversarial/Critic）機制的場景 [10-12]。
*   **2026 年最新局勢：統一架構 Microsoft Agent Framework (MAF)**
    *   為了解決 AutoGen 動態對話在生產環境容易失控、以及 SK 缺乏靈活多代理編排的痛點，微軟於 2025 年 10 月宣布推出 **Microsoft Agent Framework (MAF)**（於 2026 年 4 月發布 1.0 正式版），旨在**統一 SK 的企業級安全/狀態管理基礎與 AutoGen 的多代理編排理念** [13]。
    *   在此背景下，原版 AutoGen 已進入維護模式 [4]。新專案官方建議直接採用 **MAF** [14]，而開源社群則以 **AG2（formerly AutoGen）** 作為獨立分支繼續維護與演進 [2, 4]。

---

### 二、 架構設計 (Architecture Design)

#### 1. Semantic Kernel：SDK-first 的強型別微內核
SK 的 Agent 架構是其 core SDK 的延伸，將工具與提示詞抽象化並與編程緊密結合：
*   **核心原語**：由 **`Kernel`** 來編排 **`Plugins`**（Native functions / 自訂代碼工具）與 **`Functions`**（Semantic prompts / 提示詞）[5, 15]。
*   **Agent 抽象**：提供 **`ChatCompletionAgent`**（單一會話型代理）與 `OpenAIAssistantAgent` 作為基礎類別 [16]。
*   **會話與狀態**：conversations 狀態由強型別的 **`ChatHistory`** 或 **`ChatHistoryAgentThread`** 維持，以便在多輪對話中保留上下文 [17, 18]。
*   **宣告式定義（Declarative Spec）**：支援從 **YAML 宣告式規格書**直接實例化 `ChatCompletionAgent` [19]。其行為、提示詞、可用工具均在 YAML 中定義，方便版本控制與審計 [19]。

#### 2. AutoGen (v0.4 / AG2)：非同步、事件驅動的角色模型（Actor Model）
AutoGen 於 v0.4 進行了重大重構，拋棄了舊版同步循環的設計，改採**非同步、事件驅動的角色模型（Asynchronous Event-Driven Actor Model）**，架構解耦為三層 [20]：
*   **AgentChat**：高階 API 層。提供直觀的對話與協作定義（如 `AssistantAgent`、`UserProxyAgent`、Team 團隊等），框架會自動處理內部的對話循環，適合快速原型設計 [2, 21, 22]。
*   **Core (`autogen-core`)**：底層事件驅動層。每個 Agent 本質上是一個 **Actor（事件處理器）**，擁有獨立郵箱（Mailbox），透過發送/接收非同步訊息與事件來做出反應，適合高併發、防阻塞的 resilient 管道 [23, 24]。
*   **Extensions (`autogen-ext`)**：負責將特定 LLM 客戶端（例如 `OpenAIChatCompletionClient`）與自訂工具（透過 `FunctionTool` 依據型別提示自動生成 schema）對接到框架中 [2, 21]。

---

### 三、 可觀測性能力 (Observability)

*   **Semantic Kernel：流程攔截與過濾器機制**
    *   SK 提供 **`IFunctionInvocationFilter`**（與 `IAutoFunctionInvocationFilter`）接口 [25, 26]。
    *   這是一種強控制的**「攔截器（Interceptors）」機制**：開發者可以註冊一個 Filter，在 Plugin 函數被調用**「之前」**與**「之後」**強制進行攔截，精確捕獲輸入參數（`context.Arguments`）與工具輸出結果（`context.Result`）[26, 27]。這種方式極其適合對高風險工具進行強審計與日誌記錄。
*   **AutoGen (v0.4 / AG2)：實時事件流與執行串流**
    *   在非同步 actor 架構下，系統在執行時會向外拋出豐富的**實時運行事件（Events）**，包含模型呼叫、工具呼叫、狀態變遷與終止條件觸發 [2]。
    *   支援**實時運行串流（`run_stream` / `team.run_stream`）**，能將訊息增量（deltas）、工具執行事件與 Actor 切換信號，實時地 pipe 導出到日誌系統或前端 UIs，便於追蹤 Agent 在多輪對話中的微觀決策 [21, 23]。

---

### 四、 多 Agent 協作模式 (Multi-Agent Collaboration)

#### 1. Semantic Kernel：顯式編排與策略門禁
SK 的多代理協作高度依賴結構化的 **`AgentGroupChat`** 物件，並由顯式的執行設定進行嚴格控制 [28, 29]：
*   **發言選擇策略 (`SelectionStrategy`)**：用於控制「下一步由誰發言」 [30]。
    *   `SequentialSelectionStrategy` / `RoundRobinSelectionStrategy`：使代理依序或輪流發言 [31, 32]。
    *   **`KernelFunctionSelectionStrategy`**：開發者提供一個專門的 prompt function，LLM 會在運行時閱讀當前的 `ChatHistory` 並**輸出下一位最適合發言的 Agent 名字** [31]。
*   **對話終止策略 (`TerminationStrategy`)**：用於防止對話陷入無限循環並控制 Token 費用 [32, 33]。
    *   **`KernelFunctionTerminationStrategy`**：提供一個審查 prompt function（例如判斷 Reviewer 代理是否在其發言中提到 "APPROVED" 或 "yes"），若符合則判定任務完成、結束會話 [34]。
    *   `AggregatorTerminationStrategy`：將多個終止條件進行組合（例如滿足任一或全部時結束） [34]。
    *   `MaximumIterations`：硬性限制最大回合數以作為安全底線 [34]。
*   **狀態重置**：任務完成後，`AgentGroupChat` 會轉入 Completed 狀態，必須呼叫 `ResetAsync()` / `reset()` 才能清除對話狀態並重複使用該實例 [35, 36]。

#### 2. AutoGen (v0.4 / AG2)：動態對話「Teams」與 Graph 混合
AutoGen 擺脫了單純的 static graphs，其協作主要透過 **Teams** 與不同的通訊拓撲（Topology）來定義 [37]：
*   **GroupChat / RoundRobinGroupChat**：代理們在同一個 Thread 內自由討論，並由 `GroupChatManager` 根據歷史語意判定發言順序 [38, 39]。
*   **Swarm 模式**：去中心化、LLM 驅動的協作 [39]。當前 Agent 可以透過呼叫 tool-like delegation，在運行中將任務動態分派或交接給最契合的 Specialist 代理，形成一種湧現（Emergent）的自發協作路徑 [39]。
*   **GraphFlow 模式**：當需要高度確定性時，AutoGen 支援將多代理協作定義為一個**有向無環圖（DAG）**，由開發者顯式指定 Agent 節點間的分支流轉與狀態轉移 [39]。
*   **Debate 模式**：多個 Agent 針對同一主題各抒己見，並引入裁判（Judge）代理評審最終產出 [39]。
*   **UserProxyAgent（人機協同）**：將人類包裝成一個特殊的 Agent 參與對話，透過設定 `human_input_mode`（ALWAYS, TERMINATE, NEVER）來決定何時暫停並要求人類發言或進行敏感工具審批 [38, 40, 41]。

---
⚙️ **接下來，您可能想：**
評估如何將 SK 的 `IFunctionInvocationFilter` 攔截器設計，或 AutoGen 的 `UserProxyAgent` 人機協同模式，直接借鑑並實作到您 Cheerio 個人 AI 助理（W-074，傾向自建 harness）的底層控制流中？

**引用來源：**
- [1] [[Mastra 官方] 比較分析 - AI Agent 框架選型評估指南](https://mastra.ai/articles/ai-agent-framework)
- [2] [[AG2 官方] 教學指南 - AutoGen v0.4 事件驅動與可觀測代理開發](https://cohorte.co/blog/autogen-v0-4-ag2-crash-course-build-event-driven-observable-ai-agents-that-scale)
- [3] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [4] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [5] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [6] [[Mastra 官方] 比較分析 - AI Agent 框架選型評估指南](https://mastra.ai/articles/ai-agent-framework)
- [7] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [8] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [9] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [10] [[Mastra 官方] 比較分析 - AI Agent 框架選型評估指南](https://mastra.ai/articles/ai-agent-framework)
- [11] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [12] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [13] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [14] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [15] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [16] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [17] [[Microsoft 官方] 官方文件 - Semantic Kernel ChatCompletionAgent 運作原理](https://learn.microsoft.com/en-us/semantic-kernel/frameworks/agent/agent-types/chat-completion-agent)
- [18] [[Microsoft 官方] 官方文件 - Semantic Kernel ChatCompletionAgent 運作原理](https://learn.microsoft.com/en-us/semantic-kernel/frameworks/agent/agent-types/chat-completion-agent)
- [19] [[Microsoft 官方] 官方文件 - Semantic Kernel ChatCompletionAgent 運作原理](https://learn.microsoft.com/en-us/semantic-kernel/frameworks/agent/agent-types/chat-completion-agent)
- [20] [[AG2 官方] 教學指南 - AutoGen v0.4 事件驅動與可觀測代理開發](https://cohorte.co/blog/autogen-v0-4-ag2-crash-course-build-event-driven-observable-ai-agents-that-scale)
- [21] [[AG2 官方] 教學指南 - AutoGen v0.4 事件驅動與可觀測代理開發](https://cohorte.co/blog/autogen-v0-4-ag2-crash-course-build-event-driven-observable-ai-agents-that-scale)
- [22] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [23] [[AG2 官方] 教學指南 - AutoGen v0.4 事件驅動與可觀測代理開發](https://cohorte.co/blog/autogen-v0-4-ag2-crash-course-build-event-driven-observable-ai-agents-that-scale)
- [24] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [25] [[Microsoft 官方] 官方文件 - Semantic Kernel 代理與群聊編排機制](https://dev.to/bspann/ai-agents-in-semantic-kernel-chatcompletionagent-agentgroupchat-and-orchestration-50am)
- [26] [[Systenics AI] 實作指南 - 使用 Semantic Kernel 代理框架建置 Agent](https://systenics.ai/blog/2025-04-09-building-ai-agent-using-semantic-kernel-agent-framework)
- [27] [[Systenics AI] 實作指南 - 使用 Semantic Kernel 代理框架建置 Agent](https://systenics.ai/blog/2025-04-09-building-ai-agent-using-semantic-kernel-agent-framework)
- [28] [[Microsoft 官方] 官方文件 - Semantic Kernel 代理與群聊編排機制](https://dev.to/bspann/ai-agents-in-semantic-kernel-chatcompletionagent-agentgroupchat-and-orchestration-50am)
- [29] [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [30] [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [31] [[Microsoft 官方] 官方文件 - Semantic Kernel 代理與群聊編排機制](https://dev.to/bspann/ai-agents-in-semantic-kernel-chatcompletionagent-agentgroupchat-and-orchestration-50am)
- [32] [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [33] [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [34] [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [35] [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [36] [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [37] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [38] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [39] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [40] [[AG2 官方] 教學指南 - AutoGen v0.4 事件驅動與可觀測代理開發](https://cohorte.co/blog/autogen-v0-4-ag2-crash-course-build-event-driven-observable-ai-agents-that-scale)
- [41] [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)

### 5. Python 生態圈主流 Agent Framework（LangChain/LangGraph、CrewAI、OpenAI Agents SDK、smolagents、Pydantic AI 等）在架構設計、可觀測性、多 Agent 協作能力上的比較？

在 Python AI Agent 生態圈中，主流的五大框架——**LangChain/LangGraph、Pydantic AI、OpenAI Agents SDK、smolagents（Hugging Face）與 CrewAI**——各自代表了不同的軟體工程哲學、狀態管理策略與適用場景 [1, 2]。

以下針對**架構設計、生產級可觀測性、以及多 Agent 協作能力**三個技術維度，進行深度的技術對比與選型剖析：

---

### 一、 核心架構設計與狀態持久化比較 (Architecture & State Persistence)

#### 1. LangGraph：顯式狀態圖與線程級快取
*   **圖狀態機（Stateful Graph）**：LangGraph 將 Agent 的控制流抽象為**圖（Graph）中的節點（Nodes，代表運算或 LLM）與邊（Edges，代表條件分支與路由）** [3-5]。它在節點間傳遞並變更一個全域且強型別的**全局狀態（State）** [3]。
*   **短期會話記憶（Checkpointer）**：這是 LangGraph 最強大的機制 [6]。透過 `checkpointer` 接口，系統會在圖中的每個「超步（Super-step）」結束時，自動將 Graph State 序列化並寫入持久化資料庫 [5, 7]。
    *   **資料庫適配器**：內建用於開發測試的 `InMemorySaver` [8, 9]，並提供生產級的 `PostgresSaver` [10, 11]、`RedisSaver` [10]、`MongoDBSaver` [12] 等適配器。
    *   **AWS 專屬 DynamoDBSaver**：對於小於 350 KB 的 Checkpoint 直接以二進位序列化存入 DynamoDB；大於 350 KB 則將 Payload 上傳至 Amazon S3，DynamoDB 僅保存指標，無痛規避資料庫大小限制 [13]。
    *   此設計原生支援**「時間旅行（Time Travel，回溯至任意歷史步驟）」**、**人工中斷審批（Interrupts）**、以及當機重啟後的 **Resume（免重複執行完成的節點）** [5, 7, 8]。
*   **長期跨對話記憶（Store）**：與綁定單一對話線程（Thread）的 Checkpointer 不同，LangGraph 提供了 `BaseStore`，可用於跨線程持久化儲存全域的用戶偏好、習得事實或 RAG 知識 [6, 14-16]。

#### 2. Pydantic AI：型別安全、依賴注入與「耐用執行」
*   **端到端型別安全（Plain Python & Typed）**：由 Pydantic 官方推出，堅持「AI should be plain Python」[17, 18]。Agent 建模為強型別的泛型 `Agent[Deps, Output]` [19]。透過 `RunContext[Deps]`，在執行時將資料庫連線、 Tenant 安全憑證等以型別安全的方式進行**依賴注入（Dependency Injection）** [20-22]。
*   **有限狀態機與能力（Capabilities）**：在底層，Pydantic AI 整合了其獨立發布的 **pydantic-graph**（一個純型別的有限狀態 FSM 庫），來靜態約束 LLM 請求和 Tool 調用節點的流轉 [18, 23]。同時，它將工具、系統提示詞、生命週期 hooks 包裝成可複用的 **Capability（能力）**，支援 **On-Demand（依需/動態）加載能力** [18, 24, 25]。
*   **耐用執行（Durable Execution）**：與 Postgres 輕量持久化引擎 **DBOS**（以及 Temporal、Prefect）進行第一方原生整合 [18, 26]。 wrapped 為 `DBOSAgent` 後，系統會自動在 PostgreSQL 資料庫中為每步執行與 MCP 通訊建立 Transaction Checkpoints [27-29]。若遭遇伺服器重啟、部署中斷或 API 逾時，Agent 重啟時會**自動從上次成功的 checkpoint 重播（Replay）Resume，絕不重複執行昂貴的 LLM 呼叫或引發重複的 Side-effects（如重發 Email）** [28-31]。

#### 3. OpenAI Agents SDK：無狀態 Primitives 與雲端歷史壓縮
*   **極低抽象設計**：前身為 Swarm 教育框架，為生產級升級版 [32-34]。僅保留五大核心原語：**Agent、Handoff、Guardrail、Session、Tracing** [32, 35]。拒絕複雜的圖語法或實體文件編譯，完全由 Runner 運行時自動驅動 ReAct 工具調用迴圈 [36, 37]。
*   **會話管理與 Responses 自動壓縮**：提供 Sessions 接口（如 `SQLiteSession`、`SQLAlchemySession`）自動持久化對話 [38-40]。
    *   **ResponsesCompaction**：為了解決 Context Window 膨脹與 Prompt Cache 失效，內建 `OpenAIResponsesCompactionSession` 裝飾器 [41, 42]。當歷史累積超過設定閾值（如 10 個非 User 訊息）時，會**在背景自動調用雲端的 `responses.compact` API**，由背景模型把歷史中的多輪工具對話與 Verbose 訊息精簡為合成的、簡短的「User-Assistant 摘要對」，在背景無感完成 Context 裁剪 [41, 42]。

#### 4. smolagents：代碼即工具（Code-first）與安全沙箱
*   **AST 程式碼執行（CodeAgent）**： smolagents 的核心哲學是 LLM 會直接輸出一段 **Python 程式碼（代碼區塊）** 來呼叫工具與處理資料，而非產生 JSON 字串 [43-45]。這樣能將 Multi-step 的 loops 循環、If-else 條件式和多工具調用，**壓縮在單次 LLM 往返（Single request-response）中完成**，大幅縮減對話回合與延遲 [44-46]。
*   **沙箱執行邊界（Security Sandbox）**：由於執行 LLM 生成的代碼具有極高的安全風險，smolagents 本地的 AST 解析器 `LocalPythonExecutor` 僅提供 best-effort 限制，**並非安全邊界** [46-48]。在生產環境中，它**強制要求**配置硬體隔離的遠端沙箱（支援 **Docker、E2B、Modal、Blaxel**）來隔離執行 environment [43, 46, 49]。
*   **記憶設計**：預設的 memory 僅存活於單次運行的 step logs 中 [50, 51]。若需進階記憶，社群提供與 **Hindsight** 記憶庫的整合，賦予 Agent事實儲存（Retain）、語意檢索（Recall）與反思（Reflect）的三層記憶能力 [52]。

#### 5. CrewAI：角色扮演與任務管道編排
*   **應用層高階抽象**：以 **Role-Play（角色扮演）**為設計中樞 [53, 54]。其核心原語包括具備 Persona 設定的 **Agents**、嚴格約束 output 格式的 **Tasks**、和串聯團隊的 **Crews** [53, 55]。
*   **自動化 Embedding 記憶系統**：自帶完備的記憶適配器，會在對話與任務執行期間，自動將重要事實與用戶偏好進行語意切片，並**自動產生 Embedding 寫入向量資料庫**，實現 LTM/STM 的 RAG 召回 [56]。

---

### 二、 生產級可觀測性與評估能力對比 (Observability & Telemetry)

| 評估維度 | LangGraph | Pydantic AI | OpenAI Agents SDK | smolagents | CrewAI |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **底層標準** | 整合 LangChain Tracing 與 OTel [57, 58] | 標準 OpenTelemetry (OTel) 語意 [18, 59, 60] | 符合 OTel GenAI 語意標準 [38, 59] | 整合 OpenInference (OTel 衍生) [61] | 依賴 OTel Bridge 導出 [58] |
| **首選觀測平台** | **LangSmith** [3, 4] | **Pydantic Logfire** [60, 62] | **OpenAI 官方 Dashboard** [38] | **Arize AX / Phoenix** [61] | **Logfire / Langfuse** [58] |
| **白盒除錯原語** | 可導出 Pending Writes、圖節點狀態 snapshot 進行 replay [7, 63] | 提供 **`Agent.iter`** / `run_stream_events` 進行逐一節點/事件的白盒驅動 [23, 64] | 支持 OTel spans 導出；可直接在 OpenAI 控制台可視化推理模型的 **Thinking 思維鏈** [38, 65] | 提供本機 **`agent.replay()`** [66] 與 `agent.memory.steps` 訪問，供 AST 除錯 [66] | 主要提供宏觀的 Task 執行進度日誌，微觀 LLM trace 需完全外接遙測平台 [58] |
| **評估/測試框架** | LangSmith Evals [3] | **Pydantic Evals**（代碼優先，CI/CD 自動化回歸測試） [18, 67] | 內建對接 OpenAI Evals 工具鏈 [38] | 整合 OpenInference Evals [68] | 需自行手寫測試腳本 [55] |

---

### 三、 多 Agent 協作與編排模式比較 (Multi-Agent Collaboration)

#### 1. LangGraph：顯式狀態圖與 Reducer 合併
*   **編排模式**：多代理協作完全建立在 `StateGraph` 的拓撲上 [3-5]。
*   **控制權與上下文**：利用 **Reducer 函數** 對全域 State Channels 的通道值進行合併（例如：`add_messages` 彙整對話歷史） [5, 14]。支援**嵌套子圖（Subgraphs）**來實現上下文隔離，僅在父子圖邊界進行狀態映對 [69]。
*   **優缺點**：邏輯極其嚴謹，由圖的拓撲邊硬性約束，最不容易發生「代理決策漂移」，但代價是開發者需要手寫大量的狀態轉移、邊界路由邏輯。

#### 2. Pydantic AI：層級委託（Agent-as-a-Tool）與耐用子工作流
*   **編排模式**：主要採取 **Hierarchical Supervisor-Worker（層級式監督-執行）** 模式 [65, 70]。
*   **控制權與上下文**：主 Agent（Supervisor）藉由將子 Agent 包裝成普通的 `FunctionTool` 進行委託 [28, 65, 70, 71]。子 Agent 的 system instruction、私有變量與 tools 完全隔離，執行完畢後以 Pydantic 強型別的 `BaseModel` 作為結構化 JSON 返還給主 Agent 整合 [65, 70]。
*   **耐用保障**：在 DBOS 環境下，主 Agent 調用子 Agent 會自動被實例化為**耐用子工作流（Durable Child Workflows）** [28, 71]，擁有獨立的事務 checkpoint 與錯誤重試/隔離，極其適合企業級複雜 API 鏈結 [28, 71]。

#### 3. OpenAI Agents SDK：動態 Peer-to-Peer 交接（Handoff）
*   **編排模式**：主打 **去中心化對等交接（Handoff）** 模式，與 `Agent-as-a-Tool` 經理模式雙軌並行 [38, 65, 72]。
*   **控制權與上下文**：Handoff 被優雅地封裝為 LLM 視角下的普通工具，命名為 **`transfer_to_<agent_name>`** [35, 73]。一旦 triage（分類）代理在對話中決定交接，它會調用該工具 [73]。SDK Runner 在執行時會**暫停當前 Agent 迴圈、加載目標 Agent 的系統指令，並將當前會話歷史（Session History）無縫移轉給目標 Agent** 繼續運行 [73]。
*   **安全防禦（Hop Counter）**：為避免 Agent 之間因規則衝突陷入 A \\(\rightarrow\\) B \\(\rightarrow\\) A 的「交接漂移（Handoff Drift）」無限循環，Runner 內建了**跳數計數器（Hop Counter）**，交接跳數超標即安全熔斷 [74-76]。

#### 4. smolagents：Orchestrator-Worker 與步驟上下文隔離
*   **編排模式**：主 `Orchestrator`（通常也是 CodeAgent）作為中樞調度 [77, 78]。
*   **控制權與上下文**：Orchestrator 在其 Python 代碼中程序化地調度多個專職 Specialist 子代理（例如 Inventory 庫存、Quote 報價、Order 訂單） [78, 79]。
*   **上下文隔離**：為了阻絕 Context window 的線性增長，**子 Agent 內部運行的 step-by-step 思考日誌、工具日誌被物理隔絕（Private History）**，Orchestrator 僅把 Specialist 最終返回的 JSON 結構化數據，塞入下一個專家的 Prompt 中，以此控制 Token 消耗 [50, 51, 80]。

#### 5. CrewAI：角色扮演與 Tasks 業務管道
*   **編排模式**：以 **Process**（例如順序 `Sequential` 或層級 `Hierarchical`）串聯 Task 管道 [53, 55]。
*   **控制權與上下文**：協作基於高度業務化的 Task 依賴鏈 [53, 55]。Task 1 產生的 `expected_output` 強型別結果會自動傳遞給 Task 2 對應的 Specialist Agent persona，類似於企業流水線作業，適合處理固定且可預測的業務流程 [53, 55]。

---
⚙️ **接下來，您可能想：**
針對您 Cheerio 個人 AI 助理（W-074，傾向自建 harness）的具體需求 [81]：
1. **評估** 是否直接引入 Pydantic AI 的 `RunContext` 與依賴注入機制，以型別安全的方式傳遞 Cheerio 的爬蟲實例與本地資料庫連接 [19, 20]？
2. **探討** 如何在您自建的 Harness 中，借鑑 OpenAI Agents SDK 的 `transfer_to_` 交接原語，以最低的程式碼行數實作動態多代理分工 [73]？

**引用來源：**
- [1] [[Mastra 官方] 比較分析 - AI Agent 框架選型評估指南](https://mastra.ai/articles/ai-agent-framework)
- [2] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [3] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [4] [[Mastra 官方] 比較分析 - AI Agent 框架選型評估指南](https://mastra.ai/articles/ai-agent-framework)
- [5] [[AWS 官方] 實作指南 - 結合 LangGraph 與 DynamoDB 構建耐用代理](https://aws.amazon.com/blogs/database/build-durable-ai-agents-with-langgraph-and-amazon-dynamodb/)
- [6] [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [7] [[LangChain 官方] API 參考 - langgraph.checkpoint 模組詳細類別與方法](https://reference.langchain.com/python/langgraph.checkpoint)
- [8] [[AWS 官方] 實作指南 - 結合 LangGraph 與 DynamoDB 構建耐用代理](https://aws.amazon.com/blogs/database/build-durable-ai-agents-with-langgraph-and-amazon-dynamodb/)
- [9] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [10] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [11] [[LangChain 官方] API 參考 - LangGraph 的 Checkpointer 持久化接口說明](https://reference.langchain.com/python/langgraph/checkpoints)
- [12] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [13] [[AWS 官方] 實作指南 - 結合 LangGraph 與 DynamoDB 構建耐用代理](https://aws.amazon.com/blogs/database/build-durable-ai-agents-with-langgraph-and-amazon-dynamodb/)
- [14] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [15] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [16] [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [17] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [18] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [19] [[Pydantic 官方] 官方文件 - Pydantic AI 代理程式設計](https://pydantic.dev/docs/ai/core-concepts/agent/)
- [20] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [21] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [22] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [23] [[Pydantic 官方] 官方文件 - Pydantic AI 代理程式設計](https://pydantic.dev/docs/ai/core-concepts/agent/)
- [24] [[Pydantic 官方] 官方文件 - Pydantic AI 功能與核心能力規格](https://pydantic.dev/docs/ai/capabilities/overview/)
- [25] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [26] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [27] [[DBOS 官方] 實作指南 - 結合 Pydantic AI 與耐用執行構建可靠代理](https://pydantic.dev/articles/pydantic-ai-dbos)
- [28] [[DBOS 官方] 實作指南 - 結合 Pydantic AI 與耐用執行構建可靠代理](https://pydantic.dev/articles/pydantic-ai-dbos)
- [29] [[DBOS 官方] 實作指南 - 結合 Pydantic AI 與耐用執行構建可靠代理](https://pydantic.dev/articles/pydantic-ai-dbos)
- [30] [[DBOS 官方] 實作指南 - 結合 Pydantic AI 與耐用執行構建可靠代理](https://pydantic.dev/articles/pydantic-ai-dbos)
- [31] [[DBOS 官方] 實作指南 - 結合 Pydantic AI 與耐用執行構建可靠代理](https://pydantic.dev/articles/pydantic-ai-dbos)
- [32] [[OpenAI 官方] 官方文件 - Agents SDK 使用手冊與架構概覽](https://openai.github.io/openai-agents-python/)
- [33] [[Sid Bharath] 技術部落格 - OpenAI Agents SDK 生態與核心機制解析](https://sidbharath.com/blog/openai-agents-sdk/)
- [34] [[Lexogrine] 教學指南 - 2026 年 OpenAI Swarm 輕量代理框架實戰手冊](https://lexogrine.com/blog/openai-swarm-multi-agent-framework-2026)
- [35] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [36] [[Sid Bharath] 技術部落格 - OpenAI Agents SDK 生態與核心機制解析](https://sidbharath.com/blog/openai-agents-sdk/)
- [37] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [38] [[OpenAI 官方] 官方文件 - Agents SDK 使用手冊與架構概覽](https://openai.github.io/openai-agents-python/)
- [39] [[Sid Bharath] 技術部落格 - OpenAI Agents SDK 生態與核心機制解析](https://sidbharath.com/blog/openai-agents-sdk/)
- [40] [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [41] [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [42] [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [43] [[Hugging Face 開源] 開源專案 README - smolagents 代碼執行代理詳細規格](https://github.com/huggingface/smolagents)
- [44] [[Hugging Face 開源] 開源專案 README - smolagents 代碼執行代理詳細規格](https://github.com/huggingface/smolagents)
- [45] [[Hugging Face 開源] 開源專案 README - smolagents 代碼執行代理詳細規格](https://github.com/huggingface/smolagents)
- [46] [[Hugging Face 開源] 開源專案 README - smolagents 代碼執行代理詳細規格](https://github.com/huggingface/smolagents)
- [47] [[安全研究] 安全分析 - smolagents CodeAgent 的程式碼執行安全風險](https://www.nccgroup.com/research/autonomous-ai-agents-a-hidden-risk-in-insecure-smolagents-codeagent-usage/)
- [48] [[Hugging Face 開源] 開源專案 README - smolagents 代碼執行代理詳細規格](https://github.com/huggingface/smolagents)
- [49] [[Hugging Face 開源] 開源專案 README - smolagents 框架結構與快速開始](https://huggingface.co/docs/smolagents/index)
- [50] [[開發者社群] 案例研究 - 基於 smolagents 的訂單處理多代理與記憶實作](https://ayotomiwasalau.com/work/blogs/smolagents-framework-building-multi-agents-tools-and-memory)
- [51] [[開發者社群] 案例研究 - 基於 smolagents 的訂單處理多代理與記憶實作](https://ayotomiwasalau.com/work/blogs/smolagents-framework-building-multi-agents-tools-and-memory)
- [52] [[Hindsight] 技術部落格 - smolagents 代理記憶機制的留存與反射](https://hindsight.vectorize.io/blog/2026/04/29/smolagents-memory-tools)
- [53] [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [54] [[Arize AI] 架構設計 - OpenAI Swarm 輕量級多代理協作與編排解析](https://arize.com/blog/comparing-openai-swarm)
- [55] [[開發者社群] 實作指南 - 開發者 AI Agent 基礎構建實戰指南](https://mastra.ai/articles/how-to-build-ai-agents)
- [56] [[Mastra 官方] 架構設計 - AI Agent 記憶平台與上下文管理機制](https://mastra.ai/articles/agent-memory-platform)
- [57] [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [58] [[Pydantic 官方] 技術部落格 - Pydantic AI 框架發布與設計哲學](https://pydantic.dev/)
- [59] [[Pydantic 官方] 官方文件 - Pydantic AI 功能與核心能力規格](https://pydantic.dev/docs/ai/capabilities/overview/)
- [60] [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [61] [[Arize AI] 官方文件 - smolagents 整合可觀測性追蹤與評估](https://arize.com/docs/ax/integrations/python-agent-frameworks/hugging-face-smolagents/smolagents-tracing)
- [62] [[Pydantic 官方] 官方文件 - Pydantic AI 代理程式設計](https://pydantic.dev/docs/ai/core-concepts/agent/)
- [63] [[LangChain 官方] API 參考 - LangGraph 的 Checkpointer 持久化接口說明](https://reference.langchain.com/python/langgraph/checkpoints)
- [64] [[Pydantic 官方] 官方文件 - Pydantic AI 代理程式設計](https://pydantic.dev/docs/ai/core-concepts/agent/)
- [65] [[OpenAI 官方] 官方文件 - Agents SDK 核心功能與開發指南](https://developers.openai.com/api/docs/guides/agents)
- [66] [[Hugging Face 官方] 官方文件 - Transformer Agents 記憶管理機制](https://huggingface.co/docs/smolagents/tutorials/memory)
- [67] [[Pydantic 官方] 官方文件 - Pydantic AI 代理程式設計](https://pydantic.dev/docs/ai/core-concepts/agent/)
- [68] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [69] [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [70] [[Vstorm] 案例研究 - 基於 Pydantic AI 的 Text-to-SQL 混合代理圖架構實踐](https://vstorm.co/case-study/from-single-agent-to-hybrid-agent-graph-architecture-our-journey-with-pydantic-ai-and-text-to-sql/)
- [71] [[DBOS 官方] 實作指南 - 結合 Pydantic AI 與耐用執行構建可靠代理](https://pydantic.dev/articles/pydantic-ai-dbos)
- [72] [[Sid Bharath] 技術部落格 - OpenAI Agents SDK 生態與核心機制解析](https://sidbharath.com/blog/openai-agents-sdk/)
- [73] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [74] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [75] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [76] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [77] [[Hugging Face 開源] 開源專案 README - smolagents 代碼執行代理詳細規格](https://github.com/huggingface/smolagents)
- [78] [[開發者社群] 案例研究 - 基於 smolagents 的訂單處理多代理與記憶實作](https://ayotomiwasalau.com/work/blogs/smolagents-framework-building-multi-agents-tools-and-memory)
- [79] [[開發者社群] 案例研究 - 基於 smolagents 的訂單處理多代理與記憶實作](https://ayotomiwasalau.com/work/blogs/smolagents-framework-building-multi-agents-tools-and-memory)
- [80] [[開發者社群] 案例研究 - 基於 smolagents 的訂單處理多代理與記憶實作](https://ayotomiwasalau.com/work/blogs/smolagents-framework-building-multi-agents-tools-and-memory)
- [81] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)

### 6. TypeScript 生態圈主流 Agent Framework（Vercel AI SDK、LangChain.js、Mastra、OpenAI Agents SDK for TS 等）在架構設計、可觀測性、多 Agent 協作能力上的比較？

在 2026 年的 AI 應用開發中，TypeScript (TS) 已不再只是寫前端 UIs 的輔助，而是因為能提供**從底層大模型、工具、狀態機到 React 元件的端到端型別安全**，成為主流產品團隊建置生產級 Agent 的首選語言 `[1]`。

針對 TypeScript 生態圈四大主流 AI Agent 路線：**Vercel 陣營（AI SDK 與 Eve）**、**Mastra**、**LangChain.js / LangGraph.js**、以及 **OpenAI Agents SDK (TS 版)**，其在架構設計（與狀態/記憶持久化）、可觀測性、及多 Agent 協作能力上的技術對比與選型分析如下：

---

### 一、 架構設計與狀態/記憶持久化 (Architecture & State Persistence)

四大框架在底層狀態管理上，展現了從「輕量無狀態」、「嚴格狀態機圖」到「耐用工作流」的深刻設計取捨：

#### 1. Vercel 陣營：輕量無狀態 SDK 與「檔案系統即 Agent」的雙軌制
*   **Vercel AI SDK（輕量無狀態）**：
    定位於「模型與串流 UI 的整合層」`[2]`。它不提供複雜的 Orchestration 狀態持久化，而是專注於單次 Request 內部的 ReAct 迴圈 `[3]`。提供 `generateText`、`streamText`、React Hooks（如 `useChat`）以及 `ToolLoopAgent` 類別，用最薄的抽象處理工具呼叫、結構化 JSON 生成及串流 `[3-5]`。
*   **Vercel Eve（檔案系統即 Agent / 2026 年 6 月推出）**：
    定位為**「耐用型（Durable）後端代理框架」**`[6, 7]`。Eve 的核心理念是 **"Convention over Configuration"（約定優於配置）**，將 Agent 的所有原語映射到實體專案目錄中（類似 Next.js 的檔案路由）`[8, 9]`：
    *   `agent/instructions.md`：Markdown 格式的系統提示詞 `[10, 11]`。
    *   `agent/agent.ts`：大模型配置與 `defineAgent()` 運算引擎 `[10-12]`。
    *   `agent/tools/*.ts`：單一檔案即為單一 `defineTool()` 工具，filename 即為大模型看見的 Tool name `[9, 11]`。
    *   `agent/skills/*.md`：模組化知識庫（Markdown Wiki），Eve 會在對話時自動語意檢索，僅載入相關 Skills，保持系統提示詞簡潔 `[10, 13, 14]`。
    *   在建置時，Eve 的編譯器會遍歷整個目錄，**自動編譯出 Manifest 清單並對接模型**，開發者完全不需撰寫 import 程式碼 `[6, 7, 15]`。
    *   **耐用執行（Durable Runtime）**：Eve 底層基於 **Vercel Workflow** 技術 `[16, 17]`。它將對話與工具呼叫的每一步記錄為事件日誌（Event Log），在 Cold Start、執行超時或伺服器重啟時，透過**確定性重放（Deterministic Replay）**無損恢復 state，使 Session 得以存活數天 `[16, 18]`。
    *   **安全隔離沙箱**：Agent 需要執行大模型生成的代碼或 Bash 命令時，Eve 會自動將任務放入 Vercel 平台上硬體隔離的 **Firecracker microVM 容器**（本地開發使用 Docker）執行，確保主應用安全 `[18-20]`。

#### 2. Mastra：全包式（Batteries-Included）平台與觀察式記憶 (OM)
*   **確定性工作流引擎（Workflows）**：
    Mastra（由原 Gatsby 團隊創立）是 Platform-agnostic（執行時無關）的，可部署在 Node、Cloudflare Workers 或自建伺服器 `[21-23]`。當流程需要確定性時，它提供 `createWorkflow` API，允許使用 TypeScript 鏈式語法（`.then()` 串行、`.parallel()` 併行、`.branch()` 條件分支、`.doWhile()` 循環）手動編排工作流 DAG 圖 `[24-26]`。
*   **觀察式記憶（Observational Memory, OM）**：
    這是 Mastra 的技術亮點 `[27]`。為了防止長對話累積帶來的 Context Window 膨脹與 Prompt 失焦，Mastra 在背景啟動了兩個非阻塞的 Agent——**Observer（觀察者）** 與 **Reflector（反射者）** `[28-30]`：
    *   **Observer (30k 觸發)**：在對話 token 累積達 30k 臨界值前，背景 Observer 會分批將歷史訊息壓縮為高優先級、帶時間戳記的 dense notes `[28, 31, 32]`。一旦達臨界值，緩衝的 observations 立即激活，原始訊息歷史被安全剪除（只留最末 20% VERBATIM 歷史），**將語意壓縮 5x 至 40x** `[28, 31, 33, 34]`。
    *   **Reflector (40k 重構)**：當累積 observations 達 40k tokens 時，Reflector 在背景運行，合併重複資訊、去除失效事實，主動遺忘過期 context `[28, 35]`。
    *   **優勢**：OM 讓 context window 維持在**極度穩定且 Prompt-cacheable** 的狀態，能最大化發揮模型供應商的快取功能，節省 4x 至 10x 的 Token 費用（在 LongMemEval 達 94.87% 的 SOTA 表現）`[28, 31, 36, 37]`。支援 Thread（單一對話）與 Resource（跨對話用戶）雙重記憶 Scope `[38, 39]`。

#### 3. LangChain.js / LangGraph.js：顯式狀態圖與時間旅行
*   **顯式狀態控制 (Pregel 引擎)**：
    LangGraph.js 將對話與決策精確建模為圖中的 Node 與 Edge，並由全域 State 在節點間流轉，對邏輯有 100% 嚴格控制 `[40, 41]`。
*   **Checkpointer 快照持久化**：
    LangGraph.js 擁有業界最完備的 checkpointer 快照保存機制 `[42, 43]`。它在每個運算 Super-step 結束時，自動將全域 State 快照序列化，寫入 `PostgresSaver`、`RedisSaver` 或是由 AWS 官方維護的 **`DynamoDBSaver`** `[44-46]`。
    *   **DynamoDBSaver 大 Payload 優化**：小於 350 KB 的 checkpoint 直接以 serialized items 存入 DynamoDB，當 size 達到 350 KB 以上時，會**自動將 State payload 上傳至 Amazon S3，DynamoDB 僅保存引用指標（Reference Pointer）**，無痛規避資料庫單筆大小限制 `[45]`。
    *   此快照機制原生支持 **Time Travel（時間旅行回溯調試）**、人工中斷審批及 crash 後 Resume `[44, 47]`。
*   **Store（長期記憶）**：
    獨立於 Checkpointer，LangGraph.js 設計了全域 **`BaseStore`** 鍵值儲存（如 `InMemoryStore` 或 SQL/MongoDB-backed Store），用於跨執行緒、跨 Session 存取用戶個性化偏好與 learned facts `[48-50]`。

#### 4. OpenAI Agents SDK for TS：極低抽象與雲端歷史壓縮
*   **極簡原生 Primitives**：
    秉持 "plain JS/TS" 的極低抽象路線，不強制編譯複雜的圖或檔案夾結構 `[51, 52]`。其 Runtime 完全依賴 **`Session`** 接口進行會話持久化 `[53]`。
*   **歷史自動壓縮裝飾器**：
    提供 `SQLiteSession` 等適配器 `[52]`。特別是當歷史對話過長時，可透過 **`OpenAIResponsesCompactionSession` 裝飾器** `[54, 55]`，在背景自動調用雲端的 **`responses.compact` API** `[54, 55]`。由 OpenAI 背景模型將久遠的 tool logs、對話資訊精簡為合成的摘要對（Synthetic User-Assistant Pairs），同時保留最近 N 輪 verbatim 對話，兼顧 prompt cache 與記憶深度 `[54-56]`。

---

### 二、 可觀測性能力 (Observability & Telemetry)

生產級 Agent 的除錯難度極高，四大框架的可觀測性配套各具特色：

*   **Vercel AI SDK & Eve：平台級免配置面板與 OTel 導出**
    *   **AI SDK**：原生整合 OpenTelemetry 語意，對 `ai.streamText`、`ai.generateText` 與 `ai.toolCall` 定義清晰的 Spans `[57, 58]`。可無縫導出至標準 OTel 收集器或 **Braintrust（與 Vercel Marketplace 原生整合，支援自動 AI SDK Tracing）**、**LangSmith** 及 **Langfuse** `[57, 59]`。
    *   **Vercel Eve**：一旦部署在 Vercel 上，平台會**自動啟用「Agent Runs」專屬面板** `[58, 60]`。不需手動配置追蹤代碼，即可圖形化呈現 `ai.eve.turn` 到工具調用的完整 Trace 樹，實時查看 Reasoning 思維鏈、Tool JSON 參數、及精確區分輸入/輸出/快取的 Token 耗時與費用 `[58-60]`。
*   **Mastra：本地 Studio 可視化與自動化評估**
    *   **Mastra Studio**：本地開發時運行在 `localhost:4111` `[61]`。提供了極佳的本地觀測 Web UI，可直接在本地查看 Workflow 的視覺化執行圖、觀察式記憶（OM）的累積進度、各步驟 Spans，並在 Studio 內重放（Replay）與調試 Agent 決策 `[22, 61, 62]`。
    *   **Evals 測試框架**：內建 Evals 模組，提供 LLM-as-a-judge（大模型充當裁判）、工具調用準確性及任務完成度等評估指標，可在本地或 CI/CD pipeline 中跑 regression-testing，確保 Prompt 修改不產生退化 `[22, 63-65]`。
*   **LangChain.js / LangGraph.js：LangSmith 最精細的 Trace**
    *   與 **LangSmith** 深度原生綁定 `[40, 66]`。能追蹤到圖中每個節點的變遷、Pending writes（當節點失敗時保留其他成功節點的寫入狀態，防止 Resume 時重複執行）以及全域 State 的 Reducer 合併細節 `[67-69]`。
*   **OpenAI Agents SDK (TS)：推理模型思維鏈視覺化**
    *   內建 standard OTel spans 拋出機制 `[52, 70]`。所有 traces 可無縫導出至 OpenAI 官方開發者控制台，完美可視化推理型大模型在決策時的 **`ReasoningItem` / 背景思維鏈（Thinking/Reasoning Spans）**，讓開發者一眼看清模型「在第幾步發生決策漂移」 `[51, 52, 71]`。

---

### 三、 多 Agent 協作與編排能力 (Multi-Agent Collaboration)

多代理協作的核心在於「控制權如何轉移（Peer-to-Peer 或 Supervisor）」以及「人機協同的掛起機制」：

*   **Vercel AI SDK & Eve：層級子代理與不計費 Slack 審批門禁**
    *   **AI SDK**：**不內建多代理編排** `[72, 73]`。社群生產實踐通常採用「分離架構」：讓 AI SDK 專注於 LLM 呼叫與 UI 串流，而多代理編排則透過外部協調器（如 `open-multi-agent` 庫的 `runTeam`）負責任務分解與 DAG 執行 `[74, 75]`。
    *   **Vercel Eve**：原生支持 **Hierarchical Subagents 模式** `[19, 76]`。在 `agent/subagents/` 下建立子資料夾，即可定義完全嵌套的子代理，子代理具有獨立的 context 隔離與運行環境 `[19]`。主代理調用它時就像調用一個強型別的 Orchestration Tool，僅在邊界傳遞 Zod 驗證後的 JSON `[15, 77]`。
    *   **Slack 人工審批門禁**：對於高風險的工具調用（如 SQL 寫入或發送退款），可宣告 `approval: always()` `[15, 20, 58]`。此時，Durable Workflow 會**掛起（Suspend）該運行執行個體，在零計算（Compute）成本的情況下無限期暫停** `[20, 58]`。Eve 會向 Slack 發送審批按鈕卡片，人類核准後，Workflow 會自動從剛才中斷的步驟 **Resume 續行** `[15, 20, 58]`。
*   **Mastra：工作流封裝與三大編排混合**
    *   原生支持順序（Sequential）、對等交接（Handoff）與層級監督（Hierarchical）協作 `[25, 26, 78]`。
    *   最獨特的是：Mastra 允許**將一整套複雜、有向無環的工作流（Workflow）包裝成一個普通的 Tool**，直接交給 Supervisory Agent 呼叫 `[79]`。這將 Agent 的動態推理與 Workflow 的確定性執行做到了極佳的混合編排 `[79, 80]`。亦支持 Workflow suspend / resume 的人類審批 `[26, 81, 82]`。
*   **LangGraph.js：去中心化狀態通道與 Reducer**
    *   多代理協作完全建立在 `StateGraph` 的拓撲上 `[40, 41]`。透過嵌套子圖（Subgraphs），並藉由 Reducer 函數（如 `add_messages`）對全域 State channels 的通道值進行合併與去重 `[48, 67, 83]`。
    *   這種協作控制度極高、最為嚴謹，但也意味著開發者需要手寫大量的狀態轉移邊界邏輯。
*   **OpenAI Agents SDK for TS：Swarm 對等動態交接 (Handoff)**
    *   承襲 Swarm 的精髓，主打 **Peer-to-Peer 協作** `[70, 84]`。交接在 LLM 眼中就是一個普通 Tool，命名為 `transfer_to_<agent_name>` `[70, 85]`。
    *   一旦 triage 代理判定此任務歸屬 billing 專家，調用交接工具，Runner 便會移交對話歷史、加載 billing 的系統指令，直接由 billing 接管對答 `[85, 86]`。
    *   **防漂移機制（Hop Counter）**：為避免 Agent 之間發生 A \\(\rightarrow\\) B \\(\rightarrow\\) A 的「交接漂移無限循環」，Runner 內建了跳數限制器（Hop Counter），一旦交接跳數超標即安全熔斷 `[86, 87]`。

---

### 四、 綜合選型分級：可觀測性分級歸類

這些 TypeScript Agent 框架在**「可觀測性分級（完整過程可稽核／只能拿到工具呼叫／完全黑盒）」**上，可以進行如下歸類：

#### 1. 完整過程可稽核 (Lossless Auditing & Full Execution Tracing)
> **【定義】**：框架不僅能獲取工具呼叫的輸入與輸出，還能**完整記錄整棵執行樹（Trace Spans）**。這包括：模型內部的推理思維鏈（Reasoning CoT）、步驟級全域狀態變遷、上下文歷史更迭與裁剪精簡過程。支援執行步驟的**回溯與重放**。

*   **LangChain.js / LangGraph.js**：
    結合 **LangSmith**，能以圖形樹狀剖析節點跳轉、變更後的全域 State，並原生支援「時間旅行（Time Travel）」，可直接從歷史中的特定 checkpoint 點恢復執行 `[40, 44, 66]`。
*   **Vercel Eve**：
    部署於 Vercel 後自動啟用「Agent Runs」專屬面板，直接以圖形樹狀呈現 `ai.eve.turn` 到 `ai.toolCall` 的 Trace 樹，實時追蹤 Reasoning 思維、Tool 參數、以及 Token 消耗成本 `[58-60]`。
*   **Mastra**：
    整合 **Mastra Studio** 本地開發環境。提供本地 Web UI 查看 Workflow 視覺化執行圖、OM 觀察式記憶進度、Spans 樹，並可重放與調試 Agent 決策步驟 `[22, 61, 62]`。
*   **OpenAI Agents SDK (TS)**：
    OTel 原生，Trace 資料直接與 **OpenAI 官方開發者 Dashboard** 整合，能極其直觀地剖析推理模型的思維鏈與決策軌跡 `[52, 88]`。

#### 2. 只能拿到工具呼叫與生命週期攔截 (Only Tool-Call & Lifecycle Interception)
> **【定義】**：框架預設沒有極深度的全域層級 Trace 樹，但提供了生命週期 Hooks、過濾器（Filters）或攔截器，允許開發者在工具執行前後強行介入，捕獲、修改與稽核 Tool Call。

*   **Vercel AI SDK**：
    作為無狀態、輕量級的模型與 UI 串流整合層，其核心 `ToolLoopAgent` 僅提供基本的單次 ReAct 工具呼叫 `[3, 89]`。必須依賴開發者配置 `instrumentation.ts` 將 OTel Spans 外接導出至 Braintrust、LangSmith、Langfuse 等第三方遙測平台才能實現完整稽核 `[57, 59]`。

#### 3. 完全黑盒 (Completely Black Box)
> **【定義】**：框架本身不內建底層的模型決策或 OTel 語意追蹤。日誌通常僅停留在高階的應用任務層級（例如：*「Agent A 啟動」 \\(\rightarrow\\) 「Task B 完成」*），若需要微觀的 Token 成本、CoT 思考鏈或步驟重播，必須由開發者自行手寫攔截器或完全仰賴外部第三方平台。

*   *(註：TS 生態圈如 **CrewAI** (Python only) 之類的高階角色扮演框架在 OTel 未配置前多歸於此級 `[90]`，而在 TypeScript 原生生態中，由於 TypeScript 團隊多為產品工程導向，主流框架（Eve、Mastra、LangGraph.js、OpenAI SDK）在設計之初即將可觀測性視為一等公民，因此 TS 生態主流框架皆已跨越黑盒階段，普遍具備第一級或第二級的觀測能力 `[1]`)*。

---
💡 **後續架構評估（針對 W-074 Cheerio 個人 AI 助理）：**
若您傾向自建 Harness 進行網頁爬蟲與資料落地，**Mastra 的「觀察式記憶（OM）」**最適合搬移至爬蟲助理中（以應對 Playwright/Cheerio 帶來的龐大 Token 吞吐與 Prompt cache 優化）`[27, 33]`；而 **OpenAI Agents SDK 的「Handoff」工具化包裝** 則是最輕量、好寫的 Peer-to-Peer 協作設計模式，能以最低的程式碼行數實作動態多代理分工 `[84, 85]`。

**引用來源：**
- [1] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [2] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [3] [[The Road To Enterprise] 架構設計 - 企業級 Agent 框架選型必要性評估](https://theroadtoenterprise.com/blog/do-you-need-an-agent-framework)
- [4] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [5] [[The Road To Enterprise] 架構設計 - 企業級 Agent 框架選型必要性評估](https://theroadtoenterprise.com/blog/do-you-need-an-agent-framework)
- [6] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [7] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [8] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [9] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [10] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [11] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [12] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [13] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [14] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [15] [[GitHub 筆記] 技術筆記 - Vercel Eve 輕量級代理框架語法解析](https://github.com/coleam00/cole-medin-knowledge-base/blob/main/entities/tools/eve.md)
- [16] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [17] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [18] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [19] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [20] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [21] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [22] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [23] [[Mastra 官方] 概念指南 - 代理型工作流核心組件與建構](https://mastra.ai/articles/agentic-workflows)
- [24] [[Mastra 官方] 架構設計 - AI Agent 生產環境設計模式](https://mastra.ai/articles/ai-agent-architecture)
- [25] [[Mastra 官方] 比較分析 - AI Agent 框架選型評估指南](https://mastra.ai/articles/ai-agent-framework)
- [26] [[Mastra 官方] 實作指南 - 開發者 AI Agent 工作流全方位手冊](https://mastra.ai/articles/ai-agent-workflows)
- [27] [[AI Engineer] 技術部落格 - Mastra TypeScript AI 代理開發框架評析](https://ai.engineer/orgs/mastra)
- [28] [[Mastra 開源] 開源工作坊 README - 觀察式記憶實作範例與工作坊指南](https://github.com/mastra-ai/mastra-observational-memory-workshop)
- [29] [[ZenML] 架構設計 - Mastra 觀察式記憶與人類啟發的上下文壓縮](https://www.zenml.io/llmops-database/observational-memory-human-inspired-context-compression-for-agent-systems)
- [30] [[Mastra 官方] 官方文件 - 觀察式記憶（Observational Memory）技術規格](https://mastra.ai/docs/memory/observational-memory)
- [31] [[Mastra 官方] 架構設計 - AI Agent 記憶平台與上下文管理機制](https://mastra.ai/articles/agent-memory-platform)
- [32] [[Mastra 官方] 官方文件 - 觀察式記憶（Observational Memory）技術規格](https://mastra.ai/docs/memory/observational-memory)
- [33] [[Mastra 官方] 官方文件 - 觀察式記憶（Observational Memory）技術規格](https://mastra.ai/docs/memory/observational-memory)
- [34] [[Mastra 官方] 學術論文分析 - 觀察式記憶於 LongMemEval 的表現與壓縮研究](https://mastra.ai/research/observational-memory)
- [35] [[Mastra 官方] 學術論文分析 - 觀察式記憶於 LongMemEval 的表現與壓縮研究](https://mastra.ai/research/observational-memory)
- [36] [[Mastra 開源] 開源工作坊 README - 觀察式記憶實作範例與工作坊指南](https://github.com/mastra-ai/mastra-observational-memory-workshop)
- [37] [[Mastra 官方] 官方文件 - 觀察式記憶（Observational Memory）技術規格](https://mastra.ai/docs/memory/observational-memory)
- [38] [[ZenML] 架構設計 - Mastra 觀察式記憶與人類啟發的上下文壓縮](https://www.zenml.io/llmops-database/observational-memory-human-inspired-context-compression-for-agent-systems)
- [39] [[Mastra 官方] 官方文件 - 觀察式記憶（Observational Memory）技術規格](https://mastra.ai/docs/memory/observational-memory)
- [40] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [41] [[Vercel 官方] 概念指南 - 開發者的 LLM Agent 核心概念與要素](https://vercel.com/i/llm-agent)
- [42] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [43] [[LangChain 官方] API 參考 - LangGraph 的 Checkpointer 持久化接口說明](https://reference.langchain.com/python/langgraph/checkpoints)
- [44] [[AWS 官方] 實作指南 - 結合 LangGraph 與 DynamoDB 構建耐用代理](https://aws.amazon.com/blogs/database/build-durable-ai-agents-with-langgraph-and-amazon-dynamodb/)
- [45] [[AWS 官方] 實作指南 - 結合 LangGraph 與 DynamoDB 構建耐用代理](https://aws.amazon.com/blogs/database/build-durable-ai-agents-with-langgraph-and-amazon-dynamodb/)
- [46] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [47] [[AWS 官方] 實作指南 - 結合 LangGraph 與 DynamoDB 構建耐用代理](https://aws.amazon.com/blogs/database/build-durable-ai-agents-with-langgraph-and-amazon-dynamodb/)
- [48] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [49] [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [50] [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [51] [[OpenAI 官方] 官方文件 - Agents SDK 核心功能與開發指南](https://developers.openai.com/api/docs/guides/agents)
- [52] [[OpenAI 官方] 官方文件 - Agents SDK 使用手冊與架構概覽](https://openai.github.io/openai-agents-python/)
- [53] [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [54] [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [55] [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [56] [[OpenAI 官方] 架構設計 - 基於會話的短期記憶管理與上下文工程](https://developers.openai.com/cookbook/examples/agents_sdk/session_memory)
- [57] [[Vercel 官方] 技術部落格 - 生產環境 AI Agent 評估框架](https://vercel.com/i/ai-agent-evaluation-frameworks-production)
- [58] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [59] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [60] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [61] [[Mastra 官方] 實作指南 - TypeScript 生產生態 2026 逐步建置教學](https://dev.to/thegdsks/building-a-production-ai-agent-in-typescript-with-mastra-a-2026-step-by-step-37dc)
- [62] [[Mastra 官方] 官方文件 - 觀察式記憶（Observational Memory）技術規格](https://mastra.ai/docs/memory/observational-memory)
- [63] [[Mastra 官方] 實作指南 - 開發者 AI Agent 工作流全方位手冊](https://mastra.ai/articles/ai-agent-workflows)
- [64] [[Mastra 官方] 概念指南 - AI 工作流定義與構建方法](https://mastra.ai/articles/ai-workflows)
- [65] [[開發者社群] 實作指南 - 開發者 AI Agent 基礎構建實戰指南](https://mastra.ai/articles/how-to-build-ai-agents)
- [66] [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [67] [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [68] [[LangChain 官方] API 參考 - LangGraph 的 Checkpointer 持久化接口說明](https://reference.langchain.com/python/langgraph/checkpoints)
- [69] [[LangChain 官方] API 參考 - langgraph.checkpoint 模組詳細類別與方法](https://reference.langchain.com/python/langgraph.checkpoint)
- [70] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [71] [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [72] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [73] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [74] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [75] [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [76] [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [77] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [78] [[Mastra 開源] 開源專案 README - TypeScript AI 代理框架功能概述](https://github.com/mastra-ai/mastra)
- [79] [[Mastra 官方] 概念指南 - 代理型工作流核心組件與建構](https://mastra.ai/articles/agentic-workflows)
- [80] [[Firecrawl] 教學指南 - 使用 TypeScript 與 Mastra 打造網頁爬蟲代理](https://www.firecrawl.dev/blog/mastra-tutorial)
- [81] [[Mastra 官方] 架構設計 - AI Agent 生產環境設計模式](https://mastra.ai/articles/ai-agent-architecture)
- [82] [[Mastra 官方] 概念指南 - 代理型工作流核心組件與建構](https://mastra.ai/articles/agentic-workflows)
- [83] [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [84] [[OpenAI 開源] 開源專案 README - Swarm 輕量級多代理編排教育框架](https://github.com/openai/swarm)
- [85] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [86] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [87] [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [88] [[Lexogrine] 教學指南 - 2026 年 OpenAI Swarm 輕量代理框架實戰手冊](https://lexogrine.com/blog/openai-swarm-multi-agent-framework-2026)
- [89] [[Vercel 官方] 概念指南 - 開發者的 LLM Agent 核心概念與要素](https://vercel.com/i/llm-agent)
- [90] [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)

### 7. 綜合以上，這些框架在可觀測性分級（完整過程可稽核／只能拿到工具呼叫／完全黑盒）上可以怎麼歸類？

綜合上述對各主流框架的架構剖析，我們能依據**「追蹤細緻度（Spans/CoT）」、「狀態保存完整度（Checkpoints/Event Logs）」與「執行回溯能力（Replay/Fork）」**，將這些 AI Agent 框架在**可觀測性分級**上，劃分為以下三個層級：

---

### 第一級：完整過程可稽核（Lossless Auditing & Full Execution Tracing）

**【定義】**：框架不僅能獲取工具呼叫的輸入與輸出，還能**完整記錄整棵執行樹（Trace Spans）**。這包含：模型內部的推理思維鏈（Thinking/Reasoning CoT）、步驟級全域狀態變遷、上下文歷史更迭與自動壓縮過程 [3, 11, 16, 67]。此層級多原生內建 **OpenTelemetry (OTel)** 標準 [3, 12, 67]，或提供高密度的專屬視覺化追蹤面板，且支援執行步驟的**回溯（Time Travel）、分叉（Fork）與重放（Replay）** [11, 16, 20]。

*   **LangGraph / LangGraph.js**：
    *   **稽核深度**：擁有業界最嚴謹的狀態監控 [11]。其 **Checkpointer** 機制會在每個「超步（Super-step）」結束時，將全域狀態（State）的 Snapshot 序列化寫入資料庫（如 PostgreSQL、MongoDB，或支援大資料分流至 S3 的 DynamoDB） [5, 11, 29]。
    *   **觀測與除錯**：深度整合 **LangSmith**，可圖形化剖析節點跳轉、變更後的 State、以及中間寫入變量 [11]；原生支援**「時間旅行（Time Travel）」**，允許線程從歷史中的特定 checkpoint 點恢復執行。
*   **DeepSeek Harness (dsh)**：
    *   **稽核深度**：核心基於 Cordis 響應式微內核，系統會將模型經歷的一切——包含系統 Prompt、思考鏈（CoT Reasoning）、工具調用、子代理排程狀態與每次上下文注入，實時寫入一個 **Append-Only（單一追加）會話日誌**中 [16]。
    *   **觀測與除錯**：本機 Web UI 提供 **Trajectory View（軌跡檢視圖）** 實時呈現執行指標 [16]。由於日誌本質上是事件流，開發者可以**重放（Replay）**任務歷史、修改代碼，或是在歷史中的特定節點直接進行**分叉（Fork）**生成新分支來測試不同配置 [16]。
*   **OpenAI Agents SDK (Python / TS)**：
    *   **稽核深度**：內建標準 OTel GenAI 追蹤 [3]。針對最新推理模型提供 `ReasoningItem` 追蹤模型思考區間與決策軌跡 [3]；並可透過 `withTurnSpan` 追蹤 Sessions 會話存取與自動壓縮（Compaction）過程 [3, 54]。
    *   **觀測與除錯**：追蹤數據可無縫匯出至標準 OTel 收集器 [3]，並原生與 **OpenAI 官方開發者 Dashboard** 整合，提供白盒化的推理與工具執行樹狀圖 [62]。
*   **Pydantic AI**：
    *   **稽核深度**：以型別安全為核心，其運轉基於 `pydantic-graph` 有限狀態機 [57]。它提供 `Agent.iter` 迭代器，允許開發者在代碼中**逐一節點驅動並手動控制圖的執行**，進行極度細緻的白盒調試。
    *   **觀測與除錯**：與 **Pydantic Logfire** 原生無縫綁定（基於 OTel），可實時呈現包含訊息交換、工具參數強型別 Schema 驗證細節、Token 耗時、費用與底層 DBOS Checkpoints 事務的完整 Trace 鏈 [12, 60]。
*   **Vercel Eve**：
    *   **稽核深度**：基於 Vercel Workflow 的事件日誌（Event Log），會將 Agent 的每一步執行、工具輸出進行事件化記錄，並透過確定性重放（Deterministic Replay）在 cold start 或重啟後恢復狀態 [15, 31]。
    *   **觀測與除錯**：部署於 Vercel 後會**自動啟用「Agent Runs」專屬面板** [67]。提供層級 Trace 樹狀圖（呈現 Timing、Reasoning 與 Tool 呼叫內容），並精確區分輸入、輸出與快取 Token（Cached Tokens） [67]。
*   **smolagents (Hugging Face)**：
    *   **稽核深度**：主打 CodeAgent（模型直接輸出 Python 代碼並執行） [22, 25]。它不僅追蹤傳統 Tool 呼叫，還必須稽核 LLM 生成的 Python 代碼在 AST（抽象語法樹）解析器中的每一步執行細節 [25]。
    *   **觀測與除錯**：整合 **Arize Phoenix / AX** 追蹤 OTel Spans [7]；並支持在本機直接調用 `agent.replay()` 來重新回放特定對話步驟 [20]。

---

### 第二級：只能拿到工具呼叫與生命週期攔截（Only Tool-Call & Lifecycle Interception）

**【定義】**：框架預設沒有極度深度的全域層級 Trace 樹，但提供了強大的**生命週期 Hooks、過濾器（Filters）或攔截器** [47, 48]，允許開發者在工具執行前後強行介入，捕獲、修改與稽核 Tool Call。若要達到完整過程稽核，必須依賴開發者額外配置遙測導出（OTel Exporter）或外接第三方遙測平台 [67]。

*   **Microsoft Semantic Kernel (SK)**：
    *   **稽核深度**：SK 專為企業安全合規設計，側重於「顯式且可預測」的編排 [48]。
    *   **攔截機制**：提供 **`IFunctionInvocationFilter`** 接口 [47, 48]。開發者可自訂 Filter，在 Plugin 函數/工具執行**之前與之後**進行強制攔截，精確捕獲輸入參數（`context.Arguments`）與執行結果（`context.Result`） [47, 48]，實現對 ReAct 決策路徑的強控制與審計。
*   **Vercel AI SDK**：
    *   **稽核深度**：作為輕量級、無狀態的模型與 UI 串流整合層，其核心 `ToolLoopAgent` 僅提供基本的單次 ReAct 工具呼叫 [66, 67]。
    *   **觀測機制**：AI SDK 本身並不內建高階的編排追蹤 [67]。必須依賴開發者配置 `instrumentation.ts` 將 OTel Spans 外接導出至 Braintrust、LangSmith、Langfuse 等第三方遙測平台才能實現完整稽核 [67]。
*   **Microsoft AutoGen (v0.4 以前)**：
    *   **稽核深度**：傳統 AutoGen 協作模式主要依賴多代理在同一個 Thread 內進行非結構化的對話（Group Chat） [46]。
    *   **觀測機制**：其預設日誌主要呈現高階的 Agent 之間 Message 對話流，較難直接診斷模型內部的微觀 Reasoning Spans 決策路徑 [46]（此痛點於 v0.4 / AG2 引入非同步事件驅動與 OTel 支援後才得到顯著改善 [1]）。

---

### 第三級：完全黑盒（Completely Black Box）

**【定義】**：框架本身不內建底層的模型決策或 OTel 語意追蹤。日誌通常僅停留在高階的應用任務層級（例如：*「Agent A 啟動」\\(\rightarrow\\)「Task B 完成」*），若需要微觀的 Token 成本、CoT 思考鏈或步驟重播，必須由開發者自行手寫攔截器或完全仰賴外部第三方平台。

*   **CrewAI**：
    *   **稽核深度**：偏向高階應用層，以角色扮演（Persona）與任務管道（Tasks & Crews）為編排主軸。
    *   **觀測機制**：框架預設的執行過程較為黑盒，其內建日誌主要關注「任務執行進度與角色分發」。若要獲取微觀的模型內部思考（CoT）、Prompt Cache 命中率或進行步驟重播，必須依賴第三方 OTel 橋接（如 Logfire/Langfuse）或使用其官方 Cloud 平台才能補足觀測能力 [2]。

---
🧐 **接下來，您可能想：**
針對您 Cheerio 個人 AI 助理（W-074，傾向自建 harness）的設計，看看如何參考「第一級」中 OpenAI Agents SDK 的 `Runner` 語意或 Pydantic AI 的 `Agent.iter` 迭代器，來規劃您自建 Harness 的可觀測性架構？

## 已移除的來源（品質過濾）

- Deepseek Harness Preview link - DGX Spark / GB10 - NVIDIA Developer Forums — 此來源為 NVIDIA 社群論壇上的簡短討論串，僅包含一個預覽連結與不具實質技術分析的兩句話主觀心得，屬於缺乏技術深度與具體結論的膚淺社群問答。
- Gateway with Observational Memory | Mastra — 此頁面為 Mastra 旗下「Gateway」產品的官方行銷推廣與銷售頁，內容主要為功能宣傳、訂價方案與「聯絡銷售」按鈕，缺乏具體技術實作細節與程式碼。
- Internal AI Agents - Mastra — 此頁面為 Mastra 官網上有關內部 AI Agent 的解決方案宣傳頁，內容僅做高層次的功能特點介紹與案例宣傳，無具體實作細節與技術深度。
- DeepSeek Harness developer preview: Everything is a plugin — 此頁面為 DeepSeek Harness 官方產品行銷首頁，僅包含高層次的設計理念宣傳與單行 CLI 安裝命令，缺乏實質的技術實作深度或程式碼範例。
- AI SDK - Vercel — 此頁面為 Vercel 官網對 AI SDK 的行銷與產品推廣首頁，頁面充斥大量引導至 Vercel 商業服務的導覽連結與極度簡化的程式碼片段，缺乏具體的技術細節與實作深度。
- What is AutoGen? - IBM — 此來源為 IBM Think 專欄針對一般商業大眾撰寫的高階觀念科普文章，內容著重於概念介紹與 real-world 案例，並穿插 watsonx 產品推廣，完全沒有任何具體程式碼實作與開發技術細節。
- Deepseek Harness - Best of JS — 此頁面為 Best of JS 網站上的開源專案數據展示與 README 簡單複製，僅呈現星數、下載量與依賴項等統計數據，屬於缺乏技術深度的專案目錄展示頁。
- Mastra: TypeScript AI Framework for Agents and Apps — 此頁面為 Mastra 專案的官方宣傳與產品首頁，包含功能亮點總覽、定價模式與訂閱電子報，屬於缺乏具體技術實作細節的官網行銷推廣頁。

## 已移除的來源（蒸餾必要性）

- Agent Skills - Vercel — 此來源為 Vercel 官方的 Skill（技能）目錄列表，主要列出 React 效能最佳化、瀏覽器自動化、專案部署等獨立的實用工具包（如 next-cache-components、next-upgrade、cra-to-next-migration 等），內容與 Vercel AI SDK 的 Agent 架構設計、記憶機制、多 Agent 協作或可觀測性等核心技術問題完全無關。
- pydantic_graph.basenode | Pydantic Docs — 此頁面在爬取時僅擷取到側邊欄導覽選單（Sidebar Navigation）與目錄結構，完全沒有任何關於 Pydantic AI BaseNode 的實質技術內容、程式碼或架構說明，對回答任何問題皆無實質貢獻。
- pydantic_graph.graph_builder | Pydantic Docs — 此頁面僅包含 Pydantic 文件的側邊導覽選單與結構索引，缺乏任何有關 Pydantic Graph 實作、編排或狀態機設計的實質技術細節。
- Graph Builder API | Pydantic Docs — 此來源純屬 Pydantic Docs 的導覽側邊欄與 API 目錄，沒有任何實際的 API 說明、技術規格或 Pydantic Graph 實作程式碼。
- Graphs | Pydantic Docs — 此頁面同樣僅包含 Pydantic AI 官方文件的側邊導覽選單與結構索引，完全缺乏關於 Graph 架構與工作流編排的任何實質技術內容。

## 參考來源清單

- [[AG2 官方] 教學指南 - AutoGen v0.4 事件驅動與可觀測代理開發](https://cohorte.co/blog/autogen-v0-4-ag2-crash-course-build-event-driven-observable-ai-agents-that-scale)
- [[AI Engineer] 技術部落格 - Mastra TypeScript AI 代理開發框架評析](https://ai.engineer/orgs/mastra)
- [[AI Engineering] 架構設計 - OpenAI Agents SDK 代理交接、防護欄與追蹤](https://aiengineeringfromscratch.com/lesson?path=phases%2F14-agent-engineering%2F16-openai-agents-sdk)
- [[AWS 官方] 實作指南 - 在 AWS 部署 smolagents 多模型代理框架](https://aws.amazon.com/blogs/machine-learning/agentic-ai-with-multi-model-framework-using-hugging-face-smolagents-on-aws/)
- [[AWS 官方] 實作指南 - 結合 LangGraph 與 DynamoDB 構建耐用代理](https://aws.amazon.com/blogs/database/build-durable-ai-agents-with-langgraph-and-amazon-dynamodb/)
- [[AY Automate] 比較分析 - TypeScript AI Agent 框架選型與 Mastra 對比](https://www.ayautomate.com/blog/best-typescript-ai-agent-frameworks)
- [[Arize AI] 官方文件 - smolagents 整合可觀測性追蹤與評估](https://arize.com/docs/ax/integrations/python-agent-frameworks/hugging-face-smolagents/smolagents-tracing)
- [[Arize AI] 架構設計 - OpenAI Swarm 輕量級多代理協作與編排解析](https://arize.com/blog/comparing-openai-swarm)
- [[Cloudsway] 架構設計 - DeepSeek Harness 代理運行時概念解析](https://www.cloudsway.ai/resources/deepseek-harness-explained-what-is-an-agent-harness?id=21)
- [[Composio] 技術部落格 - OpenAI Swarm 代理切換與協作機制探討](https://composio.dev/content/swarm-the-agentic-framework-from-openai)
- [[Crewship] 技術部落格 - LangGraph 的記憶持久化與檢查點機制](https://www.crewship.dev/learn/langgraph-memory)
- [[DBOS 官方] 實作指南 - 結合 Pydantic AI 與耐用執行構建可靠代理](https://pydantic.dev/articles/pydantic-ai-dbos)
- [[DataCamp] 教學指南 - 開源 DeepSeek Harness 代理環境部署與實作](https://www.datacamp.com/tutorial/deepseek-harness)
- [[DeepSeek 開源] 開源專案 README - Harness 插件化代理運行時架構](https://github.com/deepseek-ai/deepseek-harness)
- [[Digital Applied] 架構設計 - Vercel Eve 輕量級 TypeScript 代理框架分析](https://www.digitalapplied.com/blog/vercel-eve-open-source-typescript-agent-framework-launch)
- [[Eigent AI] 架構設計 - DeepSeek Harness 開源代理運行時深度解析](https://www.eigent.ai/blog/deepseek-harness-agent-runtime)
- [[Firecrawl] 教學指南 - 使用 TypeScript 與 Mastra 打造網頁爬蟲代理](https://www.firecrawl.dev/blog/mastra-tutorial)
- [[GitHub 筆記] 技術筆記 - Vercel Eve 輕量級代理框架語法解析](https://github.com/coleam00/cole-medin-knowledge-base/blob/main/entities/tools/eve.md)
- [[Helmcode] 學術論文分析 - DeepSeek Harness 的底層 Cordis 框架設計](https://helmcode.com/posts/cordis-paper-spatiotemporal-composability)
- [[Hindsight] 技術部落格 - smolagents 代理記憶機制的留存與反射](https://hindsight.vectorize.io/blog/2026/04/29/smolagents-memory-tools)
- [[Hugging Face 官方] 官方文件 - Transformer Agents 記憶管理機制](https://huggingface.co/docs/smolagents/tutorials/memory)
- [[Hugging Face 官方] 官方文件 - smolagents 極簡程式碼思維代理入門](https://huggingface.co/learn/agents-course/unit2/smolagents/introduction)
- [[Hugging Face 官方] 官方文件 - 多代理系統協作與通信設計模式](https://huggingface.co/learn/agents-course/unit2/smolagents/multi_agent_systems)
- [[Hugging Face 官方] 教學指南 - smolagents 首個代理構建實戰](https://huggingface.co/learn/agents-course/unit1/tutorial)
- [[Hugging Face 開源] 開源專案 README - smolagents 代碼執行代理詳細規格](https://github.com/huggingface/smolagents)
- [[Hugging Face 開源] 開源專案 README - smolagents 框架結構與快速開始](https://huggingface.co/docs/smolagents/index)
- [[LangChain 官方] API 參考 - LangGraph 的 Checkpointer 持久化接口說明](https://reference.langchain.com/python/langgraph/checkpoints)
- [[LangChain 官方] API 參考 - langgraph.checkpoint 模組詳細類別與方法](https://reference.langchain.com/python/langgraph.checkpoint)
- [[LangChain 官方] 官方文件 - LangGraph 狀態持久化與檢查點（Checkpoints）](https://docs.langchain.com/oss/python/langgraph/persistence)
- [[Lexogrine] 教學指南 - 2026 年 OpenAI Swarm 輕量代理框架實戰手冊](https://lexogrine.com/blog/openai-swarm-multi-agent-framework-2026)
- [[LogRocket] 教學指南 - 使用 Vercel Eve 構建 Next.js AI 代理](https://blog.logrocket.com/vercel-eve-ai-agents/)
- [[Mastra 官方] 學術論文分析 - 觀察式記憶於 LongMemEval 的表現與壓縮研究](https://mastra.ai/research/observational-memory)
- [[Mastra 官方] 官方文件 - 多步驟 AI 流程與工作流編排](https://mastra.ai/ai-workflows)
- [[Mastra 官方] 官方文件 - 觀察式記憶（Observational Memory）技術規格](https://mastra.ai/docs/memory/observational-memory)
- [[Mastra 官方] 實作指南 - TypeScript 生產生態 2026 逐步建置教學](https://dev.to/thegdsks/building-a-production-ai-agent-in-typescript-with-mastra-a-2026-step-by-step-37dc)
- [[Mastra 官方] 實作指南 - 自訂 AI Agent 構建與部署流程](https://mastra.ai/articles/custom-ai-agents)
- [[Mastra 官方] 實作指南 - 開發者 AI Agent 工作流全方位手冊](https://mastra.ai/articles/ai-agent-workflows)
- [[Mastra 官方] 架構設計 - AI Agent 生產環境設計模式](https://mastra.ai/articles/ai-agent-architecture)
- [[Mastra 官方] 架構設計 - AI Agent 記憶平台與上下文管理機制](https://mastra.ai/articles/agent-memory-platform)
- [[Mastra 官方] 架構設計 - 代理型 RAG 架構設計與折衷評估](https://mastra.ai/articles/agentic-rag)
- [[Mastra 官方] 概念指南 - AI 工作流定義與構建方法](https://mastra.ai/articles/ai-workflows)
- [[Mastra 官方] 概念指南 - 代理型工作流核心組件與建構](https://mastra.ai/articles/agentic-workflows)
- [[Mastra 官方] 比較分析 - AI Agent 框架選型評估指南](https://mastra.ai/articles/ai-agent-framework)
- [[Mastra 開源] 開源專案 README - TypeScript AI 代理框架功能概述](https://github.com/mastra-ai/mastra)
- [[Mastra 開源] 開源工作坊 README - 觀察式記憶實作範例與工作坊指南](https://github.com/mastra-ai/mastra-observational-memory-workshop)
- [[Microsoft 官方] 官方文件 - AutoGen AgentChat 多代理協作模式](https://learn.microsoft.com/en-us/semantic-kernel/support/archive/agent-chat)
- [[Microsoft 官方] 官方文件 - Semantic Kernel ChatCompletionAgent 運作原理](https://learn.microsoft.com/en-us/semantic-kernel/frameworks/agent/agent-types/chat-completion-agent)
- [[Microsoft 官方] 官方文件 - Semantic Kernel 代理與群聊編排機制](https://dev.to/bspann/ai-agents-in-semantic-kernel-chatcompletionagent-agentgroupchat-and-orchestration-50am)
- [[MindStudio] 新聞分析 - DeepSeek Harness 插件式程式碼代理架構解析](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [[OpenAI 官方] 官方文件 - Agents SDK 使用手冊與架構概覽](https://openai.github.io/openai-agents-python/)
- [[OpenAI 官方] 官方文件 - Agents SDK 核心功能與開發指南](https://developers.openai.com/api/docs/guides/agents)
- [[OpenAI 官方] 官方文件 - Agents SDK 的代理交接機制與控制權轉移](https://openai.github.io/openai-agents-python/handoffs/)
- [[OpenAI 官方] 官方文件 - Agents SDK 的會話（Sessions）與狀態管理](https://openai.github.io/openai-agents-js/guides/sessions/)
- [[OpenAI 官方] 架構設計 - 基於會話的短期記憶管理與上下文工程](https://developers.openai.com/cookbook/examples/agents_sdk/session_memory)
- [[OpenAI 開源] 開源專案 README - Swarm 輕量級多代理編排教育框架](https://github.com/openai/swarm)
- [[ProjectPro] 教學指南 - AutoGen 多代理協作框架入門與實作](https://www.projectpro.io/article/autogen/1139)
- [[Pydantic 官方] 官方文件 - Pydantic AI 代理程式設計](https://pydantic.dev/docs/ai/core-concepts/agent/)
- [[Pydantic 官方] 官方文件 - Pydantic AI 功能與核心能力規格](https://pydantic.dev/docs/ai/capabilities/overview/)
- [[Pydantic 官方] 官方文件 - Pydantic AI 設計規格與開發指南](https://pydantic.dev/docs/ai/overview/)
- [[Pydantic 官方] 技術部落格 - Pydantic AI 框架發布與設計哲學](https://pydantic.dev/)
- [[Scaler] 教學指南 - Microsoft AutoGen 演進與代理架構教學](https://www.scaler.com/topics/agentic-ai/microsoft-autogen-agent-framework/)
- [[Sid Bharath] 技術部落格 - OpenAI Agents SDK 生態與核心機制解析](https://sidbharath.com/blog/openai-agents-sdk/)
- [[Systenics AI] 實作指南 - 使用 Semantic Kernel 代理框架建置 Agent](https://systenics.ai/blog/2025-04-09-building-ai-agent-using-semantic-kernel-agent-framework)
- [[The Road To Enterprise] 架構設計 - 企業級 Agent 框架選型必要性評估](https://theroadtoenterprise.com/blog/do-you-need-an-agent-framework)
- [[Trans-N 科技] 新聞分析 - DeepSeek Harness 插件化模型與代理迴圈](https://note.com/trans_n_ai/n/nf4946f5847de?hl=en)
- [[Vercel 官方] 官方文件 - AI SDK 多代理協作與複雜工作流支援](https://community.vercel.com/t/vercel-ai-sdk-multi-agent-support-for-complex-agentic-workflows/35594)
- [[Vercel 官方] 技術部落格 - 生產環境 AI Agent 評估框架](https://vercel.com/i/ai-agent-evaluation-frameworks-production)
- [[Vercel 官方] 概念指南 - 開發者的 LLM Agent 核心概念與要素](https://vercel.com/i/llm-agent)
- [[Vercel 官方] 設計指南 - 問題導向的代理型 AI 應用構建](https://vercel.com/i/building-agentic-ai-applications-with-a-problem-first-approach)
- [[Vstorm] 架構設計 - Pydantic AI 在生產級深度代理的架構實踐](https://pydantic.dev/articles/pydantic-deep-agents)
- [[Vstorm] 案例研究 - 基於 Pydantic AI 的 Text-to-SQL 混合代理圖架構實踐](https://vstorm.co/case-study/from-single-agent-to-hybrid-agent-graph-architecture-our-journey-with-pydantic-ai-and-text-to-sql/)
- [[ZenML] 架構設計 - Mastra 觀察式記憶與人類啟發的上下文壓縮](https://www.zenml.io/llmops-database/observational-memory-human-inspired-context-compression-for-agent-systems)
- [[ZenML] 比較分析 - Microsoft AutoGen 與 Semantic Kernel 選型對比](https://www.zenml.io/blog/semantic-kernel-vs-autogen)
- [[安全研究] 安全分析 - smolagents CodeAgent 的程式碼執行安全風險](https://www.nccgroup.com/research/autonomous-ai-agents-a-hidden-risk-in-insecure-smolagents-codeagent-usage/)
- [[開發者社群] 實作指南 - 開發者 AI Agent 基礎構建實戰指南](https://mastra.ai/articles/how-to-build-ai-agents)
- [[開發者社群] 案例研究 - 基於 smolagents 的訂單處理多代理與記憶實作](https://ayotomiwasalau.com/work/blogs/smolagents-framework-building-multi-agents-tools-and-memory)
- [https://blckalpaca.at/en/knowledge-base/ai-agents/ai-agent-frameworks-comparison/autogen-vs-ag2](https://blckalpaca.at/en/knowledge-base/ai-agents/ai-agent-frameworks-comparison/autogen-vs-ag2)
- [https://community.sap.com/t5/artificial-intelligence-blogs-posts/langgraph-checkpoint-saver-for-sap-hana-cloud/ba-p/14336448](https://community.sap.com/t5/artificial-intelligence-blogs-posts/langgraph-checkpoint-saver-for-sap-hana-cloud/ba-p/14336448)
- [https://medium.com/@bhargavkoya56/migrating-from-semantic-kernel-agents-to-microsoft-agent-framework-in-net-42f8236327dc](https://medium.com/@bhargavkoya56/migrating-from-semantic-kernel-agents-to-microsoft-agent-framework-in-net-42f8236327dc)
- [https://medium.com/@tahirbalarabe2/what-is-pydantic-ai-15cc81dea3c3](https://medium.com/@tahirbalarabe2/what-is-pydantic-ai-15cc81dea3c3)
- [https://medium.com/@vishwajeetv2003/building-multi-agent-ai-applications-with-autogen-complete-tutorial-2026-2fbd9af73a9c](https://medium.com/@vishwajeetv2003/building-multi-agent-ai-applications-with-autogen-complete-tutorial-2026-2fbd9af73a9c)
- [https://pub.towardsai.net/deepseek-harness-and-the-end-of-the-model-only-roadmap-badb4b38c302](https://pub.towardsai.net/deepseek-harness-and-the-end-of-the-model-only-roadmap-badb4b38c302)
- [https://saurabh-shukla.medium.com/adding-memory-to-ai-agents-e506dfb3e60e](https://saurabh-shukla.medium.com/adding-memory-to-ai-agents-e506dfb3e60e)
- [https://venturebeat.com/data/observational-memory-cuts-ai-agent-costs-10x-and-outscores-rag-on-long](https://venturebeat.com/data/observational-memory-cuts-ai-agent-costs-10x-and-outscores-rag-on-long)
- [6 Open-Source Tools to Reduce Your Token Usage - WeAreDevelopers](https://www.wearedevelopers.com/magazine/746-6-open-source-tools-to-reduce-your-token-usage)
- [9 LLM Observability Tools for Production AI Agents - LangChain](https://www.langchain.com/resources/llm-observability-tools)
- [A Python Library for Creating Swarm-Style Multi-Agent Systems Using LangGraph : r/machinelearningnews - Reddit](https://www.reddit.com/r/machinelearningnews/comments/1kntjcn/meet_langgraph_multiagent_swarm_a_python_library/)
- [AI Agent Memory Architectures: From Context Windows to Persistent Knowledge | Zylos Research](https://zylos.ai/research/2026-04-05-ai-agent-memory-architectures-persistent-knowledge/)
- [AI Observability metrics reference - Dynatrace Documentation](https://docs.dynatrace.com/docs/observe/dynatrace-for-ai-observability/metrics-reference)
- [Agent Memory: How to Build Agents That Learn and Remember - Letta](https://www.letta.com/blog/agent-memory/)
- [Agentic CLI Tools Compared: Claude Code vs Cline vs Aider - AIMultiple](https://aimultiple.com/agentic-cli)
- [Agents SDK | OpenAI API](https://developers.openai.com/api/docs/guides/agents)
- [Aider Documentation](https://aider.chat/docs/)
- [Aider Review: Terminal AI Coding Agent (2026) - Codegen](https://codegen.com/ai-tools/aider/)
- [Aider vs OpenCode : Meilleur CLI de Codage AI Open Source… | NxCode](https://www.nxcode.io/fr/resources/news/aider-vs-opencode-ai-coding-cli-2026)
- [Best AI Agent Memory Providers in 2026: Mem0 vs Zep vs Letta vs Cloudflare](https://www.developersdigest.tech/blog/best-ai-agent-memory-providers-2026)
- [Build a coding agent with GPT 5.1 - OpenAI Developers](https://developers.openai.com/cookbook/examples/build_a_coding_agent_with_gpt-5.1)
- [Building Memory-First AI Reminder Agents with Mem0 and Claude Agent SDK](https://mem0.ai/blog/building-a-reminder-agent-that-actually-remembers)
- [Building Multi-Agent Systems with LangGraph Swarm: A New Approach to Agent Collaboration - DEV Community](https://dev.to/sreeni5018/building-multi-agent-systems-with-langgraph-swarm-a-new-approach-to-agent-collaboration-15kj)
- [Capstone 01 — Terminal-Native Coding Agent - AI Engineering from Scratch](https://aiengineeringfromscratch.com/lesson?path=phases%2F19-capstone-projects%2F01-terminal-native-coding-agent)
- [Chat modes | aider](https://aider.chat/docs/usage/modes.html)
- [Claude Code vs Aider: Terminal AI Coding Compared - Layer3Labs](https://www.layer3labs.io/comparisons/claude-code-vs-aider)
- [Claude Code vs Open Interpreter 2026 | LOW/CODE - LowCode Agency](https://www.lowcode.agency/blog/claude-code-vs-open-interpreter)
- [Codebase Graphs Are the New Agent Map - Developers Digest](https://www.developersdigest.tech/blog/codebase-graphs-ai-coding-agents)
- [Concepts - OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-python/sandbox/guide/)
- [Configure Telemetry: OTel GenAI Semantic Conventions - Rhesis AI](https://docs.rhesis.ai/guides/telemetry-configuration/genai-conventions)
- [Context Engineering - Repo Indexing and Code Graphs - InfraGap.com](https://infragap.com/context-engineering/)
- [Context Engineering - Short-Term Memory Management with Sessions - OpenAI Developers](https://developers.openai.com/cookbook/examples/agents_sdk/session_memory)
- [Enabling Long-Term Agent Memory with Oracle AI Agent Memory - OpenAI Developers](https://developers.openai.com/cookbook/examples/vector_databases/oracle_db/deep_research_openai_agents)
- [Evaluate any agent framework with Amazon Bedrock AgentCore Evaluations - AWS](https://aws.amazon.com/blogs/machine-learning/evaluate-any-agent-framework-with-amazon-bedrock-agentcore-evaluations/)
- [Examples - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/examples/)
- [Feature: Aider Coding Agent Skill — Lightweight Model-Agnostic Pair Programming Without PTY #534 - GitHub](https://github.com/NousResearch/hermes-agent/issues/534)
- [Gen AI | OpenTelemetry](https://opentelemetry.io/docs/specs/semconv/registry/attributes/gen-ai/)
- [GitHub - openai/swarm: Educational framework exploring ergonomic, lightweight multi-agent orchestration. Managed by OpenAI Solution team.](https://github.com/openai/swarm)
- [Graft Review 2026: Agent Repo Maps in Git - Wavect](https://wavect.io/blog/graft-review-agent-repo-map/)
- [Guardrails - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/guardrails/)
- [Guardrails - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/ref/guardrail/)
- [Guardrails | OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-js/guides/guardrails/)
- [Handoffs - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/handoffs/)
- [Handoffs | OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-js/guides/handoffs/)
- [Hindsight vs Letta (MemGPT): Agent Memory Compared (2026) - Vectorize.io](https://vectorize.io/articles/hindsight-vs-letta)
- [How Coding Agents Work: Inside the Agentic Loop - Dr. TMA Pai Endowment Chair - ITIS](https://blog.ecitis.org/coding-agents-explained/)
- [How OpenTelemetry Traces LLM Calls, Agent Reasoning, and MCP Tools - Reddit](https://www.reddit.com/r/OpenTelemetry/comments/1uy4gss/how_opentelemetry_traces_llm_calls_agent/)
- [How OpenTelemetry Traces LLM Calls, Agent Reasoning, and MCP Tools | Greptime](https://greptime.com/blogs/2026-05-09-opentelemetry-genai-semantic-conventions)
- [How to build an agent harness from scratch - Tair Asim](https://tairasim.com/blog/build-agent-harness-from-scratch/)
- [Instrument the OpenAI Agents SDK with OpenTelemetry (2026) - OpenObserve](https://openobserve.ai/blog/instrument-openai-agents-sdk-opentelemetry/)
- [Instrumenting AI Agents - Honeycomb Docs](https://docs.honeycomb.io/send-data/use-cases/agents)
- [Instrumenting AI Agents for the Agent Timeline: A Practical OpenTelemetry Guide](https://www.honeycomb.io/blog/instrumenting-ai-agents-agent-timeline-opentelemetry-guide)
- [Introducing the Agent Development Environment - Letta](https://www.letta.com/blog/introducing-the-agent-development-environment/)
- [LLM Observability in Production: Comparison of Langfuse, LangSmith, and OpenTelemetry](https://explore.n1n.ai/blog/llm-observability-langfuse-langsmith-opentelemetry-2026-05-17)
- [LangGraph Multi-Agent Swarm - LangChain Reference](https://reference.langchain.com/python/langgraph-swarm)
- [LangSmith vs. Langfuse - LangChain](https://www.langchain.com/resources/langsmith-vs-langfuse)
- [LangSmith: Agent & LLM Observability Platform - LangChain](https://www.langchain.com/langsmith/observability)
- [Langfuse vs. LangSmith: Comparing LLM Observability Platforms | DataCamp](https://www.datacamp.com/blog/langfuse-vs-langsmith)
- [Mastering LangGraph's Multi-Agent Swarm - Build Fast with AI](https://www.buildfastwithai.com/blogs/mastering-langgraph)
- [Memory - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/ref/memory/)
- [Multi-Agent Systems Don't Fail at Reasoning. They Fail at Handoff. | Chanl Blog](https://www.channel.tel/blog/handoff-is-the-new-prompt)
- [Multi-agent swarms with LangGraph - YouTube](https://www.youtube.com/watch?v=JeyDrn1dSUQ)
- [Open Interpreter Agent Security Risks – AIRQ Framework](https://airq.adversa.ai/agent/open-interpreter)
- [Open Programming Language Interpreters - arXiv](https://arxiv.org/html/1703.10873v1)
- [OpenAI Agents SDK - Braintrust](https://www.braintrust.dev/docs/integrations/agent-frameworks/openai-agents-sdk)
- [OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-python/agents/)
- [OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-python/)
- [OpenAI Swarm Multi-Agent Framework in 2026: What It Is, How It Works, and How to Use It | Lexogrine Blog](https://lexogrine.com/blog/openai-swarm-multi-agent-framework-2026)
- [OpenClaw vs Open Interpreter: The Ultimate Guide to Local AI Agents - Skywork](https://skywork.ai/skypage/en/openclaw-open-interpreter-local-ai-agents/2053006736888983552)
- [OpenInference vs OpenTelemetry GenAI for Agent Tracing - Arthur AI](https://www.arthur.ai/column/openinference-vs-opentelemetry-genai-conventions-agent-tracing)
- [OpenTelemetry (OTEL) for LLM Observability - Langfuse](https://langfuse.com/integrations/native/opentelemetry)
- [OpenTelemetry Instrumentation - Datadog Docs](https://docs.datadoghq.com/llm_observability/instrument/otel_instrumentation/)
- [Openai-agents handoff implementation - API](https://community.openai.com/t/openai-agents-handoff-implementation/1370797)
- [Overview - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/sessions/)
- [Quickstart - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/sandbox_agents/)
- [Sandbox Agents | OpenAI API](https://developers.openai.com/api/docs/guides/agents/sandboxes)
- [SandboxAgent - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/ref/sandbox/sandbox_agent/)
- [Sentra vs Letta (2026): Which Agent Memory Actually Scales to a Team](https://www.sentra.app/articles/sentra-vs-letta)
- [Swarm Architecture — AI Glossary | Superteams.ai](https://www.superteams.ai/glossary/swarm-architecture/)
- [Swarm vs. Supervisor: Multi-Agent Architecture Guide - Augment Code](https://www.augmentcode.com/guides/swarm-vs-supervisor)
- [The AI Agent Memory Architecture Deep Dive: Building Agents That Remember Across Sessions, Devices, and Tools - AI Magicx](https://www.aimagicx.com/blog/ai-agent-memory-architecture-developer-guide-2026)
- [The Adapter Pattern: Unified Tracing Across AI SDK, LangChain, and OpenAI Agents](https://dev.to/raju_dandigam/the-adapter-pattern-unified-tracing-across-ai-sdk-langchain-and-openai-agents-4d3b)
- [The Warp Agent CLI - Enaxt: Sunil Khobragade](https://enaxt.vercel.app/articles/artificial-intelligence/the-warp-agent-cli)
- [Tracing - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/tracing/)
- [Tracing | OpenAI Agents SDK - GitHub Pages](https://openai.github.io/openai-agents-js/guides/tracing/)
- [Tutorial on building agent with memory using Letta : r/AI_Agents - Reddit](https://www.reddit.com/r/AI_Agents/comments/1glzob6/tutorial_on_building_agent_with_memory_using_letta/)
- [Use Agents in Railway Sandboxes | Railway Guides](https://docs.railway.com/guides/agents-in-sandboxes)
- [What is the best way to handle long-term memory in the OpenAI Agents SDK? - iCertGlobal](https://www.icertglobal.com/community/long-term-memory-strategies-in-openai-agents-sdk)
- [Which LLM memory for AI Agents? - Tech blog](https://grigio.org/w/)
- [https://lablab.ai/ai-articles/understanding-openai-swarm-a-framework-for-multi-agent-systems](https://lablab.ai/ai-articles/understanding-openai-swarm-a-framework-for-multi-agent-systems)
- [https://medium.com/@abdulkabirlive1/mastering-handoff-agents-in-the-openai-agents-sdk-complete-guide-6103bd85217a](https://medium.com/@abdulkabirlive1/mastering-handoff-agents-in-the-openai-agents-sdk-complete-guide-6103bd85217a)
- [https://medium.com/@shreyas.arjun007/open-interpreter-revolutionising-code-generation-and-execution-60bbd282368a](https://medium.com/@shreyas.arjun007/open-interpreter-revolutionising-code-generation-and-execution-60bbd282368a)
- [https://medium.com/@srivastava.anubhav/the-end-of-digital-amnesia-and-the-rise-of-the-letta-protocol-9636b7212aad](https://medium.com/@srivastava.anubhav/the-end-of-digital-amnesia-and-the-rise-of-the-letta-protocol-9636b7212aad)
- [https://medium.com/@visrow/ai-context-cruncher-4-ways-to-shrink-a-repo-for-ai-agents-be277d98b905](https://medium.com/@visrow/ai-context-cruncher-4-ways-to-shrink-a-repo-for-ai-agents-be277d98b905)
- [https://realpython.com/ref/ai-coding-tools/open-interpreter/](https://realpython.com/ref/ai-coding-tools/open-interpreter/)
- [https://towardsdatascience.com/hands-on-with-agents-sdk-safeguarding-input-and-output-with-guardrails/](https://towardsdatascience.com/hands-on-with-agents-sdk-safeguarding-input-and-output-with-guardrails/)
- [https://www.morphllm.com/agent-tracing](https://www.morphllm.com/agent-tracing)
- [langchain/langgraph-swarm](https://reference.langchain.com/javascript/langchain-langgraph-swarm)
- [mem01session - PyPI](https://pypi.org/project/mem01session/)
- [memorywire: A Vendor-Neutral Wire Format for Agent Memory Operations - arXiv](https://arxiv.org/html/2606.01138)
- [openai/openai-agents-python: A lightweight, powerful framework for multi-agent workflows - GitHub](https://github.com/openai/openai-agents-python)
