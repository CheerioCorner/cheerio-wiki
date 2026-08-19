---
title: "Skill 撰寫方法論"
type: concept
created: 2026-08-14
updated: 2026-08-17
sources: 2
tags: [skill, best-practices, authoring, evaluation, prompt-engineering]
topics: [skill-system]
canonical: concepts/skill-authoring-best-practices
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-skill-撰寫最佳實踐.md"
---

# Skill 撰寫方法論

> 撰寫高品質 Agent Skill 的通用方法論：從結構規範到評估迭代，不限於特定平台。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。**

## 核心原則

### 1. 簡潔是關鍵

Context window 是公共資源。預設假設：**Claude 已經非常聰明**，只添加它尚未擁有的上下文。

- ✅ ~50 tokens 的簡潔指令（假設 Claude 知道 PDF 和函式庫）
- ❌ ~150 tokens 的冗長解釋（包含 Claude 已知的基礎知識）

### 2. 設定適當自由度

將具體程度與任務的脆弱性和變異性匹配：

| 自由度 | 適用場景 | 範例 |
|-------|---------|------|
| **高** | 多種方法都有效、上下文決定 | Code review 流程 |
| **中** | 偏好模式、可接受變化 | 報告產生模板 |
| **低** | 操作脆弱、一致性至關重要 | 資料庫遷移腳本 |

**類比**：窄橋（兩側懸崖）→ 低自由度、精確指示；開闊原野 → 高自由度、大致方向。

### 3. 跨模型測試

- **Haiku**：指引是否足夠？
- **Sonnet**：是否清晰高效？
- **Opus**：是否避免過度解釋？

## 結構規範

### YAML Frontmatter

```yaml
---
name: processing-pdfs          # ≤64 字元，小寫/數字/連字號
description: Extract text...   # ≤1024 字元，功能+使用時機
---
```

### 命名慣例

推薦**動名詞形式**：`processing-pdfs`、`analyzing-spreadsheets`、`managing-databases`

### 檔案組織

- SKILL.md 主體 < 500 行
- 參考距離 SKILL.md 只**一層深**（避免深層巢狀）
- 超過 100 行的參考檔案頂部加目錄
- 按領域或功能建構目錄（`reference/finance.md` vs `docs/file1.md`）

## 常見模式

### 範本模式

```
# 嚴格要求
ALWAYS use this exact template structure...

# 彈性指引
Here is a sensible default format, but use your best judgment...
```

### 範例模式

提供輸入/輸出配對，比描述更清楚傳達所需風格。

### 條件式工作流程

```
1. Determine modification type:
   **Creating new?** → Follow "Creation workflow"
   **Editing existing?** → Follow "Editing workflow"
```

### 驗證循環

執行驗證器 → 修復錯誤 → 重複。**品質關鍵任務必備**。

### 寫入安全模式（Write-back Safety Gate）

> 2026-08-17 新增。處理有副作用的寫入操作——比驗證循環更嚴格的安全機制。

**四步安全門**：

1. **`--dry-run` 先預覽**：先跑一次，不動任何資料
2. **人類確認**：把預覽結果攤給人看、等點頭
3. **`--confirm` 才寫入**：確認後才真正執行
4. **讀回驗證**：寫完再讀一次，核對無誤

**三級分類**：

| 分類 | 準則 | 範例 |
|------|------|------|
| ✓ 要 | 明確的事實、明確的歸屬 | 「我的」升級案、指派給我的 WIT |
| ! 小心 | 會動到別人的資料或正式設定 | 改到同事的工時 → 需要更高權限 |
| ✕ 不要 | AI 推測、幻想、沒有證據的結論 | AI 自己猜出來的工時數字 |

**核心規則：不可逆的事，人類永遠先看過一眼。這條規則沒有例外。**

> 💡 **跨場景共通模式**：這套「寫入前預覽/護欄 + 寫入後讀回驗證，不信任寫入指令本身回報成功」的安全設計，在不同場景被獨立發明出來——例如 [[wiki/sources/2026-08-17-devops-skill-presentation|DevOps 簡報]] 的「回寫安全門」四步驟，以及 W-2026-08-055 的 `notion_verify.sh` DB read-back 三層驗證（寫入前 Relation snapshot 護欄 → 執行寫入 → 讀回三層驗證）。兩者核心精神一致：**不信任「寫入指令回報成功」，必須用讀回來驗證**。這可能是個值得抽成通用原則的設計模式。

## 評估與迭代

### 先建立評估

1. 識別差距（無 Skill 時的失敗）
2. 建立 3 個評估情境
3. 建立基準
4. 撰寫最少指示
5. 迭代改進

### Claude A+B 迭代法

- **Claude A**：協助設計和改進指示的專家
- **Claude B**：載入 Skill 執行真實任務的代理
- 交替進行：觀察 B 的行為 → 帶回 A 改進 → 再測試

**觀察重點**：意外的探索路徑、遺漏的連結、過度依賴的章節、被忽略的內容。

## 反模式清單

| ❌ 反模式 | ✅ 正確做法 |
|----------|-----------|
| Windows 路徑 `scripts\helper.py` | Unix 路徑 `scripts/helper.py` |
| 提供 5 種 PDF 函式庫選擇 | 提供預設 + escape hatch |
| 深層巢狀（SKILL.md → A → B → C） | 只一層深 |
| 時效性資訊（「2025 年 8 月前用舊 API」） | 舊模式章節（`<details>`） |
| 不一致術語（混用 URL/route/path） | 統一用詞 |
| 不處理錯誤的腳本 | 明確錯誤處理 +  helpful 訊息 |
| 魔術數字 `TIMEOUT = 47` | 自我記錄 `REQUEST_TIMEOUT = 30  # HTTP 通常 <30s` |

## 進階：可執行腳本的 Skills

### 工具腳本優勢

- 比生成的程式碼更可靠
- 節省 token（腳本程式碼不進入 context）
- 確保跨使用一致性

### 可驗證中間輸出

「計劃-驗證-執行」模式：建立結構化計劃 → 腳本驗證 → 執行。適用於批次操作、破壞性變更、高風險操作。

### MCP 工具參考

使用完全限定名稱：`ServerName:tool_name`（避免「找不到工具」錯誤）。

## 檢查清單

### 核心品質

- [ ] 描述具體且包含關鍵詞
- [ ] SKILL.md < 500 行
- [ ] 無時效性資訊
- [ ] 術語一致
- [ ] 範例具體
- [ ] 檔案參考只一層深
- [ ] 工作流程有清晰步驟

### 程式碼和腳本

- [ ] 腳本處理錯誤（不推給 Claude）
- [ ] 無魔術數字
- [ ] 所需套件列出並驗證
- [ ] 無 Windows 路徑
- [ ] 關鍵操作有驗證步驟

### 測試

- [ ] 至少 3 個評估
- [ ] Haiku/Sonnet/Opus 測試
- [ ] 真實使用情境測試

## 來源

- [[wiki/sources/2026-08-14-skill-writing-best-practices|Skill 撰寫最佳實踐]] — Anthropic 官方完整指南

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 上層導航
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Skill 系統本體
- [[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill]] — 微軟技能優化方法論
- [[wiki/entities/mattpocock-skills|mattpocock/skills]] — 實際 Skill 範例集合
