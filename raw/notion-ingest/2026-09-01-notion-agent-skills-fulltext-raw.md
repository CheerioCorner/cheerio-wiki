---
Origin: null
Run-ID: ''
Verification-ID: ''
Wiki Link: https://github.com/CheerioCorner/cheerio-wiki/blob/master/wiki/concepts/agent-skills.md
下一步: 灌溉 GitHub Copilot 和 Claude Code 既有種子（待執行）
專題名稱: Agent Skills
建立日期:
  end: null
  start: 2026-08-14
  time_zone: null
最後更新:
  end: null
  start: 2026-08-18
  time_zone: null
狀態: 🔬 研究
研究問題: Agent Skill 的封裝格式、撰寫規範、API 整合、企業治理與跨框架比較——不同 AI Agent 框架如何定義、分發和組合 Skill？有沒有一個統一的標準？
🌳 知識花園:
- 3bb5979e-3a8c-81ce-93fd-fd8be0c7ab49
- 3bb5979e-3a8c-81ba-a661-e06f1d8a1edc
- 3b35979e-3a8c-8129-ba33-f558c588a06f
- 3bf5979e-3a8c-8144-b330-f64fd3514884
- 3bf5979e-3a8c-814d-b368-eecb9fed0fee
🗺️ 視覺地圖:
- 3bc5979e-3a8c-81e0-bb89-f9066f64f664
---

