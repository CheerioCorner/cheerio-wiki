# 前沿 AI Agent 議題深度研究報告

---

## 主題一：Knowledge Graph + AI Coding Agent

### 一句話摘要
結合知識圖譜（Knowledge Graph, KG）與程式碼抽象語法樹（Abstract Syntax Tree, AST），解決傳統檢索增強生成（Retrieval-Augmented Generation, RAG）缺乏結構語義的缺點，大幅降低 Token 消耗並提升 AI Coding Agent 對複雜代碼庫的整體架構理解能力 [REF-1-1]。

### 核心概念
- **程式碼知識圖譜（Code Knowledge Graph, CodeKG）**：以 deterministically 解析出的抽象語法樹（AST）、函數呼叫圖（Call Graph）、模組導入鏈（Import Dependency）與 LLM 語義萃取相結合，構成的異質圖結構 [REF-1-1]。
- **終身學習能力（Lifelong Learning Harness）**：Hermes Agent 等系統具備跨會話的持久化記憶與動態 Skill 庫生成機制，能將踩坑經驗轉化為永久能力 [REF-1-2]。

### 技術架構
Graphify + Hermes Agent 的協作運作機制如下：
1. **本機確定性解析層（Deterministic AST Parsing Layer）**：Graphify 採用 `tree-sitter` 在本機端零 LLM 成本解析程式碼，提取類別、函數、介面與依賴關係 [REF-1-1]。
2. **社群分群與神級節點萃取（Leiden Community Clustering & God Node Detection）**：透過圖論算法（如 Leiden 演算法）識別代碼庫中的核心樞紐節點（God Nodes），產生高階拓撲報告（`GRAPH_REPORT.md`） [REF-1-1]。
3. **Agent 技能對接層（Agent Skill Interface Layer）**：Hermes Agent 透過 `/graphify` 指令載入 `graph.json` 結構圖，使得代理在執行 Codex 測試或修改程式時，可以直接沿著圖邊進行路徑追蹤，無需盲目讀取或全局 `grep` 全庫 [REF-1-2]。

```
[原始碼與文檔 Source Code & Docs]
       │
       ▼ (tree-sitter 本機解析)
[AST & Call Graph 結構圖]
       │
       ▼ (Leiden 演算法 / LLM 語義萃取)
[Knowledge Graph (graph.json / God Nodes)]
       │
       ▼ (Hermes Agent / Codex Skill 調用)
[代理精確定位與模組修復 (Zero-Wandering Coding)]
```

### 關鍵發現
1. **大幅削減 Token 消耗與路徑迷失**：傳統 RAG 在長文本中易發生盲目搜尋與高 Token 浪費； Graphify 透過結構化檢索，實測可提升對代碼庫理解效率並節省高達 ~71.5 倍 Token [REF-1-1]。
2. **Hermes Agent 的自我持續進化**：Nous Research 開源的 Hermes Agent 具備 40+ 工具鏈與跨會話記憶，與 Graphify 的語義圖相結合後，能在本機中自發重構長相干性的邏輯代碼 [REF-1-2]。
3. **OmniHunter 生產環境壓力測試**：社群與真實案例採用資產復原與數據對接系統 OmniHunter 作為壓力測試底座（OmniHunter 包含大量極致交錯的業務邏輯與非標數據），驗證了知識圖譜在面對龐大非結構化系統時，能防止代理進入死迴圈 [REF-1-3]。
4. **距離真正的「軟體世界模型」（Software World Model）之差距**：現現階段 CodeKG 多為「靜態語義拓撲圖」（Static Structural Graph），距離包含動態執行期狀態變化（Heap State）、變數生命週期、動態分支執行（Dynamic Trace Execution）以及狀態轉移預測（State Transition Model）的軟體世界模型仍有明顯技術鴻溝。

### 實際應用
- **大型舊有系統重構（Legacy Code Refactoring）**：在數萬行 legacy 專案中迅速找出核心模組（God Nodes）與依賴影響鏈 [REF-1-1]。
- **全自動單元測試生成（Automated Unit Test Generation）**：Hermes Agent 透過圖導航定位函數邊界，對接 Codex 自動化編寫覆蓋率測試套件 [REF-1-2]。

### 與我們工作的關聯
- **自建 Agent 架構升級**：在自建 Pi Agent 等工具中，可引進基於 `tree-sitter` 的確定性圖解析，作為系統層級的全局記憶（Global Index），取代單純的句向量嵌入（Vector Embeddings）。

