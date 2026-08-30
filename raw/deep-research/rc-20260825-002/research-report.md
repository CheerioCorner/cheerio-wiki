# Deep Research 報告 — rc-20260825-002

- 研究主題：Herdr — coding agent 的 terminal runtime 全面研究：架構設計（session management、plugin system、socket API）vs Pi/Tau 比較（定位差異、互補性）、agent orchestration 能力（pane spawn、blocked detection、agent 間 prompt）及其對 Cheerio 助理的啟發
- Notebook：https://notebooklm.google.com/notebook/4aa704ca-c7b6-436c-a8d0-4e415495d284
- 產生時間：2026-08-30T09:05:17.575Z
- 品質過濾：59 → 49 筆（移除 10 筆）
- 蒸餾必要性：49 → 39 筆（移除 10 筆對研究問題無貢獻的來源）

## 研究結果

### 1. Herdr 的核心架構是什麼？session state、plugin、socket API 如何運作？

Herdr 是一個基於 Rust 語言編寫，並使用 Ratatui 終端使用者介面（TUI）函式庫開發的**終端機代理人執行期（Agent Runtime）與多路複用器（Multiplexer）** [1]。它的設計理念是作為 AI 代理人專屬的底層系統，將多個並行運作的 AI 代理人（Agents）終端會話進行模組化與結構化管理 [1, 2]。

以下為 Herdr 核心架構、會話狀態（Session State）與回復、插件系統（Plugin System）以及 Socket API 運作方式的深度解析：

---

### 1. Herdr 的核心架構 (Core Architecture)

Herdr 的架構本質上是一個**客戶端-服務端（Client-Server）模型** [3, 4]，其核心組件與拓撲結構如下：

*   **背景服務端（Herdr Server）**：負責在背景執行並擁有所有的 PTY 終端機控制權、處理程序狀態、工作區、標籤頁和面板 [4-6]。因為服務端常駐背景，即使使用者關閉終端機、關閉筆電上蓋或 SSH 連線中斷，運行中的 AI 代理人也**不會被終止**，能持續在背景完成任務 [4, 6, 7]。
*   **薄客戶端（Herdr Client）**：僅作為與服務端互動的「視窗」（Viewport）[4]。本機上客戶端與服務端預設透過本地 Unix Socket 進行通訊 [4]，使用者可隨時執行 `herdr` 進行附加（Attach）或使用 `Ctrl+b q` 安全分離（Detach）[4, 5]。
*   **資源管理層級（Organizational Primitives）** [8, 9]：
    *   **工作區（Workspace）**：最頂層的專案容器（通常一個專案 Repo 對應一個工作區），其側邊欄會彙整內含代理人的狀態 rollup [9, 10]。
    *   **標籤頁（Tab）**：每個工作區內的版面配置，可用於分隔不同的視角（例如將執行中的 Agent、日誌或伺服器分開）[9, 11]。
    *   **面板（Pane）**：實體的虛擬終端機（Linux/macOS 上的 PTY 或 Windows 上的 ConPTY）[9, 11, 12]。
    *   **代理人（Agent）**：在面板中運行的具體 AI 進程 [8, 9, 13]。

---

### 2. 會話狀態與回復機制 (Session State & Restore)

為了讓使用者能同時掌控多個並行任務，Herdr 引入了** AI 狀態感知（AI-Aware State）**機制：

*   **四大語義狀態感知** [13, 14]：
    *   🔴 **Blocked（阻礙）**：代理人正停在需要使用者輸入、確認、或授權（Permission Prompt）的介面 [13-15]。
    *   🟡 **Working（工作中）**：代理人正積極進行推理、生成、編譯或執行命令 [13-15]。
    *   🔵 **Done（完成）**：任務已完成，且使用者尚未點進去檢視成果 [13, 14]。
    *   🟢 **Idle（閒置）**：任務已結束且已被使用者檢視，或正等待下一個任務 [13, 14]。
*   **狀態檢測雙軌制** [16-18]：
    *   **生命週期鉤子（Lifecycle Hooks / Plugins）**：針對深度整合的代理人（如 Pi, OMP, Kimi Code 等），代理人會透過 Hook 主動向 Herdr 通報其狀態（如 `session.idle`, `session.status`），此為最權威、無延遲的狀態來源 [16, 17, 19]。
    *   **螢幕特徵對比（Screen Manifests）**：對於沒有主動 Hook 匯報的代理人（如 Claude Code, OpenAI Codex），Herdr 會讀取終端機底部的緩衝區快照，根據 TOML 設定檔中的正則規則去匹配常見的問答 UI 模式，以此推斷代理人是否處於 `blocked`、`working` 或 `idle` 狀態 [18, 20, 21]。
*   **會話恢復（Session Restore）** [22, 23]：
    官方整合套件會向 Herdr 的 Socket 匯報本機的「原生會話識別碼」（Native Session Reference）[22, 23]。若 Herdr 伺服器因關機、重新啟動等原因重置，它能夠利用保存的會話 ID，在重啟後自動呼叫代理人對應的恢復指令（例如 `claude --resume <id>` 或 `kimi --session <id>`）將 PTY 還原並繼續對話 [23-26]。

---

### 3. 插件系統 (Plugin System)

Herdr 的插件系統採用 **Out-of-Process（進程外外掛）** 的設計架構 [27, 28]：

*   **語言無關性**：插件只是一個包含 `herdr-plugin.toml` 設定檔及可執行檔的目錄，其實作語言可以是 Bash、NodeJS、Rust、Python 或 Go 等任何本機能執行的腳本或二進位檔 [28-30]。
*   **資訊清單（Manifest）合約** [29]：在 `herdr-plugin.toml` 中聲明插件元數據，並定義 Herdr 如何與插件溝通：
    *   `[[build]]`：定義安裝插件時需執行的編譯/相依性安裝指令（如 `npm ci`）[29, 31]。
    *   `[[startup]]`：定義在會話回復、API Socket 就緒後一次性執行的初始化腳本 [29, 32, 33]。
    *   `[[actions]]`：可由 CLI 觸發、綁定快速鍵或事件的具體指令項目 [29, 34, 35]。
    *   `[[events]]`：訂閱 Herdr 內部的事件（如 `worktree.created`），並在事件發生時調用插件 [29, 36]。
    *   `[[panes]]`：在 Herdr 的版面中載入一個進程，可渲染為彈出視窗（Popup）或分割面板等終端介面 [29, 37, 38]。
    *   `[[link_handlers]]`：在終端機內 Ctrl-click 特定的 URL 正則（如 GitHub Issue 連結）時，不開啟瀏覽器，而是將其重導向至插件 Action 進行內部處理 [39]。
