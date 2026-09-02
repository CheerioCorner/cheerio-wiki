# Deep Research 報告 — rc-20260902-001

- 研究主題：Context Engineering 是什麼、在實際 AI Agent 開發中扮演什麼角色、關鍵任務有哪些、有什麼坑要避免
- Notebook：https://notebooklm.google.com/notebook/3ee340a4-88e4-4c1e-be74-846268e28e1d
- 產生時間：2026-09-02T14:53:57.648Z

## 研究結果

### 1. 實際開發時，Context Engineering 扮演什麼角色？

在實際的 AI Agent 開發中，**Context Engineering（上下文工程）扮演著決定「Flashy Demo（炫酷原型）」能否成功落地為「Production-ready System（生產級系統）」的關鍵架構角色** [1]。

隨著 LLM 發展至 agentic（智能體）時代，業界（如 Cognition AI）甚至指出，**Context Engineering 實際上已經成為開發 AI Agent 工程師的「第一要務（#1 job）」** [2]。

在實際開發中，它主要扮演以下三個核心角色：

### 1. 扮演 LLM 有限「工作記憶（RAM）」的資源守門人
Andrej Karpathy 曾提出一個著名的比喻：LLM 就像是新型作業系統的 CPU，而其 **Context Window（上下文視窗）則是 RAM（工作記憶）** [3]。
*   **對抗「Context Rot（上下文腐爛）」**：雖然現代模型動輒宣稱擁有百萬 Token 的超大上下文視窗，但「直接把所有資料塞進去」在實務上會導致災難 [4, 5]。研究顯示，隨著上下文拉長，模型會出現 **Lost in the Middle（迷失在中間）**、注意力渙散、成本暴增及延遲變長的問題 [4, 5]。
*   **避免行為鬼打牆**：在實際評估中（例如 Gemini 玩寶可夢的案例），當上下文超過 100k Token 時，Agent 就會開始傾向於「重複過去歷史中做過的動作」，失去規劃新策略的能力 [6, 7]。
*   **尋找 Minimum Viable Context（最小可行上下文）**：Context Engineering 的首要開發任務，就是**精準控制與篩選進入這塊「RAM」的 token**，只留下高訊號、高價值的資訊 [8, 9]。

---

### 2. 構建「四大核心操作」以實現複雜的多步推理
與一次性問答（Single-turn Q&A）不同，AI Agent 需要自主運行並調用工具。Context Engineering 透過以下 **四大核心運作** 撐起整個 Agent 系統的骨架 [10-12]：

*   **Write（寫入）**：當 Agent 進行長期任務時，會將中間規劃、驗證後的資訊儲存在上下文視窗之外（例如：**Scratchpad 便簽**、**Memory 記憶層** 或狀態文件）[13, 14]。
*   **Select（選擇）**：Agent 無法一次看完整個企業資料庫或工具庫。開發者需要設計動態檢索系統（如 **GraphRAG**）來精準撈取實體與關係 [15, 16]；並在工具庫過於龐大時，利用 **RAG-MCP** 技術，根據目前的任務僅動態推薦 3~5 個工具，以防止工具過多造成模型混淆（Context Confusion）[17, 18]。
*   **Compress（壓縮）**：當 Agent 的軌跡（Trajectory）和 tool 回傳的原始結果佔用太多 Token 時，開發者需要實施**自動緊湊（Auto-compact）**、遞迴摘要，或者在調用結束後**清除冗餘的 Tool 結果** [19-21]。
*   **Isolate（隔離）**：當單一 Agent 資訊過載時，在架構上將其拆分為**多 Agent 協同（Multi-agent）** [22, 23]。每個子 Agent 只分配到與其職責相關、最乾淨的上下文，藉此提升執行精準度 [22, 24]。

---