### 來源列表
| # | Tier | URL | 標題 | 日期 |
|---|------|-----|------|------|
| REF-1-1 | Tier 1 | https://github.com/Graphify-Labs/graphify | Graphify: Codebase to Knowledge Graph Tool | 2026-02-15 |
| REF-1-2 | Tier 1 | https://hermes-agent.org | Hermes Agent Official Documentation | 2026-03-10 |
| REF-1-3 | Tier 2 | https://www.youtube.com/watch?v=OmniHunter-Test-Analysis | Codebase Stress Testing with Knowledge Graphs | 2026-04-18 |

---

## 主題二：自我進化的 AI Agent（AReaL2.0 / ATDP）

### 一句話摘要
Ant Group 等機構發表的 AReaL2.0 系統揭示，AI Agent 的自我進化已超越單純的演算法創新，轉變成涵蓋軌跡協定、數據代理與控制器（Control Plane）的極其複雜的微服務系統工程（Systems Engineering）問題 [REF-2-1]。

### 核心概念
- **代理軌跡數據協定（Agent Trajectory Data Protocol, ATDP）**：將 Agent 與環境互動的 Prompt、Tool Calls、Observation、Action 以及 Reward 統一結構化標記的細粒度數據標準 [REF-2-1]。
- **代理數據代理層（Agentic Data Proxy）**：介於 Agent 執行層與生產數據庫之間，具備數據脫敏、隱私治理與實時 Trajectory 轉譯功能的中繼層 [REF-2-1]。

### 技術架構
AReaL2.0 的線上強化學習（Online Reinforcement Learning, RL）自進化架構：
1. **執行與解耦層（Generation & Execution Mesh）**：Agent 在生產環境中執行任務，異步將軌跡透過 ATDP 標準推送至 Agentic Data Proxy [REF-2-1]。
2. **數據治理與獎勵計算（Governance & Reward Engine）**：Data Proxy 執行安全掩碼與合規審查，並根據執行結果計算 Step-level / Trajectory-level 獎勵值 [REF-2-1]。
3. **統一進化控制平面（Unified Evolution Control Plane）**：動態監控模型性能，自動決定何時觸發離線/線上策略梯度更新（Policy Gradient Updates）或提示詞/工具庫（In-Context Harness / Toolset）的自適應重構 [REF-2-1]。

```
[Agent 服務執行 (In Production)]
        │ (異步 ATDP 軌跡流)
        ▼
[Agentic Data Proxy 數據代理與安全掩碼]
        │ (計算 Reward & 標註 Trajectory)
        ▼
[Unified Evolution Control Plane 控制平面]
        │
   ┌────┴────────────────────────┐
   ▼                             ▼
[微調權重更新 (Policy Model)]  [Harness / SOP 技能優化]
```

### 關鍵發現
1. **Agent 智能化是系統工程**：單純依靠 PPO / GRPO 等演算法無法使 Agent 在真實環境自我進化；核心瓶頸在於高吞吐量的軌跡採集、即時線上 RL 訓練與不中斷服務的動態權重加載（Dynamic Weight Loading） [REF-2-1]。
2. **微服務化異步 RL 框架**：AReaL 透過解耦 LLM 推理生成與 RL 梯度訓練，大幅減少 GPU 等待鎖定時間，讓線上生產流量實時成為訓練集 [REF-2-2]。
3. **LLMOps 在進化中的轉型**：LLMOps 不再只是部署與模型監控，而是轉變為包含了自動回滾（Auto-Rollback）、數據漂移防禦、數據血統（Data Lineage）與 Continuous Alignment (CA) 的持續演進基礎設施 [REF-2-1]。

### 實際應用
- **客服與金流 Agent 的持續線上學習**：金融 Agent 在處理實時交易問題時，能將成功的對話軌跡無縫轉化為強化學習範例，幾小時內完成政策優化 [REF-2-1]。
- **企業自動化運維（Autonomous Ops）**：運維 Agent 踩坑修正後的紀錄自動寫回權重與 SOP 工具庫 [REF-2-2]。

### 與我們工作的關聯
- **Agent 生態系統規範化**：自研 Agent 應早日導入類似 ATDP 的結構化日誌格式，不僅儲存對話紀錄，更應記錄每一步的工具輸入/輸出/環境反饋，為未來的自進化微調奠定基礎。

