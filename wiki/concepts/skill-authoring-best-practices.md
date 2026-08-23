---
title: "Skill 撰寫方法論"
type: concept
created: 2026-08-14
updated: 2026-08-23
sources: 3
tags: [skill, best-practices, authoring, evaluation, prompt-engineering]
topics: [skill-system]
canonical: concepts/skill-authoring-best-practices
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-skill-撰寫最佳實踐.md"
  - kind: youtube
    url: "https://www.youtube.com/watch?v=qYNs80FKIVc"
    description: "IBM《5 Best Practices for Building Agent Skills》，經圓桌會議（Claude+Gemini）定稿補入 5 段"
---

# Skill 撰寫方法論

> 撰寫高品質 Agent Skill 的通用方法論：從結構規範到評估迭代，不限於特定平台。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。**

## 核心原則

### 1. 簡潔是關鍵

Context window 是公共資源。預設假設：**Claude 已經非常聰明**，只添加它尚未擁有的上下文。

- ✅ ~50 tokens 的簡潔指令（假設 Claude 知道 PDF 和函式庫）
- ❌ ~150 tokens 的冗長解釋（包含 Claude 已知的基礎知識）

**不要讓 LLM 代寫 skill 本身**：找 AI 生成一份「幫我寫個處理 X 的 skill」看起來省事，但寫出來的內容多半是「適當處理錯誤」「驗證輸入」這種模型自己早就知道的空話——影片裡的說法是 generic mush（沒營養的通用廢話）。Skill 存在的意義，就是把模型自己拿不到的東西寫下來：具體做法、踩過的雷、事後修正過的細節。這些只能來自自己動手做一次，或是從既有的 runbook、review 意見、PR 回饋等真實素材整理出來，沒有捷徑。用 Simon Willison 的話說：把領域專業知識留給自己，把打字的苦力活交給 agent。

> 📺 依據：IBM《5 Best Practices for Building Agent Skills》[03:41][04:11][04:46][04:56]

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

### 描述是觸發器

Agent 啟動時只會載入每個 Skill 的 name 與 description，本體要等被選中才讀進 context——這代表 description 這 1024 字元，決定了這個 Skill 有沒有機會被用到。實務上模型偏向「觸發不足」：與其含蓄地描述功能，不如稍微誇大（oversell）一點也不要保守（undersell），寫得太謙虛，Agent 該用時反而不會用。

判斷標準：description 有沒有同時交代「做什麼」和「什麼情境下用」。

- ❌ 「產生報告」（太模糊，Agent 不知道什麼時候該用）
- ✅ 「當有人要求月結報表或月度歸檔時，從內部資料產生月結合規報告」（功能 + 時機都鎖定）

> 📺 依據：IBM《5 Best Practices for Building Agent Skills》[01:29][03:21][03:25]

### 命名慣例

推薦**動名詞形式**：`processing-pdfs`、`analyzing-spreadsheets`、`managing-databases`

### 檔案組織

- SKILL.md 主體 < 500 行（約 5,000 tokens 以內——行數只是粗略估算，tokens 才是真正吃掉 context 的額度，尤其中文字元的 token 密度跟英文不同，兩個維度最好都留意）
- 超過篇幅就拆到 `references/` 子目錄，agent 需要時才讀取（跟下面「參考距離 SKILL.md 只一層深」是同一套規則的兩面）
- 參考距離 SKILL.md 只**一層深**（避免深層巢狀）
- 超過 100 行的參考檔案頂部加目錄
- 按領域或功能建構目錄（`reference/finance.md` vs `docs/file1.md`）

> 📺 依據：IBM《5 Best Practices for Building Agent Skills》[07:14][07:46]。**明確排除**：不採用「超過範圍拆成多個子技能（Sub-skills）」——講的是同一個 skill 內部把細節挪到 `references/`，不是拆成多個獨立 skill。

## 常見模式

### 踩坑記錄（Gotchas）

Skill body 裡，投資報酬率最高的一段不是流程說明，而是 **gotchas**——那些會打破「合理假設」的環境特定事實。每次手動糾正 Agent 的行為，那個糾正動作本身就是一個 gotcha，值得立刻寫下來；不寫下來的下場是，同樣的錯誤下週會再犯一次，下下週還會再犯一次。

寫法上不需要長篇解釋原理，條列式記錄「什麼情況 + 什麼行為是錯的 + 正確做法是什麼」就夠了。這個段落的價值往往比整份 Skill 其他部分加起來還高，因為它裝的是別處學不到的實戰教訓。

> 📺 依據：IBM《5 Best Practices for Building Agent Skills》[05:10][05:19][05:23][05:27][05:31]

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

## 安全使用守則

> 這裡談的是「每次使用前」的個人習慣，跟組織層級的正式安全審查流程是兩回事——即使公司已經有把關，動手前自己這道檢查依然值得做。
>
> 📺 依據：IBM《5 Best Practices for Building Agent Skills》[10:59][11:04][11:07][11:10][11:33][11:36][12:00][12:13]

Skill 可以包含並執行程式碼，這代表安裝一個來路不明的 Skill，等於在自己機器上跑陌生人寫的軟體。這些腳本能存取的東西不只本機檔案系統，也包括環境裡的任何 API key。這種能力正是 Skill 好用的原因，但也是風險所在。

一份稽核掃描了近 4,000 個公開 Skill，發現超過 **35% 存在某種安全漏洞，13% 有嚴重問題**（例如 prompt injection 或直接夾帶惡意程式）。開放標準只保證格式相容，不保證內容安全。

使用任何非自己撰寫的 Skill 前，建議養成兩個習慣：

1. **用前先掃一遍**：打開 `scripts/` 目錄看一眼有哪些檔案，留意不必要的網路存取、檔案系統操作、或可疑的系統呼叫——就像拉一個新的第三方套件進專案前，會先看一眼它做了什麼。
2. **看懂邏輯再放行**：如果 Skill 裡有腳本，執行前至少搞懂它在幹嘛、能碰到哪些資源（檔案、網路、環境變數）。讓 AI 跑一支腳本，等於代替你做了一次「知情同意」——至少自己要先知情。

自己動手寫、自己讀過、也親自把脆弱步驟改成腳本的 Skill，才是能信任的 Skill；別人給的，規則一樣：用之前，先看過，切勿盲目授權執行。

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
- IBM《5 Best Practices for Building Agent Skills》（YouTube，https://www.youtube.com/watch?v=qYNs80FKIVc）— 觸發精準度、避免代寫、5,000 tokens、安全使用守則、gotchas 五項補充皆出自本影片逐字稿，時間戳詳見各段落 blockquote

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 上層導航
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Skill 系統本體
- [[wiki/concepts/skillopt-meta-skill|SkillOpt Meta-skill]] — 微軟技能優化方法論
- [[wiki/entities/mattpocock-skills|mattpocock/skills]] — 實際 Skill 範例集合
