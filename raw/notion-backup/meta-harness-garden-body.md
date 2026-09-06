# 🔬 Meta-Harness 元鞍具
> **狀態：** 🔬 研究
> **最後更新：** 2026-09-02
---
## 這是什麼
Meta-Harness（元鞍具）是一個「統一管理多個 AI agents」的研究。
核心問題是：**多個 AI agent 各有強項，但切換和管理帶來巨大挑戰**。
這個研究想要找到一個方法，讓個人和企業都能：
- 用最適合的 agent
- 統一管理成本
- 確保安全
- 保持資訊統一
---
## 為什麼要做這件事
### 問題：多 agent 的挑戰
目前市場上有很多 AI agents，各有強項：
<table header-row="true">
<tr>
<td>Agent</td>
<td>強項</td>
<td>問題</td>
</tr>
<tr>
<td>Claude Code</td>
<td>深度分析</td>
<td>閉源、貴</td>
</tr>
<tr>
<td>Codex</td>
<td>程式碼生成</td>
<td>閉源、貴</td>
</tr>
<tr>
<td>Pi</td>
<td>輕量、可擴展</td>
<td>生態系較小</td>
</tr>
<tr>
<td>GitHub Copilot</td>
<td>整合 IDE</td>
<td>依賴微軟生態</td>
</tr>
<tr>
<td>Cursor</td>
<td>編輯器體驗</td>
<td>閉源、貴</td>
</tr>
</table>
**切換的痛點：**
- 每個 agent 都有自己的設定和工作流
- 切換時需要重新適應
- 知識和 context 不互通
- 成本分散，難以控管
### 挑戰面
<table header-row="true">
<tr>
<td>面向</td>
<td>挑戰</td>
<td>影響</td>
</tr>
<tr>
<td>**成本**</td>
<td>閉源 model token 太貴</td>
<td>個人和企業預算壓力</td>
</tr>
<tr>
<td>**安全**</td>
<td>各家安全策略不統一</td>
<td>風險管控困難</td>
</tr>
<tr>
<td>**便利性**</td>
<td>切換 agent 需要重新設定</td>
<td>工作效率降低</td>
</tr>
<tr>
<td>**資訊統一**</td>
<td>不同 agent 的知識不互通</td>
<td>重複工作、知識流失</td>
</tr>
</table>
### 市場現況
- **閉源模型**：功能強，但成本高
- **開源模型**：成本低，但生態系還在建立
- **大廠策略**：Anthropic、OpenAI、微軟都有自家生態，不容易被統一管理
- **未來不確定**：混合式？開源勝？閉源主導？
### 目標
找到一個「統一管理」的方式：
1. **成本控管** — 集中管理 token 消耗
2. **資訊安全** — 統一安全策略
3. **使用者便利** — 無縫切換 agent
4. **資訊統一** — 知識在 agent 之間流通
---
## 我們怎麼做的
### 第一步：發現 Omnigent
我們發現了 Omnigent——Databricks 開源的 meta-harness。它是目前少數嘗試解決這個問題的專案。
**Omnigent 的解法：**
<table header-row="true">
<tr>
<td>支柱</td>
<td>說明</td>
<td>解決什麼問題</td>
</tr>
<tr>
<td>**Combine**</td>
<td>一行 config 切換 agent</td>
<td>便利性</td>
</tr>
<tr>
<td>**Control**</td>
<td>動態安全策略、成本控制、OS 沙箱</td>
<td>安全、成本</td>
</tr>
<tr>
<td>**Share**</td>
<td>透過 URL 分享 live session</td>
<td>資訊統一</td>
</tr>
</table>
### 第二步：理解限制
但 Omnigent 也有挑戰：
- 目前不支援 Windows（需要 WSL2）
- 大廠不一定會配合（他們想保護自己的生態）
- 社群還很早期
### 第三步：長期研究
這不是一個可以快速解決的問題，而是一個需要長期研究的議題：
- 市場還在變化
- 大廠策略會調整
- 開源生態會成長
- 需要持續觀察和調整
### 第四步：發現這不是孤例，是一整個光譜
Omnigent 只是「定義 B」（multi-agent orchestration layer）眾多實作之一。
我們進一步盤點了 10 個業界主流框架，發現：
- 業界已經從「無狀態單 agent」演進到「多 agent orchestration」的完整光譜
- 可觀測性（OTel 追蹤 + 耐用執行）已是生產級標配，不是可選功能
- 四個代表性路線，各覆蓋一個正交的架構維度：

