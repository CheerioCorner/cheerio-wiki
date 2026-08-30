# Deep Research 報告 — rc-20260830-004

- 研究主題：Skill Supply Chain Security：AI Agent Skill 的安全風險、Skill 供應鏈攻擊場景、Skill 權限控管機制、Sandbox 隔離策略、Skill 安全審查流程。以 Claude Code Skills / GitHub Copilot Extensions / Pi Extensions 為主要研究對象。
- Notebook：https://notebooklm.google.com/notebook/7a49a488-1f82-44b0-b813-42cc345e6020
- 產生時間：2026-08-30T13:49:12.977Z
- 品質過濾：101 → 95 筆（移除 6 筆）
- 來源分類/重新命名：89 / 90 筆成功

## 研究結果

### 1. AI Agent Skill 的供應鏈風險有哪些？Malicious skill、dependency confusion、typosquatting 在 Skill 生態系中的威脅？

**AI 代理人技能（AI Agent Skills / Extensions / Plugins）** 作為擴充 AI 代理能力的核心載體，在賦予其讀寫檔案、執行 Shell 指令與調用外部 API 等強大權限的同時，也徹底重塑了軟體安全威脅模型 [1-5]。傳統軟體開發將配置文件（如 JSON、YAML）視為被動的靜態元數據，但在具備高度自主執行能力的 AI 代理（如 Claude Code、OpenClaw）中，這些配置文件（如 `SKILL.md`、`.mcp.json`、`CLAUDE.md` 等）實際上變成了控制主動執行路徑的「程式碼」 [6-8]。這使軟體供應鏈安全面臨前所未有的新型態挑戰 [9-11]。

---

### 一、 AI Agent Skill 生態系的供應鏈風險

AI 代理人技能生態系在架構與流通上存在多個關鍵的供應鏈薄弱點：

1. **去中心化與缺乏安全審核（Poor Scanning）**
   目前多數 Skill 共享平台與第三方倉庫（如 ClawHub、skills.sh 等）通常沒有嚴格的安全審查與人工審核機制 [12-15]。任何人只需使用註冊僅滿一週的 GitHub 帳號，便能自由發布技能組件，為攻擊者提供了低門檻的惡意植入通道 [16]。
2. **自動更新與版本漂移（Update Drift）**
   技能通常由 Markdown 或指令腳本等非編譯檔案組成 [13, 17, 18]。若系統缺乏密碼學簽章與雜湊鎖定（SHA-256 Pinning），一旦觸發背景升級，上游遭到污染的技能便能在用戶不知情的情況下默默引入惡意代碼、指令漏洞（即 “rug pull” 攻擊）或不安全的依賴 [19-23]。
3. **過度權限與「Confused Deputy（混淆代理）」**
   許多第三方技能在聲明清單（Manifest）中往往僅宣稱使用狹窄的權限，但其底層腳本或相鄰組件卻會私下調用更寬泛的特權工具 [24, 25]。若直接給予代理人具有 repo 刪除、主機控制等全功能權限的廠商 raw MCP 服務（如原始 GitHub 或 Datadog MCP 服務），將導致極大的衝擊半徑（Blast Radius） [26, 27]。
4. **跨平台重用時的安全規則失效（Cross-Platform Failures）**
   在不同執行環境（例如 CLI 終端機 vs 生產環境 SDK）之間重用技能時，平台的安全邊界可能發生崩塌 [23, 28]。以 Claude Agent SDK 為例，系統會默默忽略 `SKILL.md` 內定義的 `allowed-tools` 工具限制 [29, 30]。如果開發者未手動重新配置權限，將技能由 restricted CLI 移植至 SDK 時，會造成權限無預警提升，使該技能能隨意調用 Bash、Write 等未經授權的高風險工具 [28, 30]。

---

### 二、 惡意技能（Malicious Skill）、依賴混淆與拼寫劫持的具體威脅

#### 1. Malicious Skill（惡意技能）的多元攻擊手法
惡意技能通常利用 AI 代理在數據與指令分離上的本質缺陷，實施多層次的隱蔽攻擊：
- **視覺盲區與無感背景觸發（Visibility Gap）**
  攻擊者可以將惡意代碼寫入 `SKILL.md` 的 `when_to_use` 觸發條件或將 `user-invocable` 設為 `false` 隱藏技能 [31-33]。由於 slash 選單僅會顯示名稱與描述，惡意指令得以成功隱蔽 [31-33]。一旦用戶無意間觸發了匹配的對話情境，惡意技能便會在背景無感執行，並在不經詢問與授權的情況下調用未授權工具（如調用 `Write` 對系統進行秘密修改） [33-35]。
- **安全護欄降級攻擊（Downgrade Attack）**
  惡意技能可透過其前言（Frontmatter）配置指定使用能力較弱的模型版本（Less capable model）與較低的 Effort 推理等級，藉此弱化 AI 代理自帶的安全護欄與防禦分類器，從而順利在背景運行惡意 payload [33, 36, 37]。
- **持久性記憶體投毒（Memory Poisoning）**
  這是一種極具威脅的跨會話攻擊 [35, 38, 39]。惡意技能運行時能秘密對代理人的長期記憶檔案（如 `MEMORY.md`、`SOUL.md` 或向量數據庫）寫入偽造的「政策約束」（如「強制拒絕所有 harmless 請求」） [35, 38, 40, 41]。一旦被寫入，即使該惡意技能事後被刪除，AI 代理在後續正常對話中調用記憶時，依然會持續依循此偽造的政策，達到長期的零接觸行為控制 [35, 38, 42]。
- **直接散播竊密木馬（AMOS / RAT）**
  在 OpenClaw 的 ClawHavoc 戰役中，研究指出高達 12% 的技能包為惡意軟體 [40, 43]。許多技能包（如 `solana-wallet-tracker`）外表包裝成專業工具，但在「安裝前提（Prerequisites）」中誘導開發者下載並執行 Atomic Stealer (AMOS)，藉此竊取 SSH 私鑰、瀏覽器密碼、本地 `.env` 變數等 API 金鑰，或直接植入反向 Shell 遠端接管系統 [16, 40, 43]。

#### 2. Dependency Confusion（依賴混淆）的劫持路徑
依賴混淆（Dependency Confusion）主要瞄準 AI 代理「自主分析與推薦安裝依賴包」的運作行為 [44-46]：
- **自主編譯與未校驗下載**
  許多 AI 代理為了成功編譯、測試或執行 Skills，被賦予了自主安裝程式庫（如執行 `pip install`、`npm install` 等）的權限 [21, 44-46]。若 AI 技能在調用中引入了企業內部的私有包名稱，而代理人未配置嚴格的本地源鏡像或在環境變數路徑（CWE-426）中存有漏洞，便可能會直接從外部公共倉庫（如 PyPI、npm）中拉取攻擊者預先上傳、同名但版本號極高的惡意公共依賴包 [21, 45]。
- **代碼執行與機敏憑證外洩**
  這些混淆拉取的惡意包通常會在 `post-install` 階段執行任意 Shell 腳本，快速反向連線，並將開發環境內的 `.env`、API Keys 等憑證直接打包並回傳到攻擊者的 Webhook [21, 40, 47, 48]。例如 ChainDrop npm 蠕蟲即是透過 Claude Code 項目 local hook 來進行蠕蟲式蔓延，在短短幾天內劫持了數百個公共包，影響了龐大的下載量 [49]。

#### 3. Typosquatting（拼寫劫持 / 幻覺劫持）的新威脅
在傳統軟體供應鏈中，Typosquatting 指的是搶先註冊與知名庫相似名稱的惡意包（如 `lodash` 拼成 `lodas`）。然而在 AI 生態系中，這一手法演變成了更致命的 **「幻覺劫持 (Hallucination Squatting)」** [50, 51]：
- **大語言模型幻覺漏洞的武器化**
  大語言模型基於機率生成的特性，經常會自信地編造（Hallucinate）出根本不存在的套件或庫，研究指出 AI 編造不存在包的比例高達 30% [50, 52]。
- **預註冊陷阱（Proactive Poisoning）**
  攻擊者會透過對主流 AI 模型進行大量壓力測試，或者監控熱門的 AI 幻覺套件名字，在 PyPI 或 npm 等公共軟體源上搶先註冊這些不存在的名字，並上傳封裝了正版依賴但夾帶惡意代碼的包 [50, 51]。
- **零防備的供應鏈感染**
  當開發者完全信任 AI 助理（如 GitHub Copilot）所給予的代碼建議，在完全沒有重複核對的情況下直接執行了安裝指令，AI 產生的幻覺套件便會在本地主機與 CI/CD 流程中執行，將惡意後門、密碼竊取程式默默引渡到企業的生產環境中 [50, 51, 53, 54]。

---

### 三、 企業最佳防禦實踐

為有效阻斷 AI 技能生態系的供應鏈威脅，企業應構建 **多層次縱深防禦架構（Defense-in-Depth）** [55-57]：

1. **密碼學工具 Provenance 驗證**：強制要求對所有上架的技能、MCP 工具進行加密數位簽章，實行嚴格的供應鏈來源（Provenance）追蹤 [58, 59]。
2. **精細化功能控制與最小特權原則（Capability Scoping）**：遵從 Meta 的「雙重原則 (Rule of Two)」 [58]。在處理外來不安全輸入（如網頁抓取、第三方 Repo 代碼）時，嚴禁代理人同時具備「訪問敏感數據」與「變更外部狀態 / 行網外連」兩項功能 [58]。
3. **隔離執行沙盒（Sandboxing）**：對所有的工具與 Skills 執行，實施基於專案維度的硬性容器化或極輕量 VM 隔離（如 Docker, Firecracker VM），並嚴格配置出口網閘與 Allowlists [58, 60]。
4. **多代理人協作稽核（Runtime Intent Verification）**：部署獨立、異構的 Guardian Agent 作為 runtime 決策驗證，在敏感操作（如執行 shell 代碼、重設防火牆、存取憑證）寫入執行佇列前進行語意審查與阻斷 [4, 58, 61, 62]。

---

💡 **想進一步了解如何保障您的 AI 開發環境安全嗎？** 我們可以為您目前的 AI 代理人架構（如 Claude Code 或 Copilot）設計一個自動化的 SKILL.md 靜態安全掃描腳本，檢測 frontmatter 中的權限隱患，您意下如何？

