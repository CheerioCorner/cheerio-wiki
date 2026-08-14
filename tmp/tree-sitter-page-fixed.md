---
Sync Status: ✅ 已同步
Tags:
- 🔬 研究
Wiki GitHub: null
Wiki Path: https://github.com/CheerioCorner/cheerio-wiki/blob/main/wiki/entities/tree-sitter.md
來源 URL: https://tree-sitter.github.io/tree-sitter/
成長狀態: 🌿 成長期
最後更新:
  end: null
  start: 2026-08-14
  time_zone: null
研究專題:
- 3b45979e-3a8c-8152-8f00-e9034894fe4b
種下日期:
  end: null
  start: 2026-08-10
  time_zone: null
種子: Tree-sitter — 高效能增量 Parser
給我的啟發？: O(log N) 增量解析是 Code Graph 的基礎。從 CST→AST→Symbol Table→Call Graph 的建構路徑非常清晰。
視覺地圖:
- 3bc5979e-3a8c-81ba-b6a2-e5e2b1ee711a
---

# Tree-sitter — 高效能增量 Parser
> **成長階段：** 🌱 種子期
> **Tags：** 🔬 研究、🛠️ 實作
---
## 這是什麼？
Tree-sitter 是一個 **Parser Generator**，能將原始程式碼轉換為結構化的語法樹（Syntax Tree）。它的核心價值在於 **增量解析（Incremental Parsing）**——編輯程式碼時不需要重新解析整份檔案，只需處理變動部分，回應速度 \< 1ms。
它是現代 Code Intelligence 的基礎設施：Neovim、Zed、Helix、GitHub 的語法高亮和程式碼理解能力，底層都依賴 Tree-sitter。它解決了三個關鍵問題：
1. **增量解析**——大型檔案（10,000+ 行）的即時解析，傳統 Parser 需要 \~100ms，Tree-sitter 只需 \~0.5ms
2. **Error Recovery**——遇到語法錯誤時不崩潰，繼續解析後續內容，這是編輯器「邊打字邊高亮」的技術基礎
3. **多語言統一**——一套架構支援 100+ 語言，不需要為每種語言寫專用 Parser
## 客觀事實
<table header-row="true">
<tr>
<td>概念</td>
<td>發現</td>
<td>來源</td>
</tr>
<tr>
<td>GLR 演算法</td>
<td>Generalized LR，支援語法模糊性（Ambiguity），生成 CST 與 AST</td>
<td>Tree-sitter 官方文檔</td>
</tr>
<tr>
<td>增量解析</td>
<td>編輯時僅提供變動範圍（Edit Range），透過重用舊語法樹節點，O(log N) 時間複雜度完成局部重建</td>
<td>Tree-sitter 官方文檔</td>
</tr>
<tr>
<td>語言支援</td>
<td>使用 `.scm` 檔案定義文法規則；tree-sitter org 官方有 33 個語言 parser repo，社群另有 100+ 種語言</td>
<td>Tree-sitter GitHub org（2026-08-14 抓取）</td>
</tr>
<tr>
<td>CST vs AST</td>
<td>CST（Concrete Syntax Tree）保留空白與分號，有利代碼格式化與重構；AST 是語義簡化版</td>
<td>LSP 與 Code Graph 研究</td>
</tr>
<tr>
<td>Parser Generator 架構</td>
<td>文法 → C 程式碼 → 增量解析器，不是解釋器，是編譯器</td>
<td>Tree-sitter 官方文檔</td>
</tr>
<tr>
<td>WASM 支援</td>
<td>web-tree-sitter 提供瀏覽器端解析能力，可嵌入 Web 應用</td>
<td>Tree-sitter GitHub</td>
</tr>
<tr>
<td>維護者</td>
<td>Max Brunsfeld（@maxbrunsfeld），原 GitHub 員工，現為獨立維護者；GitHub（Microsoft）內部仍大量使用</td>
<td>GitHub tree-sitter org（2026-08-14 抓取）</td>
</tr>
<tr>
<td>授權條款</td>
<td>MIT License</td>
<td>GitHub（2026-08-14 抓取）</td>
</tr>
<tr>
<td>GitHub 數據</td>
<td>⭐ 26,634 stars / 2,814 forks / 主要語言 Rust / 建立於 2013-11-06 / 最後推送 2026-08-14</td>
<td>GitHub API（2026-08-14 抓取）</td>
</tr>
<tr>
<td>主要採用者</td>
<td>GitHub（語法高亮）、Neovim（ treesitter 套件）、Zed（核心解析引擎）、Helix、Pulsar（原 Atom）、Android Code Search</td>
<td>各專案官方文檔</td>
</tr>
</table>
## 核心機制
### 增量解析（Incremental Parsing）
這是 Tree-sitter 最關鍵的特性。運作原理：
```javascript
初始解析：完整解析 → 建立語法樹
    ↓
編輯操作：使用者修改第 10-15 行
    ↓
變動偵測：Tree-sitter 計算 edit range
    ↓
局部重建：僅重新解析受影響的子樹（O(log N)）
    ↓
結果：新語法樹（大部分節點重用舊樹）
```
**為什麼是 O(log N)？** 每次編輯只有「受影響的子樹」需要重建。Tree-sitter 透過比較新舊文本的差異，找到最小的重解析範圍。在一萬行的檔案中改一行，可能只需要重新解析幾十個節點，而不是整棵樹。
### CST（Concrete Syntax Tree）
Tree-sitter 生成的是 **Concrete Syntax Tree**，不是簡化的 AST。這很重要：
- **CST 保留所有語法細節**——空白、註釋、分號、逗號全部保留在樹上
- **CST 是可逆的**——從 CST 可以完美還原原始文本，這是格式化工具（prettier、black）能安全重排程式碼的基礎
- **CST 支援增量**——因為每個葉節點都對應文本中的確切位置，編輯時可以精確定位變動範圍
### Query Language（S-expression 格式）
Tree-sitter 提供自己的查詢語言（`.scm` 檔案），用於從語法樹中擷取特定模式：
```scheme
; 擷取所有函式定義
(function_definition
  name: (identifier) @func_name
  parameters: (parameters) @params
  body: (block) @body)

; 擷取所有類別定義
(class_definition
  name: (identifier) @class_name
  body: (block) @class_body)
```
**這比正則表達式強大得多**：正則是字元層級的模式匹配，Tree-sitter Query 是語法樹層級的結構匹配。正則無法處理巢狀結構，Tree-sitter 可以。例如，要擷取「巢狀在函式內的類別」，正則幾乎不可能做到，Tree-sitter Query 只需要一個 nested pattern。
### Grammar 與 `.wasm` 編譯流程
```javascript
grammar.js（JavaScript 定義語法規則）
    ↓ tree-sitter generate
C 程式碼（parser.c）
    ↓ 編譯
原生 Parser（C/Rust）或 WASM 模組
    ↓
可嵌入任何環境（編輯器、瀏覽器、伺服器）
```
關鍵設計：
- Grammar 用 JavaScript 程式碼定義（`grammar.js`），比純 BNF/EBNF 更靈活
- 編譯後的 parser 是純 C 程式碼，可交叉編譯到任何平台
- WASM 版本讓同一個 parser 可在瀏覽器中執行，GitHub 的語法高亮就用這個
## 與 LSP / Code Graph 的關聯與分工
Tree-sitter、LSP 和 Code Graph 三者是 **不同層級的抽象**，各自解決不同問題：
<table header-row="true">
<tr>
<td>層級</td>
<td>工具</td>
<td>解決什麼問題</td>
<td>範圍</td>
</tr>
<tr>
<td>語法解析</td>
<td>Tree-sitter</td>
<td>程式碼 → 結構化語法樹</td>
<td>單檔</td>
</tr>
<tr>
<td>語意分析</td>
<td>LSP Language Server</td>
<td>型別推論、跨檔案引用、自動完成</td>
<td>多檔</td>
</tr>
<tr>
<td>語意圖譜</td>
<td>Code Graph</td>
<td>跨模組的符號關係網路</td>
<td>整個 codebase</td>
</tr>
</table>
**分工關係：**
- **Tree-sitter** 是 **LSP 的基礎設施**：大多數 Language Server（如 typescript-language-server、gopls）底層都用 Tree-sitter 做初始解析
- **LSP** 是 **Tree-sitter 的上層消費者**：LSP 拿到 Tree-sitter 的 CST/AST 後，再加上型別系統、作用域分析等語義資訊
- **Code Graph** 是 **兩者的最終產出**：從 Tree-sitter 的 AST 出發，經過 LSP 級的語義分析，最終建構出跨模組的關係網路
```javascript
Tree-sitter（語法層）
    ↓ CST/AST
LSP Language Server（語意層）
    ↓ 型別資訊、引用解析
Code Graph（圖譜層）
    ↓ DEFINED_IN / REFERENCES / CALLS / INHERITS
AI Code Assistant（應用層）
```
**關鍵洞察：** Tree-sitter 只做語法解析，不做語義分析（型別推論、data flow analysis）。完整的 Code Graph 需要搭配 LSP 或其他靜態分析工具。但 Tree-sitter 的增量解析能力，讓整個 pipeline 能即時運作——這是 LSP 能提供 \< 100ms 回應時間的技術基礎。
## 給我的啟發
O(log N) 增量解析是 Code Graph 的基礎。從 CST → AST → Symbol Table → Call Graph 的建構路徑非常清晰。
更進一步的思考：
1. **Tree-sitter 是「程式碼理解」的最小可行單元**——如果只能選一個工具來讓 AI 理解程式碼，Tree-sitter 是起點。它提供結構化表示，是所有上層分析的基礎
2. **增量解析的哲學值得學習**——「只處理變動部分」不只是效能優化，是一種思維模式。我們的 wiki 知識庫目前是全量重建 index，未來如果規模膨脹，可以參考這種增量更新的思路
3. **Grammar-as-Code 的設計選擇**——用 JavaScript 定義語法（而不是傳統的 BNF 文字檔），降低了社群維護門檻，這是 Tree-sitter 能累積 100+ 語言支援的關鍵設計決策
4. **Error Recovery 被低估**——大多數 Parser 遇到語法錯誤就崩潰，Tree-sitter 的 error recovery 讓它能在「程式碼寫到一半」的狀態下運作，這對編輯器體驗至關重要
## 盲區警示
- Tree-sitter 只做 **語法解析**，不做 **語義分析**（型別推論、data flow analysis）。完整的 Code Graph 需要搭配 LSP 或其他靜態分析工具
- 增量解析的效能優勢在小檔案上不明顯，但對大型 codebase 是關鍵
- `.scm` 文法檔案的維護成本不低，每種語言都需要社群維護。有些語言的 parser 可能過時或不完整
- Tree-sitter 的 GLR 演算法在處理高度模糊語法時可能產生多棵解析樹，需要額外策略選擇正確的解析結果
## 下一步
- [ ] 研究 Tree-sitter Python binding 的實際使用方式
- [ ] 測試用 Tree-sitter Query 從專案 codebase 擷取函式定義和呼叫關係
- [ ] 評估是否能用 Tree-sitter 做 wiki 內容的程式碼區塊解析
- [ ] 建立 Tree-sitter → Code Graph → AI 檢索的完整 pipeline prototype
## 連結
- 原始來源：[https://tree-sitter.github.io/tree-sitter/](https://tree-sitter.github.io/tree-sitter/)
- GitHub：[https://github.com/tree-sitter/tree-sitter](https://github.com/tree-sitter/tree-sitter)
- GitHub org（所有 parser）：[https://github.com/tree-sitter](https://github.com/tree-sitter)
- Wiki：\[\[wiki/entities/tree-sitter\|tree-sitter\]\]
- 相關種子：\[\[wiki/entities/lsp\|LSP\]\]、\[\[wiki/concepts/code-graph\|Code Graph\]\]
- 相關專題：🤖 AI Agent 架構研究、🛠️ AI 驅動的開發系統