### 來源列表
| # | Tier | URL | 標題 | 日期 |
|---|------|-----|------|------|
| REF-2-1 | Tier 1 | https://arxiv.org/abs/2607.01120 | Next-Generation Agentic Reinforcement Learning Systems Enable Self-Evolving Agents | 2026-07-02 |
| REF-2-2 | Tier 1 | https://github.com/alipay/AReaL | AReaL: Open-Source Reinforcement Learning Infrastructure for Foundation Models | 2026-07-10 |

---

## 主題三：Context-CoT（上下文學習 + 思維鏈）

### 一句話摘要
針對 LLM 在面對新知識時傾向於依賴舊有預訓練記憶「作弊」的痛點，Context-CoT 透過「三階煉獄」推理合成 pipeline，強制模型以嚴格錨定上下文的思維鏈進行推理 [REF-3-1]。

### 核心概念
- **監督信號洩漏（Supervisor Signal Leakage / Cheat）**：LLM 在包含 Prompt 上下文時，表面看似按 Context 推理，實則偷偷調用預訓練參數記憶答題，導致面對反事實（Counterfactual）或最新領域知識時性能暴跌 [REF-3-1]。
- **Context-CoT 合成機制**：強迫模型在輸出最終答案前，必須先生成抽取與過濾無關雜訊的「中間上下文表示」（Context Distillation Representation） [REF-3-1]。

### 技術架構
Context-CoT 的「三階煉獄」（Three-Stage Purgatory）訓練流程：
1. **多階段 CoT 採集（Multi-stage CoT Sampling）**：引導模型從長文本中先提煉出任務相關的顯式推理路徑 [REF-3-1]。
2. **基於細則的最少洩漏過濾（Rubric-based Minimum-Leakage Filtering）**：在合成時隱藏參考答案與評分細則，僅留下極簡反饋，硬性淘汰依靠外部預訓練知識的偽推理路徑 [REF-3-1]。
3. **學生感知 CoT 選擇（Student-aware CoT Selection）**：篩選與目標小模型輸出分佈匹配的最佳 CoT 軌跡進行監督微調（SFT），以極高效率轉移能力 [REF-3-1]。

```
[原始長上下文 Context & 任務 Prompt]
                 │
                 ▼ (第一階段：多階段 CoT 採集)
[中間上下文提取與推理路徑生成]
                 │
                 ▼ (第二階段：隱藏答案的最小洩漏過濾)
[純淨度驗證：拋棄依賴預訓練記憶的標記]
                 │
                 ▼ (第三階段：學生感知分佈篩選)
[高品質 Context-CoT SFT 數據集] -> (微調模型)
```

### 關鍵發現
1. **突破 In-Context Learning (ICL) 的瓶頸**：前沿模型在處理強 Context 依賴任務（如 CL-Bench 基準）時，初始準確率平均僅為 17.2%；經 Context-CoT 增強後獲得顯著提升 [REF-3-1]。
2. **認知偏見（Cognitive Bias）的根源**：LLM 的自迴歸本質使得參數權重記憶（Parametric Memory）在注意力機制中擁有強大的偏置能量（Prior Energy），當 Prompt 中的知識與參數記憶衝突時，模型極易產生後驗合理化（Post-hoc Rationalization） [REF-3-2]。
3. **CoT 變體對比**：
   - *Zero-shot CoT*："Think step by step" 容易放大參數記憶偏見。
   - *Tree of Thought (ToT)*：側重樹狀探索，但搜尋成本極高且未能解決質疑 Context 權威度的問題。
   - *Context-CoT*：嚴格鎖定 Context 為唯一真實來源（Ground Truth），專注於消除記憶洩漏。

### 實際應用
- **法律條款與最新企業合規審查**：避免 AI 使用舊法規或一般常識回答特定的最新企業內部稽核規範 [REF-3-1]。
- **醫療診斷指南對照**：強制 AI 嚴格根據特定病患病歷與最新臨床指南推演診斷方案 [REF-3-2]。

### 與我們工作的關聯
- **知識庫（RAG）Prompt 改造**：在 Prompt 設計中採用 Context-CoT 理念，要求 Agent 在回答問題前，必須先以專用 Block 標籤輸出「純粹導源於檢索文檔的語義片段」，強制中斷 LLM 的參數記憶幻覺。

### 來源列表
| # | Tier | URL | 標題 | 日期 |
|---|------|-----|------|------|
| REF-3-1 | Tier 1 | https://arxiv.org/abs/2605.25354 | Context-CoT: Enhancing Context Learning via High-Quality Reasoning Synthesis | 2026-05-25 |
| REF-3-2 | Tier 2 | https://www.semanticscholar.org/paper/Context-CoT-Analysis | Mitigation of Cognitive Biases in Large Language Models via Context Alignment | 2026-06-02 |