*   **上下文注入（Context Injection）**：Herdr 啟動插件進程時，會注入豐富的環境變數（如 `HERDR_SOCKET_PATH`、`HERDR_BIN_PATH`、`HERDR_PANE_ID`）以及一個精準記錄當前 UI 焦點狀態的 JSON 字串 `HERDR_PLUGIN_CONTEXT_JSON` [34, 37]。
*   **無專屬 SDK 限制**：Herdr 的**整套 CLI 即為外掛 API** [40]。插件腳本內部只需調用環境變數提供的 `HERDR_BIN_PATH`（即指向當前運行的 herdr 執行檔），即可對 Herdr 服務端發送任何指令 [38, 40]。

---

### 4. Socket API 運作方式 (How Socket API Works)

Herdr 通過本地通訊埠開放了極其強大的 Socket API，這也是其能實現**「代理人彼此協調（Agent-to-Agent Orchestration）」**的核心通道 [41, 42]：

*   **傳輸協定**：使用基於 TCP 或本地管道的 **換行符分隔 JSON（Newline-Delimited JSON / NDJSON）** 格式進行請求/響應 [43, 44]。
    *   在 **Unix** 系統上：透過 Unix Domain Socket 通訊，預設路徑為 `~/.config/herdr/herdr.sock` [4, 44-46]。
    *   在 **Windows** 系統上：預設透過 Named Pipe（具名管道）傳輸 [38, 44]。
*   **三大編排原語（Spawn, Inject, Wait）** [47]：
    程式可以藉由對該 Socket 傳送特定方法來進行以下操作：
    1.  **Spawn（生成）**：透過 `pane.split` 方法在指定的工作區或標籤頁中開啟新的面板，並使用 `agent.start` 啟動新的 AI 代理人進程 [8, 48, 49]。
    2.  **Inject（注入）**：向指定面板發送按鍵或輸入，如 `pane.send_keys` 或 `agent.prompt`，使 AI 代理人直接開始執行任務 [47, 50, 51]。
    3.  **Wait & Subscribe（等待與訂閱）**：
        *   **同步等待**：使用 `agent.wait` 阻塞 Socket 連線，直到該面板的代理人到達目標狀態（如 `--until done` 或 `--until blocked`），隨即返回結果並讓主控程序繼續執行 [50, 52, 53]。
        *   **非同步訂閱**：透過 `events.subscribe` 與服務端保持長連接，實時監聽系統事件（如代理人狀態改變事件 `pane.agent_status_changed`、輸出正則匹配事件 `pane.output_matched`），進而實現複雜的多代理人工作流編排 [44, 54]。
*   **原生 SDK**：開發者除了可以用 Bash 腳本包裝 CLI 外，也可以使用 Typed 的 Rust 版本 `herdr_sdk` 直接對該 Socket 發送 IPC 請求，構建具有完全狀態感知能力的自動化調度器 [41, 55]。

這套高度模組化的底層設計，使 Herdr 從一個單純的「終端多路複用器」演變為能讓 AI 代理人直接調用、自我切割版面與自主分配任務的**程式開發代理人作業系統（Agent Operating System）** [56, 57]。

💡 **接下來，您是否想進一步了解 Herdr 與 Pi/Tau 之間的定位差異？還是想深入研究如何撰寫一個 Herdr 的自動化編排腳本？**

