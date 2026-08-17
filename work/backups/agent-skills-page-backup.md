---
Wiki Link: https://github.com/CheerioCorner/cheerio-wiki/blob/master/wiki/concepts/agent-skills.md
下一步: 灌溉 GitHub Copilot 和 Claude Code 既有種子（待執行）
專題名稱: Agent Skills
建立日期:
  end: null
  start: 2026-08-14
  time_zone: null
最後更新:
  end: null
  start: 2026-08-15
  time_zone: null
狀態: 🔬 研究
研究問題: Agent Skill 的封裝格式、撰寫規範、API 整合、企業治理與跨框架比較——不同 AI Agent 框架如何定義、分發和組合 Skill？有沒有一個統一的標準？
🌳 知識花園:
- 3bb5979e-3a8c-81ce-93fd-fd8be0c7ab49
- 3bb5979e-3a8c-81ba-a661-e06f1d8a1edc
- 3b35979e-3a8c-8129-ba33-f558c588a06f
🗺️ 視覺地圖:
- 3bc5979e-3a8c-81e0-bb89-f9066f64f664
---

# 🔬 Agent Skills
---
## 這是什麼
Agent Skills 是「**agent 如何封裝、分發和組合專業能力**」的研究。
1. **封裝格式**：什麼是 Skill？[SKILL.md](http://SKILL.md) 的三層漸進式揭露如何運作？
2. **撰寫規範**：怎麼寫出好的 Skill？
3. **API 整合**：如何透過 API 呼叫和管理 Skills？
4. **企業治理**：大規模部署時的安全審查、生命週期管理
5. **跨框架比較**：三框架的機制與設計取捨
## 限制與限制成因
> ⚠️ 預告：以下四個限制是所有 Skill 系統的本質限制，不是特定框架的實作疏漏。後面的實例和經驗會反覆印證這些限制如何在真實場景中發作。
### 跨框架共通限制
**1. Skill 是靜態指令文字，不是執行引擎**
Skill 系統的設計本質是「把一段文字指令注入 agent 的 context」，而不是「提供一個可執行的運行時」。以本專案的 `round-table` skill 為例：[SKILL.md](http://SKILL.md) 裡定義了完整的多 AI 協調協議（派出 subagent、序列輪轉、量化共識偵測），但這份文件本身不會執行任何東西——它只是被載入到 Pi 的 context 裡，真正的協調邏輯（派 subagent、呼叫 CLI、收集結果）必須由外部 agent runtime（Pi 的 orchestrator 能力）去執行。
這不是實作疏漏，而是刻意的 trade-off：Skill 選擇維持「可攜的知識封裝格式」（純文字，跨框架可讀），代價就是協調邏輯永遠要外包給 harness 原生機制。如果 Skill 做成執行引擎，就會綁死特定 runtime，喪失跨框架可攜性。
**2. 觸發是機率性的，不是保證的 API 呼叫**
Agent 對 Skill 的觸發取決於模型對 description 的語義理解，而非硬編碼的 if-else。這代表 Skill 系統從設計上就承認自己的觸發機制是「賭模型會不會判斷正確」。
這也解釋了為什麼 Anthropic 官方的 skill-creator 方法論要求 description「要有點 push」來對抗 under-trigger——如果觸發是確定性的 API 呼叫，就不需要刻意設計措辭。此外，觸發準確率會隨時間漂移：底層模型更新、harness 版本升級，原本有效的觸發詞可能失效，這產生了持續的維護成本（評估退化成本）。
**3. 漸進式揭露有資訊天花板**
Skill 採三層漸進式揭露：Metadata（\~100 words 常駐）→ [SKILL.md](http://SKILL.md) body（觸發時載入，\<500 行為佳）→ Bundled resources（按需讀取，無上限）。但「會不會被讀取」仍取決於 agent 當下判斷要不要展開 bundled resources——這是限制疊限制：先賭觸不觸發，再賭讀不讀附加資源。[SKILL.md](http://SKILL.md) body 的 500 行上限也意味著複雜知識必須拆分到 references 檔案，但拆分後的可發現性又依賴 agent 的主動探索意願。
**4. Skill 沒有內建的安全或事務性機制**
[SKILL.md](http://SKILL.md) 可以用文字寫「請小心整頁覆寫」，但無法在機制層面阻止 agent 犯錯。安全性必須靠外部設計補上——QA 閘門、diff-based 操作、版本控制——skill 文字本身只能提醒，不能強制。這也是本專案 2026-08-15 資料損毀事故的技術根源（詳見下文實戰經驗段落）。
### 各框架的緩解方式差異
三框架面對上述共通限制時，緩解方式不同（直接呼應「複合組合技」段落已建立的 Agent-based / Hook-based / Manual 三分法）：
- **Copilot（Agent-based）**：協調邏輯轉嫁給 dispatch 出去的 subagent runtime，skill 本身只需要定義「何時該 dispatch」。Custom Agents + Fleet Mode 提供了原生的子代理派生能力。
- **Claude（Hook-based）**：部分共通限制可以用 hooks 在特定事件點強制攔截（例如寫入前強制跑檢查），但 hook 是 harness 機制，不是 [SKILL.md](http://SKILL.md) 定義內建的——Claude 的 skill 系統「解決」了限制 4，其實只是把解法外包給另一個 harness 原生功能。
- **Pi（Manual）**：沒有框架幫你自動協調，所有共通限制都直接暴露給操作者，逼得本專案發展出「圓桌會議 → 雙重 QA → 全新 session 交叉驗證」這種人工加固的多層防禦。
## 為什麼要做這件事
<table header-row="true">
<tr>
<td>框架</td>
<td>Skill 格式</td>
<td>組合方式</td>
<td>巢狀/委派</td>
<td>工具鏈整合</td>
</tr>
<tr>
<td>Claude Code</td>
<td>[SKILL.md](http://SKILL.md)（三層揭露）</td>
<td>多 Skill 並行載入（≤8）</td>
<td>Subagents</td>
<td>MCP</td>
</tr>
<tr>
<td>GitHub Copilot SDK</td>
<td>[SKILL.md](http://SKILL.md)（格式兼容）</td>
<td>多 Skill + per-agent preload</td>
<td>Custom Agents + Fleet Mode</td>
<td>MCP + Custom Tools</td>
</tr>
<tr>
<td>Pi Agent</td>
<td>[SKILL.md](http://SKILL.md)（markup 注入）</td>
<td>逐一手動觸發</td>
<td>刻意不做</td>
<td>Extension System</td>
</tr>
</table>
## 複合組合技：三框架的設計哲學差異
### Copilot 用 Agent 組合：Custom Agents + Fleet Mode
Copilot 選擇了「**Agent-based 組合**」路線——定義多個具有 scoped tools 的 Custom Agent，由 runtime 自動委派。Fleet Mode 以 SQL todos 為協調狀態，多個 sub-agent 並行執行。（詳見 🌱 GitHub Copilot）
**為什麼這樣設計？** Copilot 的定位是「雲端開發平台」，需要處理多使用者、多 repo、多任務的併發場景。Fleet Mode 的 SQL todos 協調機制本質上是分佈式任務佇列——在雲端環境下很自然。（詳見 🌱 GitHub Copilot）
### Claude Code 用 Hook 組合：Lifecycle Hooks + Subagents
Claude Code 選擇了「**Hook-based 組合**」路線——透過 lifecycle hooks 在每個階段攔截和修改行為，搭配 subagents 處理並行任務。（詳見 🌱 Claude Code）
**為什麼這樣設計？** Claude Code 的定位是「終端機 AI coding agent」，強調單一開發者的深度 coding 體驗。Hooks 讓開發者精確控制 agent 的每個動作，subagents 更像是「專家顧問」而非「並行工人」。（詳見 🌱 Claude Code）
### Pi Agent 刻意不做自動組合：逐一手動觸發
Pi Agent 選擇了「**Manual 組合**」路線——Skill 一次只載入一個，由使用者手動觸發，不做自動委派。核心無 subagent、無 plan mode、無 maxSteps。（詳見 \[\[wiki/entities/pi-agent-core\|pi-agent-core\]\]）
### 總結：三種組合哲學
<table header-row="true">
<tr>
<td>框架</td>
<td>組合哲學</td>
<td>核心機制</td>
<td>適用場景</td>
</tr>
<tr>
<td>**Copilot**</td>
<td>Agent-based</td>
<td>Custom Agents + Fleet Mode + auto-delegation</td>
<td>雲端多任務並行、企業級治理</td>
</tr>
<tr>
<td>**Claude Code**</td>
<td>Hook-based</td>
<td>Lifecycle Hooks + Subagents</td>
<td>單一開發者深度 coding</td>
</tr>
<tr>
<td>**Pi Agent**</td>
<td>Manual</td>
<td>逐一手動觸發 + 可觀測性</td>
<td>本地個人助理、跨工具編排</td>
</tr>
</table>
三者不是「好壞」的差別，而是「定位」的差別。
## 本專案實際撰寫經驗
> 以下三個案例不是理論推導，是撞了三次牆才學到的。
### 案例一：round-table——「skill 只是指令注入」的第一次認知衝擊
**起點的誤解**：開始設計 `round-table` skill 時，心智模型是「把協調協議寫進 [SKILL.md](http://SKILL.md)，之後照著做就行」。
**實際發現**：[SKILL.md](http://SKILL.md) 被載入後，只是把協議文字塞進 Pi 的 context。真正的「派 subagent、序列輪轉、收集結果」完全在 [SKILL.md](http://SKILL.md) 之外發生——必須透過 `mcp__pi-bridge__ask_pi`（帶 `enable_extensions` + `approve_project`）讓 Pi 的 agent runtime 真的去執行 bash 呼叫、派出 subagent。Skill 本身是被動的知識/指令載體，複雜協作能力來自背後的 agent runtime，不是 skill 格式本身提供的。
**教訓**：Skill 提供的是協議文件層，執行永遠要靠 harness 原生能力補上。
### 案例二：高風險寫入的三方 QA 閘門演化
**問題**：花園（Notion）寫入原本只是「skill 讀取指令 → agent 照做」，但實測發現單一 agent 直接寫入常有錯——虛構 URL、視覺地圖放錯位置、agent 謊報完成。
**因應**：在 skill 之外疊加一層流程：
1. round-table 多方討論（不同觀點交叉驗證）
2. Claude + Gemini 雙重 QA（且要求先查證再下結論，不可憑記憶）
3. 全新 session 交叉驗證才能真正寫入
這條規則現在記錄在 `garden-write-requires-roundtable-qa` 準則裡。
**教訓**：Skill 驅動的 agent 工作流程，光靠「skill 指令寫得多嚴謹」不足以防止真實的執行錯誤，必須額外搭配「獨立於執行者的驗證步驟」。而且驗證本身如果只做表面檢查（例如只確認章節標題存在），一樣會漏掉問題——驗證的嚴謹度要跟寫入的風險成正比。
### 案例三：2026-08-15 資料損毀事故
**過程**：在強化 GitHub Copilot 種子頁時，用「整頁讀出 → 本地編輯 → `ntn pages edit --allow-deleting-content` 整頁覆寫」的方式改動內容。結果連續發生兩次真的資料遺失：
- 第一次：本地備份不完整，覆寫時把四個既有章節整段清空
- 第二次：某段落被 agent 自己填入「內容同前版，此處省略」這種占位字頂替真內容
兩次都是靠「全新 session、要求逐段貼實際內文關鍵字」的交叉驗證抓到，最後從 git 歷史（`wiki/entities/github-copilot.md`）救回原文才修復。
**技術根源**：LLM 缺乏檔案鎖（File Locking）與差異比對（Diff）強制驗證能力。「整頁讀出 → 修改 → 整頁覆寫」這種操作模式在複雜場景中必然引發資料覆蓋風險。[SKILL.md](http://SKILL.md) 的文字提醒攔不住這種錯誤——真正的修補是改成診斷式流程（強制 diff-based 更新而非整頁覆寫）。
**教訓**：skill 系統沒有內建的事務性機制（atomicity），涉及狀態變更的操作必須在 harness 層實現防護，skill 內部則必須定義逆向修復操作指令。教訓已經反饋成操作規範，不是只記錄事故本身。
## 如何寫出通用的 Skill
> 基於 Anthropic 官方 skill-creator skill 的撰寫方法論。
### Skill 的解剖結構
```javascript
skill-name/
├── SKILL.md (必要)
│   ├── YAML frontmatter (name, description 必填)
│   └── Markdown 指令
└── Bundled Resources (選用)
    ├── scripts/    — 確定性/重複性任務的可執行代碼
    ├── references/ — 按需載入的文件
    └── assets/     — 輸出用的檔案（模板、圖示、字型）
```
### 三層漸進式揭露
<table header-row="true">
<tr>
<td>層級</td>
<td>內容</td>
<td>載入時機</td>
<td>建議大小</td>
</tr>
<tr>
<td>Metadata</td>
<td>name + description</td>
<td>常駐在 context</td>
<td>\~100 words</td>
</tr>
<tr>
<td>[SKILL.md](http://SKILL.md) body</td>
<td>完整指令</td>
<td>觸發時載入</td>
<td>\<500 行</td>
</tr>
<tr>
<td>Bundled resources</td>
<td>scripts/references/assets</td>
<td>按需讀取</td>
<td>無上限</td>
</tr>
</table>
**關鍵原則**：[SKILL.md](http://SKILL.md) 接近 500 行時，加入額外的層級結構並清楚指引模型下一步去哪裡找。Reference 檔案從 [SKILL.md](http://SKILL.md) 中明確引用，並說明何時該讀取。
### Description 撰寫：有點 push，對抗 under-trigger
Claude 有「under-trigger」傾向——遇到可能相關的任務時傾向不使用 Skill。Description 必須主動一點：
- ❌ 不要寫：「How to build a simple fast dashboard」
- ✅ 要寫：「How to build a simple fast dashboard. Make sure to use this skill whenever the user mentions dashboards, data visualization, internal metrics, or wants to display any kind of company data, even if they don't explicitly ask for a 'dashboard.'」
### 「解釋為什麼」原則
與其寫死板的 MUST / NEVER，不如跟模型解釋原因。LLM 有好的 theory of mind，當你解釋「為什麼這件事重要」時，模型能用判斷力處理邊界案例，而不是死板地遵守規則。如果你發現自己在寫全大寫的 ALWAYS 或 NEVER，那是黃色警報——試著重新框架，解釋推理過程。
### Description Optimization 迴圈
1. 產生 20 個 eval queries（should-trigger + should-not-trigger）
2. 用 60% train / 40% test split 分割
3. 每個 query 跑 3 次取可靠觸發率
4. 迭代優化 description，選 test score 最高的版本（避免 overfit）
5. 關鍵：should-not-trigger 的 near-miss 負樣本最有價值
### 多領域組織
當 skill 支援多個框架或領域時，用 references 分檔：
```javascript
cloud-deploy/
├── SKILL.md (共同工作流程 + 選擇邏輯)
└── references/
    ├── aws.md
    ├── gcp.md
    └── azure.md
```
Agent 只讀取相關的 reference 檔案，[SKILL.md](http://SKILL.md) 保持精簡。
## 簡單 vs 複雜實例
> 本專案的三個真實 Skill，展示從「單一知識封裝」到「複雜協調協議」的光譜。
### 簡單實例：tdd skill
- **檔案結構**：單一 [SKILL.md](http://SKILL.md)（37 行）+ 2 個 references 檔案（[tests.md](http://tests.md)、[mocking.md](http://mocking.md)）
- **Frontmatter**：只有 name + description，無 scripts、無多 agent 協調
- **內容**：TDD 紅綠燈循環的核心原則——好測試的定義、seam 概念、三個反模式、循環規則
- **本質**：「教一個 agent 怎麼想」——純粹的知識封裝，觸發後改變 agent 的思考和行為模式
這是 Skill 系統最自然的使用方式：把人類的專業知識壓縮成 agent 可消化的指令包。
### 複雜路線 1：round-table skill（一個 skill 定義協調協議）
- **內容**：一個 skill 裡定義完整的多 AI 協調協議——Pi 當主持人（不參與討論）、派出 subagent、依序呼叫 Claude/Gemini/Copilot/Codex CLI、量化共識偵測公式（newArguments / coverageRate / agreementRate 門檻）、多輪迭代直到共識或人類介入、產出結構化會議紀要
- **本質**：「教一個 agent 怎麼指揮一群 agent」——這是協調協議的規格書
但如素材 B 所述，這個 skill 本身不執行任何東西。它看起來是「一個 skill 定義了複雜協議」，但拆穿了看，複雜度真正被消化的地方是 harness 層（Pi 的 orchestrator 能力），skill 只是文件化了協議、沒有執行協議。
### 複雜路線 2：knowledge-garden（5 個獨立 skill 分工協作）
本專案的知識花園功能被拆成 5 個獨立協作的 skill：
<table header-row="true">
<tr>
<td>Skill</td>
<td>職責</td>
</tr>
<tr>
<td>knowledge-garden</td>
<td>維護入口，觸發條件偵測</td>
</tr>
<tr>
<td>knowledge-garden-page-content</td>
<td>根據資料產生完整頁面內容</td>
</tr>
<tr>
<td>knowledge-garden-to-raw</td>
<td>從花園抓取內容建立 raw 來源</td>
</tr>
<tr>
<td>knowledge-garden-trigger</td>
<td>偵測觸發條件並執行回流</td>
</tr>
<tr>
<td>knowledge-garden-visualmap</td>
<td>產生視覺地圖（Mermaid）</td>
</tr>
</table>
這是「用多個小 skill 分工」而不是「一個 skill 裡塞多階段邏輯」的複雜化路線，跟 round-table 的「一個 skill 定義協調協議」是不同的路線。兩種路線都是可行的，選擇取決於複雜度的性質（見下方結論段落）。
## 複雜多 Skill 組合時，要不要回到各 Harness 自家寫法？
> 結論：取決於複雜度的性質，不是複雜度的高低。
### 判準一：複雜度是「協調邏輯」還是「情境覆蓋」？
**若複雜度來自協調邏輯**（需要序列化、狀態管理、多方輪轉、結果彙整）→ **執行層必須回到各 harness 原生機制**。
理由：Skill 系統設計上刻意不做執行引擎，你不可能靠 [SKILL.md](http://SKILL.md) 文字本身實現真正的多 agent 協調。round-table 能運作，靠的從來不是協議規格書本身，而是 Pi 作為 harness 原生 orchestrator 去執行派工。
**若複雜度來自情境覆蓋廣度**（很多觸發情境，但每個情境內部邏輯單純）→ **應該留在 skill 系統內，用多 skill 分工解決**。
knowledge-garden 用 5 個獨立 skill 分工正是這條路線。Skill 系統的機率性觸發 + 漸進式揭露機制，本來就是為了處理「多情境、各自簡單」而設計的。
**主結論**：「協調交給 harness 原生機制，知識與情境覆蓋交給 skill 分工。」
### 判準二：組合 vs 互通
但「要不要回到 harness」本身被混淆了兩個不同的問題：
**組合（單框架內動態派生子代理）**：這件事現在就能做，本專案的 round-table 已經是可行證明——Pi 動態派出 subagent（可指定模型）加上 `chat-with-claude` / `chat-with-gemini` / `chat-with-copilot` 作為對各框架 CLI 的薄轉接層。子代理繼承的是父 session 既有的權限與沙箱邊界，信任來源是同一個 harness，不需要額外的 CI / registry / 簽章基礎設施。
**互通（映像檔跨框架搬運）**：把本專案的 skill 原封不動丟給另一套 harness 的子代理系統去讀——這才是真正需要標準化 metadata + 簽章制品的問題，因為跨框架時信任邊界消失了。本專案目前沒有跨框架搬運 skill 本體的需求：`chat-with-gemini`、`chat-with-copilot` 走的是「呼叫對方 CLI」而不是「把我方 skill 部署進對方的子代理系統」，規避了互通問題，不代表互通問題被解決了。
### Skill 的角色不只是行為指令
Skill 還可以是「被動態派生的多個子代理共用的開機設定」——同一份 skill 文字，被 N 個並行子代理各自讀取後獨立執行。這是素材 C 中 round-table 的真實樣貌，也是 Gemini 所說的「子代理映像檔模式」在本專案的具體實現。
### 需要警惕的三個暗礁
1. **抽象洩漏**：單框架組合時，skill 往往會高度依賴該 harness 的特殊上下文注入機制或工具呼叫協議，表面上是文字，實際上已與特定框架深度鎖定（Vendor Lock-in）。設計組合時需提防遷移成本。
2. **狀態漂移**：動態派生子代理涉及寫入檔案、呼叫外部 API 時，子代理失敗的事務回滾機制、親子代理間的狀態同步，不能只靠 skill 裡的自然語言描述，必須由 harness 定義嚴謹的協調協議。
3. **階梯互通**：互通不是「全有全無」。即使沒有中心化 registry，只要定義好基礎的輸入輸出結構約定（I/O Schema Contract），skill 就能在輕量級 harness 之間實現優雅降級的互通。
### 收斂句
> **Skill 系統的可攜性優勢應該保留給「知識封裝」用途，不要勉強拿來做跨 agent 執行編排——那違背了它的設計初衷。凡是涉及狀態變更的複雜操作，必須在 harness 層實現防護機制，skill 內部則定義逆向修復操作指令。**
---
## 相關種子
<table header-row="true">
<tr>
<td>種子</td>
<td>關係</td>
<td>說明</td>
</tr>
<tr>
<td>🌿 GitHub Copilot</td>
<td>灌溉</td>
<td>Copilot SDK 的 Custom Agents/Skills/Hooks + Skill 撰寫規格已整合</td>
</tr>
<tr>
<td>🌿 Claude Code</td>
<td>灌溉</td>
<td>Claude Code 的 Skill 系統 + 撰寫規格 + 企業治理 + API 整合已整合</td>
</tr>
<tr>
<td>🌿 mattpocock/skills</td>
<td>關聯</td>
<td>一個具體的 skills 集合實作（162k stars）</td>
</tr>
</table>
> ⚠️ Codex/ChatGPT 尚未收集資料，未來有專屬種子時比照 Claude Code / GitHub Copilot 的模式補上撰寫規格。
## 研究筆記
### 2026-08-15：重組
- 3 顆子種子（撰寫方法論/企業治理/API 整合）併入 Claude Code 和 GitHub Copilot 種子頁
- 研究專題引用改指向新位置
### 2026-08-15：補強五個段落
- 補齊限制成因、本專案實戰經驗、通用撰寫方法論、簡單/複雜實例、複雜組合決策判準五個段落（圓桌會議 + Claude/Gemini QA）
### 2026-08-14：初始研究
- 完成 Anthropic 官方 Agent Skills 7 篇文件 ingest
- 完成 GitHub Copilot SDK 官方文件 22 篇 ingest
- 圓桌會議決議：新建研究專題