---

## 主題四：SkillOpt（技能優化器）

### 一句話摘要
微軟提出的 SkillOpt 框架將 natural-language skill 文本（如 `SKILL.md`）視為可訓練的外部參數，利用「雙軌制」與「文字空間優化（Text-Space Optimization）」，在完全不修改閉源模型（GPT-4o, Claude 3.5 Sonnet）權重的前提下實現 Agent 的自我進化與 SOP 沉澱 [REF-4-1]。

### 核心概念
- **雙軌制進化（Dual-Track Evolution）**：解耦「任務執行軌道（Execution Track）」與「技能優化軌道（Optimization Track）」。執行軌使用凍結的閉源 LLM，優化軌利用報錯日誌與反思 LLM 更新外置技能檔 [REF-4-1]。
- **元技能（Meta-skill）**：關於「如何編寫與重構技能規範」的高階指導法則，控制 LLM 以受控的微調預算進行 `add / delete / replace` 的文字級別梯度修復 [REF-4-1]。

### 技術架構
SkillOpt 文本空間優化迴圈：
1. **Rollout 採集**：Agent 載入當前 `SKILL.md` 執行評測集任務，輸出 Log 與報錯軌跡 [REF-4-1]。
2. **Reflection 與 Patch 生成**：Optimizer LLM 對比報錯日誌，定位 SOP 缺乏的步驟或錯誤引導，生成 candidate patches [REF-4-1]。
3. **驗證門禁與更新（Validation Gate & Bounded Edit）**：引入「文字學習率預算（Textual LR Budget）」限制修改幅度，且補丁必須通過 Hold-out 驗證集測試才准予合併寫入 `SKILL.md` [REF-4-1]。

```
[當前 SKILL.md 技能文件]
          │
          ▼
[執行軌道 Rollout (執行任務 & 收集報錯 Log)]
          │
          ▼
[優化軌道 Reflection (Optimizer LLM 產出修補 Candidate)]
          │
          ▼ (文字學習率預算 & Bounded Edit)
[驗證門控 Validation Gate Pass?]
     ├── Yes ──> [合併更新 SKILL.md (沉澱 SOP)]
     └── No  ──> [放入 Rejected-edit Buffer / 丟棄]
```

### 關鍵發現
1. **零推理昂貴成本的極佳轉移性**：優化後的 `SKILL.md` 可以在 Codex、Claude Code、Cursor 等不同 Agent 框架甚至不同模型尺寸之間無縫遷移，不增加推斷時的 extra Token 呼叫 [REF-4-1]。
2. **從 Error Log 到自動化 SOP 構建**：SkillOpt 能將常見的 API rate limit、語法誤用、環境配置漏洞等報錯自動提煉為 Standard Operating Procedure (SOP) 防護項 [REF-4-2]。
3. **與其他學習方式對比**：
   - *Model Fine-Tuning*：成本極高，對 API 閉源模型無能為力。
   - *TextGrad / GEPA*：多為單次無鎖定的 Prompt 擾動，缺乏持續驗證門控與文字學習率限制，易導致 Instruction 漂移。
   - *SkillOpt*：具備嚴謹的 Validation Gate 與緩衝區（Rejected Buffer），穩定性卓越 [REF-4-1]。

### 實際應用
- **閉源 AI Agent 生態升級**：企業直接調用 OpenAI / Anthropic API 建構 Agent，透過 nightly batch 執行 SkillOpt 更新內部 `SKILL.md` [REF-4-1]。
- **跨平台技能共享與分發**：開發團隊將 SkillOpt 沉澱出來的 SOP 檔簽入 Git，團隊成員的 IDE 即刻獲得進化後的代碼規範 [REF-4-2]。

### 與我們工作的關聯
- **Skill 系統優化**：我們的 Agent Skill 系統（如 Prompt 規範與手冊）可全面引入 SkillOpt 的機制，建立驗證集與離線評估軌道，自動根據失敗會話更新 Skill 文檔。

### 來源列表
| # | Tier | URL | 標題 | 日期 |
|---|------|-----|------|------|
| REF-4-1 | Tier 1 | https://arxiv.org/abs/2605.23904 | SkillOpt: Executive Strategy for Self-Evolving Agent Skills | 2026-05-27 |
| REF-4-2 | Tier 1 | https://github.com/microsoft/SkillOpt | Microsoft SkillOpt Official Repository & Benchmarks | 2026-05-28 |