<table header-row="true">
<tr><td>框架</td><td>代表軸</td><td>核心手法</td><td>亮點</td></tr>
<tr><td>LangGraph</td><td>協調拓撲 — 集中式圖</td><td>顯式 StateGraph</td><td>Checkpointer 端到端持久化 + Time Travel</td></tr>
<tr><td>Agent Handoffs（OpenAI SDK）</td><td>協調拓撲 — 去中心化交接</td><td>transfer_to工具化</td><td>最低程式碼量的多 agent 協作模式</td></tr>
<tr><td>smolagents</td><td>動作表示法 — Code-as-Action</td><td>LLM 直接輸出 Python</td><td>打破 JSON tool calling 范式，單次往返完成多步</td></tr>
<tr><td>Mastra</td><td>記憶架構 — 觀察式記憶</td><td>Observer + Reflector 背景壓縮</td><td>LongMemEval 94.87% SOTA，5x-40x 壓縮</td></tr>
</table>

完整 10 框架 x 6 維度比較 → 見對應種子頁與 wiki
---
## 用了什麼技術/工具/概念
<table header-row="true">
<tr>
<td>技術/工具</td>
<td>用途</td>
<td>怎麼用</td>
</tr>
<tr>
<td>Omnigent</td>
<td>Meta-harness 實作</td>
<td>研究架構</td>
</tr>
<tr>
<td>Runner</td>
<td>統一 API</td>
<td>包裝任何 agent</td>
</tr>
<tr>
<td>OmniBox</td>
<td>OS 沙箱</td>
<td>安全隔離</td>
</tr>
<tr>
<td>Contextual Policies</td>
<td>動態安全策略</td>
<td>追蹤 session 狀態</td>
</tr>
<tr>
<td>LangGraph</td>
<td>業界實作參照</td>
<td>圖狀態機 + Checkpointer 持久化</td>
</tr>
<tr>
<td>OpenAI Agents SDK</td>
<td>業界實作參照</td>
<td>Handoff 工具化多 agent 協作</td>
</tr>
<tr>
<td>smolagents</td>
<td>業界實作參照</td>
<td>Code-as-Action 動作表示法</td>
</tr>
<tr>
<td>Mastra</td>
<td>業界實作參照</td>
<td>觀察式記憶（OM）背景壓縮</td>
</tr>
</table>
---
## 目前程度
### 已完成 ✅
- 理解 meta-harness 概念
- 研究 Omnigent 架構
- 建立 Omnigent 視覺地圖
- 比較 Omnigent vs Pi
- 理解市場挑戰和限制
- 盤點 10 個業界主流 framework（177 筆來源）
### 進行中 🚧
- 持續觀察市場變化
- 研究大廠策略變化（OpenAI/MS/Vercel 都已開源 SDK 協作層）
### 未開始 ⏳
- 實際部署 Omnigent
- 建立自己的 meta-harness
---
## 成效與感覺
### 感覺
這是一個**很有價值但很難**的研究。
**價值：**
- 個人和企業都有這個需求
- 成本、安全、便利性都是真實痛點
- 市場還在早期，有機會
**挑戰：**
- 大廠會保護核心生態（模型/平台鎖定），但協作層的 SDK 反而傾向開源搶市佔
- 開源 vs 閉源的未來不確定
- 技術上很難做到「統一管理」
**結論：**
這不是一個可以快速解決的問題，而是一個需要長期追蹤和研究的議題。我們能做的是：持續觀察、理解變化、在適當時機採取行動。
---
## 跟其他專題的關係
<table header-row="true">
<tr>
<td>專題</td>
<td>關聯性</td>
</tr>
<tr>
<td>AI 驅動的開發系統</td>
<td>Meta-harness 是「工具整合」的一環</td>
</tr>
<tr>
<td>AI Agent 架構研究</td>
<td>理解不同 agent 的架構</td>
</tr>
<tr>
<td>Cheerio 助理架構（W-074）</td>
<td>本次業界盤點是自建 harness 開工前的現況參照，直接影響「借鑑哪些 pattern、不綁定哪個框架」的決策</td>
</tr>
</table>
---
## 還可以做什麼
### 短期
- [ ] 持續觀察 Omnigent 發展
- [x] 研究其他 meta-harness 方案（已完成：10 框架 x 6 維度盤點）
- [ ] 評估 Tier 1 四個框架的 pattern 能否借鑑到 Cheerio 自建 harness
### 中期
- [ ] 追蹤 Handoff 工具化、觀察式記憶等具體技術點在 W-074 的落地可行性
- [ ] 研究大廠的策略變化
### 長期
- [ ] 建立自己的 meta-harness（如果市場需要）
- [ ] 探索 agent orchestration 的最佳實踐
- [ ] 測試 Omnigent 在 WSL2 的表現（低優先，觀察中，暫不主動測試）
---
## 💡 核心洞察
1. **多 agent 是趨勢** — 沒有一個 agent 能滿足所有需求
2. **切換是痛點** — 個人和企業都面臨切換的挑戰
3. **成本是關鍵** — 閉源太貴，開源在崛起
4. **大廠會保護核心生態**（模型/平台鎖定），但協作層的 SDK 反而傾向開源搶市佔 — 統一管理很難，但值得追求
5. **這是長期研究** — 市場還在變化，需要持續觀察
6. **業界已從「無狀態單 agent」演進到「多 agent orchestration」的完整光譜** — Omnigent 不是孤例，是這個光譜上的一個點
7. **可觀測性（OTel 追蹤 + 耐用執行）是生產級標配**，不是可選功能 — 這對 Cheerio 自建 harness 直接有啟發
---
## 🗺️ 架構決策與生態光譜
### 三層架構心智模型
```
第 3 層：元線束與治理層（Meta-Harness & Governance）
  └── Omnigent（Databricks）— 領域語意目錄、安全稽核、全域仲裁
第 2 層：多代理編排層（Multi-Agent Orchestration）
  └── LangGraph / OpenAI SDK / smolagents / Mastra — 狀態機、計算拓撲、工具分派
第 1 層：單代理驅動線束（Single-Agent Driver Harness）
  └── Pi Agent / Claude Code / Codex — 單一 LLM 循環、提示詞組合
```

