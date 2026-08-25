# Deep Research 報告 — rc-20260825-001

- 研究主題：AI 編碼 Agent／IDE 開發工具 Harness 的生命週期擴充機制（可能稱為 Hook、Extension、Lifecycle Event、Plugin 或 Middleware）之盤點與比較研究，以官方文件為主要來源。研究對象限定以下五個工具，且必須逐一分開調查：
1. GitHub Copilot —— 在 Visual Studio Code、Visual Studio 2022、Visual Studio 2026、GitHub Copilot CLI/App 四種介面下的擴充機制（各介面可能不同，需分別調查，不要混為一談）
2. Claude Code（Anthropic 官方的編碼 Agent CLI 工具，不是 Claude 模型本身）
3. OpenAI Codex CLI（ChatGPT 旗下的編碼 Agent 工具，不是舊版 Codex 模型）
4. Pi Coding Agent（pi-mono 專案的編碼 Agent harness；注意這跟 Inflection AI 出品的對話機器人『Pi』是完全不同的兩個產品，必須完全排除後者的所有結果）
5. DeepSeek Harness（官方頁面 https://deepseek.com/harness/en/ 這款開發工具本身；不是 DeepSeek 的大型語言模型內部架構研究，例如 MLA、MoE 等模型設計主題要完全排除）

本研究要嚴格排除機器學習模型層級的『hook』概念（例如 PyTorch/TransformerLens 的 activation hook、vLLM/SGLang 的 logits processor、LangChain/LlamaIndex 的 callback 或 instrumentation 機制），這些屬於完全不同的技術範疇，不在研究範圍內，不要納入來源。

要調查的具體問題：
1. 這五個工具各自有沒有提供「在特定事件時機自動觸發自訂邏輯」的機制？各自的正式名稱是什麼（不一定叫 Hook）？功能意圖是否等同於 Hook？
2. 這五個工具在此機制上的優勢與限制分別是什麼？整理成一張比較總表。
3. 每個工具若要實作這個機制，具體要怎麼寫？各自完整的生命週期事件有哪些（例如啟動前、工具呼叫前後、發生錯誤時、對話/工作階段結束時等），並比較彼此生命週期涵蓋範圍的差異。
4. 這個機制在各工具中是只能透過設定檔做宣告式配置，還是可以用程式碼直接注入/掛接（程式化擴充）？請逐一分類說明。
5. 對企業導入而言，這五個工具的此類機制是否存在跨工具通用的共通標準或抽象層，還是必須各自量身開發，無法像 Agent Skill 標準（例如 Claude 的 Agent Skills）一樣有跨工具通用的版本？
- Notebook：https://notebooklm.google.com/notebook/3940f00b-c38b-40c0-a639-4b1a901f91b7
- 產生時間：2026-08-25T04:56:46.400Z
- 品質過濾：83 → 71 筆（移除 3 筆）
- 蒸餾必要性：71 → 60 筆（移除 11 筆對研究問題無貢獻的來源）
- 來源分類/重新命名：60 / 60 筆成功

## 研究結果

### 1. 這五個 Harness（GitHub Copilot 的 VS Code／Visual Studio 2022／Visual Studio 2026／GitHub Copilot CLI-App、Claude Code、OpenAI Codex CLI、Pi Coding Agent、DeepSeek Harness）各自有沒有提供 Hook 或等效機制？各自的正式名稱是什麼、功能意圖是否等同於 Hook？

這五個 IDE 開發工具與編碼 Agent 平台在 **Hook 或等效生命週期擴充機制**的提供情況、正式名稱與功能意圖，詳細盤點如下：

---

### 1. GitHub Copilot
GitHub Copilot 的 Hook 機制依據不同的**使用者介面與表面（Surfaces）**而有極大的支援度差異，不可混為一談：

*   **VS Code 介面：**
    *   **是否提供：** **是** [1, 2]。
    *   **正式名稱：** **Hooks**（或 **Agent hooks**）[1-3]。
    *   **功能意圖是否等同於 Hook：** **是** [3]。它允許在 VS Code 中透過標準的 Agent 插件架構（Agent Plugins 1.0），在會話生命週期的 8 個關鍵事件點（如 `SessionStart`、`PreToolUse`、`PostToolUse`、`Stop` 等）自動觸發自訂指令 [2, 4]，將手動執行轉化為**事件驅動的自動化** [5]。
*   **Copilot CLI 與 Cloud Agent 介面：**
    *   **是否提供：** **是** [6, 7]。
    *   **正式名稱：** **Hooks**（或 **GitHub Copilot hooks**）[8, 9]。
    *   **功能意圖是否等同於 Hook：** **是** [8]。其設計是在 Agent 的執行流中，於戰略性的時間點（如工作階段啟動/結束、提交提示詞、調用工具等）自動觸發並執行自訂的 shell 指令、提示詞或斜線指令，藉此防範憑證洩漏、進行安全與合規審查、或實作審計日誌 [6, 10]。
*   **Visual Studio 2022 與 Visual Studio 2026 介面：**
    *   **是否提供：** **否（來源未提及）**。
    *   根據目前的官方文件與版本公告，Visual Studio 2022 與 2026 在「Agent 模式」下雖然支援工具呼叫、子代理、Agent Skills 瀏覽與 MCP 整合 [11-13]，但**並未記載任何能讓開發者配置或掛接自訂生命週期 Hook** 的擴充機制。

---

### 2. Claude Code (Anthropic 官方 CLI)
*   **是否提供：** **是** [14, 15]。
*   **正式名稱：** **Hooks**（或 **Claude Code Hooks**）[14, 15]。
*   **功能意圖是否等同於 Hook：** **是** [15]。這是完全等效且功能強大的 Hook 機制，當 Claude Code 達到其生命週期事件點（例如 `SessionStart`、`PreToolUse`、`Stop`、`FileChanged` 等多達 25 個以上的事件）時 [16, 17]，會**自動且確定性地執行**使用者定義之 Shell 指令（type: "command"）、HTTP POST 請求（type: "http"）、MCP 工具調用（type: "mcp_tool"）或 LLM 提示詞（type: "prompt"）[16, 18]。主要用於在關鍵時刻設置安全防護欄（例如攔截並拒絕 `rm -rf`）、檔案保存時自動排版（Linting/Formatting）或在回合結束時自動執行測試 [15, 19-21]。

---

### 3. OpenAI Codex CLI
*   **是否提供：** **是**（在 v0.116.0 企業級功能更新中引進）[22, 23]。
*   **正式名稱：** **Hooks** 或 **Hook System** [23, 24]。
*   **功能意圖是否等同於 Hook：** **是** [24]。其設計意圖是讓企業開發團隊能**攔截、審計或增強**使用者的提示詞與工具調用 [23]。它能在 Codex 進行工具調用時，自動觸發外部的無狀態二進位攔截器（如 `codex-interceptor`）[24]，透過 Unix socket 通訊將事件發送給 Falco 生態下的安全經紀人（如 `prempti`）進行規則比對，以實施即時、硬性的 Allow/Deny 安全決策與合規管控 [24, 25]。

---

### 4. Pi Coding Agent (pi-mono 專案)
*   **是否提供：** **是** [26, 27]。
*   **正式名稱：** 原生機制為 **Event hooks**、**hostcall hooks** 或 **lifecycle hooks** [27-30]；在擴充套件生態中則有相容 Claude Code 格式的 **`pi-hooks`** [31] 以及宣告式 **`pi-yaml-hooks`** [32]。
*   **功能意圖是否等同於 Hook：** **是** [33, 34]。Pi 的原生 JavaScript 擴充程式可直接在內置的 QuickJS 執行期內運行 [28]，使用 `pi.on(...)` API 監聽如工具調用前（`tool_call`）與 LLM 請求上下文（`context`）等生命週期事件 [27, 34, 35]。其功能意圖是讓擴充套件能在工具呼叫前進行安全審查或直接阻斷（例如攔截危險的 `rm -rf`）[33, 34]、在模型請求前動態裁剪或重寫對話上下文 [35]，或在特定時機（如會話啟動、工具完成前後）自動跑 bash 腳本 [36, 37]。

---

### 5. DeepSeek Harness (dsh)
*   **是否提供：** **是** [38, 39]。
*   **正式名稱：** **Cordis events**、**Session events** 或 **Live agent events** [39, 40]（基於底層的 **Cordis kernel** 生命週期事件系統 [38, 41, 42]）。
*   **功能意圖是否等同於 Hook：** **是** [40, 43]。DeepSeek Harness 採用「一切皆插件」的 Cordis 核心 [42]，包含模型適配、工具註冊、會話持久化、沙盒提供者等 major subsystems 全都是以插件形式掛載於共享 context 中 [39, 44]。它在 Agent 的「Turn（回合）」與「Step（步驟）」執行鏈中定義了明確的事件階段（包括 `session/turn/start`、`session/tool/before`、`session/step/end` 等） [40]。其意圖是允許自訂插件監聽並動態攔截這些事件 [39]，更具備「時間可組合性」——在插件被卸載時，系統會自動逆向執行其記錄的 cleanup 邏輯，乾淨撤銷 Hook 引入的所有副作用，不留任何 leftovers [45]。

---

🎯 **單一後續行動建議：**
我們是否要接著深入探討**「這五個 Harness 各自的 Hook 機制之優勢與限制」**，並將其整理成一張清晰的比較總表，以供您的研究報告推進到下一階段？