### 3. 充當標準化、可審計的「企業系統對接管道」
Agent 必須與外部環境（API、資料庫、文件夾）互動。過去，開發者得為每個工具編寫客製化的 Adapter，陷入複雜的對接地獄 [25]。
*   **標準化對接標準（AI 的 USB-C）**：在實際開發中，Context Engineering 透過 **Model Context Protocol (MCP)** 這種開放標準，建立起主機（Host）與伺服器之間的標準協議 [26]。這使得 Agent 可以在執行期（Runtime）動態發現、理解並安全地調用外部工具，大幅簡化對接複雜度 [27, 28]。
*   **提供可審計與治理（Governance）的上下文**：在企業級開發中，安全性與合規性至關重要。Context Engineering 允許在檢索和調用工具時，直接嵌入權限管控（RBAC）、敏感資訊遮蔽（Redaction），並在 Graph 資料庫中留下完整的「決策鏈（Decision Traces）」，讓 Agent 的每一次推理都有據可查 [29-31]。

---

### 💡 總結：Prompt vs. Context Engineering

我們可以這樣區分這兩者：
> **Prompt Engineering（提示工程）** 關注的是「在上下文視窗**內**」如何寫出完美的指令，就像是寫出**好食譜** [32]；
> **Context Engineering（上下文工程）** 則是系統級的學問，它決定了「**什麼東西能進入**」這個視窗，像是**備齊乾淨的食材、挑選好廚具、並規劃好廚房的運作動線** [33]。

在 Agent 實際開發中，與其花幾十個小時微調 Promp 詞，不如建立一套穩定、乾淨、可自我修復的 Context 管道，後者才是決定 AI Agent 能否商用的分水嶺 [34, 35]。

---

⚙️ **接下來，您想深入了解這四大核心操作（Write/Select/Compress/Isolate）在代碼上的具體實現方式，還是想看一些常見的 Context 失敗案例（如 Context Poisoning）與解決方案呢？**