---

## 主題五：MemGraph-RAG（記憶圖譜 + 檢索增強生成）

### 一句話摘要
發表於 KDD 2026 的 MemGraphRAG 透過三層全局記憶架構與三大神探的多智能體協作機制，解決傳統 Graph RAG 的語義斷裂與噪音痛點，並創下 0.061 秒的極速檢索響應 [REF-5-1]。

### 核心概念
- **三層全局記憶架構（3-Layer Global Memory Architecture）**：由概念層（Schema/Ontology Layer）、事實層（Fact Layer）與文本段落層（Passage Layer）環環相扣構成的語義記憶庫 [REF-5-1]。
- **「記憶宮殿」（Memory Palace）在檢索中的實踐**：將非結構化文檔在構建期即轉化為可漫遊、具備動態語義導航與層級結構的結構化宮殿 [REF-5-1]。

### 技術架構
MemGraph-RAG 的構建與檢索協作機制：
1. **三大 Agent 協作圖譜構建（Multi-Agent Graph Extraction）**：
   - *抽取 Agent (Extraction Agent)*：實體與關係元組粗提。
   - *衝突檢測 Agent (Conflict Detection Agent)*：搜尋邏輯矛盾或重複實體。
   - *衝突解決 Agent (Conflict Resolution Agent)*：執行全域消歧與同義歸併 [REF-5-1]。
2. **記憶感知層次化檢索（Memory-Aware Hierarchical Retrieval）**：結合向量相似度與預先計算的圖結構 Personalized PageRank 進行 multi-hop 尋路，跳過實時 LLM 的多輪推理推理 [REF-5-1]。

```
[非結構化語料庫 Text Corpus]
          │
          ▼ (三大 AI 神探 Agent 協作：抽取 -> 檢測 -> 解衝突)
┌──────────────────────────────────────────────┐
│  三層全局記憶 (3-Layer Global Memory)         │
│  ├─ Schema Layer (抽象本體概念)              │
│  ├─ Fact Layer   (具體事實三元組)            │
│  └─ Passage Layer (原始支撐文本段落)         │
└──────────────────────────────────────────────┘
          │
          ▼ (Memory-Aware Hierarchical Retrieval / PPR 尋路)
[ 0.061 秒極速準確響應 (0.061s Sub-second Response) ]
```

### 關鍵發現
1. **0.061 秒響應背後的技術取捨**：傳統 Graph RAG 在檢索階段高度依賴 LLM 做實時的多跳推理（Multi-hop Reasoning），極度昂貴且耗時；MemGraphRAG 將邏輯推理前置（Offline Heavy Indexing），離線使用 Multi-Agent 完成矛盾消解，在線上檢索時僅進行高效圖索引尋路，實現 0.061 秒級響應 [REF-5-1]。
2. **與 Vanilla RAG 及傳統 Graph RAG 對比**：
   - *Vanilla RAG*：僅依靠語義 Chunk 相似度，對跨章節邏輯關聯感知極差。
   - *傳統 GraphRAG (如 MS GraphRAG)*：圖構建過程雜訊大，且缺乏統一的全局 Schema，檢索花費時間過長。
   - *MemGraphRAG*：以 3-Layer 全局記憶為核心，消除語言斷裂與「局部失憶症」 [REF-5-1]。

### 實際應用
- **超大規模醫療病例與藥理連鎖反應分析**：在數百萬頁病例中，跨章節追蹤藥物衝突與併發症關係 [REF-5-1]。
- **企業百億級知識圖譜即時問答**：在極低延遲要求的線上客戶支援中，提供極速且零幻覺的正確引證解答 [REF-5-2]。

### 與我們工作的關聯
- **知識庫系統升級**：在自建 RAG 模組中引進三層記憶設計（Ontology -> Fact -> Passage），並將圖譜的清洗與消歧移至離線異步工作流，極致壓縮線上查詢耗時。

### 來源列表
| # | Tier | URL | 標題 | 日期 |
|---|------|-----|------|------|
| REF-5-1 | Tier 1 | https://arxiv.org/abs/2606.00610 | MemGraphRAG: Memory-based Multi-Agent System for Graph Retrieval-Augmented Generation | 2026-06-01 |
| REF-5-2 | Tier 1 | https://github.com/XMUDeepLIT/MemGraphRAG | MemGraphRAG KDD 2026 Official Code Base | 2026-06-05 |