**引用來源：**
- [1] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [2] [[VS Code 官方] 版本公告 - VS Code 2026年1月更新與 AI 代理支援規格](https://code.visualstudio.com/updates/v1_109)
- [3] [[VS Code 官方] 開發文件 - VS Code 擴充套件中的 Lifecycle Hooks 機制簡介](https://code.visualstudio.com/learn/customizations/5-hooks)
- [4] [[VS Code 官方] 開發文件 - VS Code 擴充套件中的 Lifecycle Hooks 機制簡介](https://code.visualstudio.com/learn/customizations/5-hooks)
- [5] [[VS Code 官方] 開發文件 - VS Code 擴充套件中的 Lifecycle Hooks 機制簡介](https://code.visualstudio.com/learn/customizations/5-hooks)
- [6] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [7] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [8] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [9] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [10] [[GitHub 官方] 指南文件 - 使用 Hooks 確保 Copilot CLI 合規且可預測的執行](https://docs.github.com/en/copilot/tutorials/copilot-cli-hooks)
- [11] [[Microsoft 官方] 開發文件 - 在 Visual Studio 中使用 Copilot 代理模式](https://learn.microsoft.com/en-us/visualstudio/ide/copilot-agent-mode?view=visualstudio)
- [12] [[Microsoft 官方] 版本公告 - Visual Studio 2026 新特性與 AI 代理整合架構](https://learn.microsoft.com/en-us/visualstudio/releases/2026/release-notes)
- [13] [[Microsoft 官方] 版本公告 - Visual Studio 2026 新特性與 AI 代理整合架構](https://learn.microsoft.com/en-us/visualstudio/releases/2026/release-notes)
- [14] [[Anthropic 官方] 開發文件 - Claude Code 擴充機制與 Plugin 設定](https://code.claude.com/docs/en/features-overview)
- [15] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [16] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [17] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [18] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [19] [[Anthropic 官方] 開發文件 - Claude Code 擴充機制與 Plugin 設定](https://code.claude.com/docs/en/features-overview)
- [20] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [21] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [22] [[OpenAI 官方] 版本公告 - Codex CLI v0.116.0 企業級 Hook 機制功能更新](https://www.augmentcode.com/learn/openai-codex-cli-enterprise)
- [23] [[OpenAI 官方] 版本公告 - Codex CLI v0.116.0 企業級 Hook 機制功能更新](https://www.augmentcode.com/learn/openai-codex-cli-enterprise)
- [24] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [25] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [26] [[Reddit 社群] 技術探討 - 如何在 Go 語言代理中實作 Pi 樣式的擴充機制](https://www.reddit.com/r/PiCodingAgent/comments/1vs8yg2/how_would_you_implement_pistyle_extensions_in_a/)
- [27] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [28] [[GitHub 開源專案] 開發文件 - 採用 Rust 實作的高效能 Pi Coding Agent](https://github.com/Dicklesworthstone/pi_agent_rust)
- [29] [[個人部落格] 案例研究 - 為 Pi Coding Agent 實作 OpenTelemetry 可觀測性擴充](https://prokopov.me/posts/pi-otel-telemetry-extension/)
- [30] [[Pi 官方] 產品規格 - pi-codebase-memory-hooks 擴充套件說明](https://pi.dev/packages/pi-codebase-memory-hooks?name=RAG&type=extension&page=2)
- [31] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [32] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [33] [[個人部落格] 架構文件 - 剖析 Pi Agent 底層運行邏輯與擴充插槽](https://edward40.com/p/pi-agent-internal/)
- [34] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [35] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [36] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [37] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [38] [[Composio 部落格] 產品介紹 - DeepSeek Harness 2026 年推薦插件清單](https://composio.dev/content/best-deepseek-harness-plugins)
- [39] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [40] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [41] [[MarkTechPost 媒體] 媒體報導 - DeepSeek 開源 DSH 代理 Harness 開發者預覽版](https://www.marktechpost.com/2026/08/17/deepseek-ai-releases-deepseek-harness-in-developer-preview/)
- [42] [[GitHub 開源專案] 架構文件 - DeepSeek Harness 官方儲存庫與插件架構](https://github.com/deepseek-ai/deepseek-harness)
- [43] [[DeepSeek 官方] 產品規格 - DeepSeek Harness 開發者預覽：全面插件化架構](https://deepseek.com/harness/en/)
- [44] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [45] [[Reddit 社群] 心得討論 - DeepSeek Harness 朝向自我演化 AI 系統的具體實踐](https://www.reddit.com/r/DeepSeek/comments/1vpmdg9/deepseek_harness_a_concrete_step_toward/)

### 2. 這五個 Harness 在此機制上的優勢與限制分別是什麼？請整理成一張比較總表。

這五個 IDE 開發工具與編碼 Agent 平台，在生命週期擴充（Hook）機制上的**優勢與限制比較總表**整理如下：

### 5 大 Harness 生命週期擴充機制比較總表

| Harness 平台 | 機制名稱與核心設計 | 核心優勢 (Core Advantages) | 核心限制 (Core Limitations) |
| :--- | :--- | :--- | :--- |
| **1. GitHub Copilot**<br>（VS Code、Copilot CLI / App） | **Agent Hooks / Lifecycle Hooks**<br>（透過 JSON 設定檔執行自訂 Shell 指令）[1, 2] | 1. **靈活的配置範圍**：支援專案庫級別的 `.github/hooks/*.json` 與個人全域的 `~/.copilot/hooks/*.json` [1]。<br>2. **即時格式化效益**：透過 `postToolUse` 事件能在檔案修改後立即執行排版，使 Agent 在下一步讀取時直接採用格式化後的乾淨代碼 [3]。<br>3. **防死鎖安全鎖（Runaway Guard）**：在連續攔截（block）達到 8 次後，系統會強制結束當前回合，避免 Hook 腳本造成無限迴圈 [4]。<br>4. **豐富的上下文中介**：Hook 能以 JSON 格式接收詳細的 Agent 執行狀態，利於做精準的安全與合規審計 [1]。 | 1. **IDE 支援不均**：**在 Visual Studio 2022 與 2026 等介面下完全不支援自訂 Hooks**，功能被限制為 VS Code 與 CLI 專屬 [5]。<br>2. **同步阻塞與效能瓶頸**：Hook 採同步（Synchronous）阻塞執行，若耗時過長會導致 Agent 卡頓，官方建議執行時間應控制在 5 秒內 [6]。<br>3. **宣告式能力閹割**：僅有透過 SDK 程式化開發的 Hook 才能動態篡改 Prompt（`modifiedPrompt`），一般 JSON 設定檔定義的 Hook 其此欄位會被強制忽略 [7]。 |
| **2. Claude Code** | **Claude Code Hooks**<br>（多處置器的 JSON 配置與 Frontmatter 宣告）[8, 9] | 1. **極細緻的觸發點與異質處理**：覆蓋 25 個以上事件（如 `FileChanged` 會透過 OS 檔案系統監控自動觸發，不論是否為 Agent 所修改）[10-12]。支援執行 Command、HTTP 請求、MCP 工具、LLM 提示詞（Prompt）與 Subagent 等多種處理器 [8]。<br>2. **非同步背景處理**：原生支援 `async: true`，適合讓耗時的測試套件或通知在背景執行而不阻塞對話流 [13, 14]。<br>3. **環境變數持久化**：可利用 `CLAUDE_ENV_FILE` 寫入變數，並使其在後續的 Bash 工具調用中持續生效 [15]。<br>4. **動態掛載與卸載**：能將 Hook 宣告在 Skill 或 Subagent 檔案的前置屬性（Frontmatter）中，隨該組件執行而動態加載並於結束時自動釋放 [9]。 | 1. **容易快速耗盡 Token**：啟用如自動審查（Stop-hook review gate）等自動化循環檢驗時，若發生連續報錯，會使雙 Agent 陷入死循環並快速刷光 API 額度 [16]。<br>2. **非同步 context 的接收延遲**：非同步 Hook 的輸出（additionalContext）必須等到下一個對話回合（Turn）或下一次使用者交互時才會被注入模型 [13, 17]。<br>3. **恢復工作階段時 Context 過期**：在 SessionStart 注入的動態 context（如時間戳記或 Git SHA）在 `--continue` 恢復會話時會被直接重播，導致資訊過期 [18]。 |
| **3. OpenAI Codex CLI** | **Codex Hook System**<br>（對接外部 Falco 安全代理的二進位攔截器）[19, 20] | 1. **專為企業級安全設計**：透過 `PreToolUse` 與 `PermissionRequest` 的攔截器（如 `codex-interceptor`）與 `prempti` 安全經紀人進行 Unix socket 溝通，完美對接企業內部安全合規治理 [19, 20]。<br>2. **預設 Fail-closed 策略**：當通訊溝通發生故障時，系統預設硬性拒絕（Deny）該工具執行，將安全漏洞風險降到最低 [21]。<br>3. **強大的沙盒與 CA 代理支援**：具備健全的 sandboxed 執行期與自訂 CA 憑證，能安全適配嚴密監控的企業防火牆環境 [22]。 | 1. **支援的生命週期極窄**：雖定義了 10 個事件，但在外部安全經紀人實務上**僅支援 `PreToolUse` 與 `PermissionRequest`**。若將其註冊到其他 8 個生命週期事件（如 PostToolUse），會直接組態報錯並強制結束進程 [23]。<br>2. **流失「互動式確認（Ask）」能力**：Codex 缺乏 per-call 的 Hook 使用者確認介面，原本在策略中的 Ask 指令會被無差別硬性轉為 Deny（拒絕）[24, 25]。<br>3. **信任授權未自動化**：即使自動寫入設定檔，Codex 仍強制要求使用者必須手動在終端執行過一次 `/hooks` 信任確認 [25, 26]。<br>4. **仍屬高度實驗性**：目前架構仍在早期開發，且並未內置於主流安裝程式中，需手動註冊與配置 [19]。 |
| **4. Pi Coding Agent** | **Event / Hostcall Hooks**<br>（動態 JS 插件或 YAML Hook 宣告）[27, 28] | 1. **零編譯動態載入**：Hook 擴充為普通 JavaScript/TypeScript，無需經過 npm compile 或 publish，直接由內置的 QuickJS runtime 讀取 [29]。Agent 甚至能「自己寫 Extension 裝給自己用」 [29]。<br>2. **深層 LLM 上下文干預**：擴充工廠能直接呼叫 `pi.on(...)` API，可在模型調用前動態篡改、修改或過濾 LLM 歷史 messages 陣列 [27]。<br>3. **異質生態適配**：可透過生態擴充（如 `pi-hooks`）直接讀取並解析 Claude Code 格式的 JSON Hook 定義，減少企業多套設定維護成本 [30, 31]。<br>4. **知識圖譜增強**：可搭配 `pi-codebase-memory-hooks`，在 `tool_result` 觸發時將 graph-first 檢索上下文與 coverage 警示自動回填給 grep/find 工具結果 [32, 33]。 | 1. **高風險的安全外洩邊界**：若啟用 `pi-yaml-hooks` 且啟用了 `user_bash` 攔截，**使用者手打的每一條命令都會送入 Hook 的 stdin JSON 中**，存在遭惡意第三方 Hook 收集或竊取機密資料的巨大隱患 [34]。<br>2. **功能適配受限與 Fail-open**：當作為 Claude 格式適配層時，`UserPromptSubmit` 無法 rewrite 或 block 提示詞（僅能以追加 context 代替），且 Hook 失敗時會採取 Fail-open（放行）[35, 36]。<br>3. **不支持部分高階 Action**：例如 `command:` action 以及 `runIn: main` 在 YAML 配置中是不被支援且會被拒絕的 [36]。 |
| **5. DeepSeek Harness (dsh)** | **Cordis Events / Session Events**<br>（基於 Cordis 微核心架構的動態插件事件）[37, 38] | 1. **時空可組合性（Spatiotemporal Composability）**：基於 Cordis 框架，當 Hook 插件被卸載（unmount）時，**系統會自動逆向執行其記錄的 cleanup 邏輯**（例如反註冊事件監聽、關閉 socket），確保完全乾淨，不留下 leftovers 造成系統洩漏 [39, 40]。<br>2. **不可變事件流與 Trajectory trace**：將 Turn 與 Step 的思考與工具結果視為不可變事件流 [41]。Trajectory 視圖可追溯每次 Action 的具體 Plugin 來源，並支援會話的 Resume、Fork 與 Replay [41, 42]。<br>3. **跨生態通用適配（OMD 分佈版）**：透過 `oh-my-dsh` 分佈版，能自動發現、聚合並載入專案內已有的 Cursor、Claude、Copilot 的 Skills 與 Hooks 配置 [43]。 | 1. **高度不穩定且無保障**：目前處於 **Developer Preview（開發者預覽）** 階段，官方聲明隨時會迎來破壞性變更（Compatibility-breaking changes），API 規格變動頻繁 [44]。<br>2. **學習曲線與開發難度陡峭**：由於「一切皆插件」，Harness 核心被設計得極為保守與精簡，必須深入理解 Cordis 微核心事件與依賴注入（Inject）才能開發出堪用的 Hook [38, 45]。<br>3. **不成熟的第三方生態與安全風險**：目前社群生態仍非常不成熟，缺乏充足的文檔 [45, 46]。且若啟用 Python SDK 的 `danger-full-access` 權限，則 Hook 具備直接篡改宿主機實體路徑的能力，存在安全風險 [46]。 |

---

📊 **單一後續行動建議：**
既然我們已經深入比較了這五個 Harness 機制的優劣勢，接下來是否需要**「針對企業 IT 部門，撰寫一份安全合規的 Hook 部署建議與基準 YAML/JSON 配置範本」**（例如：如何在 GitHub Copilot 與 Claude Code 的相容模式下撰寫防止金鑰與高危命令洩漏的 Hook 規則）？

**引用來源：**
- [1] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [2] [[VS Code 官方] 開發文件 - VS Code 擴充套件中的 Lifecycle Hooks 機制簡介](https://code.visualstudio.com/learn/customizations/5-hooks)
- [3] [[個人部落格] 案例研究 - 利用 Hooks 實現 GitHub Copilot 代碼格式化自動化](https://bartwullems.blogspot.com/2026/04/github-copilotformat-your-code-using.html)
- [4] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [5] [[Reddit 社群] 比較分析 - 探討 Visual Studio 2026 與 VS Code 的 Copilot 整合差異](https://www.reddit.com/r/GithubCopilot/comments/1s66tzo/vs2026_vs_vscode_integration/)
- [6] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [7] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [8] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [9] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [10] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [11] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [12] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [13] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [14] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [15] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [16] [[Anthropic 官方] 產品規格 - 在 Claude Code 中集成 Codex 插件之對抗性審查](https://www.ai.joaoqueiros.com/blog/codex-plugin-claude-code-adversarial-review-workflow)
- [17] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [18] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [19] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [20] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [21] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [22] [[OpenAI 官方] 版本公告 - Codex CLI v0.116.0 企業級 Hook 機制功能更新](https://www.augmentcode.com/learn/openai-codex-cli-enterprise)
- [23] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [24] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [25] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [26] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [27] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [28] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [29] [[個人部落格] 產品介紹 - Pi Agent：專為掌握底層工具的工程師設計的編碼代理](https://alexander.holbreich.org/posts/2026/pi-coding-agent/)
- [30] [[ChatGPT Learn 網站] 教學指南 - OpenAI Codex CLI 的基礎操作與設定](https://learn.chatgpt.com/docs/codex/cli)
- [31] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [32] [[Pi 官方] 產品規格 - pi-codebase-memory-hooks 擴充套件說明](https://pi.dev/packages/pi-codebase-memory-hooks?name=RAG&type=extension&page=2)
- [33] [[Pi 官方] 產品規格 - pi-codebase-memory-hooks 擴充套件說明](https://pi.dev/packages/pi-codebase-memory-hooks?name=RAG&type=extension&page=2)
- [34] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [35] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [36] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [37] [[Composio 部落格] 產品介紹 - DeepSeek Harness 2026 年推薦插件清單](https://composio.dev/content/best-deepseek-harness-plugins)
- [38] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [39] [[Reddit 社群] 心得討論 - DeepSeek Harness 朝向自我演化 AI 系統的具體實踐](https://www.reddit.com/r/DeepSeek/comments/1vpmdg9/deepseek_harness_a_concrete_step_toward/)
- [40] [[Reddit 社群] 心得討論 - DeepSeek Harness 朝向自我演化 AI 系統的具體實踐](https://www.reddit.com/r/DeepSeek/comments/1vpmdg9/deepseek_harness_a_concrete_step_toward/)
- [41] [[DeepSeek 官方] 產品規格 - DeepSeek Harness 開發者預覽：全面插件化架構](https://deepseek.com/harness/en/)
- [42] [[MindStudio 媒體] 產品介紹 - 詳解 DeepSeek Harness：以插件為核心的編碼代理](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [43] [[GitHub Issue] 架構文件 - oh-my-dsh：用於自我演化治理的 DeepSeek Harness 散佈版提案](https://github.com/deepseek-ai/deepseek-harness/discussions/3113)
- [44] [[GitHub 開源專案] 架構文件 - DeepSeek Harness 官方儲存庫與插件架構](https://github.com/deepseek-ai/deepseek-harness)
- [45] [[DataCamp 網站] 教學指南 - DeepSeek Harness 開源代理環境架設教學](https://www.datacamp.com/tutorial/deepseek-harness)
- [46] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)

### 3. 每個 Harness 若要實作這個機制，具體要怎麼寫？各自完整的生命週期事件有哪些，彼此的生命週期涵蓋範圍有什麼差異？

這五個 Harness 平台在「如何實作生命週期 Hook」、「各自擁有的事件清單」以及「設計哲學上的行為差異」，詳細技術細節與實作指引整理如下：

---

### 一、 各 Harness 的具體實作寫法與範例

#### 1. GitHub Copilot
GitHub Copilot（包括 VS Code 中的 Agent 插件、Copilot CLI 與雲端 Agent）主要使用 **JSON 格式的設定檔**來進行宣告式配置 [1-3]。
*   **配置路徑**：專案級別存放在 `.github/hooks/*.json` [2, 4]；個人全域（僅 CLI 支援）則存放在 `~/.copilot/hooks/*.json` [2, 4]。此外，在 VS Code Agent 插件架構下，則存放在 `com.github.copilot/hooks/hooks.json` [3]。
*   **實作範例（JSON 設定檔，以 `PreToolUse` 事件攔截為例）**：
    ```json
    {
      "version": 1,
      "hooks": {
        "PreToolUse": [
          {
            "type": "command",
            "bash": "./scripts/security-audit.sh",
            "powershell": ".\\scripts\\security-audit.ps1",
            "cwd": ".",
            "timeoutSec": 10
          }
        ]
      }
    }
    ```
    *(註：配置中同時提供 `bash` 與 `powershell` 能確保腳本在 Linux/macOS 與 Windows 跨平台執行 [4-6])*。
*   **輸入與輸出 Payload**：
    *   **輸入 (stdin)**：Copilot 會將詳細的上下文 JSON 傳給腳本 [2, 4, 7]。例如：
        ```json
        {
          "hook_event_name": "PreToolUse",
          "session_id": "session_abc123",
          "cwd": "/Users/user/project",
          "tool_name": "Bash",
          "tool_input": { "command": "rm -rf /tmp" }
        }
        ```
    *   **輸出 (stdout)**：若要阻止執行，腳本需往標準輸出寫入特定 JSON [8]：
        ```json
        {
          "hookSpecificOutput": {
            "hookEventName": "PreToolUse",
            "permissionDecision": "deny",
            "permissionDecisionReason": "安全政策：不允許刪除暫存區。"
          }
        }
        ```
        若執行失敗或 exit code 為 2，在 `PreToolUse` 或 `PermissionRequest` 中會被強制 Fail-Closed（判定為 deny）[9, 10]。

#### 2. Claude Code
Claude Code 支援極具干預力的 Hook 機制，提供 `command`（Shell 指令）、`http`（POST 請求）、`mcp_tool`（MCP 工具調用）、`prompt`（LLM 評估）與 `agent`（子代理驗證）共 **5 種處理器類型** [11, 12]。
*   **配置路徑**：專案級 `.claude/settings.json` [13, 14]、全域 `~/.claude/settings.json` [13, 14]，或技能（Skill）與子代理（Subagent）的 YAML 檔案前置屬性（Frontmatter）中 [14, 15]。
*   **實作範例（以 `PreToolUse` 搭配特定 Matcher 攔截為例）** [16, 17]：
    ```json
    {
      "hooks": {
        "PreToolUse": [
          {
            "matcher": "Bash",
            "hooks": [
              {
                "type": "command",
                "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.sh",
                "if": "Bash(rm *)"
              }
            ]
          }
        ]
      }
    }
    ```
    *（透過 `if` 條件可以先行篩選命令，避免不必要的進程衍生開銷 [18]；`${CLAUDE_PROJECT_DIR}` 則用於在跨路徑時解析 absolute path [19, 20]）*。
*   **異步 Hook 寫法**（適合背景不阻塞執行，例如存檔後自動格式化或跑測試）[21]：
    ```json
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          { "type": "command", "command": "./run-tests.sh", "async": true, "timeout": 300 }
        ]
      }
    ]
    ```
*   **決策與輸出**：比照標準 Unix，`exit 2` 代表 Hard Block（硬阻斷）[22]；若回傳 JSON，在 `PreToolUse` 中可透過 `permissionDecision: "deny" | "allow" | "ask" | "defer"` 進行微調 [23, 24]。若回傳 `continue: false` 則代表中止整個 Agent 的當前回合流程 [25, 26]。

#### 3. OpenAI Codex CLI
OpenAI Codex CLI 主要針對**企業安全合規審計**設計，採用了**無狀態外部二進位攔截器（Interceptor）模式** [27, 28]。
*   **配置路徑**：透過寫入 `~/.codex/hooks.json` 檔案來進行註冊 [29]。
*   **實作設定檔範例** [29]：
    ```json
    {
      "hooks": {
        "PreToolUse": [
          {
            "matcher": ".*",
            "hooks": [
              {
                "type": "command",
                "command": "/usr/local/bin/codex-interceptor",
                "timeout": 30
              }
            ]
          }
        ]
      }
    }
    ```
*   **運作機制（與 `prempti` 經紀人協作）**：
    1.  當 Codex CLI 觸發事件時，會以 **snake_case** JSON 將上下文透過 `stdin` 餵給 `codex-interceptor` 二進位檔 [28, 30]。
    2.  攔截器透過 Unix socket 通訊將 Payload 傳送給 Falco 生態的 `prempti` 插件 broker 進行集中安全規則檢驗 [28, 31]。
    3.  攔截器接收審計結果後，轉換為 **camelCase** JSON 寫回 `stdout` 供 Codex CLI 讀取 [29, 30]。
    4.  **Fail-Closed 設計**：若通訊中斷，攔截器會以 `exit 2` 結束，Codex 預設會阻斷命令 [32]。

#### 4. Pi Coding Agent
Pi Coding Agent 支援**零編譯的 JavaScript 程式化擴充**，或透過擴充生態 `pi-yaml-hooks` 來宣告 YAML 設定 [33, 34]。
*   **寫法 A：原生 JS/TS 擴充（在 embedded QuickJS 虛擬機中運行）** [33, 35]：
    ```typescript
    import type { HookAPI } from "@oh-my-pi/pi-coding-agent/extensibility/hooks";

    export default function hook(pi: HookAPI): void {
      // 監聽並攔截工具調用前 (tool_call)
      pi.on("tool_call", async (event, ctx) => {
        if (event.toolName === "bash" && String(event.input.command).includes("sudo")) {
          return { block: true, reason: "Blocked: sudo is not allowed in sandbox." };
        }
      });
      
      // 在模型調用前 (context) 動態修剪/修改 LLM 歷史 messages 陣列
      pi.on("context", async (event) => {
        const filtered = event.messages.filter(msg => msg.customType !== "debug-only");
        return { messages: filtered };
      });
    }
    ```
*   **寫法 B：`pi-yaml-hooks` 宣告檔**（存放在 `.pi/hook/hooks.yaml`）[36, 37]：
    ```yaml
    hooks:
      - event: tool.before.bash
        actions:
          - confirm: "您確定要讓 Pi 執行此 Bash 命令嗎？"
          - bash: "echo 'Executed' >> audit.log"
    ```
    *(註：`pi-yaml-hooks` 的專案 Hook 必須先在 terminal 執行 `/hooks-trust` 授權後才會被載入，以維護宿主機安全 [38, 39])*。

#### 5. DeepSeek Harness (dsh)
DeepSeek Harness 基於 **Cordis 微核心框架** 設計，核心哲學是「一切皆插件」 [40-42]。其實作是直接**編寫 TypeScript Cordis 插件**並將事件掛接至共享 context 中 [42-44]。
*   **實作範例（TS 插件寫法）** [43, 44]：
    ```typescript
    import type { Context } from "@deepseek-ai/cordis";

    export const name = "corporate-safety-gate";
    export const inject = ["tools", "sessions"]; // 依賴注入其他服務

    export function apply(ctx: Context) {
      // 監聽工具執行前事件
      ctx.on("session/tool/before", async (toolCall, session) => {
        if (toolCall.name === "bash" && toolCall.args.command.includes("rm -rf")) {
          throw new Error("DSH Policy: Dangerous command rejected!");
        }
      });
    }
    ```
*   **啟用插件 (`cordis.patch.yml`)** [45, 46]：
    ```yaml
    plugins:
      corporate-safety-gate: mounted
    ```

---

### 二、 完整生命週期事件對照

下表列出這五個 Harness 所各自支援的生命週期事件對照：

| 生命週期階段 | GitHub Copilot [47] | Claude Code [48-51] | OpenAI Codex CLI [52] | Pi Coding Agent [53, 54] | DeepSeek Harness [55] |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **工作階段啟動/初始化** | `sessionStart` | `SessionStart`, `Setup` | `SessionStart` | `session_start` (TS)<br>`session.created` (YAML) | `session/turn/start` |
| **使用者提交提示詞** | `userPromptSubmitted` | `UserPromptSubmit`, `UserPromptExpansion` | `UserPromptSubmit` | `user.prompt.submit` (YAML) | （事件流自動包含 `session/message/user`） |
| **工具執行前（攔截點）** | `preToolUse` | `PreToolUse`, `PermissionRequest` | `PreToolUse`, `PermissionRequest` | `tool_call` (TS)<br>`tool.before.*` (YAML) | `session/tool/before` |
| **工具執行成功後** | `postToolUse` | `PostToolUse`, `PostToolBatch` | `PostToolUse` | `tool_result` (TS)<br>`tool.after.*` (YAML) | `session/tool/result` |
| **工具執行失敗時** | `postToolUseFailure` | `PostToolUseFailure` | （未實務處理） | `tool_result(isError=true)` | `session/tool/result(isError)` |
| **對話上下文即將壓縮** | `preCompact` | `PreCompact` | `PreCompact` | `session_before_compact` | （由 `dsh-storage` 依據 threshold 自動排程） |
| **對話上下文壓縮完成** | — | `PostCompact` | `PostCompact` | `session_compact` | — |
| **子代理（Subagent）狀態** | `subagentStop` (僅 Stop) | `SubagentStart`, `SubagentStop` | `SubagentStart`, `SubagentStop` | `before_agent_start` | （由子代理調用插件處理） |
| **工作階段/回合正常結束** | `agentStop` | `Stop` | `Stop` | `session_shutdown` (TS)<br>`session.idle` (YAML) | `session/turn/end` |
| **回合失敗/環境異動事件** | `errorOccurred` | `StopFailure`, `FileChanged`, `CwdChanged`, `ConfigChange` | — | `file.changed` (YAML) | `session/permission/change` |

---

### 三、 生命週期涵蓋範圍與行為的核心差異

這五個工具的 Hook 機制在涵蓋範圍與底層設計行為上，存在以下幾點核心差異：

1.  **事件廣度與粒度（Claude Code 壓倒性勝出）**
    *   **Claude Code** 提供超過 25 個細緻的事件（包括作業系統級別的檔案異動監控 `FileChanged`、MCP 交互對話、Git Worktree 的建立與移除等）[11, 48-51, 56]。
    *   **OpenAI Codex CLI** 在底層雖然宣稱有 10 個事件，但在實務上**僅支援 `PreToolUse` 與 `PermissionRequest`**。若將其攔截器註冊到其他 8 個事件（如 PostToolUse），將直接導致組態錯誤並以 `exit 2` 結束 [52]。
2.  **上下文干預與修改深度（Pi 與 DeepSeek 核心最深）**
    *   **Pi Coding Agent** 原生 TS 擴充具備極強的干預能力，能在 LLM 請求調用前，攔截並**直接重寫、篡改歷史 `messages` 陣列** [57, 58]，亦支援對 `tool_result` 內容進行 direct patch（結果修剪）[57, 59]。
    *   **DeepSeek Harness (dsh)** 具備底層 Cordis 的「時空可組合性」特徵 [60]。當 Hook 插件被卸載（unmount）時，**系統會自動逆向執行其裝載時記錄的 cleanup 邏輯（反向撤銷副作用）**，不留任何 leftovers 造成系統洩漏 [60]；其 Trajectory 視圖更能追溯每個 Turn 與 Step 的完整事件流，並支援 Fork 與 Replay [40, 55, 61, 62]。
    *   **GitHub Copilot** 與 **Claude Code** 雖然也能透過 `PreToolUse` 的 `updatedInput` 修改工具輸入參數 [8, 63]，但對模型上下文主要僅能透過 `additionalContext` 進行「額外提示詞追加」 [64, 65]，干預歷史紀錄的能力相對受到安全隔離。
3.  **異步（Async）背景執行與非阻塞（Non-blocking）能力**
    *   **Claude Code** 原生支援 `async: true` 屬性 [21]。異步 Hook 在背景默默執行（如跑大型 linter 或測試），並在下一個對話回合或下一次使用者交互時，自動將輸出結果追加至 context 中 [66, 67]。
    *   **GitHub Copilot** 則為**同步（Synchronous）阻塞機制**，且官方強調 Hook 腳本應控制在 5 秒內完成，以免造成 IDE 介面卡頓或超時（超時會 fail-open 放行，但執行中會造成阻塞） [9, 68]。
4.  **Fail-Safe 與 Fail-Closed 的安全防護設計**
    *   **OpenAI Codex** 安全 Interceptor 在通訊異常或通訊協定損毀時，預設採取 **Fail-Closed**，一律 deny 命令，安全層級最高 [32]。
    *   **GitHub Copilot** 與 **Claude Code** 針對 Command 本身出錯採取 Fail-Closed [9, 22]；但若是 HTTP 類型的 Hook 發生 timeout 或網路異常，則會採取 **Fail-Open**（落入預設的權限許可流，不因 hook 斷線而鎖死），以便在雲端/邊緣混合環境中維持開發可用性 [9]。

⚙️ **單一後續行動建議：**
我們已完成 Hook 機制實作、事件清單與核心行為差異的完整盤點。接下來，是否需要**「生成一份符合 Agent Plugins 1.0 標準、能在 VS Code（GitHub Copilot）與 Claude Code 雙向相容運作的合規 Hook 配置 JSON 範本（例如在 PreToolUse 階段自動掃描 Git 敏感金鑰）」**，以作為您最終報告的實用附錄？

**引用來源：**
- [1] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [2] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [3] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [4] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [5] [[GitHub 官方] 指南文件 - 使用 Hooks 確保 Copilot CLI 合規且可預測的執行](https://docs.github.com/en/copilot/tutorials/copilot-cli-hooks)
- [6] [[GitHub 官方] 開發文件 - 在 GitHub Copilot CLI 中整合自訂 Hooks 的最佳實踐](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks)
- [7] [[個人部落格] 案例研究 - 實作 GitHub Copilot CLI 的 WakaTime 整合擴充](https://dev.to/geeknees/building-a-github-copilot-cli-extension-implementing-wakatime-integration-5c0a)
- [8] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [9] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [10] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [11] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [12] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [13] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [14] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [15] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [16] [[Anthropic 官方] 開發文件 - 建立 Claude Code 插件之開發指南](https://code.claude.com/docs/en/plugins)
- [17] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [18] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [19] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [20] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [21] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [22] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [23] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [24] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [25] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [26] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [27] [[OpenAI 官方] 版本公告 - Codex CLI v0.116.0 企業級 Hook 機制功能更新](https://www.augmentcode.com/learn/openai-codex-cli-enterprise)
- [28] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [29] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [30] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [31] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [32] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [33] [[GitHub 開源專案] 開發文件 - 採用 Rust 實作的高效能 Pi Coding Agent](https://github.com/Dicklesworthstone/pi_agent_rust)
- [34] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [35] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [36] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [37] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [38] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [39] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [40] [[Composio 部落格] 產品介紹 - DeepSeek Harness 2026 年推薦插件清單](https://composio.dev/content/best-deepseek-harness-plugins)
- [41] [[InfoQ 媒體] 媒體報導 - DeepSeek Harness 開源推動模組化 AI 代理基礎設施發展](https://www.infoq.com/news/2026/08/deep-seek-harness/)
- [42] [[GitHub 開源專案] 教學指南 - DeepSeek Harness 的 Cordis 核心擴充開發教學](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/cordis-tutorial/index.md)
- [43] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [44] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [45] [[GitHub Issue] 架構文件 - oh-my-dsh：用於自我演化治理的 DeepSeek Harness 散佈版提案](https://github.com/deepseek-ai/deepseek-harness/discussions/3113)
- [46] [[MarkTechPost 媒體] 媒體報導 - DeepSeek 開源 DSH 代理 Harness 開發者預覽版](https://www.marktechpost.com/2026/08/17/deepseek-ai-releases-deepseek-harness-in-developer-preview/)
- [47] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [48] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [49] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [50] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [51] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [52] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [53] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [54] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [55] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [56] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [57] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [58] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [59] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [60] [[Reddit 社群] 心得討論 - DeepSeek Harness 朝向自我演化 AI 系統的具體實踐](https://www.reddit.com/r/DeepSeek/comments/1vpmdg9/deepseek_harness_a_concrete_step_toward/)
- [61] [[DeepSeek 官方] 產品規格 - DeepSeek Harness 開發者預覽：全面插件化架構](https://deepseek.com/harness/en/)
- [62] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [63] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [64] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [65] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [66] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [67] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [68] [[個人部落格] 案例研究 - 利用 Hooks 實現 GitHub Copilot 代碼格式化自動化](https://bartwullems.blogspot.com/2026/04/github-copilotformat-your-code-using.html)

### 4. 這個機制在各 Harness 中是只能透過設定檔配置，還是可以用程式碼注入/掛接？請逐一分類。

這五個 Harness 平台在生命週期擴充（Hook）機制的配置與掛接方式上，可劃分為**「純設定檔宣告式配置」**與支援**「設定檔與程式化雙軌掛接」**兩大類。

以下逐一進行分類與詳細實作說明：

---

### 類別一：純設定檔宣告式配置（Strictly Declarative Configuration）

#### 3. OpenAI Codex CLI
*   **分類：純設定檔宣告配置** [1, 2]
*   **機制說明：**
    *   **Codex CLI 的 Hook 系統完全依賴設定檔**。團隊必須在全域 `~/.codex/hooks.json` 檔案（或在 `~/.codex/config.toml` 中寫入行內 `[hooks]` 區塊）中進行宣告 [1, 2]。
    *   Codex 自身在運行時並不提供可直接調用的程式化掛接 API 或 SDK。雖然它在執行事件時，會調用一個用 Rust 程式碼編寫並編譯好的外部二進位攔截器（如 `codex-interceptor`）[1, 3]，但該攔截器如何與 Codex 連接，依然得在上述 JSON/TOML 設定檔中宣告該程式的路徑與 `matcher` 規則，在事件觸發時以獨立的外部進程（Spawn）形式透過 `stdin` 餵入 JSON 上下文 [1, 3, 4]。

---

### 類別二：設定檔與程式化雙軌掛接（Declarative Config & Programmatic Injection）

#### 1. GitHub Copilot (VS Code / Copilot CLI / Cloud Agent)
*   **分類：雙軌支援**（一般專案採 JSON 設定檔，整合開發則支援 SDK 程式化 Hook） [5, 6]
*   **機制說明：**
    *   **設定檔配置**：在專案庫層級定義 `.github/hooks/*.json` 檔，或在個人全域 home 目錄設定 `~/.copilot/hooks/*.json` [5]。設定檔中以宣告式 JSON 欄位指定事件名稱（如 `preToolUse`、`postToolUse`），並綁定特定的 Shell 執行指令（`"type": "command"`）或 HTTP POST 請求（`"type": "http"`）[5, 7, 8]。
    *   **程式化掛接**：在更底層的 Copilot 整合架構中，支援 **SDK 程式化 Hook（SDK programmatic hooks）** [6]。此類程式化 Hook 擁有更深度的執行期干預能力，例如：在 `userPromptSubmitted` 階段，只有 SDK 程式化 Hook 被允許動態篡改或重寫模型提示詞的內容（透過 `modifiedPrompt` 欄位）[6, 9]；如果只是用一般 JSON 設定檔配置的 Command/HTTP 類型 Hook，該 `modifiedPrompt` 欄位在運行時會被系統安全機制強制忽視並丟棄 [9]。

#### 2. Claude Code
*   **分類：雙軌支援**（支援 settings.json 配置，亦可透過 Skill/Subagent 模組動態注入記憶體） [10, 11]
*   **機制說明：**
    *   **設定檔配置**：支援在個人、專案或 Local 層級的 `settings.json`（例如 `.claude/settings.json`），或插件 manifest 結構的 `hooks/hooks.json` 中進行 JSON 宣告 [10]。
    *   **程式化注入**：Claude Code 支援將 Hook **直接定義在自訂技能（Skill）或子代理（Subagent）的 Markdown/YAML 前置屬性（Frontmatter）中** [10-12]。當使用者或主代理在對話中調用了該 Skill，或者是該 Subagent 被喚起運行時，這些 Frontmatter 內宣告的生命週期 Hook 就會被**動態掛接至當前工作階段的記憶體中（Session Hooks in memory）** [10, 11]。此外，Claude 還原生支援基於 LLM 推理判定決策的 `type: "prompt"`（提示詞 Hook）與 `type: "agent"`（子代理自動化驗證 Hook）[13-15]。

#### 4. Pi Coding Agent
*   **分類：雙軌支援**（原生設計為純 JavaScript 程式碼掛接，輔以社群設定檔適配） [16-18]
*   **機制說明：**
    *   **程式化掛接（原生）**：Pi 具備極強的程式化擴充基因，其 Hook 擴充本質上就是普通的 JavaScript/TypeScript 檔案，直接在內置的 QuickJS 虛擬機中動態加載 [16, 19]。擴充透過匯出一個 entrypoint 工廠函數接收 `pi`（HookAPI）對象，直接以程式碼呼叫 `pi.on("tool_call", async (event, ctx) => { ... })` 或 `pi.on("context", ...)` 來動態掛接與監聽事件，甚至能直接用 JS/TS 程式碼重寫 LLM 歷史訊息陣列 [18, 20, 21]。
    *   **設定檔配置**：透過安裝社群生態套件如 **`pi-hooks`** 或 **`pi-yaml-hooks`**，Pi 亦能讀取相容於 Claude 格式的 `settings.json` 或宣告式 `hooks.yaml` 設定檔 [17, 22, 23]。這些適配套件在加載後，會讀取並解析設定檔，並在底層自動調用原生的 `pi.on(...)` API 將這些事件規則動態轉換並註冊到 Pi 的執行期事件總線上 [17, 24, 25]。

#### 5. DeepSeek Harness (dsh)
*   **分類：雙軌支援**（採用 TypeScript 編寫核心 Hook，透過 YAML 設定檔控制裝載與依賴） [26-28]
*   **機制說明：**
    *   **程式化掛接**：基於底層 **Cordis 微核心框架**，DeepSeek Harness 實踐了「一切皆插件」的設計 [29-31]。其 Hook 邏輯需要**直接用 TypeScript 寫成一個 Cordis 插件** [27, 32]。開發者在插件的 `apply(ctx)` 函數中，直接呼叫 `ctx.on("session/tool/before", async (toolCall, session) => { ... })` 程式碼來掛接與攔截生命週期事件 [27, 32, 33]。
    *   **設定檔配置**：這些自訂的 TypeScript 插件要如何在 Harness 啟動時被發現、加載與排序，則是完全透過 YAML 設定檔（如專案或全域的 `cordis.patch.yml` 或是 `cordis.yml` 檔）進行宣告式的啟用、停用與參數覆寫（例如配置 `plugins: my-plugin: mounted`）[26, 28, 34]。這使得企業團隊既能在開發期利用 TS 程式碼撰寫極具深度的干預邏輯，又能在部署期以 YAML 宣告方式自由拼裝與調整。

---

🧩 **單一後續行動建議：**
我們已經釐清了這五個工具在配置與注入方式上的技術分類。針對企業導入，您是否需要我**「提供一份標準化、雙向相容的 Hook 配置範本（例如在 PreToolUse 階段自動過濾與阻斷危險提權命令，且同時支援 VS Code/Copilot 與 Claude Code 的解析）」**，以利於您充實比較研究報告的實作章節？

**引用來源：**
- [1] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [2] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [3] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [4] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [5] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [6] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [7] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [8] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [9] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [10] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [11] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [12] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [13] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [14] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [15] [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [16] [[GitHub 開源專案] 開發文件 - 採用 Rust 實作的高效能 Pi Coding Agent](https://github.com/Dicklesworthstone/pi_agent_rust)
- [17] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [18] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [19] [[GitHub 開源專案] 開發文件 - 採用 Rust 實作的高效能 Pi Coding Agent](https://github.com/Dicklesworthstone/pi_agent_rust)
- [20] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [21] [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [22] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [23] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [24] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [25] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [26] [[GitHub Issue] 架構文件 - oh-my-dsh：用於自我演化治理的 DeepSeek Harness 散佈版提案](https://github.com/deepseek-ai/deepseek-harness/discussions/3113)
- [27] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [28] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [29] [[MarkTechPost 媒體] 媒體報導 - DeepSeek 開源 DSH 代理 Harness 開發者預覽版](https://www.marktechpost.com/2026/08/17/deepseek-ai-releases-deepseek-harness-in-developer-preview/)
- [30] [[GitHub 開源專案] 架構文件 - DeepSeek Harness 官方儲存庫與插件架構](https://github.com/deepseek-ai/deepseek-harness)
- [31] [[Tricontinental 機構] 指南文件 - 在學術與社會研究中導入 DeepSeek Harness 流程](https://thetricontinental.org/how-to-use-deepseek-harness/)
- [32] [[GitHub 開源專案] 教學指南 - DeepSeek Harness 的 Cordis 核心擴充開發教學](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/cordis-tutorial/index.md)
- [33] [[GitHub 開源專案] 教學指南 - DeepSeek Harness 的 Cordis 核心擴充開發教學](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/cordis-tutorial/index.md)
- [34] [[MarkTechPost 媒體] 媒體報導 - DeepSeek 開源 DSH 代理 Harness 開發者預覽版](https://www.marktechpost.com/2026/08/17/deepseek-ai-releases-deepseek-harness-in-developer-preview/)

### 5. 對企業來說，這五個 Harness 的此類機制是否存在跨工具通用的共通標準，還是必須各自量身開發，不像 Agent Skill 有跨工具通用版本？

對企業在導入與建置 AI 編碼 Agent 生態系時，這五個 Harness 的生命週期擴充機制（Hook）在**跨工具通用標準**上的現況與技術限制，詳細分析如下：

### 核心結論：Hook 在標準中被定義為「非通用（Client-specific）」的專屬組件
在當前推動的 **Agent Plugins 1.0（Open Plugin Spec）** 開放標準中，業界雖然建立了跨平台的 AI 代理客製化規範，但 **Hook 機制在標準中被明確歸類為「非通用（Client-specific）」且「不可跨工具移植（Non-portable）」的專屬組件 [1-3]**。這與具備跨平台通用標準的 **Agent Skills** 或 **MCP（Model Context Protocol）伺服器配置**有著本質上的不同 [1, 2, 4]。

在 **Agent Plugins 1.0** 規格下：
*   **可移植的通用標準組件（Portable）**：**Agent Skills**（存放在 `skills/` 目錄並以 `SKILL.md` 宣告）與 **MCP 伺服器配置**（定義於根目錄的 `mcp.json` 中）是跨平台完全通用的標準組件 [2, 4, 5]。任何相容該規格的 AI 用戶端（如 VS Code、Copilot CLI 與 Claude Code）都能直接讀取並加載 [4, 6, 7]。
*   **不可移植的專屬組件（Client-specific）**：**Hooks**、自訂 Agent 角色與斜線指令（Slash Commands）屬於特定用戶端專屬的能力 [2]。
*   **命名空間隔離機制**：在標準的插件套件目錄中，這些專屬組件被要求存放在反向網域（reverse-domain）的私有目錄下。例如，VS Code 與 GitHub Copilot 的自訂 Hook 會存放在 `com.github.copilot/hooks/hooks.json`，而其他不支援此命名空間的 AI 工具（例如 Claude Code 或 Codex）在載入該插件時會自動將其忽略 [1, 5, 8]。

---

### 實務上的「事實上相容（De Facto Compatibility）」發展
儘管在官方標準中 Hook 被歸類為專屬能力，但為了解決企業重複開發與維護多套組態的痛點，目前各大 Harness 平台在實務上正朝著 **以 Claude Code 格式為基礎** 的跨平台適配發展：

1.  **VS Code / GitHub Copilot 的原生雙向相容**：
    自 VS Code 的 2026 年初更新起，VS Code 的 Agent 擴充架構已原生支援**讀取並解析 Claude Code 格式的 Hook 宣告**（如 `hooks/hooks.json`）[9-11]。它不僅能識別對應的事件（如 `PreToolUse`、`Stop`），還能解析 Claude 的 `matcher` 篩選欄位語法 [12, 13]，讓企業團隊在 VS Code 與 Claude Code 之間共享同一個 `hooks.json` 設定檔 [14]。
2.  **Pi Coding Agent 的生態適配橋樑**：
    Pi Agent 社群開發了專屬擴充 **`pi-hooks`**，主要功能就是將 **Claude Code 的 Hook 格式與 Payload 直接翻譯與適配至 Pi 內部的 TypeScript 事件監聽器中**（例如將 Claude 的 `SessionStart` 映射為 Pi 的 `session_start`，將 `Stop` 映射為 `agent_end`） [15, 16]。此外，社群套件 **`pi-yaml-hooks`**（移植自 `OpenCode-Hooks`）亦提供統一的 YAML 規則引擎，能同時在 Pi 與 OMP (Oh My Pi) 雙平台上執行工具阻斷與 Bash 動作觸發 [17-19]。
3.  **DeepSeek Harness 的散佈版聚合（oh-my-dsh）**：
    DeepSeek Harness 核心採用高度微型化的 Cordis 微核心，要求以 TypeScript 編寫 Cordis 插件來完成事件掛接 [20-22]。然而，在開源散佈版 **`oh-my-dsh` (OMD)** 中，系統加入了自動發現（Auto-discovery）層，能在當前工作空間中自動偵測並複用來自 Claude、Codex、Cursor、Gemini 與 Copilot 既有的 hooks 與 skills 定義 [23]，降低在多工具混合環境下的政策治理成本。
4.  **OpenAI Codex CLI 的安全孤島**：
    Codex 在企業安全 Hook 系統上則採取完全不同的實作。它使用專屬的 TOML 配置文件（如 `.codex/config.toml`）[24]，且其 Hook（目前僅有效支援 `PreToolUse` 與 `PermissionRequest`）必須透過 Unix socket 與外部的二進位攔截器（如 `codex-interceptor` 配合 Falco 的 `prempti` 政策經紀人）進行通訊與阻斷決策 [25-27]。這套為高安全局域網設計的 Fail-Closed 機制，無法直接相容或解析 Claude 或 Copilot 的 JSON 指令 Hook [27]。

---

### 💡 企業導入的架構治理建議
由於這五個 Harness 平台的生命週期事件與回傳決策格式（如 block/allow 訊號與 stdin/stdout JSON Payload）在底層仍有不可抹滅的細微差異，企業在推動跨工具的安全與合規稽核時，建議採用以下 **「去 Harness 化（Decoupling）」** 的解耦架構設計：

1.  **統一配置層**：在跨平台專案庫中，以 **Claude Code / VS Code 雙相容的 JSON Schema** 作為 Hook 配置的主要宣告標準 [10, 11]。這樣一來，團隊在 VS Code (Copilot)、Claude Code 以及 Pi Agent (透過 `pi-hooks`) 中均能無縫讀取與重用這套宣告 [10, 11, 15]。
2.  **腳本與行為抽離**：**不要將核心的安全檢查或合規邏輯直接綁定在 Harness 特有的功能中**（例如不要過度依賴 Claude 特有的 `type: "prompt"` 或 `type: "agent"` 高階 Hook，因為這在 Copilot 與 Pi 中無法被正確執行 [16]）。
3.  **單一外部腳本調用（Single External Executable）**：將真正的業務檢驗（例如 Git 機密金鑰防洩漏掃描、Shell 安全指令黑名單稽核、Prometheus 指標收集）實作在獨立、無狀態的外部 CLI 腳本（如 `.sh` 或 `.ps1` 執行檔）中 [28-30]。而各平台的 Hook 設定檔（JSON/YAML）僅當作一個單純的「啟動開關（Trigger Entry Point）」，負責在工具執行前後（如 `PreToolUse`/`PostToolUse`）調用該外部腳本 [29, 31]。此設計能確保當企業未來更換或擴充 AI 編碼工具時，只需更新極簡的 JSON 觸發入口，而不需要重構底層的企業安全防禦與審計引擎。

📊 **後續行動建議：**
既然我們已經釐清了這五個工具在配置通用性與技術規格上的差異，您是否需要我**「提供一份標準化、雙向相容的 Hook 配置 JSON 範本（例如在 PreToolUse 階段自動過濾與阻斷危險提權命令，且同時支援 VS Code/Copilot 與 Claude Code 的解析）」**，以利於您充實比較研究報告的實作章節？

**引用來源：**
- [1] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [2] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [3] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [4] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [5] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [6] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [7] [[GitHub Issue] 技術探討 - 支援 JetBrains IDE 中 Copilot CLI 插件市場格式之提案](https://github.com/microsoft/copilot-intellij-feedback/issues/1539)
- [8] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [9] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [10] [[VS Code 官方] 版本公告 - VS Code 2026年1月更新與 AI 代理支援規格](https://code.visualstudio.com/updates/v1_109)
- [11] [[VS Code 官方] 版本公告 - VS Code 2026年1月更新與 AI 代理支援規格](https://code.visualstudio.com/updates/v1_109)
- [12] [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [13] [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [14] [[VS Code 官方] 版本公告 - VS Code 2026年1月更新與 AI 代理支援規格](https://code.visualstudio.com/updates/v1_109)
- [15] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [16] [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [17] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [18] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [19] [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [20] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [21] [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [22] [[GitHub 開源專案] 教學指南 - DeepSeek Harness 的 Cordis 核心擴充開發教學](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/cordis-tutorial/index.md)
- [23] [[GitHub Issue] 架構文件 - oh-my-dsh：用於自我演化治理的 DeepSeek Harness 散佈版提案](https://github.com/deepseek-ai/deepseek-harness/discussions/3113)
- [24] [[Adobe 開源官方] 指南文件 - 基於 OpenAI Codex 的 AI 優先開發準則](https://opensource.adobe.com/mysticat-ai-native-guidelines/04-configuration/ai-tools/codex/)
- [25] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [26] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [27] [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [28] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [29] [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [30] [[個人部落格] 案例研究 - 為 Pi Coding Agent 實作 OpenTelemetry 可觀測性擴充](https://prokopov.me/posts/pi-otel-telemetry-extension/)
- [31] [[個人部落格] 案例研究 - 利用 Hooks 實現 GitHub Copilot 代碼格式化自動化](https://bartwullems.blogspot.com/2026/04/github-copilotformat-your-code-using.html)

## 已移除的來源（品質過濾）

- API Platform - DeepSeek — 這是 DeepSeek API 平台的官方定價、銷售與推廣首頁，僅包含模型 API 價格和一般性產品連結，缺乏關於 DeepSeek Harness 的具體實作或技術細節。
- Deepseek Harness Preview link - DGX Spark / GB10 - NVIDIA Developer Forums — 這是 NVIDIA 開發者論壇上簡短的社群討論，內容多為個別使用者極其簡短的主觀初體驗與隨筆感受（例如建議目前不值得認真投入時間），缺乏具體的架構分析或技術細節支撐。
- GitHub Copilot Agent Hooks - Peter Miľovčík - Obsidian Publish — 該來源為失效的無效連結（404 頁面），實際內容僅顯示「Not found. This page does not exist」，完全沒有任何實質的資訊或技術內容。

## 已移除的來源（蒸餾必要性）

- Chat Session ID and Completion Status API · community · Discussion #185849 - GitHub — 由於網頁加載錯誤，此來源僅包含 GitHub 網站導覽選單與頁首連結，完全沒有任何與 Hook、生命週期或技術實作相關的實質內容。
- Data Processing Statement - DeepSeek Harness — 此來源為隱私權與資料處理聲明，僅說明資料如何在地端處理，完全不涉及技術擴充、Hook、Plugin 或生命週期機制。
- DeepSeek Harness | Safe Use Policy — 屬於合規與安全使用政策，主要說明限制用途（如禁止用於軍事、網路攻擊等）及基本安全建議，完全缺乏生命週期擴充或 Hook 實作等技術細節。
- DeepSeek-V4-Flash-Vision-Exp Release: Multimodal API Now Live — 此來源為 DeepSeek 的多模態模型發佈公告，介紹 Vision API 的參數與計費方式。雖然提及 DeepSeek Harness 提供支援，但對於 DSH 本身的 Hook 或生命週期擴充機制沒有任何技術上的實質貢獻。
- GitHub Copilot Chat cheat sheet — 為使用者層級的 Copilot Chat 快速鍵及斜線指令（/clear、/explain、/fix）、聊天變數（#file、#block）等速查表，與開發者用於自動化或擴充 IDE 代理的 Hook、生命週期擴充或 Middleware 機制無關。
- deepseek-harness/docs/architecture.md at master - GitHub — 由於 GitHub 網頁加載失敗，此來源僅包含「There was an error while loading」的錯誤提示，無任何有用的技術內容。
- deepseek-harness/packages/extensions/tool-cordis/README.md at master - GitHub — 由於網頁加載失敗，此來源僅包含錯誤提示與 GitHub 導覽列，對 Cordis 核心或工具擴充無任何實質技術細節。
- dsh-self-evolving — an evidence-first, crash-resumable self-evolution engine (standard DSH Cordis bundle) #2547 - GitHub — 由於 GitHub 網頁加載失敗，此來源僅包含錯誤提示與導覽選單，未包含任何有關 DSH 自演化引擎或 Cordis 綁定的實質技術細節。
- pi/packages/coding-agent/docs/rpc.md at main · earendil-works/pi - GitHub — 由於網頁加載失敗，此來源僅包含 GitHub 頁尾與導覽資訊，完全缺乏任何關於 Pi Agent RPC 機制的技術內容。
- pi/packages/coding-agent/docs/termux.md at main · earendil-works/pi - GitHub — 由於網頁加載失敗，此來源僅有 GitHub 導覽列和頁尾選單，對在 Termux 環境下運行或擴充 Pi 代理無任何實質技術貢獻。
- pi/packages/coding-agent/examples/extensions/README.md at main · earendil-works/pi - GitHub — 由於網頁加載失敗，此來源僅有 GitHub 的頁尾和導覽選單，未包含任何關於擴充範例的實質內容。

## 參考來源清單

- [[Adobe 開源官方] 指南文件 - 基於 OpenAI Codex 的 AI 優先開發準則](https://opensource.adobe.com/mysticat-ai-native-guidelines/04-configuration/ai-tools/codex/)
- [[Anthropic 官方] 指南文件 - 如何在 Claude 生態中配置與使用插件](https://support.claude.com/en/articles/13837440-use-plugins-in-claude)
- [[Anthropic 官方] 產品規格 - 在 Claude Code 中集成 Codex 插件之對抗性審查](https://www.ai.joaoqueiros.com/blog/codex-plugin-claude-code-adversarial-review-workflow)
- [[Anthropic 官方] 開發文件 - Claude Code Hooks 與生命週期事件清單](https://code.claude.com/docs/en/hooks)
- [[Anthropic 官方] 開發文件 - Claude Code 擴充機制與 Plugin 設定](https://code.claude.com/docs/en/features-overview)
- [[Anthropic 官方] 開發文件 - 在 Claude Code CLI 中推薦與動態載入自訂插件](https://code.claude.com/docs/en/plugin-hints)
- [[Anthropic 官方] 開發文件 - 建立 Claude Code 插件之開發指南](https://code.claude.com/docs/en/plugins)
- [[ChatGPT Learn 網站] 教學指南 - OpenAI Codex CLI 的基礎操作與設定](https://learn.chatgpt.com/docs/codex/cli)
- [[Composio 部落格] 產品介紹 - DeepSeek Harness 2026 年推薦插件清單](https://composio.dev/content/best-deepseek-harness-plugins)
- [[DataCamp 網站] 教學指南 - DeepSeek Harness 開源代理環境架設教學](https://www.datacamp.com/tutorial/deepseek-harness)
- [[DeepSeek 官方] 產品規格 - DeepSeek Harness 開發者預覽：全面插件化架構](https://deepseek.com/harness/en/)
- [[DeepSeek 官方] 開發文件 - DeepSeek API 與 Pi Coding Agent 整合指南](https://api-docs.deepseek.com/quick_start/agent_integrations/pi_mono/)
- [[GitHub Issue] 技術探討 - 支援 JetBrains IDE 中 Copilot CLI 插件市場格式之提案](https://github.com/microsoft/copilot-intellij-feedback/issues/1539)
- [[GitHub Issue] 架構文件 - oh-my-dsh：用於自我演化治理的 DeepSeek Harness 散佈版提案](https://github.com/deepseek-ai/deepseek-harness/discussions/3113)
- [[GitHub 官方] 指南文件 - 使用 Hooks 確保 Copilot CLI 合規且可預測的執行](https://docs.github.com/en/copilot/tutorials/copilot-cli-hooks)
- [[GitHub 官方] 指南文件 - 環境中配置 GitHub Copilot 與自訂擴充](https://docs.github.com/copilot/configuring-github-copilot/configuring-github-copilot-in-your-environment?tool=visualstudio)
- [[GitHub 官方] 版本公告 - 2026年6月 VS Code 中 GitHub Copilot 的新擴充功能](https://github.blog/changelog/2026-07-08-github-copilot-in-visual-studio-code-june-2026-releases/)
- [[GitHub 官方] 產品規格 - GitHub Copilot 插件與擴充機制概述](https://docs.github.com/en/copilot/concepts/agents/about-plugins)
- [[GitHub 官方] 開發文件 - GitHub Copilot CLI 插件 API 參考指南](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-plugin-reference)
- [[GitHub 官方] 開發文件 - GitHub Copilot CLI 擴充機制說明](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/copilot-cli/about-cli-extensions)
- [[GitHub 官方] 開發文件 - GitHub Copilot Lifecycle Hooks 機制](https://docs.github.com/en/copilot/concepts/agents/hooks)
- [[GitHub 官方] 開發文件 - GitHub Copilot 完整 Hooks 生命週期參考](https://docs.github.com/en/copilot/reference/hooks-reference)
- [[GitHub 官方] 開發文件 - 在 GitHub Copilot CLI 中整合自訂 Hooks 的最佳實踐](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-hooks)
- [[GitHub 官方] 開發文件 - 建立 GitHub Copilot CLI 插件與擴充](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-creating)
- [[GitHub 社群] 產品介紹 - Awesome GitHub Copilot 精選插件與擴充生態系統](https://awesome-copilot.github.com/plugins/)
- [[GitHub 開源專案] 教學指南 - DeepSeek Harness 的 Cordis 核心擴充開發教學](https://github.com/deepseek-ai/deepseek-harness/blob/master/docs/cordis-tutorial/index.md)
- [[GitHub 開源專案] 架構文件 - DeepSeek Harness 官方儲存庫與插件架構](https://github.com/deepseek-ai/deepseek-harness)
- [[GitHub 開源專案] 開發文件 - GitHub Copilot 的 Azure 專屬官方擴充套件庫](https://github.com/microsoft/github-copilot-for-azure)
- [[GitHub 開源專案] 開發文件 - oh-my-pi 專案中的 Pi Agent Hooks 技術指南](https://github.com/can1357/oh-my-pi/blob/main/docs/hooks.md)
- [[GitHub 開源專案] 開發文件 - pi-hooks：相容於 Claude Code 的 Pi 代理指令 Hooks](https://github.com/hsingjui/pi-hooks)
- [[GitHub 開源專案] 開發文件 - prempti 專案中的 Codex CLI Hooks 技術說明](https://github.com/falcosecurity/prempti/blob/main/hooks/codex/README.md)
- [[GitHub 開源專案] 開發文件 - 採用 Rust 實作的高效能 Pi Coding Agent](https://github.com/Dicklesworthstone/pi_agent_rust)
- [[Habr 部落格] 架構文件 - 深入 DeepSeek Harness：Cordis、會話事件與權限邊界](https://habr.com/en/articles/1070958/)
- [[InfoQ 媒體] 媒體報導 - DeepSeek Harness 開源推動模組化 AI 代理基礎設施發展](https://www.infoq.com/news/2026/08/deep-seek-harness/)
- [[LobeHub 社群] 開發文件 - 建立 Pi 代理專屬擴充功能與技能市場整合](https://lobehub.com/skills/zenobi-us-dotfiles-creating-pi-extensions)
- [[MarkTechPost 媒體] 媒體報導 - DeepSeek 開源 DSH 代理 Harness 開發者預覽版](https://www.marktechpost.com/2026/08/17/deepseek-ai-releases-deepseek-harness-in-developer-preview/)
- [[Microsoft 官方] 版本公告 - Visual Studio 2026 新特性與 AI 代理整合架構](https://learn.microsoft.com/en-us/visualstudio/releases/2026/release-notes)
- [[Microsoft 官方] 開發文件 - 在 Visual Studio 中使用 Copilot 代理模式](https://learn.microsoft.com/en-us/visualstudio/ide/copilot-agent-mode?view=visualstudio)
- [[Microsoft 官方] 開發文件 - 在 Visual Studio 中導入與配置自訂 Copilot 代理](https://learn.microsoft.com/en-us/visualstudio/ide/copilot-specialized-agents?view=visualstudio)
- [[MindStudio 媒體] 比較分析 - DeepSeek Harness、Claude Code 與 Codex 深度對比](https://www.mindstudio.ai/blog/deepseek-harness-vs-claude-code-codex)
- [[MindStudio 媒體] 產品介紹 - 詳解 DeepSeek Harness：以插件為核心的編碼代理](https://www.mindstudio.ai/blog/deepseek-harness-agentic-coding)
- [[OpenAI 官方] 架構文件 - Codex 作為平台：基於開放代理 Harness 的構建指引](https://developers.openai.com/blog/codex-as-a-platform)
- [[OpenAI 官方] 版本公告 - Codex CLI v0.116.0 企業級 Hook 機制功能更新](https://www.augmentcode.com/learn/openai-codex-cli-enterprise)
- [[OpenAI 官方] 開發文件 - Codex CLI 與開發者指南](https://developers.openai.com/learn/codex)
- [[Pi 官方] 產品規格 - pi-codebase-memory-hooks 擴充套件說明](https://pi.dev/packages/pi-codebase-memory-hooks?name=RAG&type=extension&page=2)
- [[Pi 官方] 產品規格 - pi-yaml-hooks 宣告式 CLI 事件 Hook 套件](https://pi.dev/packages/pi-yaml-hooks)
- [[Reddit 社群] 心得討論 - DeepSeek Harness 朝向自我演化 AI 系統的具體實踐](https://www.reddit.com/r/DeepSeek/comments/1vpmdg9/deepseek_harness_a_concrete_step_toward/)
- [[Reddit 社群] 技術探討 - 如何在 Go 語言代理中實作 Pi 樣式的擴充機制](https://www.reddit.com/r/PiCodingAgent/comments/1vs8yg2/how_would_you_implement_pistyle_extensions_in_a/)
- [[Reddit 社群] 比較分析 - 探討 Visual Studio 2026 與 VS Code 的 Copilot 整合差異](https://www.reddit.com/r/GithubCopilot/comments/1s66tzo/vs2026_vs_vscode_integration/)
- [[Tricontinental 機構] 指南文件 - 在學術與社會研究中導入 DeepSeek Harness 流程](https://thetricontinental.org/how-to-use-deepseek-harness/)
- [[VS Code 官方] 指南文件 - 選擇與使用 IDE 中的 AI 代理 Harness](https://code.visualstudio.com/docs/agents/run/agent-harnesses)
- [[VS Code 官方] 版本公告 - VS Code 2026年1月更新與 AI 代理支援規格](https://code.visualstudio.com/updates/v1_109)
- [[VS Code 官方] 版本公告 - VS Code 2026年2月更新與 Agent 擴充支援](https://code.visualstudio.com/updates/v1_110)
- [[VS Code 官方] 開發文件 - VS Code 中的 Agent 插件架構](https://code.visualstudio.com/docs/agent-customization/agent-plugins)
- [[VS Code 官方] 開發文件 - VS Code 擴充套件中的 Lifecycle Hooks 機制簡介](https://code.visualstudio.com/learn/customizations/5-hooks)
- [[個人部落格] 架構文件 - 剖析 Pi Agent 底層運行邏輯與擴充插槽](https://edward40.com/p/pi-agent-internal/)
- [[個人部落格] 案例研究 - 利用 Hooks 實現 GitHub Copilot 代碼格式化自動化](https://bartwullems.blogspot.com/2026/04/github-copilotformat-your-code-using.html)
- [[個人部落格] 案例研究 - 實作 GitHub Copilot CLI 的 WakaTime 整合擴充](https://dev.to/geeknees/building-a-github-copilot-cli-extension-implementing-wakatime-integration-5c0a)
- [[個人部落格] 案例研究 - 為 Pi Coding Agent 實作 OpenTelemetry 可觀測性擴充](https://prokopov.me/posts/pi-otel-telemetry-extension/)
- [[個人部落格] 產品介紹 - Pi Agent：專為掌握底層工具的工程師設計的編碼代理](https://alexander.holbreich.org/posts/2026/pi-coding-agent/)