**引用來源：**
- [1] [Weaviate: LLM Memory and Retrieval for AI Agents](https://weaviate.io/blog/context-engineering)
- [2] [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [3] [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [4] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [5] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [6] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [7] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [8] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [9] [Neo4j: Context Engineering in AI Agents (A Practical Guide)](https://neo4j.com/blog/agentic-ai/what-is-context-engineering/)
- [10] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [11] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [12] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [13] [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [14] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [15] [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [16] [Neo4j: Context Engineering in AI Agents (A Practical Guide)](https://neo4j.com/blog/agentic-ai/what-is-context-engineering/)
- [17] [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [18] [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [19] [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [20] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [21] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [22] [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [23] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [24] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [25] [Wikipedia: Model Context Protocol (MCP)](https://en.wikipedia.org/wiki/Model_Context_Protocol)
- [26] [Introduction to Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)
- [27] [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [28] [InfoWorld: The Role of MCP in Context Engineering](https://www.infoworld.com/article/4175336/the-role-of-mcp-in-context-engineering.html)
- [29] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [30] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [31] [InfoWorld: The Role of MCP in Context Engineering](https://www.infoworld.com/article/4175336/the-role-of-mcp-in-context-engineering.html)
- [32] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [33] [Mezmo: Context Engineering for Observability and LLMs](https://www.mezmo.com/learn/context-engineering-for-observability-how-to-deliver-the-right-data-to-llms)
- [34] [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [35] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)

### 2. 關鍵任務是什麼？

Andrej Karpathy 曾將 **Context Engineering (脈絡工程)** 貼切地定義為「**在每一步中，用恰到好處的資訊填滿上下文窗口的精妙藝術與科學**」[1-4]。

這項學科的核心目標是將有限的上下文窗口 (Context Window) 視為稀缺資源，對其進行主動且系統性的最佳化，使模型能以最精簡、高訊號的 Token 做出最準確的決策 [5-7]。在實際的 AI 系統或 AI Agent 開發中，Context Engineering 的**關鍵任務**主要圍繞在**四大核心操作** [8-10] 以及**系統級脈絡管道 (Pipeline) 的建構步驟** [11, 12]。

以下為 Context Engineering 的關鍵任務拆解：

### 一、 脈絡工程的四大核心操作任務
根據系統框架，脈絡工程最基礎的任務包含以下四項基本操作的設計與落實 [8-10]：

1. **寫入 (Write) — 儲存狀態與工作筆記** [13, 14]：
   * 當 Agent 執行的任務超出單次對話時，需要將中間步驟、推理決策與用戶偏好儲存在上下文窗口之外（例如：**Scratchpad**、運行時狀態、記憶庫中） [14, 15]。這樣能避免在後續步驟中重複載入冗長歷史，釋放窗口空間 [14]。
2. **選擇 (Select) — 精準拉取相關資訊** [13, 14]：
   * 評估所有可用資訊，基於相關性、新鮮度與必要性，僅拉取當前步驟真正需要的資訊進入窗口 [14, 16]。此操作旨在最大化**降低雜訊**，讓模型專注於核心任務 [17, 18]。
3. **壓縮 (Compress) — 提升資訊密度** [13, 17]：
   * 針對冗長的對話、大型文檔或工具輸出進行壓縮（如自動運行 **Auto-Compact** 運算），保留事實、決策和推理軌跡，但大幅減少所佔用的 Token 數量 [19-21]。
4. **隔離 (Isolate) — 劃分脈絡邊界** [13, 17]：
   * 確保不同的 Agent、 concurrent 工作流或沙盒環境 (Sandbox) 之間有清晰的脈絡邊界，防止資訊交叉污染 (Context Bleeding) 與無謂的 Token 浪費 [15, 22]。

---

### 二、 系統級脈絡管道 (Pipeline) 的關鍵任務
在架構可靠的 Agent 系統時，開發者必須透過以下關鍵任務來維護整個脈絡管道 [11, 12]：

#### 1. 脈絡檢索與優化 (Context Retrieval & Optimization)
檢索品質直接決定了脈絡的品質（即「垃圾進，垃圾出」） [23, 24]。
* **實施混合檢索 (Hybrid Search)**：結合能理解語意的**向量檢索**與能精準匹配關鍵字的**詞彙檢索 (BM25)** [25, 26]。
* **位置感知的重排序 (Reranking)**：利用重排序模型，將最高相關性的片段置於窗口的「最前端」或「最末端」，以對抗現代大模型因旋轉位置嵌入 (RoPE) 導致的**「遺忘中段」(Lost in the Middle)** 架構限制 [27-30]。
* **維護脈絡新鮮度 (Freshness)**：定義時間戳元數據與同步機制，過濾掉過時的資訊（例如兩年前的舊政策），避免模型依據過期資料自信地給出錯誤答案 [31, 32]。

#### 2. 脈絡處理與結構化 (Context Processing & Structuring)
* **語意分塊 (Semantic Chunking)**：打破生硬的固定字數分塊，改以語意邏輯（如段落、函數、表格）來切分數據，保留上下文的完整性 [26, 33]。
* **資訊與指令的清晰隔離**：使用 XML 標籤、Markdown 或明確的定界符結構化組織輸入，明確區分「大模型的系統指令」與「外部檢索到的參考數據」，降低模型產生混淆的機率 [34, 35]。

#### 3. 提示詞與系統指令規範 (Prompt & Instruction Specifying)
* **設計結構化的提示詞規範 (Prompt Specs)**：用規格書般明確的 Objective (目標)、Constraints (硬性邊界限制) 與 Output Contract (輸出格式合約) 取代自由格式的文本 [36, 37]。
* **防範行為漂移 (Behavioral Drift)**：隨着對話歷史與工具輸出增加，System Prompt 容易在窗口中被邊緣化。任務之一是策略性地放置關鍵限制，使模型始終遵循核心規範 [35]。

#### 4. 記憶系統層級規劃 (Memory Layer Management)
* **冷、溫、熱記憶分層**：規劃**熱記憶**（當前 Session 狀態與對話歷史 [15, 38]）、**溫記憶**（近期交互與常用事實 [38]）以及**長期/冷記憶**（基於知識圖譜 GraphRAG 的持久化實體與關係 [38, 39]）。
* **記憶更新與衰退機制 (Decay)**：設計定期清理與主動更新機制，將完成的計畫、已解決的爭論予以剪枝或封存，避免窗口飽和與**脈絡腐敗 (Context Rot)** [40-42]。

#### 5. 工具與 API 的動態裝載 (Tool Context Management)
* **動態工具檢索 (RAG-MCP)**：當 Agent 可用工具太多時，會造成工具描述佔用大量空間並干擾模型 [43, 44]。關鍵任務是透過語意檢索，在當前步驟中僅動態裝載最相關的 3-5 個工具，研究指出這可將**工具調用準確率提升 3 倍** [44-46]。
* **工具輸出預處理 (Tool Output Filtering)**：API 返回的原始 JSON 往往包含大量模型不需要的贅字，必須在代碼中先行過濾、整合或轉為 typed 緊湊格式，再送入大模型 [47-49]。
* **標準化連接 (Model Context Protocol - MCP)**：利用 open-source 的通用標準 MCP 協定，解除 AI 與各種資料庫、第三方工具之間的專屬耦合，使 context 傳遞更為穩定、可重複且易維護 [50-52]。

#### 6. 治理、驗證與可觀測性評估 (Governance, Validation & Evaluation)
* **脈絡驗證與防毒隔離 (Quarantine)**：在外部輸入或不可信數據進入上下文窗口之前進行質量、安全、合規檢查，並主動遮蔽敏感資訊 (PII) [53, 54]。
* **可觀測性與評估 (Evaluation & Auditing)**：記錄每一次推論所輸入的完整 Context（包含考慮了多少片段、哪些規則被過濾等） [55, 56]。使用**消融測試 (Ablation Tests)** 來衡量不同脈絡組件（如移除歷史對話、減少檢索片段數）對最後輸出品質與 token 成本的真實貢獻 [57, 58]。

---

💡 既然大模型在面對多個工具、冗長歷史與多重資料來源時容易產生「脈絡混淆」或「 lost-in-the-middle 」，也許我們可以深入聊聊你的 AI Agent 主要使用哪些工具與資料源？這樣我們就能針對你的實際場景，規劃最適合的 **Minimum Viable Context (MVC)** 篩選策略。

**引用來源：**
- [1] [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [2] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [3] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [4] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [5] [Weaviate: LLM Memory and Retrieval for AI Agents](https://weaviate.io/blog/context-engineering)
- [6] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [7] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [8] [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [9] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [10] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [11] [IBM: What Is Context Engineering?](https://www.ibm.com/think/topics/context-engineering)
- [12] [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [13] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [14] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [15] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [16] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [17] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [18] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [19] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [20] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [21] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [22] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [23] [Weaviate: LLM Memory and Retrieval for AI Agents](https://weaviate.io/blog/context-engineering)
- [24] [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [25] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [26] [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [27] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [28] [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [29] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [30] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [31] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [32] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [33] [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [34] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [35] [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [36] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [37] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [38] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [39] [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [40] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [41] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [42] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [43] [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [44] [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [45] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [46] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [47] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [48] [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [49] [Neo4j: Context Engineering in AI Agents (A Practical Guide)](https://neo4j.com/blog/agentic-ai/what-is-context-engineering/)
- [50] [Batch Calibration: Rethinking Calibration for In-Context Learning and Prompt Engineering](https://arxiv.org/pdf/2309.17249)
- [51] [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [52] [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [53] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [54] [Mezmo: Context Engineering for Observability and LLMs](https://www.mezmo.com/learn/context-engineering-for-observability-how-to-deliver-the-right-data-to-llms)
- [55] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [56] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [57] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [58] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)

### 3. 要避免的坑又是什麼？

在實際開發 AI Agent 時，上下文工程（Context Engineering）要避免的「坑」（失效模式）主要可以歸納為學術界與業界公認的**四大核心失效模式**，以及數個常見的**工程與數據管理誤區**。

---

### 一、 四大核心上下文失效模式 (Core Context Failures)

1. **上下文污染 (Context Poisoning)**
   - **坑在哪裡**：當 Agent 在多輪對話或任務執行中產生了**幻覺（Hallucination）或錯誤資訊**，這些錯誤被寫入上下文（例如 Goals 區域、任務摘要或短期記憶）並被後續步驟不斷重複引用 [1-4]。這會導致錯誤像滾雪球一樣不斷累積，使 Agent 陷入死循環，執著於無法實現或無意義的目標 [1, 5, 6]。
   - **實例**：Google DeepMind 的 Gemini 2.5 玩神奇寶貝（Pokémon）的實驗中，Agent 一旦對遊戲狀態產生幻覺並寫入目標欄位，就會制定出荒謬的策略，花費大量時間執著於不可能達成的任務 [2, 5]。
   - **避坑指南**：引入**上下文驗證與隔離（Context Validation & Quarantine）**機制 [1, 7, 8]。在將外部資訊或工具執行結果寫入長期記憶/上下文之前，必須先經由確定性的 API、數據庫或 schema 進行檢驗，驗證失敗的資訊直接隔離 [8-10]。

2. **上下文分心 (Context Distraction) 與「迷失中段」 (Lost in the Middle)**
   - **坑在哪裡**：當上下文累積得過長，即使裡面的資訊 100% 正確，模型也會因為「注意力預算」被攤薄，開始過度專注於累積的對話歷史或工具輸出，反而**忽略了原有的系統指令或推理邏輯** [6, 11-13]。
   - **實例與數據**：
     - Stanford 研究指出，LLM 具有 **"Lost in the Middle"（迷失中段）** 的現象，模型對位於上下文最前與最後的資訊記憶最深，而位於中段（如 20 個文檔中的第 5-15 個）的資訊，其檢索與利用準確度會**暴跌 30% 以上** [14, 15]。
     - 即使是百萬 token 級別的 frontier 模型，其「注意力衰退」也是從第一個 token 就開始的（Chroma 測試 18 款模型的 Context Rot 研究） [16-18]。
     - 在玩 Pokémon 的實驗中，當 context 超過 100k token，Agent 開始傾向於**一味重複歷史行為**，而不是根據當前狀態規劃新策略 [6, 19, 20]。Databricks 研究也發現，模型在 context 僅達到 **32k token** 左右時，正確率就開始明顯下滑 [21, 22]。
   - **避坑指南**：
     - 實施**上下文壓縮（Compaction）與剪枝（Trimming）** [23, 24]。定期將冗長對話歷史進行結構化摘要，並清理深埋在歷史中且已執行完畢的 raw tool 執行結果 [25-28]。
     - 實施**位置感知重排（Position-aware Re-ranking）**：確保最重要的關鍵事實與核心指令被放置在 context 的最前或最後（例如 Gemini 官方推薦將大量參考文檔放前面，具體任務與指令放最後） [29-32]。

3. **上下文混淆 (Context Confusion)**
   - **坑在哪裡**：當 context 中充斥著太多不相關的參考文檔或**過量的工具定義（Tool Definitions）**時，模型會受到干擾，做出錯誤的推理或誤調用不相關的工具（工具過載） [11, 33-35]。
   - **實例與數據**：Berkeley Function-Calling Leaderboard 的數據指出，**任何模型在被給予超過一個工具時，其表現都會有所下滑** [36, 37]。一項研究中，給予輕量化的 Llama 3.1 8b 模型全部 46 個工具時任務失敗，但將工具載入量精簡至 19 個時即順利成功 [34, 37, 38]。
   - **避坑指南**：實施**動態工具加載（RAG-MCP）** [38, 39]。將工具的 metadata 向量化，根據用戶當前的任務動態檢索並僅向 LLM 暴露最相關的少數工具（建議單次對話保持在 20-30 個工具以下），這能使工具調用準確率提升 3 倍並減少 50% 以上的 token 浪費 [40, 41]。

4. **上下文衝突 (Context Clash)**
   - **坑在哪裡**：多個數據源導入了相互矛盾的資訊，或者 Agent 在多輪交互中，**早期的錯誤假設、已過期的使用者偏好仍保留在 context 裡**，與後續新獲得的正確事實發生衝突，導致模型推理中斷或任意偏袒其中一方 [11, 42-44]。
   - **實例與數據**：Microsoft 與 Salesforce 的聯合研究發現，將同一個 benchmark 提示詞「切片（sharded）」到多輪對話中（模擬人類邊聊邊補充細節的習慣），模型的平均表現**暴跌了 39%**（連 OpenAI o3 的分數也從 98.1 跌至 64.1） [45, 46]。主因就是模型在前面幾輪因資訊不足做出的錯誤假設，殘留在 context 內，嚴重毒化了後續的最終推理 [2, 46]。
   - **避坑指南**：
     - **主動剪枝（Pruning）與覆蓋**：當新的指示或偏好產生時，必須在 context 內顯式覆蓋或剔除舊的衝突事實（例如：當使用者說「改訂商務艙」，就必須把之前 context 中的「經濟艙」剔除） [47, 48]。
     - 區分**決策（靜態）上下文**與**操作（動態）上下文**，動態上下文放最後利用近因效應 [49]。

---

### 二、 其他常見的工程與數據管理「深坑」

1. **「盲目依賴大窗口」的誤區**
   - **坑在哪裡**：認為百萬級 token 的大 context window 可以解決一切，因而把所有企業文件、程式庫、對話歷史不加過濾地塞給模型 [50, 51]。
   - **代價**：除了上述的 Context Rot 之外，這會帶來極高的**延遲與成本** [12, 52, 53]。Factory AI 的研究指出，企業級 Agent 的 pre-reasoning 階段往往就要燒掉 50k - 100k 的 tokens（包含架構定義、血緣關係、政策等） [54, 55]，這導致一次簡單查詢在 API 費用上就可能花費高昂，每天一萬次查詢將演變成每年 30 萬至 60 萬美元的固定開銷 [54, 56]。
   - **避坑指南**：落實 **Minimum Viable Context (MVC，最小可行上下文)** 原則 [57]，只提供 Agent 執行當前步驟所需的最小高信號 token 集 [57, 58]。

2. **時效性缺口 (Freshness Gap)**
   - **坑在哪裡**：傳統向量檢索（RAG）在計算相似度時，**並不會考慮文檔的時間屬性** [59]。兩年前的報價單與昨天的更新政策在語意上可能非常相似，模型很容易因為相似度高而提取了過期數據，導致 Agent 做出違反合規或錯誤的業務決策 [59, 60]。
   - **避坑指南**：在數據源與檢索層建立強制性的**時效性元數據過濾（Metadata Filtering & Freshness SLAs）**，對具有時間限制的業務 Fact 進行生命週期管理，過期即標記失效 [59, 61]。

3. **缺乏企業語境的原始數據 (Raw Data without Metadata)**
   - **坑在哪裡**：直接將數據庫表格、Jira ticket 或 logs 倒給 Agent [62, 63]。例如把一個欄位名稱為 `"rev_adj"` 的表格丟給 Agent，Agent 即使懂英文也無法得知其背後的特定業務含義（例如它是代表調整後營收還是某個特定的退款機制） [63, 64]。
   - **避坑指南**：建立**業務詞彙表（Business Glossary）**與元數據映射 [64]。這能作為 Context 的錨點，確保 Agent 在檢索數據時，能夠精確對齊企業內經認證（certified）的定義 [64, 65]。

4. **緩存優化 (Caching) 優先於相關性優化**
   - **坑在哪裡**：本末倒置。在還沒有做好 Context 篩選、去噪和精簡之前，就盲目實施 Prompt Caching 去節省 API 成本 [66, 67]。這無異於「用極有效率的方式去快取一堆垃圾資訊」，根本無法提升推理品質 [66, 67]。
   - **避坑指南**：嚴格遵循「先挑選最相關 context、再設計邏輯結構、最後才對靜態 prefixes（如穩定不變的 system instructions）實施 Prompt Caching」的優化順序 [66]。

---

🛠️ 想看看如何為您的 AI Agent 實施 **動態工具檢索 (RAG-MCP)** 或 **上下文壓縮合約 (Compaction)** 來避開工具過載與 Context Rot 的坑嗎？我可以提供具體的實作架構或範例合約！

**引用來源：**
- [1] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [2] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [3] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [4] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [5] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [6] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [7] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [8] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [9] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [10] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [11] [Weaviate: LLM Memory and Retrieval for AI Agents](https://weaviate.io/blog/context-engineering)
- [12] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [13] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [14] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [15] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [16] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [17] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [18] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [19] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [20] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [21] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [22] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [23] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [24] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [25] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [26] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [27] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [28] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [29] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [30] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [31] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [32] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [33] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [34] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [35] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [36] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [37] [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [38] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [39] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [40] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [41] [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [42] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [43] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [44] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [45] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [46] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [47] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [48] [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [49] [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [50] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [51] [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [52] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [53] [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [54] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [55] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [56] [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [57] [Neo4j: Context Engineering in AI Agents (A Practical Guide)](https://neo4j.com/blog/agentic-ai/what-is-context-engineering/)
- [58] [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [59] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [60] [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [61] [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [62] [Red Hat Developer: Building Effective AI Agents with Model Context Protocol (MCP)](https://developers.redhat.com/articles/2026/01/08/building-effective-ai-agents-mcp)
- [63] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [64] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [65] [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [66] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [67] [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)

## 參考來源清單

- [Anthropic: Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Atlan: Working Memory in LLMs & The Context Window as Cognitive Architecture](https://atlan.com/know/working-memory-llms/)
- [Batch Calibration: Rethinking Calibration for In-Context Learning and Prompt Engineering](https://arxiv.org/pdf/2309.17249)
- [DeepLearning.AI: How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html)
- [Elastic: Architecting Reliable AI with Context Engineering](https://www.elastic.co/what-is/context-engineering)
- [Elasticsearch Labs: Components, Techniques, and Best Practices](https://www.elastic.co/search-labs/blog/context-engineering-overview)
- [Firecrawl: Context Engineering vs Prompt Engineering for AI Agents](https://www.firecrawl.dev/blog/context-engineering)
- [IBM: What Is Context Engineering?](https://www.ibm.com/think/topics/context-engineering)
- [InfoWorld: The Role of MCP in Context Engineering](https://www.infoworld.com/article/4175336/the-role-of-mcp-in-context-engineering.html)
- [Introduction to Model Context Protocol (MCP)](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)
- [LangChain: Context Engineering](https://www.langchain.com/blog/context-engineering-for-agents)
- [Mezmo: Context Engineering for Observability and LLMs](https://www.mezmo.com/learn/context-engineering-for-observability-how-to-deliver-the-right-data-to-llms)
- [Microsoft Open Source: Context Engineering for AI Agents](https://microsoft.github.io/ai-agents-for-beginners/12-context-engineering/)
- [Neo4j: Best Tools for Context Engineering in Agentic AI Systems](https://neo4j.com/blog/agentic-ai/context-engineering-tools/)
- [Neo4j: Context Engineering in AI Agents (A Practical Guide)](https://neo4j.com/blog/agentic-ai/what-is-context-engineering/)
- [OvalEdge: Context Engineering Techniques & Why AI Gets It Wrong](https://www.ovaledge.com/blog/context-engineering-techniques)
- [PrompTessor: How to Give AI the Right Information at the Right Time](https://promptessor.com/blog/context-engineering-how-to-give-ai-the-right-information-at-the-right-time)
- [Red Hat Developer: Building Effective AI Agents with Model Context Protocol (MCP)](https://developers.redhat.com/articles/2026/01/08/building-effective-ai-agents-mcp)
- [Redis: Why AI Agents Need Better Info, Not More](https://redis.io/blog/quality-context-ai-agents/)
- [Weaviate: LLM Memory and Retrieval for AI Agents](https://weaviate.io/blog/context-engineering)
- [Wikipedia: Model Context Protocol (MCP)](https://en.wikipedia.org/wiki/Model_Context_Protocol)
- [[arXiv:2507.13334] A Survey of Context Engineering for Large Language Models](https://arxiv.org/abs/2507.13334)