### 決策分流指引
- 需要**確定性狀態控制 + 時光旅行**？→ LangGraph
- 需要**最輕量的多 agent 協作**？→ Agent Handoffs（OpenAI SDK 為例）
- 需要**突破 JSON tool calling 的表達力上限**？→ smolagents（Code-as-Action）
- 需要**長對話記憶管理 + TS 生態**？→ Mastra（觀察式記憶）

### 📐 完整 10 框架 x 6 維度比較
<table header-row="true">
<tr>
<td>框架</td>
<td>Agent Selection</td>
<td>Context Management</td>
<td>Parallel Execution</td>
<td>Failure Handling</td>
<td>觀測性分級</td>
</tr>
<tr>
<td>OpenAI Agents SDK</td>
<td>Handoff 工具化</td>
<td>Session History + Compaction</td>
<td>去中心化 Handoff + 經理委託</td>
<td>Hop Counter 安全熔斷</td>
<td>第一級</td>
</tr>
<tr>
<td>DeepSeek Harness</td>
<td>Supervisor-Worker</td>
<td>Append-Only 日誌</td>
<td>Spawn/Fork + Ralph Mode</td>
<td>Replay/Fork</td>
<td>第一級</td>
</tr>
<tr>
<td>LangGraph</td>
<td>StateGraph 拓撲約束</td>
<td>Reducer + Checkpointer</td>
<td>嵌套子圖</td>
<td>Time Travel + Resume</td>
<td>第一級</td>
</tr>
<tr>
<td>Vercel Eve</td>
<td>Subagent 目錄結構</td>
<td>Event Log + 確定性重放</td>
<td>Hierarchical Subagents</td>
<td>Suspend/Resume</td>
<td>第一級</td>
</tr>
<tr>
<td>Mastra</td>
<td>Workflow-as-Tool</td>
<td>觀察式記憶 5x-40x 壓縮</td>
<td>併行 DAG + Handoff</td>
<td>suspend/resume</td>
<td>第一級</td>
</tr>
<tr>
<td>Pydantic AI</td>
<td>FunctionTool 委託</td>
<td>DBOS Transaction Checkpoints</td>
<td>耐用子工作流</td>
<td>DBOS 重播</td>
<td>第一級</td>
</tr>
<tr>
<td>smolagents</td>
<td>Orchestrator-Worker</td>
<td>Private History 隔離</td>
<td>多 Specialist 並行</td>
<td>沙箱隔離</td>
<td>第一級</td>
</tr>
<tr>
<td>AutoGen</td>
<td>Swarm LLM 驅動</td>
<td>Actor Mailbox</td>
<td>Swarm + GraphFlow + Debate</td>
<td>UserProxyAgent</td>
<td>第二級</td>
</tr>
<tr>
<td>Semantic Kernel</td>
<td>SelectionStrategy</td>
<td>ChatHistory</td>
<td>AgentGroupChat</td>
<td>TerminationStrategy</td>
<td>第二級</td>
</tr>
<tr>
<td>CrewAI</td>
<td>Role-Play 分派</td>
<td>Embedding + 向量 RAG</td>
<td>Sequential/Hierarchical</td>
<td>有限</td>
<td>第三級</td>
</tr>
</table>

---
## 🗺️ 專題視覺地圖
[Meta-Harness 元鞍具 視覺地圖](https://app.notion.com/p/3c55979e3a8c81e0ab8acd1a6fb9017b)