# Skill —— AI Agent 技能系統
> 一篇完整的研究專題，從核心概念到跨框架比較、從設計方法論到實戰教訓，系統性梳理 AI Agent Skill 生態系的全貌。
---
## 一、Skill 是什麼？
### 1.1 核心定義
Skill 是**可重複使用、基於檔案系統的模組化能力單元**。它把「指令＋中繼資料＋可選資源」封裝成一個目錄，為 AI Agent 提供特定領域的專業知識——工作流程、上下文、最佳實踐——將通用代理轉變為專家。（參考：wiki/sources/[2026-08-14-agent-skills-overview.md](http://2026-08-14-agent-skills-overview.md)，Anthropic 官方文件）
和一次性 prompt 最大的差別：prompt 是每次都要重新貼、用完就丟；Skill 是建好之後，Agent 在正確時機自動載入、重複使用。你可以把 prompt 想成「每次都手寫的信」，Skill 則是「印好的標準信封——內容完整，蓋上特定場合的郵戳就能寄出」。
> **觸發模式的兩種設計**：Skill 的「正確時機」在不同框架中有不同的實作方式。**Model-invoked**（模型觸發）：模型根據 description 語意判斷是否需要載入該 Skill，如 Claude Code 與 Copilot——Skill 的 description 始終在 context 中，模型自動決定觸發與否。**User-invoked**（使用者觸發）：使用者明確呼叫 Skill，如 Pi Agent 的 `/skill:<name>`——system prompt 以 markup 列出所有 Skill 的 name + description，但不自動注入內容，由使用者決定何時呼叫。這是設計選擇的光譜，不是矛盾：即使在 Model-invoked 框架中，description 的品質仍然決定觸發準確度；即使在 User-invoked 框架中，description 仍然是模型理解 Skill 用途的唯一依據。（原創：Cheer 於本研究專題中提出的分類框架，整合自 wiki/entities/[pi-agent-core.md](http://pi-agent-core.md) 與 wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md) 的觀察）
### 1.2 三層漸進式揭露（Progressive Disclosure）
這是 Skill 系統最關鍵的架構設計。所有 AI Agent 的 context window 都是公共資源，塞太多東西就會分散注意力、降低品質。三層揭露的目的是：**只有相關內容佔用 context**。（參考：wiki/sources/[2026-08-14-agent-skills-overview.md](http://2026-08-14-agent-skills-overview.md)，Anthropic 官方文件；wiki/concepts/[agent-skills.md](http://agent-skills.md)，本專案整理之根概念）
<table header-row="true">
<tr>
<td>層級</td>
<td>載入時機</td>
<td>Token 成本</td>
<td>內容</td>
</tr>
<tr>
<td>**第 1 層：中繼資料**</td>
<td>始終（啟動時）</td>
<td>約 100 tokens / Skill</td>
<td>YAML `name`  • `description`</td>
</tr>
<tr>
<td>**第 2 層：指令**</td>
<td>Skill 被觸發時</td>
<td>\< 5k tokens</td>
<td>[SKILL.md](http://SKILL.md) 主體</td>
</tr>
<tr>
<td>**第 3 層以上：資源**</td>
<td>按需</td>
<td>存取前為零</td>
<td>腳本、參考檔案、模板</td>
</tr>
</table>
關鍵設計：腳本透過 bash 執行，只有**輸出**進入 context——腳本程式碼本身永不載入。這使得捆綁大量資源的 Skill 不會造成 context 負擔。（參考：wiki/entities/[anthropic-agent-skills.md](http://anthropic-agent-skills.md)，Anthropic 官方 Skill 系統實作）
這個設計的精妙之處在於它解決了一個根本矛盾：你希望 Skill 包含足夠的知識和工具，但 context window 是有限的。三層揭露讓你在第一層只付 100 tokens 的「索引費」，在需要時才支付完整內容的「載入費」。
### 1.3 Skill 的檔案結構
Anthropic 官方 skill-creator 方法論定義的標準結構如下（參考：wiki/concepts/[skill-design-methodology.md](http://skill-design-methodology.md)「Step 5：交辦包設計」，本專案花園既有內容也如此記載）——**這是應該記住的主要慣例**：
```javascript
skill-name/
├── SKILL.md               # 主要指令（必要，觸發時載入）
├── scripts/                # 確定性/重複性任務的可執行代碼（選用）
│   └── helper.py
├── references/             # 按需載入的參考文件（選用）
│   └── guide.md
└── assets/                 # 輸出用的檔案，如模板、圖示（選用）
    └── template.docx
```
這對應三層漸進式揭露的實踐：先看標籤（name + description）→ 再讀交辦單（[SKILL.md](http://SKILL.md)）→ 卡住才翻附錄（scripts/references/assets）。
> **補充：扁平檔案的簡化範例**。部分概覽文件（`wiki/entities/anthropic-agent-skills.md`、`wiki/concepts/agent-skills.md` 裡的 "my-skill" 範例）會用 `REFERENCE.md`、`FORMS.md` 這類扁平檔案示範「第三層資源理論上可以是任意命名的檔案，不強制用資料夾」：
	```javascript
my-skill/
├── SKILL.md
├── REFERENCE.md         # 參考資料（按需載入）
├── FORMS.md             # 進階指南（按需載入）
└── scripts/
    └── analyze.py
	```
> 這只是用來說明彈性的簡化示意，**不是**另一套跟官方標準並列的規範。小型、資源少的 Skill 可以用扁平檔案；正式建議、尤其是資源較多或要交辦給他人維護的 Skill，一律用 `scripts/references/assets` 資料夾組織。
[SKILL.md](http://SKILL.md) 的 frontmatter 必須包含兩個欄位：
```yaml
---
name: my-skill-name          # ≤64 字元，小寫/數字/連字號
description: ...             # ≤1024 字元，功能 + 使用時機
---
```
命名慣例推薦動名詞形式（`processing-pdfs`、`analyzing-spreadsheets`），描述同時包含功能**以及**觸發條件。（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)，Anthropic 官方 Skill 撰寫最佳實踐）
### 1.4 跨平台使用
Skill 並非綁定單一平台。以 Anthropic 官方系統為例：
- **Claude API**：透過 Messages API 的 `container` 參數指定 Skills，需要程式碼執行工具 + beta header `skills-2025-10-02`。Skills 在沙箱容器中執行，無網路存取。（參考：wiki/sources/[2026-08-14-agent-skills-api-quickstart.md](http://2026-08-14-agent-skills-api-quickstart.md)，Anthropic 官方 API 文件）
- **Claude Code**：基於檔案系統，放置於 `~/.claude/skills/`（個人）或 `.claude/skills/`（專案），也可透過 Plugin Marketplace 安裝。（參考：wiki/sources/[2026-08-14-anthropics-skills-github-repo.md](http://2026-08-14-anthropics-skills-github-repo.md)，Anthropic 官方 GitHub repo）
- [**claude.ai**](http://claude.ai)：Settings \> Features 上傳 zip 檔案，適用於啟用程式碼執行的付費方案。（參考：wiki/sources/[2026-08-14-agent-skills-overview.md](http://2026-08-14-agent-skills-overview.md)，Anthropic 官方文件）
但有一個重要限制：**自訂 Skills 不跨平台同步**，需要在每個平台分別管理。（參考：wiki/concepts/[agent-skills.md](http://agent-skills.md)，本專案整理之根概念）
---
## 二、如何設計一支 Skill 的邊界
在「怎麼寫好一支 Skill」之前，先回答更上游的問題：**這支 Skill 的邊界在哪裡？**
### 2.1 為什麼邊界很重要
常見的錯誤是把太多功能塞進一支 Skill——又想讓 AI 查資料、又想讓它更新資料、又想讓它產生報告。結果是 description 太模糊、觸發不準確、LLM 混淆意圖。另一個極端是拆得太細，導致管理成本暴增、組合困難。
（參考：wiki/sources/[2026-08-17-devops-skill-presentation.md](http://2026-08-17-devops-skill-presentation.md)，Cheer 自製簡報；原創：wiki/concepts/[skill-design-methodology.md](http://skill-design-methodology.md)，Cheer 基於 DevOps Board 實戰提煉之設計方法論）
### 2.2 Intention-First 分析順序
先不要急著拆 Skill，先看人類真正想完成什麼。分析順序應該是：
1. **Human Intention（人類意圖）**：為什麼要做這件事？
2. **Event（狀態變化）**：這次更新代表什麼？
3. **Boundary（責任邊界）**：哪些是同一件事？
4. **Skill（可執行框架）**：切出可獨立執行的單元
核心原則：系統設計要從人的意圖開始往下拆。太早看系統（有哪些 API、資料表怎麼設計）會迷失方向。（原創：Cheer 於 2026-08 DevOps Skill 簡報中提出，見 wiki/sources/[2026-08-17-devops-skill-presentation.md](http://2026-08-17-devops-skill-presentation.md)；wiki/concepts/[skill-design-methodology.md](http://skill-design-methodology.md) 為 Cheer 基於實戰提煉之概念頁）
### 2.3 Event Storming 拆邊界
把「發生的事」攤在時間軸上，從中找出邊界。**判準：動詞不一樣的地方，就是邊界。**
例如「升級案處理完已知問題，接下來要準備結案會議簡報——幫我更新工作」，攤開來看：
1. 標記目前工作完成（寫入）
2. 記錄花了多少工時（寫入）
3. 新增結案簡報工作（寫入）
4. 查目前進度與工時（讀取）← **動詞改變了**
「寫」和「讀」是不同性質的操作——寫有副作用、讀沒有。切開這兩個邊界，得到兩支 Skill：`work-maintainer`（寫入）和 `work-status`（讀取）。好處是叫得對 Skill、風險隔離、各修各的。（參考：wiki/concepts/[skill-design-methodology.md](http://skill-design-methodology.md)，Cheer 基於 DevOps Board 實戰提煉；原創：「動詞改變＝邊界」的判準為 Cheer 在 DevOps Skill 簡報中提出的核心洞察）
這對應到 Domain-Driven Design 的幾個核心概念：Bounded Context（同一個詞在不同情境是不同的東西）、Single Responsibility（一支只做一件事）、Design-First（先把邊界與契約想清楚，才動手寫）。（參考：wiki/sources/[2026-08-17-devops-skill-presentation.md](http://2026-08-17-devops-skill-presentation.md)，Cheer 自製簡報中引用之 DDD 理論）
### 2.4 決策樹方法
「流程圖，就是規格。」
把所有意圖畫成一棵決策樹：根節點是 Human 意圖（自然語言），中間節點是分類判斷（跟哪個層級／類別有關），**葉節點就是每一支 Skill 的邊界**。
以 DevOps Board 系列為例，12 條 Human 意圖（分六大類別：Collection、Project、Team、Board、Work、Analytics），加上 1 支獨立的 Auth Skill，最終切出 13 支 Skill。這個過程的關鍵洞察是：**流程圖不是起點，領域知識才是**。地基是人類餵養的領域知識（12 條意圖＋領域脈絡），中間是意圖決策樹（流程圖），上面才是 13 支 Skill（成品）。地基不穩，上面的流程圖與 Skill 都會塌。（原創：Cheer 於 2026-08 DevOps Skill 簡報中提出之決策樹方法，見 wiki/sources/[2026-08-17-devops-skill-presentation.md](http://2026-08-17-devops-skill-presentation.md)；wiki/concepts/[skill-design-methodology.md](http://skill-design-methodology.md) 為 Cheer 基於實戰提煉之概念頁）
### 2.5 交辦包設計
把每支 Skill 想成「交辦一件事給一位新同事」：
<table header-row="true">
<tr>
<td>檔案</td>
<td>角色</td>
<td>必要性</td>
</tr>
<tr>
<td>`SKILL.md`</td>
<td>交辦單：怎麼做、規則是什麼</td>
<td>必要</td>
</tr>
<tr>
<td>`scripts/`</td>
<td>現成工具：照著跑就對</td>
<td>選用</td>
</tr>
<tr>
<td>`references/`</td>
<td>參考手冊：卡住才翻</td>
<td>選用</td>
</tr>
<tr>
<td>`assets/`</td>
<td>空白範本：照格式填</td>
<td>選用</td>
</tr>
</table>
Progressive Disclosure 在這裡的實踐：先看標籤（name + description）→ 再讀交辦單（[SKILL.md](http://SKILL.md)）→ 卡住才翻附錄（scripts / references / assets）。（參考：wiki/concepts/[skill-design-methodology.md](http://skill-design-methodology.md)，Cheer 基於 DevOps Board 實戰提煉）
---
## 三、如何寫出高品質的 Skill
邊界切好之後，下一步是把內容寫好。以下是從 Anthropic 官方方法論和實戰經驗中提煉出的撰寫原則。
### 3.1 核心原則
**簡潔是關鍵。** Context window 是公共資源。預設假設：Claude 已經非常聰明，只添加它尚未擁有的上下文。（參考：wiki/sources/[2026-08-14-skill-writing-best-practices.md](http://2026-08-14-skill-writing-best-practices.md)，Anthropic 官方 Skill 撰寫最佳實踐）
- ✅ 約 50 tokens 的簡潔指令（假設 Claude 知道 PDF 和函式庫）
- ❌ 約 150 tokens 的冗長解釋（包含 Claude 已知的基礎知識）
**設定適當自由度。** 將具體程度與任務的脆弱性和變異性匹配：（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)，Cheer 基於 Anthropic 官方文件提煉之概念頁）
<table header-row="true">
<tr>
<td>自由度</td>
<td>適用場景</td>
<td>範例</td>
</tr>
<tr>
<td>**高**</td>
<td>多種方法都有效、上下文決定</td>
<td>Code review 流程</td>
</tr>
<tr>
<td>**中**</td>
<td>偏好模式、可接受變化</td>
<td>報告產生模板</td>
</tr>
<tr>
<td>**低**</td>
<td>操作脆弱、一致性至關重要</td>
<td>資料庫遷移腳本</td>
</tr>
</table>
類比：窄橋（兩側懸崖）→ 低自由度、精確指示；開闊原野 → 高自由度、大致方向。
**跨模型測試。** Haiku、Sonnet、Opus 各有不同需求——Haiku 可能需要更明確的指引，Sonnet 要清晰高效，Opus 則要避免過度解釋。（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)，Cheer 基於 Anthropic 官方文件提煉）
### 3.2 Description 撰寫技巧
Description 是 Skill 的「名片」——它在第一層漸進式揭露中始終佔用 context，決定了 Agent 能不能正確觸發這支 Skill。
撰寫規則：（參考：wiki/sources/[2026-08-14-skill-writing-best-practices.md](http://2026-08-14-skill-writing-best-practices.md)，Anthropic 官方 Skill 撰寫最佳實踐明確要求「始終使用第三人稱」）
- 使用**第三人稱**（Anthropic 官方明確要求）
- 同時包含功能**以及**使用時機
- 具體並包含關鍵詞（例如不要只寫「處理文件」，要寫「從 PDF 檔案中提取文字並結構化」）
- ≤1024 字元
Description 寫得不好會導致兩個問題：**under-trigger**（該觸發沒觸發，因為描述太模糊）和 **over-trigger**（不該觸發卻觸發了，因為描述太廣泛）。
### 3.3 常見撰寫模式
**範本模式**：嚴格 vs 彈性指引。有些 Skill 需要嚴格的格式要求（例如財務報告），有些則給合理預設但允許自由判斷。
**範例模式**：提供輸入/輸出配對，比描述更清楚傳達所需風格。
**條件式工作流程**：用決策點引導 Agent 走不同路徑。例如「先判斷是新增還是編輯，走不同流程」。
**驗證循環**：執行驗證器 → 修復錯誤 → 重複。品質關鍵任務必備的模式。（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)，Cheer 基於 Anthropic 官方文件提煉）
### 3.4 寫入安全模式（Write-back Safety Gate）
處理有副作用的寫入操作——比驗證循環更嚴格的安全機制。四步安全門：（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)，Cheer 基於 Anthropic 官方文件提煉）
1. **`--dry-run`**** 先預覽**：先跑一次，不動任何資料
2. **人類確認**：把預覽結果攤給人看、等點頭
3. **`--confirm`**** 才寫入**：確認後才真正執行
4. **讀回驗證**：寫完再讀一次，核對無誤
三級分類：
<table header-row="true">
<tr>
<td>分類</td>
<td>準則</td>
<td>範例</td>
</tr>
<tr>
<td>✓ 要</td>
<td>明確的事實、明確的歸屬</td>
<td>「我的」升級案、指派給我的 WIT</td>
</tr>
<tr>
<td>! 小心</td>
<td>會動到別人的資料或正式設定</td>
<td>改到同事的工時 → 需要更高權限</td>
</tr>
<tr>
<td>✕ 不要</td>
<td>AI 推測、幻想、沒有證據的結論</td>
<td>AI 自己猜出來的工時數字</td>
</tr>
</table>
核心規則：**不可逆的事，人類永遠先看過一眼。這條規則沒有例外。**
這套安全設計在不同場景被獨立發明出來——例如 DevOps 簡報的「回寫安全門」四步驟，以及本專案實戰中 `notion_verify.sh` 的 DB read-back 三層驗證。兩者核心精神一致：**不信任「寫入指令回報成功」，必須用讀回來驗證**。（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)；原創：跨場景共通模式的對比分析為 Cheer 於本研究專題中提出）
### 3.5 Description Optimization 迭代迴圈
Description 的品質不是一次寫對的，而是靠系統性的迭代優化。以下是從實戰中提煉出具體數字的優化流程：（沿用既有花園內容）
1. **產生 20 個 eval queries**：一半是 should-trigger（應該觸發），一半是 should-not-trigger（不應該觸發）
2. **用 60% train / 40% test split 分割**：避免在訓練集上過擬合
3. **每個 query 跑 3 次取可靠觸發率**：LLM 觸發有隨機性，單次跑不準
4. **迭代優化 description**：選 test score 最高的版本，不要選 train score 最高的（overfit）
5. **關鍵洞察**：should-not-trigger 的 **near-miss 負樣本**最有價值——那些「差一點就誤觸發」的案例，最能幫你收緊 description 邊界
以上是從泛化的評估流程收斂為可操作的具體數字。Anthropic 官方建議的通用評估流程是：（參考：wiki/sources/[2026-08-14-skill-writing-best-practices.md](http://2026-08-14-skill-writing-best-practices.md)，Anthropic 官方 Skill 撰寫最佳實踐）
1. 識別差距（無 Skill 時的失敗案例）
2. 建立 3 個評估情境
3. 建立基準
4. 撰寫最少指示
5. 迭代改進
一個實用的技巧是 **Claude A+B 迭代法**：Claude A 是協助設計和改進指示的專家，Claude B 是載入 Skill 執行真實任務的代理。交替進行——觀察 B 的行為 → 帶回 A 改進 → 再測試。觀察重點：意外的探索路徑、遺漏的連結、過度依賴的章節、被忽略的內容。（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)，Cheer 基於 Anthropic 官方文件提煉）
### 3.6 反模式清單
<table header-row="true">
<tr>
<td>❌ 反模式</td>
<td>✅ 正確做法</td>
</tr>
<tr>
<td>Windows 路徑 `scripts\helper.py`</td>
<td>Unix 路徑 `scripts/helper.py`</td>
</tr>
<tr>
<td>提供 5 種 PDF 函式庫選擇</td>
<td>提供預設 + escape hatch</td>
</tr>
<tr>
<td>深層巢狀（[SKILL.md](http://SKILL.md) → A → B → C）</td>
<td>只一層深</td>
</tr>
<tr>
<td>時效性資訊（「2025 年 8 月前用舊 API」）</td>
<td>舊模式章節（`<details>`）</td>
</tr>
<tr>
<td>不一致術語（混用 URL / route / path）</td>
<td>統一用詞</td>
</tr>
<tr>
<td>不處理錯誤的腳本</td>
<td>明確錯誤處理 + helpful 訊息</td>
</tr>
<tr>
<td>魔術數字 `TIMEOUT = 47`</td>
<td>自我記錄 `REQUEST_TIMEOUT = 30  # HTTP 通常 <30s`</td>
</tr>
</table>
（參考：wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)，Cheer 基於 Anthropic 官方文件提煉）
## 四、Skill / Hook / Extension：三層能力邊界
Skill 很強大，但它有天然的限制。要理解這些限制，需要把它放到更大的擴充架構中去觀察。
### 4.1 三層能力光譜
<table header-row="true">
<tr>
<td>層級</td>
<td>本質</td>
<td>驅動方式</td>
<td>適用場景</td>
</tr>
<tr>
<td>**Skill**</td>
<td>宣告式指導——提供知識、流程、規則</td>
<td>依框架而異：Model-invoked 框架中模型自動觸發，User-invoked 框架中靠人類呼叫</td>
<td>重複性任務、有明確流程的工作</td>
</tr>
<tr>
<td>**Hook**</td>
<td>事件驅動強制——在生命週期中攔截、檢查、強制規則</td>
<td>主動（事件觸發，不必人類每次記得叫）</td>
<td>安全把關、合規審計、不可逆操作的前置檢查</td>
</tr>
<tr>
<td>**Extension / Agent**</td>
<td>平台原生能力延伸——把角色、權限、工具封裝成執行單元</td>
<td>常駐服務、跨系統整合</td>
<td>需要自訂工具、需要存取平台 API、需要常駐服務</td>
</tr>
</table>
（參考：wiki/concepts/[agent-extensibility-hierarchy.md](http://agent-extensibility-hierarchy.md)，Cheer 基於 DevOps Board 實戰提煉之概念頁；wiki/sources/[2026-08-17-devops-skill-presentation.md](http://2026-08-17-devops-skill-presentation.md)，Cheer 自製簡報）
### 4.2 Skill 的限制
Skill 讓 AI「變懂」，提供知識與流程。但它有幾個根本限制：（參考：wiki/concepts/[agent-extensibility-hierarchy.md](http://agent-extensibility-hierarchy.md)，Cheer 基於 DevOps Board 實戰提煉）
- 在 User-invoked 框架中，要靠**人類記得叫它**——如果忘了叫，知識就白費了；在 Model-invoked 框架中，description 品質決定觸發準確度，但仍有 under-trigger 風險
- LLM 可能**選錯 Skill**（13 支擺在那裡，AI 挑錯了那一支）
- LLM 可能**跳過步驟**（它覺得不是必要條件，就自己省略掉）
- LLM 可能**產生幻覺**（查不到就自己編一個看起來合理的答案）
> ⚠️ 注意：上述「User-invoked / Model-invoked」描述的是**觸發模式**（Skill 被呼叫的方式），而下一節 4.3～4.5 討論的「Agent-based / Hook-based / Manual」描述的是**組合協調哲學**（多個能力如何被編排執行）。兩者是不同維度——觸發模式回答「誰決定何時叫 Skill」，組合協調回答「叫了之後怎麼串起來做事」。例如 Pi Agent 在觸發模式上是 User-invoked，但在組合協調上屬於 Manual（需要人類手動串接多支 Skill）。
#### 四個跨框架共通限制（結構化版本）
以上四點是直覺式的列舉，但它們其實對應到 Skill 系統架構層面的四個根本限制。這四個限制不因框架而異——無論是 Claude Code、Copilot SDK 還是 Pi Agent，都必須面對：（沿用既有花園內容）
<table header-row="true">
<tr>
<td>#</td>
<td>限制</td>
<td>說明</td>
<td>影響</td>
</tr>
<tr>
<td>1</td>
<td>**Skill 是靜態指令文字，不是執行引擎**</td>
<td>真正協調邏輯必須外包給 harness 原生機制</td>
<td>round-table 的 [SKILL.md](http://SKILL.md) 定義了完整協議，但這份文件本身不會執行任何東西，真正的「派 subagent、序列輪轉、收集結果」完全在 [SKILL.md](http://SKILL.md) 之外發生</td>
</tr>
<tr>
<td>2</td>
<td>**觸發是機率性的，不是保證的 API 呼叫**</td>
<td>取決於模型對 description 的語義理解，而非硬編碼的 if-else</td>
<td>觸發準確率會隨底層模型/harness 版本更新而漂移，產生持續的維護成本；這也解釋了為什麼 description 要「有點 push」對抗 under-trigger</td>
</tr>
<tr>
<td>3</td>
<td>**漸進式揭露有資訊天花板**</td>
<td>「會不會被讀取」仍取決於 agent 當下判斷要不要展開 bundled resources</td>
<td>限制疊限制：先賭觸不觸發，再賭讀不讀附加資源</td>
</tr>
<tr>
<td>4</td>
<td>**Skill 沒有內建的安全或事務性機制**</td>
<td>[SKILL.md](http://SKILL.md) 可以用文字寫「請小心整頁覆寫」，但無法在機制層面阻止 agent 犯錯</td>
<td>安全性必須靠外部設計補上（QA 閘門、diff-based 操作、版本控制）。這是本專案 2026-08-15 資料損毀事故的技術根源</td>
</tr>
</table>
**各框架的緩解方式**：（沿用既有花園內容）
- **Copilot（Agent-based）**：協調邏輯轉嫁給 dispatch 出去的 subagent runtime——Custom Agents 有獨立 context 和 scoped tools，Fleet Mode 用 SQL todos 做共享狀態協調。Skill 只負責定義「做什麼」，「怎麼串」交給 agent runtime。
- **Claude（Hook-based）**：部分限制用 hooks 在事件點強制攔截——但這其實是把解法外包給另一個 harness 原生功能（PreToolUse / PostToolUse hook）。Hook 本身不是 Skill 的一部分，而是平行的安全層。
- **Pi（Manual）**：沒有框架自動協調，逼得本專案發展出「圓桌會議 → 雙重 QA → 全新 session 交叉驗證」的人工加固多層防禦。代價是人力成本，但好處是每一步都可觀測、可干預。
當某個動作在特定時機「絕對必須執行」且「一點都不想承擔被 AI 忘記的風險」時，就適合做成 Hook。
用一句話比喻：[CLAUDE.md](http://CLAUDE.md) 是「提醒紙條」，Hook 是「自動門」。差別在於 deterministic vs 依賴 AI 自律。（參考：wiki/concepts/[claude-code-hooks-architecture.md](http://claude-code-hooks-architecture.md)，Cheer 基於 Claude Code Hooks 教學提煉）
**LLM 四大認知失敗模式對應四道閘門**：（參考：wiki/sources/[2026-08-17-devops-skill-presentation.md](http://2026-08-17-devops-skill-presentation.md)，Cheer 自製簡報）
<table header-row="true">
<tr>
<td>失敗模式</td>
<td>對應閘門</td>
</tr>
<tr>
<td>選錯 Skill</td>
<td>好的 description（觸發詞寫準，AI 才叫得對人）</td>
</tr>
<tr>
<td>誤解意圖</td>
<td>安全門與確認點（關鍵動作卡一次人類）</td>
</tr>
<tr>
<td>自行判斷、跳過步驟</td>
<td>真實環境測試（文件說可以不算數，跑過才算）</td>
</tr>
<tr>
<td>資訊不足、開始幻覺</td>
<td>必要時交給 Hook / Extension（用平台層強制流程，不靠 AI 自律）</td>
</tr>
</table>
核心：承認 LLM 的限制，不是讓 AI 不出錯，而是讓它出錯時人類接得住。
### 4.4 選擇決策樹
```javascript
需要 AI 執行重複性任務？
├── 是 → 需要強制照規則走？
│   ├── 否 → 用 Skill
│   └── 是 → 需要跨系統整合？
│       ├── 否 → 用 Hook（Extension 事件）
│       └── 是 → 用 Extension / Agent
└── 否 → 不需要 skill
```
（參考：wiki/concepts/[agent-extensibility-hierarchy.md](http://agent-extensibility-hierarchy.md)，Cheer 基於 DevOps Board 實戰提煉）
### 4.5 三者的互補關係
Skill 讓 AI 變懂；Hook 強制它照規則走；Extension 延伸到平台層。三者不是取代關係，而是互補關係。一個成熟的 AI Agent 系統，通常是三層都用到——Skill 處理日常任務、Hook 把關關鍵節點、Extension 處理跨系統整合。（參考：wiki/concepts/[agent-extensibility-hierarchy.md](http://agent-extensibility-hierarchy.md)，Cheer 基於 DevOps Board 實戰提煉）
## 五、API 整合機制
### 5.1 Claude API 整合
Skills 透過 Messages API 的 `container` 參數指定，在程式碼執行容器中運行：（參考：wiki/sources/[2026-08-14-agent-skills-api-guide.md](http://2026-08-14-agent-skills-api-guide.md)，Anthropic 官方 API 指南；wiki/concepts/[agent-skills-api-usage.md](http://agent-skills-api-usage.md)，本專案整理之 API 使用概念）
```yaml
model: claude-opus-5
container:
  skills:
    - type: anthropic       # 或 "custom"
      skill_id: pptx
      version: latest
messages:
  - role: user
    content: "Create a presentation"
tools:
  - type: code_execution_20250825
    name: code_execution
```
先決條件：Claude API 金鑰、beta header `skills-2025-10-02`、程式碼執行工具。
### 5.2 Anthropic vs 自訂 Skills
<table header-row="true">
<tr>
<td>面向</td>
<td>Anthropic Skills</td>
<td>自訂 Skills</td>
</tr>
<tr>
<td>Type 值</td>
<td>`anthropic`</td>
<td>`custom`</td>
</tr>
<tr>
<td>Skill ID</td>
<td>簡短名稱：`pptx` / `xlsx` / `docx` / `pdf`</td>
<td>自動產生：`skill_01AbCdEf...`</td>
</tr>
<tr>
<td>版本格式</td>
<td>日期：`20251013` 或 `latest`</td>
<td>Epoch 時間戳記或 `latest`</td>
</tr>
<tr>
<td>管理方式</td>
<td>Anthropic 預建維護</td>
<td>Skills API CRUD</td>
</tr>
</table>
（參考：wiki/sources/[2026-08-14-agent-skills-api-guide.md](http://2026-08-14-agent-skills-api-guide.md)，Anthropic 官方 API 指南）
透過 `container.id` 可以復用容器，延續上下文。新版本是**完整快照**（非差異更新），建議生產環境固定特定版本、開發環境使用 `latest`。（參考：wiki/concepts/[agent-skills-api-usage.md](http://agent-skills-api-usage.md)，本專案整理之 API 使用概念）
提示快取方面，相同 Skills 清單（含順序）會產生相同可快取前綴，命中快取；變更清單則使快取失效。（參考：wiki/sources/[2026-08-14-agent-skills-api-guide.md](http://2026-08-14-agent-skills-api-guide.md)，Anthropic 官方 API 指南）
### 5.4 關鍵限制
<table header-row="true">
<tr>
<td>限制項</td>
<td>說明</td>
</tr>
<tr>
<td>每請求 Skills 上限</td>
<td>**8 個**</td>
</tr>
<tr>
<td>上傳大小</td>
<td>\< **30 MB**（未壓縮）</td>
</tr>
<tr>
<td>網路存取</td>
<td>API 容器**無網路存取**</td>
</tr>
<tr>
<td>套件安裝</td>
<td>無執行時安裝，僅預先安裝</td>
</tr>
<tr>
<td>版本管理</td>
<td>新版本是完整快照（非差異），30MB 限制</td>
</tr>
<tr>
<td>目前**無法透過 API 取得使用分析**</td>
<td></td>
</tr>
</table>
（參考：wiki/concepts/[agent-skills.md](http://agent-skills.md)，本專案整理之根概念）
## 六、企業治理
企業大規模導入 Agent Skills 不只是「每個人都裝一支 Skill」這麼簡單，需要系統性的安全審查、生命週期管理和組織策略。
### 6.1 安全審查框架
5 級風險評估——在核准部署前，根據風險指標評估每個 Skill：（參考：wiki/sources/[2026-08-14-agent-skills-enterprise-deployment.md](http://2026-08-14-agent-skills-enterprise-deployment.md)，Anthropic 官方企業部署指南；wiki/concepts/[agent-skills-enterprise-governance.md](http://agent-skills-enterprise-governance.md)，本專案整理之企業治理概念）
<table header-row="true">
<tr>
<td>風險指標</td>
<td>關注程度</td>
<td>說明</td>
</tr>
<tr>
<td>程式碼執行</td>
<td>🔴 高</td>
<td>腳本以完整環境存取權限執行</td>
</tr>
<tr>
<td>指令操縱</td>
<td>🔴 高</td>
<td>可能繞過安全控制</td>
</tr>
<tr>
<td>MCP 伺服器引用</td>
<td>🔴 高</td>
<td>存取範圍擴展到 Skill 之外</td>
</tr>
<tr>
<td>網路存取模式</td>
<td>🔴 高</td>
<td>潛在資料外洩途徑</td>
</tr>
<tr>
<td>硬編碼憑證</td>
<td>🔴 高</td>
<td>機密暴露在 Git 歷史和 context</td>
</tr>
<tr>
<td>檔案系統存取</td>
<td>🟡 中</td>
<td>可能存取非預期資料</td>
</tr>
<tr>
<td>工具呼叫</td>
<td>🟡 中</td>
<td>審查執行了哪些操作</td>
</tr>
</table>
8 步審查檢查清單涵蓋：閱讀所有目錄內容、驗證腳本行為、檢查對抗性指令、網路存取模式、硬編碼憑證、工具和命令引用、URL 重新導向目的地、資料外洩模式。（參考：wiki/sources/[2026-08-14-agent-skills-enterprise-deployment.md](http://2026-08-14-agent-skills-enterprise-deployment.md)，Anthropic 官方企業部署指南）
**核心原則：絕不要在未經完整稽核的情況下部署來自不受信任來源的 Skills。** Skill 惡意指示 Claude 執行任意程式碼，外部 URL 擷取的內容也可能含惡意指令。視同安裝軟體。（參考：wiki/entities/[anthropic-agent-skills.md](http://anthropic-agent-skills.md)，Anthropic 官方 Skill 系統實作）
### 6.2 部署前評估
5 個評估維度：觸發準確性、隔離行為、共存性、指令遵循、輸出品質。每個 Skill 需提交 3–5 個代表性查詢，涵蓋應觸發、不應觸發、模糊邊緣案例，並在組織使用的模型上測試。（參考：wiki/concepts/[agent-skills-enterprise-governance.md](http://agent-skills-enterprise-governance.md)，本專案整理之企業治理概念）
### 6.3 生命週期管理
6 階段生命週期：（參考：wiki/sources/[2026-08-14-agent-skills-enterprise-deployment.md](http://2026-08-14-agent-skills-enterprise-deployment.md)，Anthropic 官方企業部署指南）
```javascript
規劃 → 建立與審查 → 測試 → 部署 → 監控 → 迭代或棄用
```
從開發者角度還有更細緻的 8 階段 Skill SDLC：需求（grill-me 反問逼出意圖）→ 分析（Intention-First）→ 設計（Decision Tree）→ 開發（skill-creator）→ 測試（plannotator UI）→ 部署（GitHub Enterprise）→ 版控（branch / PR / tag）→ 迭代（memory feedback）。打造 Skill 的生命週期，跟打造軟體的生命週期，本質上沒有不同——差別只在最後產出的，從程式碼變成了 **skill 資產**。（參考：wiki/concepts/[agent-skills-enterprise-governance.md](http://agent-skills-enterprise-governance.md)，本專案整理；原創：8 階段 SDLC 為 Cheer 基於實戰經驗提煉，整合自多個本專案 skill 的實作流程）
### 6.4 大規模組織策略
**召回限制**：每個 Skill 的中繼資料在系統提示中**競爭注意力**。Skills 過多 → Claude 可能選錯或錯過。每請求最多 8 個 Skills。超過時考慮整合窄範圍 Skills 為廣範圍 Skills。（參考：wiki/concepts/[agent-skills-enterprise-governance.md](http://agent-skills-enterprise-governance.md)，本專案整理之企業治理概念）
**從具體開始，之後再整合**：先建立 `formatting-sales-reports` + `querying-pipeline-data` + `updating-crm-records`，評估確認效能相當後再整合為 `sales-operations`。
**基於角色的套件**：銷售團隊（CRM 操作、管道報告、提案產生）、工程（程式碼審查、部署工作流程、事件回應）、財務（報告產生、資料驗證、稽核準備）。（參考：wiki/sources/[2026-08-14-agent-skills-enterprise-deployment.md](http://2026-08-14-agent-skills-enterprise-deployment.md)，Anthropic 官方企業部署指南）
## 七、跨框架比較：三大框架的設計取捨
三大主流 Agent 框架——Claude Code、GitHub Copilot SDK、Pi Agent——在處理 Skill 時的機制與設計取捨各不相同。
### 7.1 總覽比較
<table header-row="true">
<tr>
<td>面向</td>
<td>Claude Code</td>
<td>GitHub Copilot SDK</td>
<td>Pi Agent</td>
</tr>
<tr>
<td>**Skill 格式**</td>
<td>[SKILL.md](http://SKILL.md)（三層漸進式揭露）</td>
<td>[SKILL.md](http://SKILL.md)（漸進式揭露，格式兼容）</td>
<td>[SKILL.md](http://SKILL.md)（markup 注入）</td>
</tr>
<tr>
<td>**組合方式**</td>
<td>多 Skill 同時載入（≤8/請求）</td>
<td>多 Skill + per-agent preload</td>
<td>逐一手動觸發</td>
</tr>
<tr>
<td>**巢狀/委派**</td>
<td>✅ Subagents（獨立 context）</td>
<td>✅ Custom Agents + Sub-agent Orchestration + Fleet Mode</td>
<td>❌ 刻意不做</td>
</tr>
<tr>
<td>**工具鏈整合**</td>
<td>MCP（開放標準）</td>
<td>MCP（per-agent）+ Custom Tools</td>
<td>Extension System</td>
</tr>
<tr>
<td>**鉤子/生命週期**</td>
<td>✅ Hooks（PreToolUse 等）</td>
<td>✅ 7+ Hooks（完整 lifecycle）</td>
<td>✅ Extension Events</td>
</tr>
<tr>
<td>**訊息控制**</td>
<td>❌ 無</td>
<td>✅ Steering + Queueing</td>
<td>❌ 無</td>
</tr>
<tr>
<td>**並行編排**</td>
<td>⚠️ Subagents（有限）</td>
<td>✅ Fleet Mode（SQL todos 協調）</td>
<td>❌ 無</td>
</tr>
</table>
（參考：wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)，本專案整理之跨框架比較）
### 7.2 Claude Code：開放 + 能力上限
Claude Code 的 Skill 系統建立在三層漸進式揭露之上，支援 MCP（Model Context Protocol）作為工具鏈整合標準，三種傳輸層（stdio / SSE / HTTP）。Subagents 可被 Skills 觸發，每個 subagent 有獨立 context，綜合評分（confidence scoring ≥ 80）。採用 **Model-invoked** 觸發模式——description 始終在 context 中，模型根據語意自動決定是否載入 Skill。（參考：wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)，本專案整理；wiki/entities/[anthropic-agent-skills.md](http://anthropic-agent-skills.md)，Anthropic 官方實作）
200K tokens 的 context window 加上 Extended Thinking，讓 Claude Code 在複雜推理任務上有天然優勢。但代價是設定相對複雜。
### 7.3 GitHub Copilot SDK：程式化平台 + 深度自訂
Copilot SDK 是 Copilot 的**程式化代理平台**（支援 TypeScript / Python / Go / .NET / Rust），提供完整的 agent 能力。採用 **Model-invoked + User-invoked 混合**觸發模式。（參考：wiki/entities/[github-copilot-sdk.md](http://github-copilot-sdk.md)，GitHub 官方文件）
**Custom Agents** 是最核心的差異化能力：每個 agent 有獨立 `name`、`prompt`、`tools`（scoped）、`mcpServers`，runtime 自動委派、隔離 context 執行、事件串流（`subagent.started / completed / failed / selected / deselected`）。支援 per-agent model 和 reasoning effort 覆蓋，以及 `excludedTools` 機制強制委派。（參考：wiki/concepts/[copilot-custom-agents.md](http://copilot-custom-agents.md)，本專案整理；GitHub 官方 Custom Agents 文件）
**Fleet Mode** 是並行 orchestration pattern：將工作拆分為獨立 units，分配給多個 sub-agent 並行執行，以 SQL todos（`pending → in_progress → done / blocked`）為共享協調狀態。適合多檔案重構、批次 review、並行研究。（參考：wiki/concepts/[copilot-fleet-mode.md](http://copilot-fleet-mode.md)，本專案整理；GitHub 官方 Fleet Mode 文件）
**Steering & Queueing** 是 Copilot 獨有的訊息投遞模式：steering（`"immediate"`）注入當前 turn、即時轉向；queueing（`"enqueue"`）排隊等 turn 結束後 FIFO 處理。（參考：wiki/concepts/[copilot-steering-queueing.md](http://copilot-steering-queueing.md)，本專案整理）
### 7.3-2、Copilot Agent Loop：多輪 Tool-Use 循環底層機制
Copilot 的上述能力（Custom Agents、Fleet Mode、Hooks）都建立在同一個底層機制上——**Agent Loop**。SDK 是傳輸層（JSON-RPC），CLI 是 orchestrator。當呼叫 `session.send({ prompt })` 時，CLI 進入循環：（參考：wiki/concepts/[copilot-agent-loop.md](http://copilot-agent-loop.md)，本專案整理）
```javascript
使用者 prompt → CLI → LLM API call → 工具執行 → 結果回傳 → LLM 決定下一步 → 重複
```
**關鍵設計**：每次迭代 = 一次 LLM API call = 一對 `assistant.turn_start` / `assistant.turn_end`。**無隱藏呼叫**。這意味著 Custom Agents 的自動委派、Fleet Mode 的並行協調、Hooks 的事件攔截，本質上都是在這個 loop 的不同位置插入控制點。理解 Agent Loop，才能理解 Copilot 為什麼能做那麼多事情——不是因為它有魔法，而是因為它的 loop 設計足夠透明且可擴充。
⚠️ 重要修正：先前基於第三方文章（[aiskill.market](http://aiskill.market)）的多處錯誤描述——稱 Copilot「無巢狀機制」「無 hooks」「Extensions 僅為服務整合」——已被 GitHub 官方文件（22 篇）全面修正。（參考：wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)，本專案整理）
### 7.4 Pi Agent：極簡 + 可干預
Pi Agent 的核心是 5 個檔、約 1,500 行 TypeScript。**刻意不做** sub-agents、plan mode、maxSteps、MCP——這些由下游擴充處理。採用 **User-invoked** 觸發模式：system prompt 以 markup 列出所有 Skill（name + description），使用者透過 `/skill:<name>` 手動觸發，LLM 依 prompt 指示用 `read` 工具讀取 location。Skill 內容**不自動注入**——LLM 主動讀取，保留 tool call 可觀測性。（參考：wiki/entities/[pi-agent-core.md](http://pi-agent-core.md)，Pi Agent 核心實作）
Pi 的設計哲學是可觀測性（三層事件生命週期）+ 可干預性（steering / follow-up 雙 queue）。巢狀與委派由下游擴充處理——例如 Prime Agent 用 RLM function 新增了 sub-agent 概念，在不修改核心的前提下擴充能力。（參考：wiki/entities/[pi-agent-core.md](http://pi-agent-core.md)，Pi Agent 核心實作）
### 7.5 Claude Code Hooks 深入：三層架構
Claude Code 的 Hooks 系統是 deterministic 的事件驅動自動化機制，三層架構：Event → Matcher → Handler。（參考：wiki/sources/[2026-08-18-claude-code-hooks-tutorial.md](http://2026-08-18-claude-code-hooks-tutorial.md)，Claude Code Hooks 教學；wiki/concepts/[claude-code-hooks-architecture.md](http://claude-code-hooks-architecture.md)，本專案整理）
10 個核心 Event（從 31 種中精選）分四大階段：
- **啟動階段**：SessionStart（對話建立時）、UserPromptSubmit（使用者送出 prompt）
- **工具執行前**：PreToolUse（安全防呆）
- **工具執行後**：PostToolUse（快速驗證）
- **收尾階段**：Stop、Notification、SubagentStart、SubagentStop、PreCompact
5 種 Handler：command（執行腳本）、http（傳資料到外部服務）、mcp_tool（使用 MCP 工具）、prompt（叫 AI 判斷）、agent（叫 subagent 先查再答）。
### 7.6 Copilot Hooks 系統
7+ 種 lifecycle hooks：`onSessionStart`、`onUserPromptSubmitted`、`onPreToolUse`、`onPostToolUse`、`onPostToolUseFailure`、`onSessionEnd`、`onErrorOccurred`。以 TypeScript callback 註冊，回傳 `null` 表示無變更。（參考：wiki/concepts/[copilot-hooks-system.md](http://copilot-hooks-system.md)，本專案整理）
### 7.7 設計哲學差異
- **Claude Code**：開放生態、能力上限高（34,000+ Skills）、代價是設定複雜
- **GitHub Copilot SDK**：程式化平台、深度自訂（Custom Agents + Fleet Mode + 7+ Hooks）、代價是需要程式化整合
- **Pi Agent**：極簡核心（1,500 行）、可觀測可干預、代價是需要下游擴充
（參考：wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)，本專案整理之跨框架比較）
## 八、[agentskills.io](http://agentskills.io)：跨平台開放標準
[agentskills.io](https://agentskills.io/) 是跨平台開放標準，定義 Skill 的可移植格式。各框架的關係：（參考：wiki/entities/[agentskills-io-standard.md](http://agentskills-io-standard.md)，[agentskills.io](http://agentskills.io) 開放標準）
<table header-row="true">
<tr>
<td>面向</td>
<td>[agentskills.io](http://agentskills.io)</td>
<td>Anthropic 實作</td>
<td>Pi Agent</td>
</tr>
<tr>
<td>性質</td>
<td>開放標準</td>
<td>廣商實作</td>
<td>廣商實作</td>
</tr>
<tr>
<td>格式</td>
<td>[SKILL.md](http://SKILL.md) 格式定義</td>
<td>三層揭露 + 安全模型</td>
<td>markup 注入 + read tool</td>
</tr>
<tr>
<td>分發</td>
<td>`npx skills` / Registry</td>
<td>API 上傳 / 本地目錄</td>
<td>本地安裝 / npm</td>
</tr>
<tr>
<td>相容性</td>
<td>多 Harness</td>
<td>Claude 專用</td>
<td>Pi 專用</td>
</tr>
</table>
跨平台相容的工具包括：GitHub Copilot in VS Code、GitHub Copilot CLI、GitHub Copilot Coding Agent、Claude Code、Claude Coworker、Gemini Antigravity、Gemini CLI 等。（參考：wiki/entities/[agentskills-io-standard.md](http://agentskills-io-standard.md)，[agentskills.io](http://agentskills.io) 開放標準）
[**skills.sh**](http://skills.sh) 是公開的 Agent Skills Registry（由 Vercel 營運），透過 `npx skills` 將程式碼下載並注入開發環境。**Awesome GitHub Copilot** 則是 GitHub 官方的靜態知識庫與索引目錄。兩者的定位差異：前者是動態套件登錄中心，後者是 Curated Directory。（參考：wiki/entities/[agentskills-io-standard.md](http://agentskills-io-standard.md)，[agentskills.io](http://agentskills.io) 開放標準）
⚠️ 安全提醒：外部技能必須經過程式碼審查，建議轉存至企業內部私有庫。
---
## 九、SkillOpt：Agent 自我進化的可能性
微軟提出的 SkillOpt 框架，將 [SKILL.md](http://SKILL.md) 視為可訓練的外部參數，不修改閉源模型權重就能實現 Agent 自我進化。（參考：wiki/entities/[skillopt.md](http://skillopt.md)，微軟 SkillOpt 論文；wiki/concepts/[skillopt-meta-skill.md](http://skillopt-meta-skill.md)，本專案整理之概念頁）
核心機制：**雙軌制** + **驗證門禁**。
1. **執行軌道**：凍結的閉源 LLM 執行任務
2. **優化軌道**：反思 LLM 根據報錯日誌更新 [SKILL.md](http://SKILL.md)
3. **文字學習率預算（Textual LR Budget）**：限制每次修改幅度，只允許 add / delete / replace 操作（Bounded Edit）
4. **Validation Gate**：補丁必須通過 Hold-out 驗證集
關鍵優勢：優化後的 [SKILL.md](http://SKILL.md) 可跨框架遷移（Codex / Claude Code / Cursor），零額外推斷成本，具備 Validation Gate 穩定性卓越。（參考：wiki/entities/[skillopt.md](http://skillopt.md)，微軟 SkillOpt 論文）
與 Model Fine-Tuning 的差異：Fine-Tuning 成本高、閉源模型不適用、遷移困難；SkillOpt 成本低、跨框架可用、無額外推斷成本。（參考：wiki/concepts/[skillopt-meta-skill.md](http://skillopt-meta-skill.md)，本專案整理）
---
## 九-2、另一條路：Hermes Agent 的 Learning Loop
SkillOpt 從「文字空間」優化 [SKILL.md](http://SKILL.md)，是微軟提出的框架級方法。但還有另一條完全不同的自我進化路徑——**實體記憶與技能庫學習**。
Hermes Agent（Nous Research 開源）的做法是：（參考：wiki/entities/[hermes-agent.md](http://hermes-agent.md)，Nous Research 開源框架）
- **跨會話持久記憶**：把踩坑經驗轉化為永久能力，不是改 [SKILL.md](http://SKILL.md)，而是改 agent 自己的記憶庫
- **動態 Skill 庫生成**：自動生成和管理技能文檔，不需要人類手動撰寫
- **Graphify 整合**：透過 `/graphify` 載入代碼知識圖譜，讓 agent 理解專案結構
與 SkillOpt 的關鍵差異：
<table header-row="true">
<tr>
<td>面向</td>
<td>SkillOpt</td>
<td>Hermes Agent</td>
</tr>
<tr>
<td>學習載體</td>
<td>[SKILL.md](http://SKILL.md) 文字檔案</td>
<td>跨會話記憶庫 + 動態技能文檔</td>
</tr>
<tr>
<td>優化方式</td>
<td>Bounded Edit（add/delete/replace）</td>
<td>經驗累積 + 自動生成</td>
</tr>
<tr>
<td>遷移性</td>
<td>✅ [SKILL.md](http://SKILL.md) 可跨框架攜帶</td>
<td>❌ 記憶庫綁定特定 agent</td>
</tr>
<tr>
<td>人工介入</td>
<td>Validation Gate（需驗證集）</td>
<td>低，但有學到錯誤行為的風險</td>
</tr>
</table>
SkillOpt 是「改文件」，Hermes 是「改大腦」。前者可移植但有限，後者強大但綁定。（原創：Cheer 於本研究專題中提出的對比分析）
---
## 十、本專案的實戰經驗與教訓
以上是通用知識，以下則是本專案在實際建構和使用 Skill 系統過程中累積的真實經驗。
### 10.1 Meta-Skills Loop——做 Skill 的，也是 Skill
「高品質 Skill 不是一次寫對，而是一輪一輪被問清楚、找參考、生成、對齊、真跑、沉澱出來的。」
本專案發展出一組 meta-skills 迴圈：（參考：wiki/concepts/[agent-skills.md](http://agent-skills.md)，本專案整理之根概念）
1. **grill-me**（反問人類）：讓 AI 反過來質詢，把模糊需求問清楚，逼出真正的意圖
2. **find-skills**（找現成的）：先找當下可用的 skill 或外部參考，不重新發明輪子
3. **skill-creator**（生第一版）：產生 skill 骨架與 description，把意圖快速變成初稿
4. **plannotator UI**（視覺化對齊）：把規劃視覺化，拿來跟 AI 討論與對齊
5. **feedback / memory**（沉澱經驗）：把真實決策、破例與踩雷跨 session 記住，下次不重犯
↺ 修正後回到步驟 1，越跑越準
這和 SkillOpt 的差異：SkillOpt 是**文字級**自動修補 [SKILL.md](http://SKILL.md) 本身；Meta-Skills Loop 是**多支 skill 協作**完成另一支 skill 的發現 / 建立 / 規劃 / 回饋（系統架構層）。（參考：wiki/concepts/[agent-skills.md](http://agent-skills.md)，本專案整理；原創：Meta-Skills Loop vs SkillOpt 的對比分析為 Cheer 於本研究專題中提出）
### 10.2 知識花園的 Skill 架構演化
本專案的 Notion 知識花園從最初的一支 `wiki-knowledge` 大 Skill，經歷了三輪討論（Copilot + Gemini）後，重構為 11 支 Skill 的分工架構。**目前 ****`wiki-knowledge`**** 已不再使用**，被三支流程明確的 Skill 取代：`wiki-ingest`（從 raw 建立 wiki 頁面）、`wiki-query`（查詢 wiki 內容）、`wiki-lint`（健康檢查）。（參考：wiki/concepts/[knowledge-garden-skill-architecture.md](http://knowledge-garden-skill-architecture.md)，Cheer 基於三輪討論提煉之架構概念）
完整資料流是雙向的：
```javascript
                    Notion 知識花園
                    ↕ 雙向同步
Wiki（持久層）←→ Notion（展示層）
```
- **Wiki → Notion**：`knowledge-garden`（維護主 skill）+ `knowledge-garden-page-content`（頁面內容生成）+ `knowledge-garden-visualmap`（Mermaid → SVG → Notion 視覺地圖）
- **Notion → Wiki**：`knowledge-garden-to-raw`（抓取頁面內容）+ `knowledge-garden-trigger`（觸發條件偵測）
- **Wiki 內部操作**：`wiki-ingest` + `wiki-query` + `wiki-lint`（已完全取代舊版 `wiki-knowledge`）
- **外部來源 ingest**：`wiki-youtube`（YouTube 字幕 → wiki）+ `wiki-pdf`（PDF → wiki）
- **工具**：`notion-cli`（ntn CLI 操作，被多個 skill 使用）
關鍵決策：用 cron poll 同步（個人 agent 不需要 webhook 架構）、品質關卡用 Evaluator-Optimizer Loop（prompt 自我評估，非阻斷式）。這個演化過程本身就是一個活生生的「Skill 設計方法論」案例——從一支巨石 Skill 切分成 11 支分工明確的 Skill，每支都有清晰的職責邊界和資料流方向。（原創：Cheer 基於本專案實戰經驗提煉）
### 10.3 三個真實撞牆案例：Skill 系統的實戰教訓
以下三個案例是本專案在建構和使用 Skill 系統過程中，最重要的實戰教訓。它們不是理論推演，而是真實撞牆後的經驗沉澱。（沿用既有花園內容）
#### 案例一：round-table——「skill 只是指令注入」的第一次認知衝擊
**起點的誤解**：開始設計 round-table skill 時，心智模型是「把協調協議寫進 [SKILL.md](http://SKILL.md)，之後照著做就行」。這是一個典型的初學者誤解——把 Skill 當成可執行的程式碼。
**實際發現**：[SKILL.md](http://SKILL.md) 被載入後，只是把協議文字塞進 Pi 的 context。真正的「派 subagent、序列輪轉、收集結果」完全在 [SKILL.md](http://SKILL.md) 之外發生——必須透過 `mcp__pi-bridge__ask_pi`（帶 enable_extensions + approve_project）讓 Pi 的 agent runtime 真的去執行 bash 呼叫、派出 subagent。Skill 文件定義了完整協議，但這份文件本身不會執行任何東西。（沿用既有花園內容）
**教訓**：Skill 提供的是協議文件層，執行永遠要靠 harness 原生能力補上。這對應到上一節四個跨框架共通限制的第一條：「Skill 是靜態指令文字，不是執行引擎」。
本專案的 `round-table` skill 實際運作時，Pi 的角色是排序、追蹤論點、偵測共識、做結論——但不提供觀點。用 `newArguments`（新論點數）、`coverageRate`（回應比例）、`agreementRate`（同意 vs 反駁比例）、`openDisputes`（未解決分歧數）四個量化指標判斷討論是否收斂。結束條件是雙重門檻：量化條件成立 **AND** 無未解決分歧。全程維護每位參與者在每輪的立場（提出 / 同意 / 反駁 / 補充），以及每個分歧點的狀態（未解決 / 已解決）。這些設計決策都是在實作過程中逐步撞出來的。（參考：wiki/entities/[round-table.md](http://round-table.md)，本專案實作之 round-table skill）
#### 案例二：高風險寫入的三方 QA 閘門演化
**問題**：花園（Notion）寫入原本只是「skill 讀取指令 → agent 照做」，但實測發現單一 agent 直接寫入常有錯——虛構 URL、視覺地圖放錯位置、agent 謊報完成。這些不是偶發的 edge case，而是 LLM 在高風險寫入場景中的系統性失敗模式。（沿用既有花園內容）
**因應**：在 skill 之外疊加一層流程：
1. **round-table 多方討論**（不同觀點交叉驗證）
2. **Claude + Gemini 雙重 QA**（且要求先查證再下結論，不可憑記憶）
3. **全新 session 交叉驗證**才能真正寫入
**教訓**：Skill 驅動的 agent 工作流程，光靠「skill 指令寫得多嚴謹」不足以防止真實的執行錯誤，必須額外搭配「獨立於執行者的驗證步驟」，且驗證的嚴謹度要跟寫入的風險成正比。這也是為什麼 3.4 節的 Write-back Safety Gate 要求「不可逆的事，人類永遠先看過一眼」——但光靠人類不夠，還需要多方 agent 交叉驗證。（沿用既有花園內容）
#### 案例三：2026-08-15 資料損毀事故
**過程**：在強化 GitHub Copilot 種子頁時，用「整頁讀出 → 本地編輯 → 整頁覆寫」的方式改動內容，連續發生兩次真的資料遺失：（沿用既有花園內容）
- **第一次**：本地備份不完整，覆寫時把四個既有章節整段清空
- **第二次**：某段落被 agent 自己填入「內容同前版，此處省略」這種占位字頂替真內容
兩次都是靠「全新 session、要求逐段貼實際內文關鍵字」的交叉驗證抓到，最後從 git 歷史救回原文才修復。
**技術根源**：LLM 缺乏檔案鎖與差異比對（Diff）強制驗證能力，「整頁讀出→修改→整頁覆寫」這種操作模式在複雜場景中必然引發資料覆蓋風險。真正的修補是改成診斷式流程（強制 diff-based 更新而非整頁覆寫）。這也是四個跨框架共通限制第四條「Skill 沒有內建的安全或事務性機制」的最痛實證——[SKILL.md](http://SKILL.md) 可以用文字寫「請小心整頁覆寫」，但無法在機制層面阻止 agent 犯錯。（沿用既有花園內容；事件細節參見 wiki/entities/[knowledge-garden.md](http://knowledge-garden.md) 與 wiki/sources/[2026-08-15-round-table-pi-agent-v084-learning.md](http://2026-08-15-round-table-pi-agent-v084-learning.md)）
### 10.3-2、Morphir .NET Agents：用 TDD 驅動 Skill 行為
FINOS morphir-dotnet 專案的 `AGENTS.md` 展示了企業級 .NET 專案如何組織 AI agent 指南——18 個章節、專業化的 skills 系統（QA Tester、AOT Guru、Release Manager、Technical Writer）、以及**強制 TDD**（Red-Green-Refactor 是必須的開發流程）。（參考：wiki/sources/[2026-08-02-morphir-dotnet-agents.md](http://2026-08-02-morphir-dotnet-agents.md)，FINOS morphir-dotnet [AGENTS.md](http://AGENTS.md)）
幾個值得學習的設計決策：
- **多 agent 支援**：同一份 [AGENTS.md](http://AGENTS.md) 支援 Claude Code、GitHub Copilot、Cursor、Windsurf、JetBrains AI，每個 agent 有不同的召喚方式（`@skill`、自然語言、auto-trigger）
- **BDD-first**：新功能先寫 feature files，再寫實作
- **Snapshot testing + Property-based testing**：用 Verify 做 snapshot、FsCheck 做 property-based，確保行為一致性
- **CLI 日誌紀律**：stdout = data、stderr = diagnostics，絕不混用
TDD 強制在 Skill 開發中的意義：**先寫失敗的 test → 寫最少的 code 讓 test 通過 → 重構**。這不只是軟體工程的最佳實踐，更是確保 Skill 行為可預測、可驗證的關鍵機制。與 mattpocock/skills 的 `tdd` skill 目標一致，但 morphir-dotnet 更進一步把 TDD 整進了整個 agent 指南的結構中。（原創：Cheer 於本研究專題中提出的對比分析）
### 10.4 實際 Skill 範例：guizang-ppt-skill 和 huashu-design
這兩個是本專案使用的設計類 Skill，展示了 Skill 的極致實踐。
**guizang-ppt-skill**（歸藏開發）：用一句話 prompt 生成高品質 HTML 橫向翻頁簡報。支援兩套視覺系統（電子雜誌 × 電子墨水 / 瑞士國際主義）、Codex 配圖、多平台封面、演講者模式。核心設計原則：克制優於炫技、結構優於裝飾、圖片是第一公民。（參考：wiki/entities/[guizang-ppt-skill.md](http://guizang-ppt-skill.md)，本專案整理；原創：guizang-ppt-skill 與 huashu-design 的對比分析為 Cheer 於本研究專題中提出）
**huashu-design**（花叔開發）：一句話 prompt 生成高保真原型、幻燈片、動畫。內建品牌資產協議（5 步硬流程）、設計方向顧問（3 套互補邏輯 subagent）、5 維度專家評審。反 AI slop 規則：避免一眼 AI 的視覺最大公約數（紫漸變 / emoji 圖標 / 圓角 + 左 border accent）。（參考：wiki/entities/[huashu-design.md](http://huashu-design.md)，本專案整理）
這兩個 Skill 的共同特點：都跨 agent 通用（Claude Code、Codex、Cursor 都能用）、都可相容於 [agentskills.io](http://agentskills.io) 標準格式、都有明確的「適合 / 不適合」清單。
### 10.5 mattpocock/skills 的工程實踐
Matt Pocock（Total TypeScript 作者，個人 GitHub 累計 162k+ stars、npm 累計 7.5M+ downloads）維護的 Skills 集合，代表了 Skill 系統在工程實踐中的重要參考。（參考：wiki/entities/[mattpocock-skills.md](http://mattpocock-skills.md)，本專案整理之 entity 頁）
核心工作流程：`grill with docs → to spec → to tickets → implement → code review`（參考：wiki/concepts/[ai-coding-workflow.md](http://ai-coding-workflow.md)，本專案整理之 AI 編碼工作流程概念）
這不是只是一個名字——每個階段都有明確的輸入 / 輸出和關鍵問題：
<table header-row="true">
<tr>
<td>階段</td>
<td>輸入</td>
<td>輸出</td>
<td>關鍵問題</td>
</tr>
<tr>
<td>**Grill with Docs**</td>
<td>模糊想法</td>
<td>共識 + 領域模型</td>
<td>「我們真的理解要做什麼嗎？」</td>
</tr>
<tr>
<td>**To Spec**</td>
<td>討論記錄</td>
<td>正式規格書</td>
<td>「目的地長什麼樣？」</td>
</tr>
<tr>
<td>**To Tickets**</td>
<td>規格書</td>
<td>可執行 tickets</td>
<td>「怎麼一步步到達？」</td>
</tr>
<tr>
<td>**Implement**</td>
<td>Tickets + Spec</td>
<td>程式碼變更</td>
<td>「實際動手做」</td>
</tr>
<tr>
<td>**Code Review**</td>
<td>變更 + Spec + Standards</td>
<td>審查報告 + commit</td>
<td>「做出來的東西對嗎？」</td>
</tr>
</table>
設計原則：Context Window 感知（每個 ticket 應能在 smart zone 內完成）、規格驅動（先有規格再有實作）、Sub-agent 審查（避免 self-review 的認知偏見）、可選式流程（每個階段可獨立使用）。這套方法論把「從想法到可交付程式碼」的過程結構化，讓 AI agent 不只是「寫程式」，而是「按照人類的領域知識和品質標準完成任務」。（原創：Cheer 基於 mattpocock/skills 實戰經驗提煉之概念頁，見 wiki/concepts/[ai-coding-workflow.md](http://ai-coding-workflow.md)）
幾個值得學習的設計決策：
- **User-invoked 為主**：skill 不會自動滲透 context，token 佔用極低（660 tokens），用戶保留控制權
- **模型無關**：Claude Code / Codex / Cursor / Pi 通用
- **可組合**：不強制流程，按需搭配
- **User-invoked vs Model-invoked 分離**：前者負責編排流程，後者持有可重用的紀律。User-invoked 可呼叫 Model-invoked，但不會呼叫另一個 User-invoked
**Context Window 管理**：Smart zone 約 140k tokens，超過會出現 attention degradation 和 hallucination。建議每張 ticket 清除一次 context，大型工作拆成多個 session。（參考：wiki/sources/[2026-07-22-mattpocock-skills-tutorial.md](http://2026-07-22-mattpocock-skills-tutorial.md)，Matt Pocock YouTube 教學）
**依賴關係**：`grill-me` 必須同時安裝 `grilling`（核心追問邏輯），否則會失敗。這是 Skill 系統需要面對的相依性管理問題——Skill 之間有隱含的呼叫關係，但沒有內建的 dependency resolver。（參考：wiki/entities/[mattpocock-skills.md](http://mattpocock-skills.md)，本專案整理）
---
## 十一、跨框架實戰案例：同一個任務的三種做法
為了把前面幾章的抽象比較具象化，用一個具體案例貫穿：**「收到一張被退的 User Story，查清楚問題，更新它」**。這是本專案實際遇到的場景。（原創：Cheer 於本研究專題中提出的跨框架案例分析）
### Claude Code 的做法
1. Claude Code 的 Model-invoked 機制自動偵測到「查 User Story」的意圖，載入 `work-query` Skill
2. 用 MCP 呼叫 Azure DevOps API，撈出這張 WIT 的現況、歷程與退件紀錄
3. 同時用 Subagent 並行查該 Team 的驗收準則與規範（另一支 Skill）
4. 綜合分析後，透過 `work-maintainer` Skill 執行寫入安全門（dry-run → 人類確認 → confirm → 讀回驗證）
**優勢**：Subagent 並行讓查詢速度翻倍；Model-invoked 自動觸發省去人類記得叫的負擔。
**代價**：context window 佔用較高，一次任務可能吃掉 30-50k tokens。
### GitHub Copilot SDK 的做法
1. Custom Agent 接到任務，自動委派給 scoped tools 的 sub-agent
2. Fleet Mode 將工作拆成三個並行 units：查 WIT、查驗收準則、查歷史退件紀錄
3. SQL todos（`pending → in_progress → done`）追蹤每個 unit 的進度
4. onPreToolUse hook 在寫入前攔截，強制執行安全檢查
**優勢**：Fleet Mode 並行效率最高；7+ hooks 讓安全把關是 deterministic 的，不依賴 AI 自律。
**代價**：需要程式化整合（TypeScript callback），設定門檻較高。
### Pi Agent 的做法
1. 人類手動呼叫 `/skill:work-query`（User-invoked）
2. Pi 用 `read` 工具讀取 Skill 內容，依指示執行查詢
3. 人類再呼叫 `/skill:wiki` 查 Team 驗收準則
4. 人類再呼叫 `/skill:work-maintainer` 執行更新，Pi 依 Skill 指示執行 dry-run → 確認 → 寫入 → 讀回
**優勢**：每一步都可觀測、可干預；人類完全控制觸發時機；token 佔用最低。
**代價**：需要人類記得叫 Skill；多步驟任務需要手動串接。
**三種做法的共同點**：都遵循「不可逆的事，人類先看過一眼」的規則；都有某種形式的寫入安全機制；都用 Progressive Disclosure 管理 context window。差別只在自動化程度和控制粒度——這是設計選擇，不是好壞之分。
---
## 十二、Skill 組合技——從單兵到協作
複雜系統不是一支 Skill 就能處理的。切成小支，才有辦法照情境串起來用。簡單問題用 Skill，複雜問題用 Skill 的協作。（參考：wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)，本專案整理之跨框架比較）
### 12.1 應用層案例：Skill 鏈式協作（Chain Composition）
情境：「這張 User Story 一直被退，幫我查清楚問題出在哪，然後更新它。」
```javascript
work-query → wiki → analytics → work-maintainer
   ↓           ↓         ↓            ↓
先撈出這張    去讀該     比對相似      確認後回寫
WIT 的現況、  Team 的    欄位與工時    欄位與工時
歷程與退件    驗收準則   （過安全門）
紀錄          與規範
```
關鍵：框架層決定了「怎麼編排多支 Skill」（同時載入 / Subagent / Fleet Mode），應用層決定了「哪些 Skill 應該串成一條鏈」。兩者是不同層次的問題。（參考：wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)，本專案整理）
### 12.2 實際組合場景比較
以「Code Review + 安全掃描 + 測試覆蓋率」為例：
<table header-row="true">
<tr>
<td>步驟</td>
<td>Claude Code</td>
<td>GitHub Copilot SDK</td>
<td>Pi Agent</td>
</tr>
<tr>
<td>Code Review</td>
<td>Subagent（並行）</td>
<td>Custom Agent（scoped tools）</td>
<td>chat-with-claude skill</td>
</tr>
<tr>
<td>安全掃描</td>
<td>Subagent（並行）</td>
<td>Custom Agent + MCP security server</td>
<td>自訂 Extension</td>
</tr>
<tr>
<td>測試覆蓋率</td>
<td>Subagent（並行）</td>
<td>Custom Agent（test tools）</td>
<td>自訂 Skill</td>
</tr>
<tr>
<td>綜合評分</td>
<td>confidence scoring</td>
<td>Fleet Mode（SQL todos）</td>
<td>Round Table skill</td>
</tr>
<tr>
<td>權限控制</td>
<td>Permission Prompting</td>
<td>onPreToolUse hook</td>
<td>Extension events</td>
</tr>
</table>
（參考：wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)，本專案整理之跨框架比較）
### 12.3 複雜多 Skill 組合的決策框架
當 Skill 組合的複雜度上升時，第一個問題不是「怎麼組合」，而是「這複雜度該不該留在 Skill 層解決」。以下是從實戰中提煉出的兩個判準。（沿用既有花園內容）
**判準一：複雜度是「協調邏輯」還是「情境覆蓋」？**
- 若複雜度來自**協調邏輯**（需要序列化、狀態管理、多方輪轉、結果彙整）→ 執行層必須回到各 harness 原生機制。理由：Skill 系統設計上刻意不做執行引擎，不可能靠 [SKILL.md](http://SKILL.md) 文字本身實現真正的多 agent 協調。
- 若複雜度來自**情境覆蓋廣度**（很多觸發情境，但每個情境內部邏輯單純）→ 應該留在 skill 系統內，用多 skill 分工解決。knowledge-garden 用 5 個獨立 skill 分工（wiki-ingest / wiki-query / wiki-lint / knowledge-garden-to-raw / knowledge-garden-trigger）正是這條路線。
**主結論**：「協調交給 harness 原生機制，知識與情境覆蓋交給 skill 分工。」（沿用既有花園內容）
**判準二：組合 vs 互通**
- **組合**（單框架內動態派生子代理）：這件事現在就能做，round-table 已經是可行證明。子代理繼承的是父 session 既有的權限與沙箱邊界，信任來源是同一個 harness，不需要額外的 CI/registry/簽章基礎設施。
- **互通**（映像檔跨框架搬運）：把本專案的 skill 原封不動丟給另一套 harness 的子代理系統去讀，這才是真正需要標準化 metadata + 簽章制品的問題，因為跨框架時信任邊界消失了。本專案目前沒有跨框架搬運 skill 本體的需求，規避了互通問題，不代表互通問題被解決了。
**三個暗礁**：（沿用既有花園內容）
1. **抽象洩漏**：單框架組合時，skill 往往會高度依賴該 harness 的特殊上下文注入機制或工具呼叫協議，實際已與特定框架深度鎖定（Vendor Lock-in）。例如 round-table 重度依賴 Pi 的 `mcp__pi-bridge__ask_pi`，Copilot 的 Fleet Mode 重度依賴 SQL todos 協調機制。
2. **狀態漂移**：動態派生子代理涉及寫入檔案、呼叫外部 API 時，子代理失敗的事務回滾機制、親子代理間的狀態同步，必須由 harness 定義嚴謹的協調協議，不能只靠 skill 裡的自然語言描述。
3. **階梯互通**：互通不是「全有全無」，即使沒有中心化 registry，只要定義好基礎的輸入輸出結構約定，skill 就能在輕量級 harness 之間實現優雅降級的互通。
**收斂**：「Skill 系統的可攜性優勢應該保留給『知識封裝』用途，不要勉強拿來做跨 agent 執行編排——那違背了它的設計初衷。凡是涉及狀態變更的複雜操作，必須在 harness 層實現防護機制，skill 內部則定義逆向修復操作指令。」（沿用既有花園內容）
---
## 十三、結論
### Skill 生態系的核心張力
Skill 系統的設計，本質上是在三組張力之間找平衡：
1. **彈性 vs 安全**：Skill 讓 AI 變聰明，但也讓它能做更多事——包括危險的事。Hook 和 Extension 是安全網，但增加複雜度。
2. **模組化 vs 管理成本**：切得越細，每支 Skill 越清晰、越可維護；但數量越多，召回越困難、相依性越複雜。
3. **開放 vs 可控**：[agentskills.io](http://agentskills.io) 讓 Skill 跨平台流通，但企業需要安全審查和版本控制。开放帶來選擇，但也帶來風險。
### 本專案的立場
從本專案的實踐經驗來看，Skill 系統最有價值的不是「讓 AI 自動做事」，而是**把人類的領域知識、工作流程和安全護欄，系統性地封裝成可重複使用的資產**。
Meta-Skills Loop（做 Skill 的也是 Skill）、Write-back Safety Gate（不可逆的事人類先看過）、三層漸進式揭露（只有相關內容佔用 context）——這些設計原則的共同精神是：**承認 AI 的限制，用系統設計去補足，而不是假設 AI 永遠不出錯**。
Skill 系統還在快速演化中。SkillOpt 的文字空間優化、Fleet Mode 的並行編排、[agentskills.io](http://agentskills.io) 的跨平台標準——每一個方向都在拓展「AI Agent 能做什麼」的邊界。但無論技術怎麼變，「人類先看過一眼」這條規則，沒有例外。
---
## 參考來源
本文章所有論點均來自以下 wiki 檔案：
### Sources（原始來源筆記）
- wiki/sources/[2026-08-14-agent-skills-overview.md](http://2026-08-14-agent-skills-overview.md)
- wiki/sources/[2026-08-14-anthropics-skills-github-repo.md](http://2026-08-14-anthropics-skills-github-repo.md)
- wiki/sources/[2026-08-14-agent-skills-api-quickstart.md](http://2026-08-14-agent-skills-api-quickstart.md)
- wiki/sources/[2026-08-14-agent-skills-api-guide.md](http://2026-08-14-agent-skills-api-guide.md)
- wiki/sources/[2026-08-14-skill-writing-best-practices.md](http://2026-08-14-skill-writing-best-practices.md)
- wiki/sources/[2026-08-14-agent-skills-enterprise-deployment.md](http://2026-08-14-agent-skills-enterprise-deployment.md)
- wiki/sources/[2026-08-02-morphir-dotnet-agents.md](http://2026-08-02-morphir-dotnet-agents.md)
- wiki/sources/[2026-08-14-copilot-vs-claude-code-skills-ecosystem.md](http://2026-08-14-copilot-vs-claude-code-skills-ecosystem.md)
- wiki/sources/[2026-08-15-round-table-pi-agent-v084-learning.md](http://2026-08-15-round-table-pi-agent-v084-learning.md)
- wiki/sources/[2026-08-17-devops-skill-presentation.md](http://2026-08-17-devops-skill-presentation.md)
- wiki/sources/[2026-08-18-claude-code-hooks-tutorial.md](http://2026-08-18-claude-code-hooks-tutorial.md)
- wiki/sources/[2026-07-11-mattpocock-skills.md](http://2026-07-11-mattpocock-skills.md)
- wiki/sources/[2026-07-22-mattpocock-skills-tutorial.md](http://2026-07-22-mattpocock-skills-tutorial.md)
### Concepts
- wiki/concepts/[agent-skills.md](http://agent-skills.md)
- wiki/concepts/[skill-authoring-best-practices.md](http://skill-authoring-best-practices.md)
- wiki/concepts/[agent-skills-api-usage.md](http://agent-skills-api-usage.md)
- wiki/concepts/[agent-skills-enterprise-governance.md](http://agent-skills-enterprise-governance.md)
- wiki/concepts/[skill-design-methodology.md](http://skill-design-methodology.md)
- wiki/concepts/[agent-extensibility-hierarchy.md](http://agent-extensibility-hierarchy.md)
- wiki/concepts/[skills-complex-composition-comparison.md](http://skills-complex-composition-comparison.md)
- wiki/concepts/[github-copilot-vs-claude-code-skills-ecosystem.md](http://github-copilot-vs-claude-code-skills-ecosystem.md)
- wiki/concepts/[knowledge-garden-skill-architecture.md](http://knowledge-garden-skill-architecture.md)
- wiki/concepts/[claude-code-hooks-architecture.md](http://claude-code-hooks-architecture.md)
- wiki/concepts/[copilot-agent-loop.md](http://copilot-agent-loop.md)
- wiki/concepts/[copilot-custom-agents.md](http://copilot-custom-agents.md)
- wiki/concepts/[copilot-fleet-mode.md](http://copilot-fleet-mode.md)
- wiki/concepts/[copilot-hooks-system.md](http://copilot-hooks-system.md)
- wiki/concepts/[copilot-steering-queueing.md](http://copilot-steering-queueing.md)
- wiki/concepts/[skillopt-meta-skill.md](http://skillopt-meta-skill.md)
### Entities
- wiki/entities/[anthropic-agent-skills.md](http://anthropic-agent-skills.md)
- wiki/entities/[github-copilot-sdk.md](http://github-copilot-sdk.md)
- wiki/entities/[mattpocock-skills.md](http://mattpocock-skills.md)
- wiki/entities/[skillopt.md](http://skillopt.md)
- wiki/entities/[agentskills-io-standard.md](http://agentskills-io-standard.md)
- wiki/entities/[guizang-ppt-skill.md](http://guizang-ppt-skill.md)
- wiki/entities/[huashu-design.md](http://huashu-design.md)
- wiki/entities/[pi-agent-core.md](http://pi-agent-core.md)
- wiki/entities/[knowledge-garden.md](http://knowledge-garden.md)
- wiki/entities/[round-table.md](http://round-table.md)
### Topic Index
- wiki/topics/[skill.md](http://skill.md)
### 花園既有種子（本研究專題相關）
本文章的內容横跨以下 Notion 知識花園種子，它們是本次研究的基礎素材：（沿用既有花園內容）
<table header-row="true">
<tr>
<td>種子名稱</td>
<td>花園連結</td>
<td>與本文的關係</td>
</tr>
<tr>
<td>GitHub Copilot — AI 程式碼輔助生態系</td>
<td>[Notion](https://app.notion.com/p/3bb5979e3a8c81baa661e06f1d8a1edc)</td>
<td>第七章跨框架比較的核心對象，Copilot SDK 的 Custom Agents、Fleet Mode、Hooks 系統</td>
</tr>
<tr>
<td>Claude Code — Anthropic AI Coding Agent</td>
<td>[Notion](https://app.notion.com/p/3bb5979e3a8c81ce93fdfd8be0c7ab49)</td>
<td>第七章跨框架比較的核心對象，Model-invoked 觸發模式與 Hooks 三層架構</td>
</tr>
<tr>
<td>mattpocock/skills — 第一棵樹苗</td>
<td>[Notion](https://app.notion.com/p/3b35979e3a8c81b299d6e6689d78f20e)</td>
<td>10.5 節的完整案例，User-invoked 為主的工程實踐與工作流程方法論</td>
</tr>
<tr>
<td>Skill 設計方法論（研究專題）</td>
<td>[研究專題](https://app.notion.com/p/f29355ae1f104ba69bcb960efd57e547)</td>
<td>第二～三章的 Intention-First 分析、Event Storming 拆邊界、交辦包設計</td>
</tr>
<tr>
<td>Agent 擴充架構層級</td>
<td>wiki/concepts/[agent-extensibility-hierarchy.md](http://agent-extensibility-hierarchy.md)</td>
<td>第四章 Skill / Hook / Extension 三層能力光譜與決策樹</td>
</tr>
</table>