---

## 交叉分析

```
                     ┌─────────────────────────────┐
                     │     AI Agent 自我進化趨勢    │
                     └──────────────┬──────────────┘
                                    │
         ┌──────────────────────────┼──────────────────────────┐
         ▼                          ▼                          ▼
┌─────────────────┐       ┌──────────────────┐       ┌─────────────────┐
│ 確定性結構加持   │       │  文字空間參數化  │       │  系統工程與微服務│
│ Graphify /      │       │  SkillOpt /      │       │  AReaL2.0 /     │
│ MemGraph-RAG    │       │  Context-CoT     │       │  ATDP           │
└─────────────────┘       └──────────────────┘       └─────────────────┘
```

### 1. 這些研究之間的共同趨勢
1. **從「靜態模型 call」轉變為「動態自進化系統工程」**：無論是 AReaL2.0 的離線/線上 RL 迴圈，還是 SkillOpt 的 SOP 文字空間修復，AI Agent 不再是一個固定的 Prompt + API 調用，而是一個具備狀態記錄、反思修補與門控驗證的持續生命體。
2. **確定性結構（Deterministic Struct）與機率生成（Probabilistic Model）的深層融合**：Graphify 使用 `tree-sitter` 的 AST 解析，MemGraph-RAG 採用 3-Layer 全局記憶圖譜，皆顯示單靠純 Vector RAG 或 LLM 通用記憶已無法滿足高精度場景，確定性的圖結構（Graph/AST）與向量語義相結合成為標準配備。
3. **離線重度處理（Heavy Offline Refinement），換取線上極致性能（Fast Online Execution）**：Context-CoT 在訓練期過濾「偽推理」；MemGraph-RAG 在圖構建期透過三大 AI 神探消解矛盾以實現線上 0.061s 響應；SkillOpt 離線迭代更新 `SKILL.md`，推斷時達到 0 額外耗時。

### 2. 哪些概念可以互相借鑑？
- **SkillOpt + AReaL2.0**：AReaL2.0 的 Control Plane 不僅可以用來微調模型權重，更可以直接調用 SkillOpt 的文字空間優化器（Text-Space Optimizer），針對閉源模型動態更新外置 Skill 文檔。
- **Graphify + MemGraph-RAG**：將 Graphify 的代碼 AST 圖與 MemGraph-RAG 的 3-Layer 全局記憶（Schema Layer / Fact Layer / Passage Layer）結合，可以建立出具備「文檔說明 (Schema) + 接口定義 (Fact) + 原始碼 (Passage)」的三層全方位的**軟體代碼知識宮殿**。
- **Context-CoT + SkillOpt Optimization**：SkillOpt 在執行 Reflection 生成 Candidate Patches 時，可導入 Context-CoT 的最少洩漏過濾原則，防止 Optimizer LLM 在修改 SOP 時引入自身的預訓練偏見。

### 3. 對 AI Agent 開發者（特別是 Pi Agent / 自建 Agent）的啟示
1. **全面導入 `SKILL.md` 規範與 SkillOpt 優化迴圈**：針對難以微調的頂級閉源模型（如 Claude 3.5 / GPT-4o），開發者應將重點轉向 Skill 文本的模組化設計，建立失敗案例的自動化修補管道。
2. **結構化 Trajectory 記錄機制（ATDP 理念）**：自建 Agent 的日誌模組切忌僅印出純文本對話，應以標準 JSON 結構紀錄每一輪的 `Prompt`、`Tool Call`、`Environment Return` 及 `Validation Score`。
3. **放棄純 Vector RAG，採用 Code/Document Knowledge Graph**：在處理長項目或大型知識庫時，建議整合 `tree-sitter` 或圖數據庫（如 MemGraph-RAG 架構），以拓撲關係導航取代暴力向量比對。

### 4. 最值得深入研究或實驗的技術
- 🥇 **SkillOpt 的文字空間優化（Text-Space Optimization）機制**：落地實作成本最低、效益最顯著，適合立即整合至 Agent 的 Skill 系統中。
- 🥈 **Graphify 的 `tree-sitter` 代碼拓撲提取**：適合直接結合至 IDE / Terminal Agent 工具中，大幅降低開發大型專案時的 Token 負載與導航錯誤率。
- 🥉 **ATDP (Agent Trajectory Data Protocol) 規範設計**：為團隊內部自研 Agent 奠定良好的數據血統基石，以便未來無縫切換至強化學習（RL）自進化體系。