**引用來源：**
- [1] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [2] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [3] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [4] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [5] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [6] [[Herdr 官方] 產品規格 - 定位為 AI 代理人專屬之終端執行期環境](https://herdr.dev/)
- [7] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [8] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [9] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [10] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [11] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [12] [[Herdr 官方] 產品規格 - Windows 系統相容性與環境設定支援](https://herdr.dev/docs/windows-beta/)
- [13] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [14] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [15] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [16] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [17] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [18] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [19] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [20] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [21] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [22] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [23] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [24] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [25] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [26] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [27] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [28] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [29] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [30] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [31] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [32] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [33] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [34] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [35] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [36] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [37] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [38] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [39] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [40] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [41] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [42] [[Herdr 官方] 產品規格 - 定位為 AI 代理人專屬之終端執行期環境](https://herdr.dev/)
- [43] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [44] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [45] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [46] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [47] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [48] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [49] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [50] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [51] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [52] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [53] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [54] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [55] [[Docs.rs 官方] 開發者文件 - Rust 版本 herdr_sdk API 規格手冊](https://docs.rs/herdr-sdk)
- [56] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [57] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)

### 2. Herdr 與 Pi/Tau 的定位差異是什麼？互補性如何？

### 1. 定位差異 (Positioning Differences)

**Herdr** 與 **Pi/Tau** 處於 AI 代理人開發生態系中完全不同的層級，兩者的核心定位差異如下：

*   **Pi / Tau：上層的「AI 代理人與開發套件」（Agentic Harness & Distribution）**
    *   **Pi (pi.dev)** 是一個**極簡、高擴充性的 AI 代理人框架（Harness）** [1, 2]。它不提供臃腫的預設功能，而是專注於「上下文工程（Context Engineering）」與高雅的擴充性，允許開發者透過 TypeScript 擴充套件（Extensions）、自訂工具、技能（Skills）和主題來自由重塑它 [2-4]。
    *   **Tau** 則是針對 Pi 的 **「全配版發行版」（Batteries-included Distribution）** [5, 6]。它將 Pi 裸露的極簡核心封裝成開箱即用的完整體驗，整合了網頁搜尋、程式碼審查、OS 級沙盒（Sandbox）、本機記憶體（Memory）等實用工具與工作流 [5-7]。
    *   **核心本質：** 它們是**大腦與執行者**，負責調用 LLM 進行推理、讀寫檔案、執行 Git 指令、生成程式碼並與使用者對話 [2, 6, 8]。
*   **Herdr：下層的「代理人終端執行期與多路複用器」（Agent Terminal Runtime & Multiplexer）**
    *   **Herdr** 是一個基於 Rust 寫成的**終端機代理人執行期環境** [9, 10]。它採用類似 `tmux` 的多路複用架構（Panes, Tabs, Workspaces），在背景運行 headless 伺服器來管理 PTY 終端進程 [10-14]。
    *   **核心本質：** 它扮演**基礎建設與容器（Substrate）** 的角色，坐在「代理人進程之下」 [15, 16]。它不具備 LLM 推理能力，也不直接編寫程式，而是專注於**維持代理人進程不中斷**（防止因關閉筆電、SSH 斷線導致任務夭折） [13, 17]，並將所有並行運作的代理人狀態（`working`、`idle`、`blocked`、`done`）匯整成統一的注意力佇列 [18-21]。

> **Gary Dotzlaw 的經典架構比喻：** 
> 乾淨的系統架構應將職責分離，**Herdr 是基礎設施（Substrate）**，負責底層的 PTY 隔離、狀態信號、生命週期監控與通訊 Socket；而 **Pi/Tau 則是上層的協調與執行者（Orchestrator/Agent）**，負責專家的分工、路徑編排與任務交付 [16, 22, 23]。

---

### 2. 互補性如何？(How They Complement Each Other)

Herdr 與 Pi/Tau 的互補性極高，可以說 **Herdr 賦予了 Pi/Tau 企業級的並行執行與自動編排能力，而 Pi/Tau 則為 Herdr 提供了精準的生命週期報告。** 具體互補點體現在以下四個維度：

#### ① 完美的生命週期權威（Lifecycle Authority）[24, 25]
當代理人（例如 Claude Code）不支援原生 Hook 時，Herdr 只能透過「畫面正則匹配（Screen Manifest）」來猜測代理人是否進入阻礙（Blocked）狀態，這容易產生幾秒鐘的偵測延遲或誤判 [26-28]。
*   **互補實現：** **Pi 是 Herdr 官方認證的「生命週期權威（Lifecycle Authority）」** [24, 25, 29]。
*   當使用者在 Herdr 中執行 `herdr integration install pi` 時 [30, 31]，Herdr 會自動將一個 TypeScript 擴充套件（`herdr-agent-state.ts`）寫入 Pi 的全域或專案擴充目錄中 [31]。
*   此後，Pi 在運行時會透過內建事件 Hook 偵測狀態，並**主動且即時地向 Herdr 的 Socket API 匯報其生命週期事件**（例如 `working`、`idle`、`blocked`、`done`） [25, 29, 32]。這完全消除了畫面讀取的落後與不確定性，實現無延遲的精準監控。

#### ② 本機會話的持久化與恢復（Session Restore）[33, 34]
當長達數小時的 AI 代理人編譯或程式碼重構工作正在進行時，最怕遇到網路斷線或本機重啟。
*   **互補實現：** Herdr 作為背景守護進程，確保了 Pi 進程在筆電蓋上或 SSH 連線中斷後繼續安全運作 [13, 17, 35]。
*   若不幸發生本機重開機或伺服器重置，Pi 會在啟動時向 Herdr 註冊其「原生會話識別碼（Native Session ID）」 [34, 36]。Herdr 重啟後，能自動利用此 Session ID 呼叫 Pi 的恢復指令（如 `pi --resume <session_id>`），將終端與對話歷史還原，達到**零脈絡遺失（Zero context loss）**的強健狀態 [36, 37]。

#### ③ 代理人驅動編排（Agent-driven Orchestration / Sub-agents）[38, 39]
Pi 原生不內建多代理人（Sub-agents）或複雜的版面自動化功能（這是為了保持核心極簡） [2, 40]。
*   **互補實現：** 由於 Pi/Tau 具有極高的腳本擴充能力 [2]，它能輕易透過 Shell 或 SDK 調用 Herdr 的 **Socket API 與 CLI 工具** [34, 39]。
*   在實戰中，一個主導的 **Pi 代理人可以自己讀取 Herdr 的 CLI 文件，然後主動對本機 Socket 發送指令**：自動建立新的標籤頁、平行切割（`pane split`）出多個子面板、在其中啟動多個子 Pi 代理人（例如並行處理 API 修改、測試撰寫、部署配置），並使用 `herdr agent wait` 阻塞等待子代理人全部回報 `done` 後，再將結果收網 [39, 41-43]。這讓 Pi 輕鬆擁有了跨進程、完全隔離的**多代理人調度能力** [44, 45]。

#### ④ 安全沙盒與顯示優化（Sandbox & UI Polish）[7, 25, 46]
*   **互補實現：** Tau 包含了高度客製化的 `/sandbox`（作業系統級沙盒） [7]、`tool-display-mode`（工具輸出壓縮） [7]，以及狀態列顯示。這些功能與 Herdr 提供的自訂元數據 API（`pane report-metadata`）相結合 [46, 47]，可以將 Tau 的即時工作摘要（例如 "Refactoring auth middleware"）直接注入到 Herdr TUI 的側邊欄中 [46]，向使用者提供最極致、可視性最高的終端開發體驗 [46, 47]。

---

### 總結

| 項目 | Herdr (Substrate 下層) [15, 16] | Pi / Tau (Agent 上層) [2, 5] |
| :--- | :--- | :--- |
| **職責** | 終端多路複用、進程持久化、注意力佇列管理、狀態偵測。 [9, 19, 48] | 大腦推理、LLM 通訊、工具鏈執行（Git, 編輯器, 網頁爬蟲）。 [2, 6, 8] |
| **互動** | 開放本機 Unix Socket 與 CLI 命令供上層調用。 [21, 49] | 透過內建 Hook 主動回報精準狀態；或主動調用 Socket API 進行子面板編排。 [25, 29, 39] |
| **價值** | 提供安全隔離且不會中斷的「終端作業系統」。 [15, 50] | 提供敏捷、功能強大且可塑性極高的「軟體工程師」。 [2, 6, 8] |

兩者結合，開發者只需拋出一個初始 Prompt，Pi 就能在 Herdr 的隔離空間中化身為一隊「自給自足」的並行專家，在後台默默、安全地把龐大的軟體專案重構完成 [22, 38, 51]。

* 🧭 **您想進一步看看，如何使用 Pi 的擴充套件，或者如何撰寫一個讓 Pi 呼叫 Herdr API 的簡單編排腳本（例如 spawn 兩個子 Pane 並等待其狀態）嗎？**

**引用來源：**
- [1] [[Pi 官方] 產品規格 - Pi Coding Agent 的終端開發助理功能](https://pi.dev/)
- [2] [[Pi 官方] 產品規格 - Pi Coding Agent 的終端開發助理功能](https://pi.dev/)
- [3] [[Pi 官方] 產品規格 - Pi Coding Agent 的終端開發助理功能](https://pi.dev/)
- [4] [[Pi 官方] 產品規格 - Pi Coding Agent 的終端開發助理功能](https://pi.dev/)
- [5] [[GitHub 專案] 產品規格 - Tau 開箱即用版 Pi Agent 套件整合包](https://github.com/goncalossilva/tau)
- [6] [tau-all-agent · Packages · Pi](https://pi.dev/packages/tau-all-agent?type=theme)
- [7] [[GitHub 專案] 產品規格 - Tau 開箱即用版 Pi Agent 套件整合包](https://github.com/goncalossilva/tau)
- [8] [tau-coding-agent · Packages · Pi](https://pi.dev/packages/tau-coding-agent?page=40)
- [9] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [10] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [11] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [12] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [13] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [14] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [15] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [16] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [17] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [18] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [19] [[Herdr 官方] 產品規格 - 定位為 AI 代理人專屬之終端執行期環境](https://herdr.dev/)
- [20] [[Herdr 官方] 產品規格 - 定位為 AI 代理人專屬之終端執行期環境](https://herdr.dev/)
- [21] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [22] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [23] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [24] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [25] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [26] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [27] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [28] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [29] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [30] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [31] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [32] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [33] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [34] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [35] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [36] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [37] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [38] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [39] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [40] [[Pi 官方] 產品規格 - Pi Coding Agent 的終端開發助理功能](https://pi.dev/)
- [41] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [42] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [43] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [44] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [45] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [46] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [47] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [48] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [49] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [50] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [51] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)

### 3. Herdr 的 agent orchestration（pane spawn、blocked detection、agent 間 prompt）如何實現？

在 Herdr 的設計中，**代理人編排（Agent Orchestration）**是將終端多路複用器（PTY Multiplexer）轉化為「AI 代理人專用作業系統」的核心功能 [1-3]。這種能力不僅讓人類能輕鬆監督多個 AI [4, 5]，更允許**運作中的 AI 代理人直接透過 API 來操控、切割、注入提示並監控其他代理人** [5-7]。

以下是 Herdr 在「面板生成 (Pane Spawn)」、「阻礙狀態偵測 (Blocked Detection)」與「代理人間 Prompting (Agent-to-Agent Prompting)」這三個關鍵編排維度的技術實現原理：

---

### 1. 面板生成 (Pane Spawn) 的實現

在 Herdr 中，終端版面的拓撲結構（Layout）與實體執行的代理人（Agent）在邏輯上是**完全分離的** [8, 9]：
*   **版面切割（Pane Split）**：編排腳本或主控代理人需要先透過 Socket API 或 CLI 發送版面指令（如 `pane.split` 或 `herdr pane split`）[10-12]。例如：
    ```bash
    split=$(herdr pane split --current --direction right --no-focus)
    ```
    該指令會在背景建立一個新的實體 PTY 虛擬終端 [5, 13, 14]，並將其與父面板完全隔離 [15, 16]。
*   **擷取 JSON 回傳 ID**：創建指令執行成功後，Herdr 會傳回包含詳細拓撲資訊的 JSON 資料 [10, 12, 17]。編排程序必須從中解析出新生成的 opaque 面板識別碼（例如 `.result.pane.pane_id`，如 `w1:p2`），而不能預測或預判 ID [10, 12, 18, 19]。
*   **啟動代理人（Agent Start）**：新生成的 Pane 最初只是一個標準的 Shell 面板 [8, 9]。**`agent start` 指令本身不會創建或分割任何版面，它必須在一個「處於閒置/乾淨互動提示字元（Interactive Prompt）」的現有 Shell 面板中被呼叫** [8, 9, 20, 21]。
    ```bash
    herdr agent start reviewer --kind codex --pane "$review_pane"
    ```
    當此指令執行時，Herdr 會在該 PTY 中啟動對應代理人種類的 canonical 可執行檔 [20-22]，並阻塞（預設等待至多 30 秒）[23-25]，直到 Herdr 成功在終端中檢測到該代理人啟動完成、進入 `idle` 狀態，才返回成功訊號 [23-25]。

---

### 2. 阻礙狀態偵測 (Blocked Detection) 的實現

為了精準識別代理人何時停在「等待使用者輸入、確認授權或回答問題」的階段（即 `blocked` 狀態）[26-28]，Herdr 採用了**單一面板狀態權威（Status Authority）的雙軌制設計** [29, 30]：

*   **軌道一：生命週期鉤子與插件（Lifecycle Hooks / Plugins —— 精準權威）**
    *   **適用對象**：具有完整生命週期回報機製的代理人（如 Pi, OMP, Kimi Code CLI, OpenCode, MastraCode 等） [31-33]。
    *   **原理**：當用戶安裝了該代理人的 Herdr 官方整合套件後，代理人啟動時會繼承 `HERDR_SOCKET_PATH` 等變數 [34, 35]。代理人內部的 Event Hook 會在狀態轉移時，**主動向 Herdr Socket 發送狀態報告**（對應 CLI 的 `pane report-agent` 或是 JSON-RPC 的 `pane.report_agent`）[36-38]。
    *   **優勢**：由於是代理人主動、即時地報告 `state: "blocked"`，**此軌道為絕對精準且無延遲的權威來源** [29, 39]。一旦此機制啟用，Herdr 會自動**屏蔽畫面特徵讀取**，避免競態與衝突 [29, 32]。
*   **軌道二：螢幕特徵正則匹配（Screen Manifest TOML Rules —— fallback 自動偵測）**
    *   **適用對象**：未提供或未安裝主動回報機製的代理人（如 Claude Code, OpenAI Codex, GitHub Copilot CLI 等） [31-33]。
    *   **原理**：Herdr 的背景服務器（Herdr Server）會**直接、定期讀取該面板的 PTY 底層螢幕快照緩衝區（recent bottom-buffer snapshot）** [30]，此讀取不受使用者滾動畫面（Scroll back）影響 [30]。
    *   **TOML 匹配**：Herdr 會載入針對各代理人編寫的 TOML 偵測資訊清單（Manifests，通常存放於 `~/.config/herdr/agent-detection/<agent>.toml`）[30, 40]，使用其中定義的正則表達式規則與終端底部的文字模式（例如 Claude Code 的授權詢問字樣）進行匹配 [26, 30, 41]。
    *   **嚴格性判定**：為了防止將仍在思考 redraw 的 spinner 動態誤判為阻礙，**Herdr 的 Blocked 偵測極其嚴格** [26, 42, 43]。只有當快照文字與已知 manifest 的 approval 或 question UI **完全精準符合**時，才會標記為 `blocked` [26, 44]；若沒有任何 Manifest 規則符合，Herdr 會一律將其 Fallback 標記為 `idle` [26, 43]。

---

### 3. 代理人間 Prompt 的實現 (Agent-to-Agent Prompting)

要讓一個 AI 代理人向另一個 AI 代理人發送任務並收集結果，其控制鏈是建立在 Herdr 本地 Socket API（Unix Domain Socket 或 Windows Named Pipe）上的 **Spawn、Inject、Wait** 三大編排原語（Primitives）[45-47]：

*   **Socket 通訊協定**：
    Herdr 暴露一個發送 **換行符分隔 JSON（NDJSON）** 的本地 Socket [45, 47]。任何代理人或腳本，只要在環境變數中讀取到 `HERDR_SOCKET_PATH`，就可以透過發送 JSON-RPC 請求來操控會話 [35, 45, 48]。
*   **原子化注入與等待（Prompt & Wait）**：
    為了解決「發送 Prompt」與「開始等待（Wait）」之間可能發生的競態條件（Race condition，例如在發送與等待的指令空檔間代理人就已經執行完畢），Herdr 設計了**原子化整合原語** [49, 50]：
    *   **`agent.prompt` 方法**：接受一個可選的 `wait` 物件參數，內含 `until`（目標狀態，如 `done`、`blocked`）與 `timeout_ms` [49]。這允許主控端在**單次 Socket 請求**中，同時完成「送出文字」與「開啟狀態監聽」[49, 50]。
    *   **防衝撞保護**：如果被 Prompt 的目標代理人已經處於 `blocked` 狀態，該命令會立即返回 `agent_blocked` 錯誤，並拒絕寫入任何位元組，防止破壞現有的對話狀態 [24, 49-51]。
    *   **超時與停滯檢測**：注入的 Prompt 必須在 **5 秒內**使目標代理人觀察到生命週期狀態改變（轉為 `working`），否則 Herdr 會拋出 `agent_prompt_stalled` 並中斷等待，防止主控端無限期卡死 [24, 51, 52]。
*   **會話鎖定與事件訂閱（Wait and Subscribe）**：
    *   **`agent wait` (同步阻塞)**：這是伺服器端驅動的 [49]。它在 Socket 上會「釘住（Pin）」當前解析出的面板佔用進程 [49]。即使該 Pane 後來有其他進程介入，也只有原本的代理人進程達到 `idle`、`done` 或 `blocked` 才能滿足這個阻塞 Wait 釋放 [49, 51, 53]。
    *   **`events.subscribe` (非同步監聽)**：主控代理人或自動化腳本能透過 Socket 維持長連接，訂閱 `pane.agent_status_changed`、`pane.output_matched` 等核心事件 [46, 54]。當事件觸發時，Socket 會直接向客戶端推送狀態更新，從而驅動更複雜的無阻塞（Non-blocking）並行任務分發與工作流協調 [5, 46, 54]。
*   **技能封裝（Wrap as a Skill）**：
    在實務編排上，官方與社群（如 Dotzlaw 框架）強烈建議將這一套「Spawn - Inject - Wait - Read」的 CLI 呼叫鏈，利用 `npx skills add` 封裝成一個**具體的 Agent 技能（Skill）** [55-57]。這讓主導編排的 AI 代理人只需呼叫該技能，即可自動化、標準化地操作 Socket 產生子專家面板（Specialist Panes）並安全回收結果，大幅降低 LLM 猜測 CLI 命令的機率 [55, 58, 59]。

💡 **您是否想參考一段具體的 Python 腳本或 Node.js 程式碼，看看如何與這個本地 Socket 通訊來發送 `agent.prompt` 並監聽 `pane.agent_status_changed` 事件？**

**引用來源：**
- [1] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [2] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [3] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [4] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [5] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [6] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [7] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [8] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [9] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [10] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [11] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [12] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [13] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [14] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [15] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [16] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [17] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [18] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [19] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [20] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [21] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [22] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [23] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [24] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [25] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [26] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [27] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [28] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [29] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [30] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [31] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [32] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [33] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [34] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [35] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [36] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [37] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [38] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [39] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [40] [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [41] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [42] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [43] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [44] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [45] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [46] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [47] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [48] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [49] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [50] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [51] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [52] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [53] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [54] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [55] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [56] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [57] [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [58] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [59] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)

### 4. Herdr 對 Cheerio 助理的設計有什麼啟發或可借鑑之處？

將 **Herdr** 這套終端代理人執行期（Agent Runtime）與多路複用器的架構思維 [1, 2]，對照到 **Cheerio 網頁數據擷取助理** 的開發 [3]，能為我們在建構「企業級、自主、高容錯性」的 AI 爬蟲助理時，提供許多極具前瞻性的架構設計借鑑。

以下是 Herdr 對 Cheerio 助理設計的 **六大核心啟發與借鑑之處**：

---

### 1. 將爬蟲改為「背景常駐與分離附加」（Client-Server 持久化）
*   **痛點：** 許多網頁爬取或多頁面、深度的鏈接爬行任務（Link Discovery）極耗時間 [3, 4]。傳統的 Cheerio 爬蟲若只是個單次運行的 CLI 腳本，一旦中斷、網絡掉線、或關閉筆電，執行中的爬蟲與已抓取的暫存數據就會化為烏有 [5, 6]。
*   **Herdr 的啟發：** Herdr 採用 **Server-Client 架構**，實際的終端 PTY 與代理人進程由常駐背景的伺服器（Herdr Server）託管，用戶端（Client）隨時可以分離（Detach）與重新附加（Reattach）[6-8]。
*   **借鑑設計：** Cheerio 助理應從單次腳本升級為 **背景爬蟲服務（Scraping Daemon）**。
    *   主導任務的 AI Agent 可以發起一個長達數小時的爬取作業，然後安全離線 [5, 6]。
    *   助理在背景持續透過 Axios 請求網頁並由 Cheerio 解析 [9, 10]。
    *   用戶或主控 Agent 隨時可透過 thin client 重新連接至該「爬取會話（Scrape Session）」，查詢實時進度，而不會干擾正在執行的 Node.js 進程 [5, 6]。

---

### 2. 引入「語義狀態感知」解決防爬蟲擋牆（Blocked Detection）
*   **痛點：** 爬蟲在抓取過程中，隨時會遭遇 403 Forbidden、IP 封鎖、CAPTCHA 驗證，或者目標網站需要手動登入認證 [11-13]。傳統爬蟲在遇到這些「阻礙」時，通常只能以拋出 Error 崩潰收尾 [14]。
*   **Herdr 的啟發：** Herdr 定義了明確的四個狀態：🔴 `blocked`、🟡 `working`、🔵 `done`、🟢 `idle` [15, 16]。特別是 **Blocked（阻礙）偵測**，當判定代理人停在確認或授權畫面時，系統會暫停並等待人工介入 [17, 18]。
*   **借鑑設計：** Cheerio 助理應具備 **「AI 狀態感知（AI-Aware State）」能力** [19]。
    *   當助理遭遇驗證碼（CAPTCHA）或登入牆時，不應直接報錯崩潰，而是將自身的會話狀態轉移為 🔴 **Blocked**，並發送系統通知（或透過 Discord/Telegram 機器人通知開發者）[17, 20]。
    *   助理可提供一個臨時的主動界面（例如呼叫 Puppeteer 打開一個 headless browser）[21, 22]，等待人類輸入驗證碼或完成 OAuth 登入 [22]，隨後將 Session 狀態調回 🟡 **Working**，無縫繼續 Cheerio 的高效靜態解析 [23]。

---

### 3. 多代理人協調（Agent Orchestration）避免 Context 爆炸
*   **痛點：** 單一爬蟲 Agent 同時處理「URL 探測、HTML 抓取、規避頻率限制、結構化解析、JSON 儲存」等多重任務時，會迅速撐爆 LLM 的上下文窗口，且程式碼一多極易出錯 [24-26]。
*   **Herdr 的啟發：** 透過 **Spawn、Inject、Wait** 三大原語，主控 Agent 可動態切割面板（`pane.split`），生成多個完全環境隔離的子專家進程（Sub-agents）並行工作，最後再將結果收網 [27-29]。
*   **借鑑設計：** 採取 **「微型專家爬蟲群（Micro-specialist Swarms）」** 設計：
    *   **Orchestrator (主控助理)：** 負責規劃爬取藍圖與決定調度。
    *   **Link Discoverer (連結偵測面板)：** 僅負責使用 Cheerio 從主頁面爬取、過濾與Нор正則表達式篩選子網址 [3, 30]。
    *   **Static Scrapers (特化抓取進程)：** 分配到多個隔離的背景面板，並行向不同子網址發送 Axios GET 請求（利用 Local Cache 減少重覆請求）[31, 32]。
    *   **Data Parser (結構化提取器)：** 負責用 Cheerio 的選取器（jQuery-like API）將 HTML 轉換為乾淨、標準的 JSON 檔案 [9, 33]。
    這不僅隔離了各任務的 Blast Radius（出錯影響範圍），更降低了單次 LLM 運行的 Context 消耗 [25, 34]。

---

### 4. 採用「資訊清單與合約化（Manifest-based Contracts）」對抗網頁結構變更
*   **痛點：** 目標網頁的 CSS Selector 經常變更（例如 `.product_card` 改名），這會使 Cheerio 的提取函數直接抓到 null 數據，導致整條自動化流水線斷裂 [35, 36]。
*   **Herdr 的啟發：** Herdr 為了相容各種不同的 AI 代理人，制定了標準的 `herdr-plugin.toml` 插件合約 [37, 38] 以及對外開放的規範化狀態回報協定 [39, 40]。
*   **借鑑設計：** Cheerio 助理應擺脫將 CSS 選擇器 Hardcode 在程式碼中的壞習慣。
    *   設計一個 **抓取資訊清單（Scraping Manifest）**，以 JSON/TOML 格式定義網頁類型與對應的標籤 Schema（如 `bookTitle = ".book-title"`）[41, 42]。
    *   助理在運行時調用該 Manifest。一旦解析失敗或數據不符合預期，可利用類似 Herdr `agent.explain` 的機制 [43, 44]，向 AI 助理生成一個 **HTML 解構診斷報告（DOM Explain Report）**，讓 AI 主動重新推理、修正 CSS Selector，自動更新 Manifest 而不需開發者重寫程式碼 [44, 45]。

---

### 5. 提供「本地 Socket API」實現 Tmux-as-a-Service / Crawler-as-a-Service
*   **痛點：** 網頁爬蟲通常是孤立的系統，很難讓其他開發工具或當前終端正在運行的 AI Agent（如 Claude Code 或 Pi）無縫調用 [46, 47]。
*   **Herdr 的啟發：** Herdr 透過本地的 **NDJSON Unix Socket / 具名管道**，向系統中的所有進程開放控制權，任何程序只要能發送 JSON-RPC 即可調用其多路複用與狀態管理功能 [48, 49]。
*   **借鑑設計：** Cheerio 助理應設計一條 **本地通訊總線（Scraping Bus）** [48]：
    *   開放本地 Socket 供系統中的其他開發 Agent 呼叫 [48, 50]。
    *   當 Claude Code 需要了解某個 API 文件時，它不需重新執行自建的 Axios，而是向 Cheerio 助理的 Socket 發送一個請求 `{"method": "scrape.fetch", "params": {"url": "..."}}` [49, 51]。
    *   助理將網頁解析、清洗為 Markdown 後直接回傳，真正實現 **「爬蟲作為本地基礎設施（Crawler-as-Substrate）」** 的無縫整合 [52, 53]。

---

### 6. 會話中斷恢復與「狀態鎖定」（Session Restore & Idempotency）
*   **痛點：** 大型爬取任務中，進程因 OOM（記憶體不足）或系統崩潰重啟時，通常要從頭抓取，耗費大量 API Tokens 與頻率限制額度 [20, 54]。
*   **Herdr 的啟發：** Herdr 支援 **Restart Restore（重啟還原）** 與會話持久化 [55]。它會儲存各代理人的本機會話識別碼（Native Session ID），在伺服器異常重置後，能重啟進程並精準恢復對話歷史 [55, 56]。
*   **借鑑設計：**
    *   Cheerio 助理的任務佇列、URL 狀態、已成功寫入的 JSON/CSV 資料等應實作 **Idempotent 恢復機制** [33, 55]。
    *   每當進程重啟，助理能依據保存的「Job Session ID」自動比對已下載快取與未完成的 URL Queue [32, 55]，跳過已完成的抓取，實現 **「零數據與頻率浪費」的崩潰重啟恢復** [23]。

---

### 總結

Herdr 帶給 Cheerio 助理最大的啟發在於：**不要只把網頁爬蟲當作一個「單次的 LLM 工具（One-off tool）」，而應該將它視為一個具備持久化 Session、狀態自我感知、並且能被其他 AI 進程調用的「網頁數據執行期（Web Data Runtime）」[52, 57]。**

🧭 **您是否想為 Cheerio 助理規劃一個基於此架構的「背景常駐 + 狀態回報」JSON-RPC Socket 的實作雛形？**

**引用來源：**
- [1] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [2] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [3] [[AI SDK AGENTS 官方] 案例研究 - Cheerio 網頁爬蟲與數據擷取助理](https://www.aisdkagents.com/patterns/cheerio-scraper)
- [4] [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [5] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [6] [[Herdr 官方] 產品規格 - 定位為 AI 代理人專屬之終端執行期環境](https://herdr.dev/)
- [7] [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [8] [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [9] [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [10] [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [11] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [12] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [13] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [14] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [15] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [16] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [17] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [18] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [19] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [20] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [21] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [22] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [23] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [24] [[AI SDK AGENTS 官方] 案例研究 - Cheerio 網頁爬蟲與數據擷取助理](https://www.aisdkagents.com/patterns/cheerio-scraper)
- [25] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [26] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [27] [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [28] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [29] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [30] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [31] [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [32] [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [33] [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [34] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [35] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [36] [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [37] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [38] [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [39] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [40] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [41] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [42] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [43] [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [44] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [45] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [46] [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [47] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [48] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [49] [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [50] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [51] [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [52] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [53] [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [54] [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [55] [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [56] [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [57] [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)

## 已移除的來源（品質過濾）

- Best AI Chat Agents & Chatbot Tools for Businesses | RevAvenues — 屬於高層級的工具目錄行銷頁面，僅對多款商業聊天機器人（如 Exei、BusinessOnBot、Kommunicate 等）進行表面的功能宣傳介紹，缺乏具體技術實作細節，符合條件 1（廣告／宣傳／行銷頁面）。
- Build with any npm package with this AI assist - DEV Community — 屬於產品推廣推廣頁面，宣傳名為「CommandDash」的免費 Agent 建立工具，其內容極其膚淺，僅包含「訪問網站、貼上連結、建立、提問」等步驟，沒有任何技術架構、程式碼或技術細節支撐，符合條件 1（無具體實作細節的推廣頁）與條件 2（膚淺心得文）。
- Cheerio AI Agent Sandbox - ScrapeNinja — 屬於 ScrapeNinja 旗下工具的行銷推廣頁面，一味稱讚其「自主迭代與自我驗證」的優勢，用以推銷其商業產品，缺乏實質的技術架構與實作代碼，符合條件 1（廣告／宣傳／行銷頁面）。
- Cheerio AI Raises ₹8 Crore to Build the Future of Agentic AI for Enterprise Workflows — 屬於新創公司的種子輪融資新聞公關稿（PR Release），探討團隊背景、投資人陣容、新創估值與企業願景等商業資訊，完全沒有任何技術實作細節，符合條件 1（行銷／宣傳／公關推廣頁）。
- Cheerio AI | Agentic AI for Enterprise Revenue & Growth — 屬於商業軟體（B2B SaaS）Cheerio AI 的行銷官網首頁，包含大量的產品宣傳、預約 Demo 按鈕、客戶證言與高層級的功能概念簡介，完全不具備任何技術深度或代碼實作細節，符合條件 1（廣告／宣傳／行銷頁面）。
- Cheerio AI | Valuation, Funding Rounds & Stock Price - Caplight — 屬於新創公司股權估值與次級市場交易資訊的商業資料庫檔案頁面（Caplight），目的在於引導用戶登入並訂閱其付費數據服務，並非技術文件或教程，符合條件 1（廣告／宣傳／行銷頁面）。
- Cheerio AI: Funding, Team & Investors - Startup Intros — 屬於新創公司基本背景資訊介紹檔案（Startup Intros），僅列出融資歷史、投資人與簡單商業 FAQ，不具備任何開發者代碼或技術實作細節，符合條件 1（廣告／宣傳／行銷頁面）。
- Cheerio | APIs.io Providers — 屬於 APIs.io 對 Cheerio 進行自動化 API 評分、分類與特徵羅列的目錄檔案，主要用以推廣其付費服務（如 Pro 評級與諮詢），並未包含任何 Cheerio 本身的具體技術實作或開發者代碼，符合條件 1（廣告／宣傳／行銷頁面）。
- How do you create and deploy an AI Assistant on WhatsApp or Instagram? - Cheerio AI — 屬於商業 B2B SaaS 平台 Cheerio AI 的操作引導指南，引導客戶登入其專有帳號、選擇模型並部署客服機器人（包含錢包自動扣款與視覺化後台操作），與開發者 Cheerio HTML 庫完全無關，且不具備技術實作細節，符合條件 1（廣告／宣傳／行銷頁面）。
- ScrapeNinja Web Scraping API: Turns websites into data, on scale. — 屬於商業 Web Scraping API (ScrapeNinja) 的產品功能介紹與訂價訂閱首頁，目的在吸引用戶付費訂閱其代理伺服器與爬蟲服務，無任何具體代碼實作或技術深度，符合條件 1（廣告／宣傳／行銷頁面）。

## 已移除的來源（蒸餾必要性）

- AMV0027/sudoai: local-first, terminal-native AI runtime with agentic capabilities—reason, act, and automate directly from your CLI. - GitHub — 此專案為獨立開發的本地端終端 AI 執行期工具（sudoai），具備自己的 ReAct 引擎與 native 工具箱，與本研究的核心對象 Herdr 及 Pi/Tau 無任何關聯，亦無法解答 Cheerio 助理的架構啟發。
- Add url filtering to Cheerio scraper. Also fix multiple issues of link limit enforcement. #1417 - GitHub — 這是一筆關於 Flowise 專案中 Cheerio 網頁爬蟲節點網址過濾與數量限制的 GitHub PR 修正，屬於特定工具的內部 Bug 修正細節，對本次研究的終端執行期（Herdr）架構或 Cheerio 助理的系統級設計無任何貢獻。
- Build a custom RAG AI agent in TypeScript and Jupyter | Deno — 這是一篇教導如何在 Deno 與 Jupyter 環境下使用 LangChain 及 Cheerio 建立一般本地端 RAG 助理的教學，內容主要圍繞 Ollama 與 Llama3.2 模型調用，與 Herdr 執行期架構（session、plugin、socket）或 Pi/Tau 比較完全無關。
- GitHub - Aashutosh31/arc-ai-project: Realtime multimodal AI agent with voice streaming, RAG memory, and autonomous workflows — 此專案為基於 MERN Stack 的多模態數位助理 ARC-AI，其架構高度與語音、WhatsApp 自動化與 Socket.IO 管道綁定，雖然工具箱中包含 Cheerio 網頁抓取，但與 Herdr 多路複用器、Pi/Tau 架構或本研究的主題無實質關聯。
- GitHub - UnlimitedBytes/searcho: A React web application that uses 5 AI agents to research and vote on the best answer to your questions. — 這是一個 React 前端、Node 骨幹的多 Agent 搜尋 consensus 投票系統（Searcho），僅在 Playwright 抓取資料後使用 Cheerio 輔助，對探討 Herdr 終端多路複用器、Pi/Tau 的定位差異或執行期 socket 串接完全不具備貢獻度。
- Simple extension for SuperAgent that adds cheerio to html responses · GitHub — 這是一個極小型的開源套件（superagent-cheerio），僅用於將 HTML 解析庫 Cheerio 綁定到 HTTP 請求庫 SuperAgent 的 response 物件上，不含任何 AI Agent、Herdr 執行期或 Pi/Tau 的架構設計細節。
- TiborUdvari/cheerio-ai-cheerleader - GitHub — 這是一個結合 MobileNet 與 p5.js 辨識健身動作、鼓勵用戶的可愛 AI 啦啦隊專案。其命名 Cheerio AI 僅為雙關啦啦隊口號，與 HTML 解析程式庫 Cheerio 或 AI 執行期 Herdr 完全無關，屬於同名誤導的離題來源。
- Using scripts in skills - Agent Skills — 此文件為一般的 Agent Skills 腳本呼叫指南（涉及 uvx, pipx, deno run 等工具說明），不包含 Herdr、Pi/Tau 兩者之比較、互補性、終端 session 控制，或 Cheerio 助理設計的實質技術資訊。
- superqode - PyPI — 這是另一個獨立的 Python 終端 Coding Agent Harness 框架（SuperQode），雖然同為終端 Agent 工具，但擁有自己獨立的 HarnessSpec 與 ACP 協定，對回答 Herdr 的架構設計（session state、plugin、socket API）與 Pi/Tau 的定位比較毫無貢獻。
- [NEW] ScrapeNinja official integration with n8n: web scraping API with rotating proxies and real browser - Page 2 — 這是一個 n8n 社群論壇的討論串，聚焦於 n8n PostgreSQL 資料庫連線、ScrapeNinja 網頁抓取與 MoMoProxy 代理設定，即使提及 ScrapeNinja 的 Cheerio AI Agent Sandbox，也與 Herdr 的終端執行期架構與 Cheerio 助理之設計啟發完全離題。

## 參考來源清單

- [[AI SDK AGENTS 官方] 案例研究 - Cheerio 網頁爬蟲與數據擷取助理](https://www.aisdkagents.com/patterns/cheerio-scraper)
- [[Apify 部落格] 技術教學 - 2025 年最新 Cheerio 網頁數據抓取架構與最佳實踐](https://blog.apify.com/web-scraping-with-cheerio/)
- [[Awesome MCP Servers] 產品規格 - Herdr 所支援的 Claude 與 Agent 技能庫整合](https://mcpservers.org/agent-skills/ogulcancelik/herdr)
- [[Better Stack 社群] 技術文章 - 具備 AI 狀態感知能力的終端多路複用器 Herdr](https://betterstack.com/community/guides/ai/herdr-ai-agent/)
- [[Cheerio 官方] 產品規格 - HTML/XML 快速解析與操作庫](https://github.com/cheeriojs)
- [[CoddyKit 媒體] 產品規格 - Rust 驅動的終端 AI Agent 多路複用與管理](https://www.coddykit.com/pages/blog-detail?id=512895&slug=herdr-the-rust-powered-agent-multiplexer-that-runs-all-your-ai-coding-agents-in-)
- [[DEV Community] 技術分析 - 併行 Coding Agent 吞吐量與效能提升論證](https://dev.to/pvgomes/herdr-and-the-throughput-case-for-parallel-coding-agents-4dk0)
- [[Docs.rs 官方] 開發者文件 - Rust 版本 herdr_sdk API 規格手冊](https://docs.rs/herdr-sdk)
- [[Dotzlaw 諮詢] 比較分析 - Herdr 併行會話與 AI Agent 版 Tmux 模式](https://dotzlaw.com/insights/claude-code-13-herdr-parallel-agent-sessions/)
- [[GitHub 專案] 產品規格 - Tau 開箱即用版 Pi Agent 套件整合包](https://github.com/goncalossilva/tau)
- [[GitHub 專案] 開發者文件 - Pi Coding Agent 的測試套件與擴充驗證](https://github.com/badlogic/pi-package-test)
- [[GitHub 專案] 開發者文件 - Pi Coding Agent 與 Claude Code 相容技能庫](https://github.com/badlogic/pi-skills)
- [[Hacker News] 社群討論 - 終端 AI Agent 多路複用器 Herdr 技術反響](https://news.ycombinator.com/item?id=48714802)
- [[Herdr 官方] 架構文件 - 插件系統設計與自訂工具擴充規格](https://herdr.dev/docs/plugins/)
- [[Herdr 官方] 架構文件 - 核心概念與多路複用架構設計](https://herdr.dev/docs/concepts/)
- [[Herdr 官方] 產品規格 - Agent 自動化與工作流編排](https://herdr.dev/docs/agent-automation/)
- [[Herdr 官方] 產品規格 - Windows 系統相容性與環境設定支援](https://herdr.dev/docs/windows-beta/)
- [[Herdr 官方] 產品規格 - 代理人 (Agents) 生命週期與管理](https://herdr.dev/docs/agents/)
- [[Herdr 官方] 產品規格 - 定位為 AI 代理人專屬之終端執行期環境](https://herdr.dev/)
- [[Herdr 官方] 開發者文件 - Socket API 通訊協定與動態監控數據](https://herdr.dev/docs/socket-api/)
- [[Herdr 官方] 開發者文件 - 外部工具、IDE 與 Agent 整合生態系](https://herdr.dev/docs/integrations/)
- [[Herdr 官方] 開發者文件 - 快速上手指南與基礎環境部署](https://herdr.dev/docs/quick-start/)
- [[Herdr 官方] 開發者文件 - 終端命令列 (CLI) 完整指令參考](https://herdr.dev/docs/cli-reference/)
- [[Herdr 部落格] 架構文件 - 程式開發 Agent 的終端執行期趨勢](https://herdr.dev/blog/coding-agents-are-becoming-runtimes/)
- [[Oxylabs 官方] 技術教學 - 使用 Cheerio 與 Node.js 的網頁數據抓取步驟指南](https://oxylabs.io/blog/cheerio-web-scraping)
- [[Pi 官方] 產品規格 - Pi Coding Agent 的終端開發助理功能](https://pi.dev/)
- [[Reddit 社群] 社群問答 - Herdr 與 cmux、tmux 終端多路複用工具實用性對比](https://www.reddit.com/r/ClaudeCode/comments/1v8lk76/herdr_cmux_tmux_are_they_really_that_helpful/)
- [[Reddit 社群] 社群討論 - Pi Coding Agent 的 Telegram 擴充插件實踐](https://www.reddit.com/r/PiCodingAgent/comments/1uibcp0/pitelegram_extension_built_by_badlogic_himself/)
- [[Shareuhack 媒體] 產品規格 - 2026 年在單一終端監控 Claude Code、Codex 與 Devin 實戰](https://www.shareuhack.com/en/posts/herdr-terminal-agent-multiplexer-guide-2026)
- [[Y Combinator 專頁] 產品規格 - Herdr 構建開源 Agent 執行期的商業與技術願景](https://www.ycombinator.com/companies/herdr)
- [[cheeriojs 專案] 開發者文件 - Cheerio 核心功能與開發指引](https://github.com/cheeriojs/cheerio)
- [pi/packages/coding-agent/docs/rpc.md at main · earendil-works/pi - GitHub](https://github.com/badlogic/pi-mono/blob/main/packages/coding-agent/docs/rpc.md)
- [pi/packages/coding-agent/examples/extensions/README.md at main · earendil-works/pi - GitHub](https://github.com/badlogic/pi-mono/blob/main/packages/coding-agent/examples/extensions/README.md)
- [tau-all-agent · Packages · Pi](https://pi.dev/packages/tau-all-agent?type=theme)
- [tau-coding-agent · Packages · Pi](https://pi.dev/packages/tau-coding-agent?page=40)
