# Tree-sitter — 高效能增量 Parser

> **成長階段：** 🌱 種子期
> **Tags：** 🔬 研究、🛠️ 實作

---

## 這是什麼？

Tree-sitter 是一個 Parser Generator，能將原始程式碼轉換為結構化的語法樹（Syntax Tree）。它的核心價值在於**增量解析**——編輯程式碼時不需要重新解析整份檔案，只需處理變動部分，回應速度 < 1ms。

它是現代 Code Intelligence 的基礎設施：VS Code、Neovim、Zed、GitHub 的語法高亮和程式碼理解能力，底層都依賴 Tree-sitter。

---

## 客觀事實

| 概念 | 發現 | 來源 |
|------|------|------|
| GLR 演算法 | Generalized LR，支援語法模糊性（Ambiguity），生成 CST 與 AST | Tree-sitter 官方文檔 |
| 增量解析 | 編輯時僅提供變動範圍（Edit Range），透過重用舊語法樹節點，O(log N) 時間複雜度完成局部重建 | Tree-sitter 官方文檔 |
| 語言支援 | 使用 `.scm` 檔案定義文法規則，支援 100+ 語言（Python、TypeScript、Go、Rust、C++ 等） | Tree-sitter GitHub |
| CST vs AST | CST（Concrete Syntax Tree）保留空白與分號，有利代碼格式化與重構；AST（Abstract Syntax Tree）是語義簡化版 | LSP 與 Code Graph 研究 |
| Parser Generator 架構 | 文法 → C 程式碼 → 增量解析器，不是解釋器，是編譯器 | Tree-sitter 官方文檔 |
| WASM 支援 | web-tree-sitter 提供瀏覽器端解析能力，可嵌入 Web 應用 | Tree-sitter GitHub |

---

## 深入技術分析

### 從 CST 到 Code Graph 的完整路徑

```
原始程式碼
    ↓ Tree-sitter 解析
CST（Concrete Syntax Tree）
    ↓ 語法轉換
AST（Abstract Syntax Tree）
    ↓ 作用域鏈分析
Scoped Symbol Table（作用域符號表）
    ↓ 引用解析
Def-Use Chain（定義-使用鏈）
    ↓ 關係邊構建
Call Graph / Module Dependency / Inheritance Tree
    ↓
Code Graph（程式碼語意圖譜）
```

**每一步的意義：**
1. **CST → AST**：移除語法噪音（空白、分號），保留語義結構
2. **AST → Scoped Symbol Table**：建立作用域層級（block → function → class → module），每個符號知道自己在哪个作用域中
3. **Scoped Symbol Table → Def-Use Chain**：將每個 Reference 連結至對應的 Definition，這是「這變數從哪來」的答案
4. **Def-Use Chain → Code Graph**：構建關係邊——`DEFINED_IN`、`REFERENCES`、`CALLS`、`INHERITS`

### Tree-sitter Query Language

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

**這比正則表達式強大得多**：正則是字元層級的模式匹配，Tree-sitter Query 是語法樹層級的結構匹配。正則無法處理巢狀結構，Tree-sitter 可以。

### 增量解析的運作原理

```
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

**關鍵優勢**：對於大型檔案（10,000+ 行），傳統 Parser 需要 ~100ms 重新解析，Tree-sitter 只需 ~0.5ms。這是即時語法高亮和 LSP 即時診斷的技術基礎。

---

## 我的觀點

### Tree-sitter 在 AI Agent 生態系中的位置

Tree-sitter 不是直接給 AI 用的工具，而是**AI 理解程式碼的基礎設施**。它的價值在於：

1. **Code Graph 的起點**：沒有 Tree-sitter，就沒有結構化的程式碼表示，AI 只能靠正則和字串匹配來「猜」程式碼結構
2. **LSP 的依賴**：大多數 Language Server 底層都用 Tree-sitter 做解析，AI 透過 LSP 間接依賴它
3. **多語言統一**：一套 Tree-sitter 架構支援 100+ 語言，不需要為每種語言寫專用 Parser

### 來源的做法 vs 我的做法

| 面向 | Tree-sitter 的設計 | 我們的做法 | 觀察 |
|------|-------------------|-----------|------|
| 解析策略 | 增量解析（O(log N)） | 每次全量解析 | 效能差異巨大，但目前 wiki 規模不需要增量 |
| 查詢語言 | `.scm` 格式的結構化查詢 | 正則 + grep | Tree-sitter Query 能處理巢狀結構，正則不行 |
| 語言支援 | 100+ 語言的文法定義 | 只處理 Markdown | 如果未來要分析 code，Tree-sitter 是必備 |
| 整合方式 | C library + 多語言 binding | 未整合 | 可以考慮用 Python binding 做程式碼分析 |

### 盲區警示

- Tree-sitter 只做**語法解析**，不做**語義分析**（型別推論、data flow analysis）。完整的 Code Graph 需要搭配 LSP 或其他靜態分析工具
- 增量解析的效能優勢在小檔案上不明顯，但對大型 codebase 是關鍵
- `.scm` 文法檔案的維護成本不低，每種語言都需要社群維護

---

## 下一步

- [ ] 研究 Tree-sitter Python binding 的實際使用方式
- [ ] 測試用 Tree-sitter Query 從專案 codebase 擷取函式定義和呼叫關係
- [ ] 評估是否能用 Tree-sitter 做 wiki 內容的程式碼區塊解析
- [ ] 建立 Tree-sitter → Code Graph → AI 檢索的完整 pipeline prototype

---

## 連結

- 原始來源：https://tree-sitter.github.io/tree-sitter/
- GitHub：https://github.com/tree-sitter/tree-sitter
- Wiki：[[wiki/entities/tree-sitter|tree-sitter]]
- 相關種子：[[wiki/entities/lsp|LSP]]、[[wiki/concepts/code-graph|Code Graph]]
- 相關專題：🤖 AI Agent 架構研究、🛠️ AI 驅動的開發系統