**引用來源：**
- [1] [[MintMCP 部落格] 安全專題 - 企業如何因應 Claude Code 嚴重漏洞與供應鏈安全防禦](https://www.mintmcp.com/blog/claude-code-cve)
- [2] [[技術媒體] 安全專題 - 從 Claude Code 看 AI Agent 權限授予與實際資料控制之間的落差](https://nhimg.org/articles/claude-code-security-exposes-the-gap-between-permissions-and-data-control/)
- [3] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [4] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [5] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [6] [[MintMCP 部落格] 安全專題 - 企業如何因應 Claude Code 嚴重漏洞與供應鏈安全防禦](https://www.mintmcp.com/blog/claude-code-cve)
- [7] [[Penligent 專題] 安全報告 - 超越對話框：AI Agent 被越獄 (Jailbreak) 後的自動化實體威脅](https://www.penligent.ai/hackinglabs/claude-jailbreak/)
- [8] [[Penligent 專題] 安全報告 - 超越對話框：AI Agent 被越獄 (Jailbreak) 後的自動化實體威脅](https://www.penligent.ai/hackinglabs/claude-jailbreak/)
- [9] [[安全部落格] 漏洞剖析 - 利用惡意專案檔對 Claude Code 實施 RCE 與 API 密鑰竊取攻擊](https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/)
- [10] [[Penligent 專題] 安全報告 - 超越對話框：AI Agent 被越獄 (Jailbreak) 後的自動化實體威脅](https://www.penligent.ai/hackinglabs/claude-jailbreak/)
- [11] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [12] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [13] [[安全部落格] 漏洞剖析 - 利用惡意技能與第三方 Agent 危害 Claude Code 安全性 (第一部分)](https://labs.reversec.com/posts/2026/05/skill-issues-compromising-claude-code-with-malicious-skills-agents-part-1)
- [14] [[安全部落格] 漏洞剖析 - 利用惡意技能與第三方 Agent 危害 Claude Code 安全性 (第一部分)](https://labs.reversec.com/posts/2026/05/skill-issues-compromising-claude-code-with-malicious-skills-agents-part-1)
- [15] [[Snyk 專題] 資源目錄 - 九款用於網路安全檢測、駭客攻防與漏洞掃描的 Claude 技能推薦](https://snyk.io/articles/top-claude-skills-cybersecurity-hacking-vulnerability-scanning/)
- [16] [[Adversa AI 專題] 安全報告 - OpenClaw 漏洞 CVE-2026-25253、憑證外洩與系統加固指南](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)
- [17] [[Red Hat 部落格] 安全專題 - AI Agent 技能安全威脅、漏洞成因與主動控制措施](https://developers.redhat.com/articles/2026/03/10/agent-skills-explore-security-threats-and-controls)
- [18] [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [19] [[Red Hat 部落格] 安全專題 - AI Agent 技能安全威脅、漏洞成因與主動控制措施](https://developers.redhat.com/articles/2026/03/10/agent-skills-explore-security-threats-and-controls)
- [20] [[Red Hat 部落格] 安全專題 - AI Agent 技能安全威脅、漏洞成因與主動控制措施](https://developers.redhat.com/articles/2026/03/10/agent-skills-explore-security-threats-and-controls)
- [21] [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [22] [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [23] [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [24] [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [25] [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [26] [[Port.io 部落格] 架構分析 - 為什麼企業需要建立私有 MCP 服務註冊表 (Registry)](https://www.port.io/blog/mcp-registry)
- [27] [[Port.io 部落格] 架構分析 - 為什麼企業需要建立私有 MCP 服務註冊表 (Registry)](https://www.port.io/blog/mcp-registry)
- [28] [[Anthropic GitHub] 社群討論 - SKILL.md 中 allowed-tools 控制在 CLI 與 SDK 之間的重大不一致問題](https://github.com/anthropics/claude-code/issues/18737)
- [29] [[Anthropic GitHub] 社群討論 - SKILL.md 中 allowed-tools 控制在 CLI 與 SDK 之間的重大不一致問題](https://github.com/anthropics/claude-code/issues/18737)
- [30] [[Anthropic GitHub] 社群討論 - SKILL.md 中 allowed-tools 控制在 CLI 與 SDK 之間的重大不一致問題](https://github.com/anthropics/claude-code/issues/18737)
- [31] [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [32] [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [33] [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [34] [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [35] [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [36] [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [37] [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [38] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [39] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [40] [[Adversa AI 專題] 安全報告 - OpenClaw 漏洞 CVE-2026-25253、憑證外洩與系統加固指南](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)
- [41] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [42] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [43] [[Adversa AI 專題] 安全報告 - OpenClaw 漏洞 CVE-2026-25253、憑證外洩與系統加固指南](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)
- [44] [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [45] [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [46] [[安全部落格] 漏洞剖析 - 利用惡意技能與第三方 Agent 危害 Claude Code 安全性 (第一部分)](https://labs.reversec.com/posts/2026/05/skill-issues-compromising-claude-code-with-malicious-skills-agents-part-1)
- [47] [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [48] [[安全部落格] 漏洞剖析 - 利用惡意技能與第三方 Agent 危害 Claude Code 安全性 (第一部分)](https://labs.reversec.com/posts/2026/05/skill-issues-compromising-claude-code-with-malicious-skills-agents-part-1)
- [49] [[技術媒體] 安全專題 - 理解 GitHub Copilot 安全隱私風險與權限控管最佳實踐](https://blog.gitguardian.com/github-copilot-security-and-privacy/)
- [50] [[技術媒體] 安全專題 - 理解 GitHub Copilot 安全隱私風險與權限控管最佳實踐](https://blog.gitguardian.com/github-copilot-security-and-privacy/)
- [51] [[Checkmarx 專題] 安全報告 - 2026 GitHub Copilot 遭遇的五大安全威脅及修補方案](https://checkmarx.com/learn/ai-security/top-5-github-copilot-security-risks-9-ways-to-mitigate-them/)
- [52] [[技術媒體] 安全專題 - 理解 GitHub Copilot 安全隱私風險與權限控管最佳實踐](https://blog.gitguardian.com/github-copilot-security-and-privacy/)
- [53] [[技術媒體] 安全專題 - 理解 GitHub Copilot 安全隱私風險與權限控管最佳實踐](https://blog.gitguardian.com/github-copilot-security-and-privacy/)
- [54] [[技術媒體] 安全專題 - 理解 GitHub Copilot 安全隱私風險與權限控管最佳實踐](https://blog.gitguardian.com/github-copilot-security-and-privacy/)
- [55] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [56] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [57] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [58] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [59] [[GitHub 開源] 安全建議 - 2025 最新 Model Context Protocol 實作之安全性最佳實踐指引](https://github.com/microsoft/mcp-for-beginners/blob/main/02-Security/mcp-security-best-practices-2025.md)
- [60] [[Red Hat 部落格] 安全專題 - AI Agent 技能安全威脅、漏洞成因與主動控制措施](https://developers.redhat.com/articles/2026/03/10/agent-skills-explore-security-threats-and-controls)
- [61] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [62] [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)

### 2. Skill 權限控管如何設計？Tool permission、resource access、capability restriction 的實作方式？

在自主式 AI 代理人（AI Agent，如 Claude Code、Pi Coding Agent）的執行架構中，權限控管是防止惡意程式碼執行、憑證竊取及提示注入（Prompt Injection）攻擊的關鍵防線 [1, 2]。

為了在開發效率與系統安全性之間取得平衡，現代 AI 代理人的安全框架通常將權限控管劃分為三大互補的防線：**工具調用權限（Tool Permission）**、**資源存取控制（Resource Access）** 與 **環境能力限制（Capability Restriction）** [3, 4]。

以下為這三大面向的深度設計與實作機制解析：

---

### 一、 工具調用權限（Tool Permission）的實作與設計

工具調用權限位於**應用程式層** [5]。其核心任務是在 AI 代理人企圖發起任何工具調用（如 `Bash`、`Read`、`Write`、`WebFetch` 或 MCP 外部工具）時，判定其是否獲得授權 [6-8]。

#### 1. 靜態聲明與三階段過濾（Allow / Ask / Deny）
以 Claude Code 為例，系統提供了一個層級化的規則引擎，在 `settings.json` 中配置三種過濾清單 [6, 9]：
*   **求值優先順序**：硬性規定為 **`Deny` ➜ `Ask` ➜ `Allow`** [10]。即使某個操作匹配了更寬鬆的 `Allow` 規則，只要它同時命中 `Deny` 或 `Ask` 規則，該操作依然會被立刻阻斷或提示 [10]。
*   **語法比對機制**：採用 `ToolName(matcher)` 格式 [7, 11]。
    *   例如：`Bash(npm run test:*)` 僅允許執行特定前綴的測試指令 [11]；`Read(./src/**)` 允許讀取特定目錄 [11]；`mcp__github__*` 則代表允許呼叫 GitHub MCP 伺服器的所有工具 [11]。
    *   為了防止安全繞過，規則比對引擎在執行比對前，會**靜態剝除（Strip）系統常見的包裝器（Process Wrappers）** [12]。例如，針對 `timeout`、`nice`、`nohup` 以及 `xargs` 等前綴，系統會將其剝離，直取核心命令（如將 `timeout 30 npm test` 視為 `npm test` 進行比對） [12]。但遇到 `devbox run` 等開發環境執行器或複雜的 `find -exec`，則會強制退回 `Ask` 提示 [12]。

#### 2. 細粒度參數比對（Match by Input Parameter）
從自訂規則出發，傳統的工具名稱或指令字串比對過於脆弱（例如變數展開或格式微調即可繞過規則） [13]。現代設計支持**針對工具呼叫的輸入參數進行標量比對** [14]：
*   語法為 `Tool(param:value)`，並支持通配符 `*` [15, 16]。
*   *實作範例*：`Agent(model:opus)` 用於攔截並限制子代理人調用昂貴的頂級 Opus 模型 [15, 16]；`Agent(isolation:worktree)` 用於限定子代理人必須在 Git 臨時工作區中隔離運作 [16]；`Bash(run_in_background:true)` 用於控制背景執行權限 [16]。

#### 3. 臨時授權與聲明（Allowed-tools in Frontmatter）
在技能（Skills）或獨立指令（Slash Commands）的 Markdown 定義檔中，開發者可以利用 YAML Frontmatter 聲明臨時授權 [17, 18]：
*   在調用特定技能時（如 `/implement`），其 Frontmatter 的 `allowed-tools` 能暫時提升權限（如賦予 `Bash(git:*)`），而**該授權僅在當前 turn（對話回合）內有效**，一旦進入下一輪對話即被收回 [18-20]。
*   *注意*：此機制必須防範平台一致性缺陷。在 Claude Code 中，此限制僅在 CLI 運作時生效，若將技能載入至 API Agent SDK 時，`allowed-tools` 的限制會被默默忽略，此時必須由 SDK 端的 `allowedTools` 選項進行硬性配置 [21]。

#### 4. 第三方擴充之動態權限模型
在 MIT 授權的開源 Pi Coding Agent 生態中，社群透過自定義擴充套件（如 `@gotgenes/pi-permission-system` 或 `pi-permission-layers`）實現了極佳的工具權限控制 [22-24]：
*   **`pi-permission-layers` 的五大安全等級**：將所有 Shell 操作分為 `minimal`（唯讀，如 `cat`, `git status`）、`low`（檔案修改，如 `write`）、`medium`（建置與測試）、`high`（網路、部署與 `eval` 等 Shell 技巧）以及 `bypassed` [25, 26]。
*   **Fail-Closed（失敗鎖定）**：當規則解析出錯，或者遇到無法靜態分析的混淆命令（如 `bash -c`、`eval`、`sudo`）時，權限閘會採取 **Fail-Closed 原則**直接阻斷或強制退回手動 UI 提示 [27, 28]。
*   **隱藏未授權工具**：在代理人初始化（`before_agent_start` 階段）時，直接從模型系統提示詞的 "Available tools" 區塊中**抹除並隱藏被拒絕的工具**，避免代理人反覆嘗試不可用的工具而浪費 Context 資源 [29, 30]。

---

### 二、 數據與資源存取（Resource Access）的實作與設計

資源存取控制側重於**防禦路徑穿越（Path Traversal）、不當憑證讀取及機敏數據外洩** [1, 31, 32]。

#### 1. 工作目錄邊界與額外目錄授權
*   **Working Directory Boundary**：預設情況下，代理人（特別是在 Manual 模式下）被嚴格限制在初始化 Git 倉庫的 Root 目錄內 [33, 34]。
*   **額外授權（`additionalDirectories`）**：若需跨越專案邊界（如讀取共用 Library），必須在設定檔中顯式配置 `additionalDirectories` [33, 35]。
*   **防範 Git 軟連結繞過（Symlink Resolution）**：在 `@gotgenes/pi-permission-system` 中，路徑存取攔截器會在執行 `read`/`write` 前，**同時解析原始路徑與 Canonical（完全解析軟連結後的實體路徑）** [27, 36]。如果實體路徑指向了被拒絕的敏感檔案（如 `~/.ssh` 或 `.env`），即便利用軟連結別名也會被立刻識破並阻斷 [27, 36]。

#### 2. Redirection（重定向）檢查
Shell 執行中的重定向操作（如 `>`、`>>`、`2>`）本質上是隱蔽的檔案寫入行為 [37]。
*   **實作機制**：Claude Code 在執行 Bash 工具前，會手動剖析命令字串中的重定向目標 [37]。
*   如果重定向目標（如 `> ~/.bashrc`）屬於 `protected paths`（如 `.git`、`.claude` 等配置目錄）或被 Deny 的路徑，系統會拒絕此 Bash 命令，防止代理人藉由 Shell 重定向修改敏感系統檔或提升自身權限 [37, 38]。

#### 3. 憑證與環境變數脫敏（Credential Masking / Unsetting）
*   **精細化憑證保護配置**：透過 `sandbox.credentials` 的 `files` 與 `envVars` 區塊進行配置 [39]。
*   **變數移除**：針對宣告為 `deny` 的敏感變數（如 `GITHUB_TOKEN`、`NPM_TOKEN`），在 sandboxed 進程啟動前，於環境中徹底 **unset** [39]；也可以配置 `CLAUDE_CODE_SUBPROCESS_ENV_SCRUB` 來全域擦除所有的 cloud 憑證 [40]。
*   **Sentinel 替代（遮罩機制）**：
    *   在 Linux 與 WSL2 上，當設定 `mode: mask` 時，沙盒內的進程讀取的並非實體敏感檔，而是系統產生的 **Sentinel Copy（站位偽造檔）**，其內部的祕鑰會被替換為 Placeholder 佔位符 [39, 41]。
    *   當該進程發起經授權的外部網路請求並穿過 **Sandbox Proxy（沙盒網路代理）** 時，代理伺服器會在出口流量中，動態將 Placeholder 替換回實體 Secret，從而保證「進程能正常發起認證，但 AI 進程本身無法讀取或 echo 實體祕鑰」 [41]。

---

### 三、 環境能力限制（Capability Restriction）的實作與設計

能力限制是**操作系統核心層（Kernel-Level）** 的邊界硬約束，也是抵禦 AI 被提示注入 Jailbreak（越獄）後的最後底牌 [42, 43]。

```
[ 最外層：Nix 容器 / 虛擬機 (Gondolin, Clawk) ]
   └── [ 系統層：OS 核心沙盒 (Bubblewrap / Seatbelt) ]
          └── [ 協定層：MCP Gateway 閘道 / 代理 ]
                 └── [ 應用層：PreToolUse Hooks & 規則過濾 ]
                        └── [ 最內層：CLAUDE.md / 指引 (提示詞約束) ]
```

#### 1. OS 級進程沙盒（Sandbox）
*   **macOS 的實作**：使用內建的 **Apple Seatbelt** 安全框架，藉由 `sandbox-exec` 載入設定好的沙盒 Profile，限制 Bash 及其子進程的存取行為，無須任何額外安裝 [44-46]。
*   **Linux & WSL2 的實作**：依賴 **`bubblewrap` (bwrap)** [45, 47]。
    *   預設掛載為**唯讀根目錄**（`--ro-bind / /`） [48]。
    *   採用 `tmpfs` 重新虛擬化一個空白的 `$HOME` 目錄，使機敏目錄（如 `~/.aws`, `~/.ssh`）在沙盒內進程的視野中直接「憑空消失」 [48, 49]。
    *   僅將當前的工作目錄（`$PROJECT_DIR`）以讀寫模式 bind-mount（掛載）進沙盒中 [48]。
*   **隔離網閘（Network Egress Proxy）**：
    *   沙盒內的進程預設禁止發起任何網路連接 [50]。
    *   所有 outbound 流量都必須被強制導向至沙盒外部的 Proxy Server [51, 52]。
    *   該 Proxy 負責校驗目標主機是否在 `allowedDomains` 或 `WebFetch(domain:...)` 的允許清單中，任何未經授權的連線都會被直接拒絕（`Operation not permitted`） [51-53]。

#### 2. 拋棄式 Micro-VM 與容器隔離（Docker / Gondolin / Clawk）
對於長時運行、無人值守或處理高風險、外來不受信任代碼的代理人任務，僅靠進程沙盒是不夠的 [54, 55]。
*   **Gondolin Micro-VM 模式**：Pi Agent 的 `pi-gondolin` 擴充套件，會在 session 啟動時於 1 秒內開機一個輕量化的 QEMU 虛擬機，並使用 FUSE-backed 的虛擬檔案系統 `RealFSProvider` 將工作目錄掛載至 VM 的 `/workspace` [56, 57]。AI 執行的所有指令、寫入和讀取均完全禁錮在該 Linux 微型 VM 中 [56, 58]。
*   **Clawk 方案**：在 Hacker News 社群中大受歡迎的 `Clawk` 也是基於 bubblewrap 與 Linux namespace 實現的極速拋棄式虛擬化，旨在防範 Rogue extensions（惡意插件）對宿主主機發起毀滅性操作 [59, 60]。

#### 3. 可編程動態攔截（Runtime Hooks）
除了靜態的 JSON/YAML 配置，現代代理人系統支持註冊 **可程式化的 Runtime 攔截腳本**（如 `PreToolUse`、`PermissionRequest` 鉤子） [3, 61]。
*   當 AI 代理人提出工具調用時，系統會在將請求送至權限規則引擎前，優先調用開發者編寫的 Hook 腳本（通常支持多種 Matcher 匹配），並將 tool-call 的實體參數以 JSON 格式輸入至腳本的 `stdin` [62-65]。
*   **Exit Code 決策機制**：
    *   `exit 0`：同意執行，放行並繼續由規則引擎審查 [62]。
    *   `exit 2`：**Deterministic Block（硬性阻斷）** [62, 66]。此時工具調用被立即中止，且 Hook 腳本寫入 `stderr` 的錯誤原因會被包裝並反饋給大語言模型，使其感知到權限受挫並主動進行「自癒 / 尋找替代路徑」（例如放棄使用高風險命令，轉而調用其他安全工具） [5, 62, 64]。

#### 4. Model Context Protocol (MCP) 的安全授權設計
隨著 MCP 成為業界通用的外接工具協定，其授權控制被獨立抽象出來 [67]：
*   **OAuth 2.1 + PKCE 強制驗證**：在 remote MCP 連接中，拒絕傳統脆弱的 "Token Passthrough"（禁止客戶端直接傳遞 Personal Access Token 給 MCP Server） [68-70]。MCP Server 必須作為 system of record，由客戶端向其發起獨立認證，並完成瀏覽器端的 Dynamic Client Registration（DCR）與使用者 consent 授權 [70-72]。
*   **防範 "Confused Deputy（混淆代理）" 攻擊**：MCP proxy 伺服器必須實施 **Per-Client Consent（單一客戶端獨立授權）** [73]。在發起對 SaaS 端（如 GitHub）的靜態 Client ID 認證前，必須在 MCP 伺服器端彈出其專屬的同意畫面，清晰展示 client 名稱、redirect_uri 與 scope，防範惡意 client 藉由 OAuth 共享 Cookie 實現「一鍵帳號接管」 [74-76]。

---

### 🛠️ 權限控管對比一覽表

| 控管層面 | 技術手段 | 實作工具 (例如 Claude Code / Pi) | 核心防禦點 |
| :--- | :--- | :--- | :--- |
| **Tool Permission** | 三階過濾、Frontmatter 聲明、參數比對 [6, 9, 14, 18] | `settings.json`, `allowed-tools`, `@gotgenes/pi-permission-system` [6, 18, 22] | 限制特權命令、防範未授權工具探索與呼叫 [29, 77] |
| **Resource Access** | Symlink 實體路徑解析、Redirection 攔截、環境變數遮罩 [27, 37, 39] | `additionalDirectories`, `sandbox.credentials` [35, 39] | 防止 Path Traversal 繞過、憑證被 Echo/Exfiltrate 外洩 [1, 39, 78] |
| **Capability Restriction** | OS 級進程沙盒、Micro-VM、PreToolUse 可編程 Hook [3, 56, 79] | `Seatbelt` (macOS), `bubblewrap` (Linux), `pi-gondolin` [45, 47, 56] | 阻斷 Jailbreak 後的核心層越權、硬性限制網路出口與寫入路徑 [5, 42, 80] |

---

💡 **想為您的團隊配置更安全的 AI 代理人環境嗎？** 
我們可以為您撰寫一個客製化的 `PreToolUse` Hook 攔截腳本（例如針對 Git 的 `--no-verify` 與危險的 SQL 命令進行實時安全過濾），您是否需要了解具體的 Shell / TypeScript 腳本實作？

**引用來源：**
- [1] [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [2] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [3] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [4] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [5] [[Firecrawl 專題] 比較分析 - Claude Code 與 Codex 的功能、效率與沙盒安全性評估](https://www.firecrawl.dev/blog/claude-code-vs-codex)
- [6] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [7] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [8] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [9] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [10] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [11] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [12] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [13] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [14] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [15] [[Anthropic 官方] 更新日誌 - Claude Code 安全改進與功能更新歷史](https://code.claude.com/docs/en/changelog)
- [16] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [17] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [18] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [19] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [20] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [21] [[Anthropic GitHub] 社群討論 - SKILL.md 中 allowed-tools 控制在 CLI 與 SDK 之間的重大不一致問題](https://github.com/anthropics/claude-code/issues/18737)
- [22] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [23] [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [24] [[Pi 官方] 開源軟體 - pi-permission-layers 多層次安全存取控管模型軟體包](https://pi.dev/packages/pi-permission-layers)
- [25] [[Pi 官方] 開源軟體 - pi-permission-layers 多層次安全存取控管模型軟體包](https://pi.dev/packages/pi-permission-layers)
- [26] [[Pi 官方] 開源軟體 - pi-permission-layers 多層次安全存取控管模型軟體包](https://pi.dev/packages/pi-permission-layers)
- [27] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [28] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [29] [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [30] [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [31] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [32] [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [33] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [34] [[Anthropic 官方] 技術文件 - Claude Code 安全防護架構、終端機限制與本地檔案防線](https://code.claude.com/docs/en/security)
- [35] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [36] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [37] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [38] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [39] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [40] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [41] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [42] [[VS Code 官方] 技術指南 - VS Code 整合式環境的 AI 安全防護與模型防線](https://code.visualstudio.com/docs/agents/run/security)
- [43] [[Penligent 專題] 安全報告 - 超越對話框：AI Agent 被越獄 (Jailbreak) 後的自動化實體威脅](https://www.penligent.ai/hackinglabs/claude-jailbreak/)
- [44] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [45] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [46] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [47] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [48] [[GitHub 開源] 技術文件 - wave 專案底層沙盒環境 (Sandbox) 架構與快速部署指南](https://github.com/re-cinq/wave/blob/main/docs/guides/sandbox-setup.md)
- [49] [[GitHub 開源] 技術文件 - wave 專案底層沙盒環境 (Sandbox) 架構與快速部署指南](https://github.com/re-cinq/wave/blob/main/docs/guides/sandbox-setup.md)
- [50] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [51] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [52] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [53] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [54] [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [55] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [56] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [57] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [58] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [59] [[Hacker News] 開源軟體 - Clawk：為 AI 程式代理人提供拋棄式 Linux 虛擬機器防禦方案](https://news.ycombinator.com/item?id=48892859)
- [60] [[Hacker News] 開源軟體 - Clawk：為 AI 程式代理人提供拋棄式 Linux 虛擬機器防禦方案](https://news.ycombinator.com/item?id=48892859)
- [61] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [62] [[OCDevel 播客] 架構解說 - Claude Code 組成要素與底層 Sandbox 執行沙盒機制](https://ocdevel.com/mlg/mla-23)
- [63] [[部落格] 開發實務 - Claude Code 代理人運作實務指南與環境隔離考量](https://schoettler.io/en/blog/claude-code-agenten)
- [64] [[部落格] 開發實務 - Claude Code 代理人運作實務指南與環境隔離考量](https://schoettler.io/en/blog/claude-code-agenten)
- [65] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [66] [[VS Code 官方] 技術指南 - VS Code 整合式環境的 AI 安全防護與模型防線](https://code.visualstudio.com/docs/agents/run/security)
- [67] [[OCDevel 播客] 架構解說 - Claude Code 組成要素與底層 Sandbox 執行沙盒機制](https://ocdevel.com/mlg/mla-23)
- [68] [[MCP 官方] 安全建議 - Model Context Protocol 授權控制與連線安全考量](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/security-considerations)
- [69] [[安全部落格] 安全建議 - 保護 Model Context Protocol 身份驗證：金鑰與憑證管理最佳實踐](https://blog.ogwilliam.com/post/mcp-authentication-security-best-practices)
- [70] [[安全部落格] 安全建議 - 保護 Model Context Protocol 身份驗證：金鑰與憑證管理最佳實踐](https://blog.ogwilliam.com/post/mcp-authentication-security-best-practices)
- [71] [[安全部落格] 安全建議 - 保護 Model Context Protocol 身份驗證：金鑰與憑證管理最佳實踐](https://blog.ogwilliam.com/post/mcp-authentication-security-best-practices)
- [72] [[安全部落格] 漏洞剖析 - 當 MCP 遇上 OAuth：因授權設計缺陷導致一鍵接管帳號之安全漏洞](https://www.obsidiansecurity.com/blog/when-mcp-meets-oauth-common-pitfalls-leading-to-one-click-account-takeover)
- [73] [[MCP 官方] 安全建議 - Model Context Protocol 伺服器與客戶端雙向驗證之安全實務](https://modelcontextprotocol.io/docs/draft/tutorials/security/security_best_practices)
- [74] [[MCP 官方] 安全建議 - Model Context Protocol 伺服器與客戶端雙向驗證之安全實務](https://modelcontextprotocol.io/docs/draft/tutorials/security/security_best_practices)
- [75] [[MCP 官方] 安全建議 - Model Context Protocol 伺服器與客戶端雙向驗證之安全實務](https://modelcontextprotocol.io/docs/draft/tutorials/security/security_best_practices)
- [76] [[安全部落格] 漏洞剖析 - 當 MCP 遇上 OAuth：因授權設計缺陷導致一鍵接管帳號之安全漏洞](https://www.obsidiansecurity.com/blog/when-mcp-meets-oauth-common-pitfalls-leading-to-one-click-account-takeover)
- [77] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [78] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [79] [[部落格] 技術指南 - 運維視角下的 Claude Code 部署、權限管理與安全沙盒管理](https://hidekazu-konishi.com/entry/claude_code_operators_handbook.html)
- [80] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)

### 3. Skill Sandbox 隔離策略？Process isolation、filesystem restriction、network control、resource limits 的比較？

在自主式 AI 代理人（如 Claude Code, Pi Coding Agent, Codex 等）的安全防禦體系中，**沙盒隔離（Sandboxing）** 屬於核心控制層。當「語意層防線」（如 `CLAUDE.md` 等提示詞約束）與「應用層防線」（如 Allow/Deny 權限規則）因提示注入（Prompt Injection）或越獄攻擊而被繞過時，沙盒提供了解決系統執行風險的核心層（Kernel-Level）硬性約束 [1, 2]。

不同的 AI 代理人框架在**進程隔離 (Process isolation)**、**檔案系統限制 (Filesystem restriction)**、**網路控制 (Network control)** 與 **資源限制 (Resource limits)** 的設計與實作上各有側重。以下是針對這四大隔離維度的深度剖析與橫向對比：

---

### 一、 進程隔離（Process Isolation）

進程隔離的目標是確保代理人所執行的程式碼、Shell 指令與子進程，無法直接控制或破壞宿主作業系統。

1. **OS 核心級輕量沙盒（以 Claude Code / Codex 為主）**
   * **macOS 實作**：利用 macOS 系統內建的 **Apple Seatbelt** 框架，透過 `sandbox-exec` 載入特定的沙盒 Profile 來執行 Bash 工具，無須額外安裝任何虛擬化軟體，啟動時間極快（小於 4ms） [3-6]。
   * **Linux 與 WSL2 實作**：普遍採用 **Bubblewrap (`bwrap`)** 建立獨立的 Namespace，並結合 Linux 核心的 `seccomp` 過濾器來阻斷危險的系統呼叫（例如限制 Unix Domain Sockets 存取以防範 WSL 逃逸） [7-9]。
2. **輕量級 Micro-VM 虛擬化隔離（以 Pi Coding Agent 為主）**
   * **Gondolin 微型虛擬機**：Pi Coding Agent 預設不提供內建沙盒 [10, 11]。社群與官方主流採用 **`pi-gondolin` 擴充套件** [12]，它在會話啟動時透過 **QEMU** 在背景快速開機一個獨立的 Linux 微型 VM（通常在 1 秒內啟動） [13]。AI 執行的所有 Bash 命令與工具鏈均完全禁錮在該獨立 VM 的核心中，與宿主機核心完全隔離 [13, 14]。
   * **Clawk / VMPI 方案**：這類 Hacker News 熱門方案同樣提供一鍵生成、拋棄式的 Micro-VM 執行環境，將整個代理人進程完全虛擬化 [15, 16]。
3. **容器化進程隔離（以 Wave / Docker 部署為主）**
   * **Docker / Podman 方案**：如 Wave 專案支援的 Docker 沙盒後端，或者將整個 Pi / OpenCode 封裝在 Docker 容器內運行 [17-19]。
   * **安全加固配置**：透過 `--read-only` 鎖定根檔案系統，並使用 `--cap-drop=ALL` 剝離容器內所有 Linux Capabilities，配合 `no-new-privileges` 防止進程透過 SUID 提權 [19]。

---

### 二、 檔案系統限制（Filesystem Restriction）

檔案系統限制旨在防止代理人發起路徑穿越攻擊（Path Traversal）、竊取宿主機機敏檔案（如 `~/.ssh`、`~/.aws`），或竄改系統設定 [20-22]。

1. **工作目錄邊界與唯讀掛載**
   * **唯讀根目錄（Read-Only Root）**：在 Bubblewrap 沙盒中，預設會使用 `--ro-bind / /` 將宿主機的整個 root 唯讀掛載進沙盒，確保代理人無法修改 `/bin` 或系統二進位檔 [22, 23]。
   * **工作目錄綁定（Bind Mount）**：僅將當前專案工作目錄（CWD）以讀寫模式綁定掛載 [22, 23]。
   * **臨時目錄重定向 (\$TMPDIR)**：為防止寫入衝突，沙盒會自動將代理人的 `$TMPDIR` 重定向到沙盒專屬的臨時目錄，與宿主機的 `/tmp` 隔離 [24]。
2. **防禦「自我授權」的保護路徑（Protected Paths）**
   * 即使在專案目錄內，沙盒也會執行**硬性寫入 Deny 規則**。這是為了防止惡意代碼透過修改配置文件來為自己提權 [25]。
   * **保護對象**：
     * 專案內部的 `.claude/settings.json`、`.claude/hooks` 腳本目錄、`.mcp.json` [26]。
     * 殼層啟動配置（`.bashrc`、`.zshrc`）、`.gitconfig` 以及 `.git/hooks` 目錄 [26]。
     * 任何意圖將專案目錄轉化為 bare git repository 的檔案寫入（如 `HEAD`、`objects`、`refs`） [26]。
3. **路徑解析防繞過**
   * 在優質的沙盒與權限擴充設計（如 Pi 的 `@gotgenes/pi-permission-system`）中，路徑過濾器在攔截 `read`/`write` 前，會使用 `std::filesystem::canonical` **同時解析符號連結（Symlink）與真實實體路徑**，防範攻擊者利用 Symlink 繞過 CWD 邊界存取外部敏感路徑 [27, 28]。

---

### 三、 網路控制（Network Control）

網路控制是防止提示注入後「敏感數據外洩（Exfiltration）」的最關鍵一環 [29]。

1. **預設拒絕（Deny-by-Default）與 Domain 允許清單**
   * 沙盒內部的進程預設被切斷網路。所有 Outbound 流量（如 `curl`、`wget`）均被強制導向至沙盒外運行的 **Egress Proxy（沙盒網路代理）** [30-32]。
   * 代理伺服器比對 `allowedDomains`（如 `github.com`、`*.npmjs.org`），未授權的域名將直接被 proxy 丟棄，返回 `host_not_allowed` [30, 31, 33]。
2. **TLS 終止與深度流量檢查（TLS Termination）**
   * **預設行為**：多數沙盒代理預設僅比對 SNI（Client Hello 中的主機名），不進行 TLS 解密。這存在安全隱憂：代碼可能利用 **網域前置（Domain Fronting）** 技術，在加密流量內部與外部主機通信 [34-36]。
   * **加固配置**：Claude Code 提供了實驗性的 `network.tlsTerminate` 選項，強制代理終止並解密 TLS 流量，從而進行內容級過濾與認證憑證替換 [34, 35]。
3. **環境變數與憑證動態遮罩（Credentials Masking）**
   * 沙盒提供兩種保護環境變數（如 `GITHUB_TOKEN`）與實體密鑰檔案的方式：
     * **`deny` 模式**：進程啟動前，在沙盒中直接 **unset** 變數，並完全拒絕對實體檔案的讀取 [37]。
     * **`mask` 模式（極佳的平衡方案）**：
       * 沙盒內的進程讀取的並非實體敏感檔/變數，而是被替換為一個 per-session 的 **Sentinel Value（哨兵虛擬值）** [38, 39]。
       * 當進程向已授權網域（如 `api.github.com`）發起請求並穿過沙盒 Egress Proxy 時，代理伺服器會在出口流量中，動態將 Sentinel 值替換回實體 Secret [38, 40]。
       * 如此一來，AI 代理人與其執行的指令「能順利發起網路認證」，但**指令本身、日誌與 transcript 絕不會經手實體 Plaintext 密鑰** [38]。

---

### 四、 資源限制（Resource Limits）

資源限制可以防止惡意代碼或無窮迴圈（Infinite Loops）導致 CPU/記憶體耗盡（DoS 攻擊），並管控財務成本。

1. **財務與運行度量限制（Budgets）**
   * **單次會話預算**：如 `claude -p --max-budget-usd <amount>` 可以硬性限制單次無人值守任務的 Token 消耗上限，達到額度自動中斷 [41]。
   * **Request 頻率限制**：限制單一會話中工具呼叫與請求的最大頻率，防範盲目執行的自動化任務在短時間內刷出天價帳單 [42]。
2. **硬體層資源配額 (Quota)**
   * 在 Docker 與 Micro-VM（Gondolin、Clawk）沙盒模式下，系統管理員能直接限制分配給該執行容器的 CPU Core 數、Memory 大小、以及 I/O 頻寬 [43]。
   * 即使 AI 代理人被惡意代碼植入挖礦程式或發起 Fork 炸彈（Fork Bomb），也無法拖垮開發者主機。

---

### 五、 沙盒隔離策略橫向比較矩陣

| 隔離維度 / 方案項目 | OS 級進程沙盒 (Seatbelt/Bubblewrap) [9, 44] | 輕量化 Micro-VM (Gondolin/Clawk) [13, 16] | Docker 容器沙盒 [19, 43] |
| :--- | :--- | :--- | :--- |
| **進程隔離強度** | **中等**：共享主機核心，僅透過 Namespace/Seatbelt 進行限制 [5, 45]。 | **極高**：獨立核心，強硬的 Hypervisor/VM 虛擬化邊界 [46, 47]。 | **高**：共享核心，透過 cgroups 與 namespaces 隔離，存在容器逃逸風險。 |
| **檔案系統邊界** | 唯讀掛載 `/`，可配置 `denyRead` 與 CWD 讀寫限制 [21, 22]。 | CWD 透過 FUSE/RealFS 掛載至 VM 的 `/workspace` [13, 48]。 | 檔案系統完全隔離，僅能存取 Bind Mount 目錄，Root 可設為唯讀 [19, 43]。 |
| **憑證與環境變數** | 支援 `credentials` 的 `deny` 與動態 `mask`（動態動能替換） [37, 38]。 | 憑證完全保留在宿主機（Host）上，VM 內無敏感 Token [46, 49]。 | API 變數需注入容器，若無 Proxy，Token 容易被 Exfiltrate [49, 50]。 |
| **網路出口控制** | 強制 Egress Proxy，支援 Domain Allowlist 及 TLS 終止 [30, 31, 34]。 | VM 內部網路出口可完全切斷，或透過宿主代理轉接 [46, 51]。 | 可配置 `--network=none` 或容器級出口 Proxy [19, 43]。 |
| **系統效能損耗** | **極低**：基本無感，啟動延遲小於 4ms [6]。 | **低**：QEMU 啟動小於 1s，但對大檔案檢索（Grep）會有 FUSE 轉接損耗 [13, 47]。 | **中等**：需要拉取鏡像及容器生命週期管理的開銷 [52]。 |
| **平台相容性** | 僅支援 macOS, Linux, WSL2 (不支援原生 Windows) [53, 54]。 | 支援 macOS 與 Linux (需安裝 QEMU) [55]。 | 支援所有裝有 Docker Daemon 的平台（多用於 CI/CD） [56]。 |

---

💡 **架構落地建議**：
* **日常本地互動開發**：建議開啟 **OS 級進程沙盒**（如 `claude /sandbox`），配合精細的 `settings.json` allow/deny 規則，能在保障安全的前提下減少 84% 的審批中斷 [44, 52, 57]。
* **執行未受信任、外來 Repositories 或 unattended（無人值守）自動化迴圈**：強烈建議採用 **Micro-VM 隔離（如 Gondolin）**，將所有執行指令與寫入完全圈禁在 VM 中，確保實體主機、SSH 私鑰及 cloud 憑證獲得百分之百的安全保障 [29, 46, 49]。

**引用來源：**
- [1] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [2] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [3] [[Admantium 部落格] 技術解析 - Claude Code 命令列工具診斷、使用者權限控制架構](https://admantium.com/blog/claude05_diagnostics_and_permissions/)
- [4] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [5] [[Firecrawl 專題] 比較分析 - Claude Code 與 Codex 的功能、效率與沙盒安全性評估](https://www.firecrawl.dev/blog/claude-code-vs-codex)
- [6] [[Hacker News] 開源軟體 - Clawk：為 AI 程式代理人提供拋棄式 Linux 虛擬機器防禦方案](https://news.ycombinator.com/item?id=48892859)
- [7] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [8] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [9] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [10] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [11] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [12] [[Reddit 社群] 技術討論 - 如何以最安全的方式執行 Pi Agent (Docker 與本機隔離策略)](https://www.reddit.com/r/PiCodingAgent/comments/1u6ovco/secure_way_of_running_pi/)
- [13] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [14] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [15] [[Reddit 社群] 技術討論 - 如何以最安全的方式執行 Pi Agent (Docker 與本機隔離策略)](https://www.reddit.com/r/PiCodingAgent/comments/1u6ovco/secure_way_of_running_pi/)
- [16] [[Hacker News] 開源軟體 - Clawk：為 AI 程式代理人提供拋棄式 Linux 虛擬機器防禦方案](https://news.ycombinator.com/item?id=48892859)
- [17] [[Pi GitHub] 技術文件 - Pi Coding Agent 容器化 (Containerization) 與沙盒隔離部署手冊](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/containerization.md)
- [18] [[GitHub 開源] 技術文件 - wave 專案底層沙盒環境 (Sandbox) 架構與快速部署指南](https://github.com/re-cinq/wave/blob/main/docs/guides/sandbox-setup.md)
- [19] [[GitHub 開源] 技術文件 - wave 專案底層沙盒環境 (Sandbox) 架構與快速部署指南](https://github.com/re-cinq/wave/blob/main/docs/guides/sandbox-setup.md)
- [20] [[VS Code 官方] 技術指南 - VS Code 整合式環境的 AI 安全防護與模型防線](https://code.visualstudio.com/docs/agents/run/security)
- [21] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [22] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [23] [[GitHub 開源] 技術文件 - wave 專案底層沙盒環境 (Sandbox) 架構與快速部署指南](https://github.com/re-cinq/wave/blob/main/docs/guides/sandbox-setup.md)
- [24] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [25] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [26] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [27] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [28] [[Fastio 專題] 資源目錄 - 2026 全球 AI 代理人、MCP 工具與安全機制文獻存檔](https://fast.io/resources/archive/4/)
- [29] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [30] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [31] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [32] [[Anthropic 官方] 技術文件 - Claude Code 安全防護架構、終端機限制與本地檔案防線](https://code.claude.com/docs/en/security)
- [33] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [34] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [35] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [36] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [37] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [38] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [39] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [40] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [41] [[部落格] 技術指南 - 運維視角下的 Claude Code 部署、權限管理與安全沙盒管理](https://hidekazu-konishi.com/entry/claude_code_operators_handbook.html)
- [42] [[VS Code 官方] 技術指南 - VS Code 整合式環境的 AI 安全防護與模型防線](https://code.visualstudio.com/docs/agents/run/security)
- [43] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [44] [[OCDevel 播客] 架構解說 - Claude Code 組成要素與底層 Sandbox 執行沙盒機制](https://ocdevel.com/mlg/mla-23)
- [45] [[Firecrawl 專題] 比較分析 - Claude Code 與 Codex 的功能、效率與沙盒安全性評估](https://www.firecrawl.dev/blog/claude-code-vs-codex)
- [46] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [47] [[Hacker News] 開源軟體 - Clawk：為 AI 程式代理人提供拋棄式 Linux 虛擬機器防禦方案](https://news.ycombinator.com/item?id=48892859)
- [48] [[Pi GitHub] 技術文件 - Pi Coding Agent 容器化 (Containerization) 與沙盒隔離部署手冊](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/containerization.md)
- [49] [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [50] [[Hacker News] 開源軟體 - Clawk：為 AI 程式代理人提供拋棄式 Linux 虛擬機器防禦方案](https://news.ycombinator.com/item?id=48892859)
- [51] [[Reddit 社群] 技術討論 - 如何以最安全的方式執行 Pi Agent (Docker 與本機隔離策略)](https://www.reddit.com/r/PiCodingAgent/comments/1u6ovco/secure_way_of_running_pi/)
- [52] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [53] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [54] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [55] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [56] [[GitHub 開源] 技術文件 - wave 專案底層沙盒環境 (Sandbox) 架構與快速部署指南](https://github.com/re-cinq/wave/blob/main/docs/guides/sandbox-setup.md)
- [57] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)

### 4. 現有 Skill 生態系（Claude Code Skills / Copilot Extensions / Pi Extensions）的安全機制比較？各自的審查流程與信任模型？

當前自主式 AI 代理人（AI Agent）已從單純的聊天問答演進為具備主動執行能力的系統。這類代理人技能（Skills / Extensions / Plugins）由於具備讀寫檔案、執行命令、呼叫 API 等特權 [1, 2]，使系統面臨全新的安全挑戰 [3-5]。

以下針對目前主流的 AI 代理人技能生態系——**Claude Code (Anthropic)**、**GitHub Copilot (MCP 協定)** 以及 **Pi Coding Agent (Earendil)**，就其安全機制、審查流程與信任模型進行深度的比較與剖析。

---

### 一、 安全機制比較 (Security Mechanisms)

#### 1. Claude Code
Claude Code 的安全架構建立在應用層與作業系統層的**多層次控制疊疊（Layered Control Stack）**之上 [6-8]：
*   **權限控制層（settings.json）**：提供細粒度的 Allow、Ask、Deny 規則比對 [9-11]。評估順序硬性規定為 **Deny ➜ Ask ➜ Allow** [12]。規則可針對特定工具參數進行標量比對（如限制特定的 Bash 指令或 Git 前綴） [13]。
*   **可程式化鉤子（Hooks）**：允許開發者編寫 `PreToolUse`、`PermissionRequest` 等 Hook 腳本 [12, 14, 15]。在工具執行前進行確定性的過濾，若 Hook 返回 `exit 2` 將直接阻斷執行，並可提供錯誤回饋供模型進行自癒 [12, 16, 17]。
*   **環境隔離（Sandboxing）**：執行 `/sandbox` 可在作業系統底層對 Bash 工具及其子進程實施隔離 [15, 18, 19]。macOS 採用內建的 **Apple Seatbelt** (`sandbox-exec`)，Linux 與 WSL2 則基於 **Bubblewrap** (`bwrap`) 進行 Namespace 隔離與唯讀掛載 `/` [18-20]。
*   **環境變數與憑證防護（Credentials Masking）**：支援 `mode: mask`，使沙盒內進程僅能讀取 Sentinel Value（哨兵虛擬值），只有當流量通過 Sandbox Egress Proxy（沙盒網路代理）前往已授權域名時，才會動態替換為實體 Secret，防止 AI 讀取或在日誌中洩漏明文金鑰 [21-23]。

#### 2. GitHub Copilot
GitHub 於 2025 年 11 月 10 日正式停用並下架了早期基於 GitHub App 的舊版 Copilot Extensions [24-26]，全面過渡至 **Model Context Protocol (MCP)** 開源協定 [26-28]：
*   **客戶端-伺服器解耦（Host-Server Separation）**：Copilot 作為 Host 端（客戶端），透過本地 `mcp.json` 配置或遠端連線，與獨立運作的 MCP 伺服器進行 JSON-RPC 2.0 通訊 [29-31]。工具的實體執行權被侷限在 MCP 伺服器所在的獨立環境或進程中 [29, 30]。
*   **安全認證（OAuth 2.1 + PKCE）**：遠端 MCP 連線採用業界標準的 OAuth 2.1 與 PKCE 流程 [32-34]。VS Code 的安全憑證存儲區（Secure Secrets Store）負責保管敏感的 API 金鑰與 Token，杜絕直接將 PAT 憑證傳遞給未受信任伺服器的混淆代理（Confused Deputy）與帳號接管風險 [32, 34]。
*   **唯讀限制（Read-Only Scope）**：在 2026 年 7 月 GA 的 Copilot Code Review 功能中，GitHub 實施了極為嚴格的**唯讀約束**：審查代理人可以使用 MCP 連線拉取 issue、文件與服務目錄資訊，但**所有工具呼叫皆被硬性限制為唯讀**，無法對外部系統發起任何寫入或變更（Mutation） [35-37]。

#### 3. Pi Coding Agent
Pi 採取了與上述兩者截然不同的「極簡、可自訂」哲學 [38, 39]：
*   **無內建安全沙盒**：Pi 預設**沒有內建任何安全沙盒或預設的權限過濾機制** [40-42]。它直接以啟動該進程的本機用戶帳戶與權限運行 [41, 43, 44]。Zechner（創始人）的設計理念認為，一味堆疊漏洞百出的偽沙盒只會帶來安全幻覺，真正的隔離應留給底层的硬性沙盒與虛擬化技術 [41, 43, 45]。
*   **社群擴充權限層**：雖然 core 沒有權限防線，但 Pi 擁有極強的 TypeScript 擴充機制 [46, 47]。社群與官方目前主流使用 **`@gotgenes/pi-permission-system`** 擴充（分支自 `pi-permission-layers`） [48-50]：
    *   在代理人啟動前（`before_agent_start`）自動掃描並在系統提示詞中**隱藏未授權工具**，防範模型反覆嘗試而被阻斷 [49, 51, 52]；
    *   實施與 OpenCode 規格高度相容的 allow/ask/deny 工具與 Bash 規則比對 [53, 54]；
    *   採用路徑 Canonical 解析，防範利用符號連結（Symlink）繞過工作目錄邊界的 Path Traversal 攻擊 [49, 52, 55]。
*   **微型虛擬機隔離（Gondolin）**：對於不信任的倉庫，Pi 官方強烈建議搭配 **`pi-gondolin` 擴充套件** [56-58]。它會在 session 啟動時於 1 秒內開機一個輕量化的 QEMU Linux 微型 VM，並透過 FUSE 把工作目錄掛載為 `/workspace` [59, 60]。AI 所執行的所有 Bash 指令和讀寫，都會被物理圈禁在 VM 中，確保實體主機的 SSH 密鑰與敏感環境變數不受威脅 [56, 59, 61]。

---

### 二、 審查與分發流程比較 (Review & Distribution Processes)

#### 1. Claude Code
*   **去中心化分發**：Claude Code 目前並未建立官方中心化的 Skills 市集。Skills 主要是存放在專案本地目錄（如 `.claude/skills/`）或全域目錄（`~/.claude/skills/`）的 `SKILL.md` 的 Markdown 檔案夾，內含 YAML 元數據與選用腳本 [15, 62]。
*   **無官方安全審批**：Anthropic 官方對社群或第三方開發的 Skills **不進行任何安全性審查或靜態掃描** [63]。即使是官方 Directory 的 Connectors，也僅是「上架合規審查」而非安全審計 [63]。
*   **自主審批責任**：安裝任何第三方技能包前，安全責任完全在於使用者 [64-66]。使用者必須逐一手動檢視 `SKILL.md`、`allowed-tools` 配置，以及是否夾帶了 inline shell 指令（`!`）或危險的 package 安裝指令 [62, 64, 66]。

#### 2. GitHub Copilot
*   **雙層註冊與 curation 審查**：
    *   **官方/特約 Extensions**：透過 GitHub Marketplace 進行分發，需要通過官方人工合規性與安全性 vetting，且要求發布者提供加密簽章（Signed Extensions） [67-69]。
    *   **GitHub MCP Registry**：GitHub 官方維護一個 curated 的 MCP 伺服器目錄（github.com/mcp） [70-72]。雖然 registry 已支援與開源 MCP 註冊表自動同步版本，但引入全新的 MCP 伺服器仍需要**人工手動審查與 curation 准入**（Manual Onboarding Process） [70, 73]。
*   **企業私有註冊表（Enterprise Registry）**：為了保障合規，企業可以部署私有的 MCP 服務註冊表，不向公網開放，僅允許連線經過內部安全團隊審核、硬性授權的內部 MCP 服務 [74-76]。

#### 3. Pi Coding Agent
*   **完全去中心化的 npm/git 生態**：Pi 技能（Extensions & Packages）直接透過 npm 公共倉庫（如安裝 `npm:pi-subagents`）或直接克隆 Git 倉庫來分發與載入 [53, 77-79]。
*   **零官方干預與零審查**：Pi 官方不對 npm 目錄上的 Pi Packages 進行任何審查、測試或封鎖 [80-82]。Pi 套件運作權限極高（能載入 TypeScript 程式碼在主機直接執行），因此 Earendil 官方在文件與 SECURITY.md 中反覆警告，**不可隨意安裝來自陌生人的 Pi 套件，版本升級時應當作 application code 變更一樣進行嚴格的 diff 程式碼審查** [53, 78, 83]。

---

### 三、 信任模型比較 (Trust Models)

#### 1. Claude Code
*   **首度載入的 codebase 信任機制（Trust Verification）**：當首次在某個新專案目錄下啟動 Claude Code，或者偵測到新的 MCP 伺服器時，系統會彈出信任確認提示（Trust Prompt） [84, 85]。如果拒絕信任，Claude 僅會以 restricted 唯讀模式運行 [20, 86]。
*   **Auto 模式下的模型分類器信任（Classifier-Assisted Trust）**：在 auto 模式下，Claude Code 引入了**二階審查模型（Classifier）** [20, 87]。它不完全依賴規則引擎，而是將 AI 即將執行的工具呼叫與當前上下文送往一個獨立、輕量的安全分類器模型 [20, 87, 88]。分類器會自主判斷該操作是否具有破壞性（如無害的唯讀 git status vs 破壞性的 rm -rf），進而自動核准或阻斷 [87, 89]。然而，Anthropic 官方亦強調這並非絕對物理屏障，惡意專案檔（如 RCE 漏洞 CVE-2026-21852）仍有可能在 trust 彈出前透過 hooks 提權 [84, 90, 91]。

#### 2. GitHub Copilot
*   **企業治理與 RBAC 信任模型**：GitHub Copilot 採用了集中式的治理模型（Managed Settings） [92]。企業組織管理員擁有絕對的控制權，可以在 Settings 頁面中，以組織維度為所有的開發者終端機全域強制啟用、停用或限制特定 MCP 伺服器與網域（Allowlist） [37, 72, 92]。
*   **不相信工具端（Zero PAT Passthrough）**：不允許客戶端直接傳遞個人存取 Token（PAT）給工具，所有的權限與身分完全透過 OAuth 2.1 作用域（Scopes）與同意畫面（User Consent Display）來約束 [32, 34]。開發者可以清晰看到 MCP 伺服器索取的具體權限（如 read-only），杜絕了工具越權的「混淆代理」漏洞 [32, 34]。

#### 3. Pi Coding Agent
*   **強硬的項目信任門禁（Project Trust Gate）**：Pi 建立了一個嚴格的 input-loading guard [45, 56]：
    *   只要專案目錄中含有本地配置（`.pi/settings.json`）、本地擴充（`.pi/extensions`）、自訂提示詞（`SYSTEM.md`）或 skills 時，Pi 在**未獲得明確的 Project Trust 授權前，絕對不會載入這些專案資源** [93-95]。
    *   這成功阻斷了攻擊者透過惡意 codebase 靜態檔案（如克隆即觸發惡意 hooks 提權）的攻擊路徑 [45, 93, 96]。
*   **不相信「安全劇場（Security Theatre）」**：Pi 承認「只要 AI 被賦予程式碼寫入與執行特權，任何純軟體層的攔截與彈窗最終都能被 Prompt Injection 繞過」 [43, 45]。因此它的終極信任模型是——**「不信任主機」**。Pi 預設把對外資料流程、LLM token 流向、檔案系統掛載與網路控制，完全委託給外部物理容器（如 Docker, OpenShell, macOS sandbox-exec 等）進行硬隔離 [41, 61, 97]。

---

### 四、 三大生態系橫向對比矩陣

| 比較維度 / 生態系 | Claude Code Skills [10, 32] | GitHub Copilot Extensions (MCP) [28, 29] | Pi Coding Agent Extensions [40, 98] |
| :--- | :--- | :--- | :--- |
| **定義格式** | Markdown + YAML YAML Frontmatter [62, 99] | TypeScript / JSON-RPC 2.0 (MCP 規格) [29, 30, 100] | TypeScript Modules / manifest JSON [46, 53] |
| **核心安全邊界** | **應用程式層控制**：settings.json 規則 [12, 20] | **通訊規格與身分層隔離** [30, 32] | **無內建邊界**：預設具備啟動帳戶之完整權限 [41, 42] |
| **沙盒隔離技術** | macOS (Seatbelt); Linux/WSL (Bubblewrap) [19, 20] | 外部 MCP 伺服器之虛擬化/容器隔離（如 Docker） [32, 101] | 物理掛載隔離：支援 Gondolin (QEMU Micro-VM) [59, 61] |
| **權限限制維度** | Allow / Ask / Deny 規則過濾、PreToolUse Hooks [12, 15] | 唯讀連線限制、OAuth Scopes 劃分、組織 Allowlist [32, 35, 72] | 無（社群 `@gotgenes/pi-permission-system` 提供過濾） [41, 49] |
| **審查流程** | 無中心化市集，使用者負全責，須手動檢視 SKILL.md [63, 64, 66] | **雙層機制**：Marketplace  vetting + MCP Registry 手動 curation [68, 70, 73] | 完全去中心化，無官方安全審核，須自主對 npm 包進行程式碼 diff [80, 81, 83] |
| **信任機制基礎** | 專案首次 trust、Auto 模式 Classifier 分類器判斷 [85, 87] | 企業全域政策（Managed Settings）、OAuth User Consent [32, 34, 92] | 項目信任門禁（Project Trust Gate） [56, 94, 95] |
| **憑證防護模式** | 支援 mask (Sentinel) 哨兵與 Egress Proxy [21, 22] | VS Code Secure secrets store 保管 [32] | 本地 `auth.json`，支援 OpenShell 憑證外部代理代理 [102, 103] |
| **最適合場景** | 需要開箱即用、具備豐富 hooks 進行高頻互動的本地開發 [2, 20] | 企業內部已有現成 API 與 OAuth 架構，需高規格合規管控與審查 [37, 104] | 經驗豐富的架構師/平台團隊構建高度客製化的私有/離線開發環境 [105-107] |

---

💡 **接下來，您是否想要我們協助：**
1. 針對您目前的開發環境（如 Claude Code 或 Pi），提供一份基準安全配置 `.jsonc` 文件，以實施精細的 `settings.json` 權限 Allow/Deny 過濾？
2. 或者是深入探討如何配置本機的 Gondolin Micro-VM 隔離環境？

**引用來源：**
- [1] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [2] [[部落格] 技術指南 - 運維視角下的 Claude Code 部署、權限管理與安全沙盒管理](https://hidekazu-konishi.com/entry/claude_code_operators_handbook.html)
- [3] [[安全部落格] 漏洞剖析 - 利用惡意專案檔對 Claude Code 實施 RCE 與 API 密鑰竊取攻擊](https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/)
- [4] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [5] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [6] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [7] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [8] [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [9] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [10] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [11] [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [12] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [13] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [14] [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [15] [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [16] [[OCDevel 播客] 架構解說 - Claude Code 組成要素與底層 Sandbox 執行沙盒機制](https://ocdevel.com/mlg/mla-23)
- [17] [[部落格] 開發實務 - Claude Code 代理人運作實務指南與環境隔離考量](https://schoettler.io/en/blog/claude-code-agenten)
- [18] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [19] [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [20] [[Firecrawl 專題] 比較分析 - Claude Code 與 Codex 的功能、效率與沙盒安全性評估](https://www.firecrawl.dev/blog/claude-code-vs-codex)
- [21] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [22] [[Penligent 專題] 安全報告 - 超越對話框：AI Agent 被越獄 (Jailbreak) 後的自動化實體威脅](https://www.penligent.ai/hackinglabs/claude-jailbreak/)
- [23] [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [24] [[Fastio 部落格] 趨勢分析 - GitHub Copilot 擴充生態系全面向 Model Context Protocol (MCP) 的轉型](https://fast.io/resources/github-copilot-trending-extensions/)
- [25] [[Fastio 部落格] 趨勢分析 - GitHub Copilot 擴充生態系全面向 Model Context Protocol (MCP) 的轉型](https://fast.io/resources/github-copilot-trending-extensions/)
- [26] [[GitHub 官方] 停用公告 - 淘汰基於 GitHub App 的 Copilot Extensions 及全面過渡至 MCP 協議](https://github.blog/changelog/2025-09-24-deprecate-github-copilot-extensions-github-apps/)
- [27] [[Fastio 部落格] 趨勢分析 - GitHub Copilot 擴充生態系全面向 Model Context Protocol (MCP) 的轉型](https://fast.io/resources/github-copilot-trending-extensions/)
- [28] [[ZigiWave 部落格] 技術趨勢 - GitHub Copilot 2026：代理人模式 (Agent Mode) 與 MCP 協定整合](https://zigiwave.com/resources/github-copilot-features)
- [29] [[Fastio 部落格] 趨勢分析 - GitHub Copilot 擴充生態系全面向 Model Context Protocol (MCP) 的轉型](https://fast.io/resources/github-copilot-trending-extensions/)
- [30] [[Fastio 部落格] 趨勢分析 - GitHub Copilot 擴充生態系全面向 Model Context Protocol (MCP) 的轉型](https://fast.io/resources/github-copilot-trending-extensions/)
- [31] [[Fastio 部落格] 趨勢分析 - GitHub Copilot 擴充生態系全面向 Model Context Protocol (MCP) 的轉型](https://fast.io/resources/github-copilot-trending-extensions/)
- [32] [[VS Code 官方] 技術指南 - VS Code 整合式環境的 AI 安全防護與模型防線](https://code.visualstudio.com/docs/agents/run/security)
- [33] [[MCP GitHub] 社群討論 - MCP 伺服器發布至官方 Registry 出現不可見問題之機制與流程](https://github.com/github/github-mcp-server/discussions/1257)
- [34] [[安全部落格] 漏洞剖析 - 當 MCP 遇上 OAuth：因授權設計缺陷導致一鍵接管帳號之安全漏洞](https://www.obsidiansecurity.com/blog/when-mcp-meets-oauth-common-pitfalls-leading-to-one-click-account-takeover)
- [35] [[Digital Applied 專題] 技術新聞 - 基於 MCP 協定的 Copilot Code Review 正式推出與安全審查應用](https://www.digitalapplied.com/blog/mcp-adoption-week-copilot-code-review-ga)
- [36] [[Digital Applied 專題] 技術新聞 - 基於 MCP 協定的 Copilot Code Review 正式推出與安全審查應用](https://www.digitalapplied.com/blog/mcp-adoption-week-copilot-code-review-ga)
- [37] [[Digital Applied 專題] 技術新聞 - 基於 MCP 協定的 Copilot Code Review 正式推出與安全審查應用](https://www.digitalapplied.com/blog/mcp-adoption-week-copilot-code-review-ga)
- [38] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [39] [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [40] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [41] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [42] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [43] [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [44] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [45] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [46] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [47] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [48] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [49] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [50] [[Pi 官方] 開源軟體 - pi-permission-layers 多層次安全存取控管模型軟體包](https://pi.dev/packages/pi-permission-layers)
- [51] [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [52] [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [53] [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [54] [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [55] [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [56] [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [57] [[Reddit 社群] 技術討論 - 如何以最安全的方式執行 Pi Agent (Docker 與本機隔離策略)](https://www.reddit.com/r/PiCodingAgent/comments/1u6ovco/secure_way_of_running_pi/)
- [58] [[Pi GitHub] 技術文件 - Pi Coding Agent 容器化 (Containerization) 與沙盒隔離部署手冊](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/containerization.md)
- [59] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [60] [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [61] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [62] [[Snyk 專題] 資源目錄 - 九款用於網路安全檢測、駭客攻防與漏洞掃描的 Claude 技能推薦](https://snyk.io/articles/top-claude-skills-cybersecurity-hacking-vulnerability-scanning/)
- [63] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [64] [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [65] [[Anthropic 官方] 技術文件 - Claude Code 安全防護架構、終端機限制與本地檔案防線](https://code.claude.com/docs/en/security)
- [66] [[安全部落格] 漏洞剖析 - 利用惡意技能與第三方 Agent 危害 Claude Code 安全性 (第一部分)](https://labs.reversec.com/posts/2026/05/skill-issues-compromising-claude-code-with-malicious-skills-agents-part-1)
- [67] [[GitHub 官方] 社群討論 - Copilot CLI 社群插件擴充 API 設計與安全限制提案](https://github.com/github/copilot-cli/issues/1017)
- [68] [[GitHub 官方] 社群討論 - Copilot CLI 社群插件擴充 API 設計與安全限制提案](https://github.com/github/copilot-cli/issues/1017)
- [69] [[GitHub 官方] 社群討論 - Copilot CLI 社群插件擴充 API 設計與安全限制提案](https://github.com/github/copilot-cli/issues/1017)
- [70] [[MCP GitHub] 社群討論 - MCP 伺服器發布至官方 Registry 出現不可見問題之機制與流程](https://github.com/github/github-mcp-server/discussions/1257)
- [71] [[MCP GitHub] 社群討論 - MCP 伺服器發布至官方 Registry 出現不可見問題之機制與流程](https://github.com/github/github-mcp-server/discussions/1257)
- [72] [[GitHub 官方] 停用公告 - 淘汰基於 GitHub App 的 Copilot Extensions 及全面過渡至 MCP 協議](https://github.blog/changelog/2025-09-24-deprecate-github-copilot-extensions-github-apps/)
- [73] [[MCP GitHub] 社群討論 - MCP 伺服器發布至官方 Registry 出現不可見問題之機制與流程](https://github.com/github/github-mcp-server/discussions/1257)
- [74] [[OCDevel 播客] 架構解說 - Claude Code 組成要素與底層 Sandbox 執行沙盒機制](https://ocdevel.com/mlg/mla-23)
- [75] [[MCP GitHub] 社群討論 - MCP Server 官方中心化與去中心化註冊表安全審查機制規劃](https://github.com/orgs/modelcontextprotocol/discussions/159)
- [76] [[MCP GitHub] 社群討論 - MCP Server 官方中心化與去中心化註冊表安全審查機制規劃](https://github.com/orgs/modelcontextprotocol/discussions/159)
- [77] [[Pi 官方] 資源目錄 - Pi Coding Agent 套件市集與第三方 Extensions 資源清單](https://pi.dev/packages)
- [78] [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [79] [[安全機構] 評測報告 - 實測 Pi Coding Agent 安全邊界與沙盒隔離機制](https://petronellatech.com/blog/pi-dev-platform-review/)
- [80] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [81] [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [82] [[Pi GitHub] 安全公告 - Pi Coding Agent 漏洞回報機制、核心安全性承諾與聲明](https://github.com/earendil-works/pi/blob/main/SECURITY.md)
- [83] [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [84] [[安全部落格] 漏洞剖析 - 利用惡意專案檔對 Claude Code 實施 RCE 與 API 密鑰竊取攻擊](https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/)
- [85] [[Anthropic 官方] 技術文件 - Claude Code 安全防護架構、終端機限制與本地檔案防線](https://code.claude.com/docs/en/security)
- [86] [[部落格] 技術指南 - 運維視角下的 Claude Code 部署、權限管理與安全沙盒管理](https://hidekazu-konishi.com/entry/claude_code_operators_handbook.html)
- [87] [[Anthropic 官方] 配置指南 - Claude Code 權限模式選擇：Auto / Notify / Strict 詳解](https://code.claude.com/docs/en/permission-modes)
- [88] [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [89] [[Anthropic 官方] 技術文件 - Claude Code 安全防護架構、終端機限制與本地檔案防線](https://code.claude.com/docs/en/security)
- [90] [[MintMCP 部落格] 安全專題 - 企業如何因應 Claude Code 嚴重漏洞與供應鏈安全防禦](https://www.mintmcp.com/blog/claude-code-cve)
- [91] [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [92] [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [93] [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [94] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [95] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [96] [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [97] [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [98] [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [99] [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [100] [[GitHub 官方] 開源軟體 - Copilot Extensions 開發套件的請求驗證與安全防護 API](https://github.com/copilot-extensions/preview-sdk.js/)
- [101] [[GitHub 開源] 安全建議 - 2025 最新 Model Context Protocol 實作之安全性最佳實踐指引](https://github.com/microsoft/mcp-for-beginners/blob/main/02-Security/mcp-security-best-practices-2025.md)
- [102] [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [103] [[Pi GitHub] 技術文件 - Pi Coding Agent 容器化 (Containerization) 與沙盒隔離部署手冊](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/containerization.md)
- [104] [[技術部落格] 深度分析 - GitHub Copilot 擴充架構與 Cursor 原生功能在安全性與整合度上的比較](https://emasterlabs.com/github-copilot-extensions-vs-cursor)
- [105] [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [106] [[安全機構] 評測報告 - 實測 Pi Coding Agent 安全邊界與沙盒隔離機制](https://petronellatech.com/blog/pi-dev-platform-review/)
- [107] [[安全機構] 評測報告 - 實測 Pi Coding Agent 安全邊界與沙盒隔離機制](https://petronellatech.com/blog/pi-dev-platform-review/)

## 已移除的來源（品質過濾）

- GitHub Copilot - Microsoft Azure — 此為微軟 Azure 的產品功能介紹首頁與常見問答 (FAQ) 行銷頁面，主要用於宣傳產品功能，缺乏實質的技術實作或安全分析細節。
- Announcing Telerik & Kendo UI AI Coding Assistants — 此為產品發布行銷頁面，且內容提及的 GitHub Copilot 擴充功能已於 2025 年 11 月被微軟正式停用並下架，Progress 亦已廢棄該擴充，屬於過期且無實質技術參考價值的推廣資訊。
- Models disappeared after VS Code update — GitHub Education account · community · Discussion #189398 — 此為簡短的社群問答，內容僅圍繞學生免費方案中模型消失的政策調整與基本重啟軟體排解，缺乏具體技術細節與安全研究價值。
- Pi-OS: an "operating system" managed by the Pi Coding Agent : r/PiCodingAgent - Reddit — 此為社群論壇上的趣味分享心得，作者自承該專案「完全無用 (completely useless)」，僅作為好玩展示，無具體實質的技術深度或安全論據支撐。
- GitHub Copilot Extension Developer Policy — 此開發者政策頁面針對已於 2025 年 11 月 10 日起完全停用且下架的 GitHub App-based Copilot Extensions，屬於已被 Model Context Protocol (MCP) 後續更新取代的過期資訊。
- What copilot canvas extensions y'all built · community · Discussion #197856 - GitHub — 此為極其簡短且無結論的社群問答，提問未獲其他用戶實質回答，僅有機器人/官方回覆的 boilerplate 連結，不具任何實質技術細節。

## 參考來源清單

- [[Admantium 部落格] 技術解析 - Claude Code 命令列工具診斷、使用者權限控制架構](https://admantium.com/blog/claude05_diagnostics_and_permissions/)
- [[Adversa AI 專題] 安全報告 - OpenClaw 漏洞 CVE-2026-25253、憑證外洩與系統加固指南](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)
- [[Agensi 部落格] 技術指南 - 適用於 Claude Code 的安全稽核與程式碼審查 Skill](https://www.agensi.io/learn/best-code-review-skills-claude-code)
- [[Anthropic GitHub] 社群討論 - SKILL.md 中 allowed-tools 控制在 CLI 與 SDK 之間的重大不一致問題](https://github.com/anthropics/claude-code/issues/18737)
- [[Anthropic GitHub] 社群討論 - allowed-tools 權限限制未強制執行的 Bug 追蹤](https://github.com/anthropics/claude-code/issues/18837)
- [[Anthropic 官方] 使用指南 - Claude Code CLI 程式碼安全自動審查與合規流程](https://support.claude.com/en/articles/11932705-automated-security-reviews-in-claude-code)
- [[Anthropic 官方] 安全公告 - 將前沿資安防禦與漏洞挖掘技能開放予防守方的政策與理念](https://www.anthropic.com/news/claude-code-security)
- [[Anthropic 官方] 技術文件 - Claude Code 安全防護架構、終端機限制與本地檔案防線](https://code.claude.com/docs/en/security)
- [[Anthropic 官方] 更新日誌 - Claude Code 安全改進與功能更新歷史](https://code.claude.com/docs/en/changelog)
- [[Anthropic 官方] 系統規格 - Claude Platform 的 Agent Skill 定義與基礎安全架構](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [[Anthropic 官方] 系統配置 - Claude Code 操作指令與 MCP 權限清單設定](https://code.claude.com/docs/en/permissions)
- [[Anthropic 官方] 系統配置 - 於 Claude Code 中配置受限沙盒的 Bash 工具執行環境](https://code.claude.com/docs/en/sandboxing)
- [[Anthropic 官方] 配置指南 - Claude Code 權限模式選擇：Auto / Notify / Strict 詳解](https://code.claude.com/docs/en/permission-modes)
- [[Arm 官方] 新聞稿 - GitHub Copilot 全新 Arm 擴充：協助無縫安全遷移至 Arm 架構](https://newsroom.arm.com/news/arm-extension-github-copilot)
- [[Checkmarx 專題] 安全報告 - 2026 GitHub Copilot 遭遇的五大安全威脅及修補方案](https://checkmarx.com/learn/ai-security/top-5-github-copilot-security-risks-9-ways-to-mitigate-them/)
- [[Checkmarx 專題] 安全報告 - Claude Code 核心安全威脅、控制措施與企業最佳實踐](https://checkmarx.com/learn/ai-security/claude-code-security-top-6-risks-controls-and-best-practices/)
- [[CodeToByte] 開發實務 - 撰寫自訂 GitHub Copilot 代理人的核心 API 與身分驗證指南](https://www.codetobyte.com/posts/writing-custom-agents-github-copilot)
- [[Composio 專題] 資源目錄 - 十款提升開發效率與環境整合的 Pi Agent 推薦擴充](https://composio.dev/content/top-pi-extensions)
- [[DataCamp 專題] 教學指南 - Claude Code 安全指引：MCP、權限機制與 Sandbox 隔離](https://www.datacamp.com/tutorial/claude-code-security)
- [[Digital Applied 專題] 技術新聞 - 基於 MCP 協定的 Copilot Code Review 正式推出與安全審查應用](https://www.digitalapplied.com/blog/mcp-adoption-week-copilot-code-review-ga)
- [[Fastio 專題] 資源目錄 - 2026 全球 AI 代理人、MCP 工具與安全機制文獻存檔](https://fast.io/resources/archive/4/)
- [[Fastio 部落格] 趨勢分析 - GitHub Copilot 擴充生態系全面向 Model Context Protocol (MCP) 的轉型](https://fast.io/resources/github-copilot-trending-extensions/)
- [[Firecrawl 專題] 比較分析 - Claude Code 與 Codex 的功能、效率與沙盒安全性評估](https://www.firecrawl.dev/blog/claude-code-vs-codex)
- [[GitHub 官方] 企業指南 - 企業引入 GitHub Copilot 安全性評估與合規核准準備材料](https://docs.github.com/enterprise-cloud@latest/copilot/tutorials/roll-out-at-scale/govern-at-scale/resources-for-approval)
- [[GitHub 官方] 使用手冊 - 行動端 GitHub Copilot 安全互動與權限作用域](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/chat-with-copilot/chat-in-mobile)
- [[GitHub 官方] 停用公告 - 淘汰基於 GitHub App 的 Copilot Extensions 及全面過渡至 MCP 協議](https://github.blog/changelog/2025-09-24-deprecate-github-copilot-extensions-github-apps/)
- [[GitHub 官方] 社群討論 - Copilot CLI 社群插件擴充 API 設計與安全限制提案](https://github.com/github/copilot-cli/issues/1017)
- [[GitHub 官方] 系統規格 - GitHub OAuth 授權範圍 (Scopes) 劃分與權限控管機制](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps)
- [[GitHub 官方] 開源軟體 - Copilot Extensions 開發套件的請求驗證與安全防護 API](https://github.com/copilot-extensions/preview-sdk.js/)
- [[GitHub 服務] 產品規格 - AgentAuditKit MCP 安全掃描工具與自動化審查機制](https://github.com/marketplace/actions/agentauditkit-mcp-security-scan)
- [[GitHub 社群] 資源目錄 - GitHub Copilot 開發夥伴、延伸擴充套件推薦清單](https://awesome-copilot.github.com/plugin/partners/)
- [[GitHub 開源] 安全建議 - 2025 最新 Model Context Protocol 實作之安全性最佳實踐指引](https://github.com/microsoft/mcp-for-beginners/blob/main/02-Security/mcp-security-best-practices-2025.md)
- [[GitHub 開源] 技術文件 - wave 專案底層沙盒環境 (Sandbox) 架構與快速部署指南](https://github.com/re-cinq/wave/blob/main/docs/guides/sandbox-setup.md)
- [[GitHub 開源] 漏洞驗證 - Claude Code 資訊洩露漏洞 CVE-2026-21852 概念驗證 POC](https://github.com/atiilla/CVE-2026-21852-PoC)
- [[GitHub 開源] 資源目錄 - OpenClaw 適用於程式代理人與整合開發環境的優秀 Skill 彙整](https://github.com/VoltAgent/awesome-openclaw-skills/blob/main/categories/coding-agents-and-ides.md)
- [[GitHub 開源] 開源專案 - pi-gondolin：用於增強 Pi Agent 檔案操作與通訊之擴充擴展](https://github.com/pasky/pi-gondolin)
- [[GitHub 開源] 開源專案 - 基於 Go 語言的 Copilot 安全代理與 API 憑證轉接服務](https://github.com/teamcoltra/go-copilot-api)
- [[GitHub 開源] 開源軟體 - 基於 Claude Code 的自動化安全審查與程式漏洞分析技能](https://github.com/WorldFlowAI/everything-claude-code/blob/main/skills/security-review/SKILL.md)
- [[Glean 官方] 配置指南 - 於 GitHub Copilot 中配置 Glean 企業搜尋的整合與權限控管](https://docs.glean.com/administration/platform/embedded-integrations/github-copilot-extension-install)
- [[Hacker News] 開源軟體 - Clawk：為 AI 程式代理人提供拋棄式 Linux 虛擬機器防禦方案](https://news.ycombinator.com/item?id=48892859)
- [[HiddenLayer 專題] 安全報告 - Claude Code 擴充技能 (Skills) 隱藏安全風險深度分析](https://www.hiddenlayer.com/research/whats-the-matter-with-skills)
- [[MCP GitHub] 社群討論 - MCP Server 官方中心化與去中心化註冊表安全審查機制規劃](https://github.com/orgs/modelcontextprotocol/discussions/159)
- [[MCP GitHub] 社群討論 - MCP 伺服器發布至官方 Registry 出現不可見問題之機制與流程](https://github.com/github/github-mcp-server/discussions/1257)
- [[MCP 官方] 安全建議 - Model Context Protocol 伺服器與客戶端雙向驗證之安全實務](https://modelcontextprotocol.io/docs/draft/tutorials/security/security_best_practices)
- [[MCP 官方] 安全建議 - Model Context Protocol 授權控制與連線安全考量](https://modelcontextprotocol.io/specification/2026-07-28/basic/authorization/security-considerations)
- [[MCP 官方] 架構解析 - 深入理解 Model Context Protocol 的授權機制與工具調用防線](https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/authorization)
- [[MITRE 官方] 漏洞紀錄 - Claude Code 資訊洩露漏洞 CVE-2026-21852 報告](https://www.cve.org/CVERecord?id=CVE-2026-21852)
- [[MintMCP 部落格] 安全專題 - 企業如何因應 Claude Code 嚴重漏洞與供應鏈安全防禦](https://www.mintmcp.com/blog/claude-code-cve)
- [[NIST 官方] 漏洞紀錄 - CVE-2026-21852 漏洞技術細節與嚴重性評估](https://nvd.nist.gov/vuln/detail/CVE-2026-21852)
- [[OCDevel 播客] 架構解說 - Claude Code 組成要素與底層 Sandbox 執行沙盒機制](https://ocdevel.com/mlg/mla-23)
- [[Penligent 專題] 安全報告 - 超越對話框：AI Agent 被越獄 (Jailbreak) 後的自動化實體威脅](https://www.penligent.ai/hackinglabs/claude-jailbreak/)
- [[Pi GitHub] 安全公告 - Pi Coding Agent 漏洞回報機制、核心安全性承諾與聲明](https://github.com/earendil-works/pi/blob/main/SECURITY.md)
- [[Pi GitHub] 技術文件 - Pi Coding Agent 容器化 (Containerization) 與沙盒隔離部署手冊](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/containerization.md)
- [[Pi 官方] 技術指南 - Pi Coding Agent 擴充套件 (Extensions) 開發架構與規格](https://pi.dev/docs/latest/extensions)
- [[Pi 官方] 技術文件 - Pi Coding Agent 安全模型：本地存取防禦與沙盒組態](https://pi.dev/docs/latest/security)
- [[Pi 官方] 資源目錄 - Pi Coding Agent 套件市集與第三方 Extensions 資源清單](https://pi.dev/packages)
- [[Pi 官方] 開源軟體 - gotgenes 貢獻之 Pi 權限控制架構與套件發布](https://pi.dev/packages/@gotgenes/pi-permission-system)
- [[Pi 官方] 開源軟體 - pi-lab 官方許可與精細權限控制組件軟體包](https://pi.dev/packages/@pi-lab/permissions)
- [[Pi 官方] 開源軟體 - pi-permission-layers 多層次安全存取控管模型軟體包](https://pi.dev/packages/pi-permission-layers)
- [[Pi 官方] 開源軟體 - pi-skill-updates：Pi Agent 的技能安全動態更新與版本控管套件](https://pi.dev/packages/pi-skill-updates)
- [[Port.io 部落格] 架構分析 - 為什麼企業需要建立私有 MCP 服務註冊表 (Registry)](https://www.port.io/blog/mcp-registry)
- [[Red Hat 部落格] 安全專題 - AI Agent 技能安全威脅、漏洞成因與主動控制措施](https://developers.redhat.com/articles/2026/03/10/agent-skills-explore-security-threats-and-controls)
- [[Reddit 社群] 技術討論 - 如何以最安全的方式執行 Pi Agent (Docker 與本機隔離策略)](https://www.reddit.com/r/PiCodingAgent/comments/1u6ovco/secure_way_of_running_pi/)
- [[Reddit 社群] 技術討論 - 社群熱門安全主題 Claude 技能安裝數據與實用度分析](https://www.reddit.com/r/devsecops/comments/1vpi58g/top_7_claude_skills_for_security_by_install_count/)
- [[SentinelOne 專題] 安全報告 - Claude Code 資訊外洩漏洞成因與緩解機制](https://www.sentinelone.com/vulnerability-database/cve-2026-21852/)
- [[SkillsLLM 官方] 資源目錄 - opencode-power-pack 代理人市集技能清單與安全能力規格](https://skillsllm.com/skill/opencode-power-pack)
- [[Snyk 專題] 資源目錄 - 九款用於網路安全檢測、駭客攻防與漏洞掃描的 Claude 技能推薦](https://snyk.io/articles/top-claude-skills-cybersecurity-hacking-vulnerability-scanning/)
- [[VS Code 官方] 技術指南 - VS Code 整合式環境的 AI 安全防護與模型防線](https://code.visualstudio.com/docs/agents/run/security)
- [[Verdent Guides] 使用指南 - Pi 代理人工作流、插件系統配置與執行權限邊界](https://www.verdent.ai/guides/coding/pi-coding-agent)
- [[ZigiWave 部落格] 技術趨勢 - GitHub Copilot 2026：代理人模式 (Agent Mode) 與 MCP 協定整合](https://zigiwave.com/resources/github-copilot-features)
- [[arXiv 學術] 漏洞剖析 - 利用提示注入 (Prompt Injection) 攻擊 AI 程式代理人技能與協議的系統化分析](https://arxiv.org/html/2601.17548v1)
- [[arXiv 學術] 系統架構 - Claude Code 功能、底層原理與潛在安全邊界手冊](https://arxiv.org/html/2608.26742v1)
- [[安全機構] 評測報告 - 實測 Pi Coding Agent 安全邊界與沙盒隔離機制](https://petronellatech.com/blog/pi-dev-platform-review/)
- [[安全部落格] 安全報告 - 馴服 OpenClaw：自主式 AI 代理人面臨的威脅分析與防禦緩解](https://arxiv.org/html/2603.11619v1)
- [[安全部落格] 安全建議 - 保護 Model Context Protocol 身份驗證：金鑰與憑證管理最佳實踐](https://blog.ogwilliam.com/post/mcp-authentication-security-best-practices)
- [[安全部落格] 漏洞剖析 - 利用惡意專案檔對 Claude Code 實施 RCE 與 API 密鑰竊取攻擊](https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/)
- [[安全部落格] 漏洞剖析 - 利用惡意技能與第三方 Agent 危害 Claude Code 安全性 (第一部分)](https://labs.reversec.com/posts/2026/05/skill-issues-compromising-claude-code-with-malicious-skills-agents-part-1)
- [[安全部落格] 漏洞剖析 - 當 MCP 遇上 OAuth：因授權設計缺陷導致一鍵接管帳號之安全漏洞](https://www.obsidiansecurity.com/blog/when-mcp-meets-oauth-common-pitfalls-leading-to-one-click-account-takeover)
- [[安全部落格] 配置指南 - 2026 最新 Claude Code 高安全防護部署與權限限制配置](https://claudedirectory.org/for/security)
- [[技術媒體] 安全專題 - 從 Claude Code 看 AI Agent 權限授予與實際資料控制之間的落差](https://nhimg.org/articles/claude-code-security-exposes-the-gap-between-permissions-and-data-control/)
- [[技術媒體] 安全專題 - 理解 GitHub Copilot 安全隱私風險與權限控管最佳實踐](https://blog.gitguardian.com/github-copilot-security-and-privacy/)
- [[技術媒體] 技術解析 - Claude Code 2026 權限控制：白名單、黑名單與沙盒機制](https://claudedirectory.org/blog/claude-code-permissions-guide)
- [[技術部落格] 深度分析 - GitHub Copilot 擴充架構與 Cursor 原生功能在安全性與整合度上的比較](https://emasterlabs.com/github-copilot-extensions-vs-cursor)
- [[社群整理] 資源目錄 - Claude Code 與 MCP Server 安全審查與安裝指南彙整](https://agentskillshub.dev/skills/)
- [[部落格] 技術分析 - Pi Coding Agent 套件 SDK 架構對開發與技能安全供應鏈的重大進展](https://thomas-wiegold.com/blog/pi-coding-agent/)
- [[部落格] 技術指南 - 運維視角下的 Claude Code 部署、權限管理與安全沙盒管理](https://hidekazu-konishi.com/entry/claude_code_operators_handbook.html)
- [[部落格] 技術評測 - Pi 代理人體驗：簡潔、可高度客製化的命令列與沙盒限制](https://www.glukhov.org/ai-devtools/pi/pi-coding-agent-review/)
- [[部落格] 開發實務 - Claude Code 代理人運作實務指南與環境隔離考量](https://schoettler.io/en/blog/claude-code-agenten)
- [[部落格] 開發實務 - GitHub Copilot Extensions 生態系特點與權限使用模型](https://pascoal.net/2024/10/22/gh-copilot-extensions/)
- [[開源社群] 開源專案 - 為 Pi Coding Agent 設計的權限強制執行擴充套件](https://github.com/MasuRii/pi-permission-system)
- [https://blog.liambellows.com/ai/2025/11/04/github-copilot-extensions-are-dying-here-s-why-you-should-care-and-what-to-do/](https://blog.liambellows.com/ai/2025/11/04/github-copilot-extensions-are-dying-here-s-why-you-should-care-and-what-to-do/)
- [https://medium.com/@michael.hannecke/a-sovereign-coding-agent-on-macos-pi-in-an-apple-container-zero-npm-on-the-host-46f62ffade0a](https://medium.com/@michael.hannecke/a-sovereign-coding-agent-on-macos-pi-in-an-apple-container-zero-npm-on-the-host-46f62ffade0a)
- [https://medium.com/@sauravkumarsct/mcp-authorization-for-agentic-ai-the-confused-deputy-5af8bb835261](https://medium.com/@sauravkumarsct/mcp-authorization-for-agentic-ai-the-confused-deputy-5af8bb835261)
- [https://medium.com/@sujaypawar/how-claude-code-actually-works-1f6d4f1eea82](https://medium.com/@sujaypawar/how-claude-code-actually-works-1f6d4f1eea82)
- [https://pub.towardsai.net/claude-code-properly-wired-a-one-stop-guide-to-spec-driven-development-on-a-real-project-e677fad8d793](https://pub.towardsai.net/claude-code-properly-wired-a-one-stop-guide-to-spec-driven-development-on-a-real-project-e677fad8d793)
