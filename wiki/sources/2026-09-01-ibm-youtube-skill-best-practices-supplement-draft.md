---
title: "IBM YouTube 影片 Skill 五項最佳實踐 — 補充草稿"
type: draft
created: 2026-09-01
updated: 2026-08-22
tags: [skill, best-practices, youtube, draft, supplement]
topics: [skill-system]
status: draft — 圓桌定稿完成，待寫入 skill-authoring-best-practices.md + 雙模型覆核
provenance:
  - kind: youtube
    url: "https://youtu.be/qYNs80FKIVc?si=ONtRQtqXU73YXi8K"
    description: "IBM Martin Keen & Bri Kopecki 講解開發 AI Agent Skills 的五項最佳實踐"
  - kind: raw
    path: "raw/youtube/2026-09-01-ibm-skill-best-practices.md"
    description: "字幕逐字稿（英文手動字幕，Pi 於 2026-09-01 抓取並比對驗證）"
---

# IBM YouTube 影片 Skill 五項最佳實踐 — 補充草稿

> ⚠️ **草稿狀態**：本文件是待覆核的補充草稿，尚未併入任何既有文章或 Notion 花園。

---

## 影片來源資訊

| 項目 | 內容 |
|------|------|
| 標題 | IBM — 開發 AI Agent Skills 的五項最佳實踐 |
| 講者 | Martin Keen & Bri Kopecki（IBM） |
| URL | https://youtu.be/qYNs80FKIVc?si=ONtRQtqXU73YXi8K |
| 觀看日期 | 2026-09-01（Cheer 觀看） |

---

## 使用者原話心得摘要

> 這部影片講的內容，跟我們之前在花園裡討論過的「做花園裡面專題宗旨的 SKILL」那篇文章大致上重疊，但影片更聚焦在 SKILL 本身怎麼寫（而不是我們原本討論的專題宗旨/策展層面），所以有幾個值得補充收錄的重點：
>
> 1. **如何精準觸發**（SKILL 的觸發時機/描述要精準）
> 2. **專業知識部分**：SKILL 的內容要來自實際操作經驗與知識領域，不能是 AI 生成的；建議 SKILL.md 篇幅抓在 500 行/5000 字以內
> 3. **Script（腳本）部分**：確定性、可程式化的東西（像 business rule）應該寫成腳本，不要讓 AI 在執行時亂猜或臨場自己寫。另外兩個重點：(a) 使用任何 SKILL 前都要先掃描，避免夾帶惡意軟體或漏洞；(b) 使用前必須理解該 SKILL 裡如果含有 script，它的整體邏輯跟資源存取權限是什麼。

> ⚠️ **以上是 Cheer 觀看後的心得複述，非逐字稿**。2026-09-01 已由 Pi 抓字幕逐句比對，見下方「影片驗證結果」——大方向對，但有 1 處單位錯誤（字→tokens）與幾處語意簡化，詳見該節。

---

## 影片驗證結果（2026-09-01，Pi 抓字幕逐項比對）

字幕來源：`youtube_transcript_api`，英文手動字幕，197 segments 合併為 175 段，時長 00:13:09。Raw transcript：[[raw/youtube/2026-09-01-ibm-skill-best-practices]]。

| # | 心得摘要說法 | 影片實際內容（[MM:SS]） | 判定 |
|---|-------------|----------------------|------|
| 1 | 觸發時機/描述要精準 | [01:29] "The description is the trigger"；[01:54] name ≤64 字元、description ≤1024 字元；額外提到 [03:21][03:25] models tend to **under trigger**，描述應稍微 **oversell**，不要太保守 | ✅ 一致，但漏掉「稍微誇大描述」這個實用技巧 |
| 2 | 內容來自實際經驗，不能是 AI 生成 | [04:11] "build from real expertise"；[03:41] 原話是「不要讓 LLM 代寫 skill 本身」（會得到 generic mush），跟「內容不能含 AI 生成的東西」語意不同；[05:10] 明講 **gotchas 是 skill body 裡最高價值的段落**；[04:46][04:56] 引用 Simon Willison 的話 | ⚠️ 「不能是 AI 生成的」過度簡化了原意；漏掉 gotchas 段落與 Willison 引言這兩個重點 |
| 2b | 500 行 / **5000 字**以內 | [07:14] 原話是 "roughly about **5,000 tokens**"，不是 5000 字。tokens ≠ 字元（中文 1 字≈1-2 tokens），這個換算會讓實際篇幅認知差 2-3 倍 | 🔴 **單位錯誤，需修正**：字 → tokens |
| — | （心得未提及）progressive disclosure | [07:46] 影片只提過一次 "progressive disclosure"：超過 500 行就拆到 `references/` 子目錄，agent 需要時才讀取。這跟 `agent-skills.md` 的「三層漸進式揭露」是**不同概念**，不要混為一談（見下方待確認事項第 2 點的修正結論） | ℹ️ 新資訊，補充進 Topic 2 |
| 3 | 確定性東西寫成腳本 | [08:24][08:32] "loose step → instructions；fragile step → code"；[09:01] 腳本不進入 context，省 tokens 又比 agent 臨場發揮可靠 | ✅ 一致 |
| 3b/3c | 使用前掃描 + 理解腳本邏輯 | [10:59] 影片明確是**同一項** best practice "vet a skill before you run it"；[11:36] 引用審計數據：近 4,000 個公開 skills 中 **35% 有安全漏洞、13% 有嚴重問題**（prompt injection / malware）；[12:00] 類比「像對待任何第三方依賴一樣檢查」 | ⚠️ 草稿拆成 3b/3c 兩子項，影片結構是 5 項不是 6 項；漏掉 35%/13% 數據 |

**結論**：五項主題方向都對，但「5000 字」是需要修正的硬傷，「不能是 AI 生成的」需要換句話說更貼近原意，「漸進式解構」的猜測（見待確認事項 #2）已確認是誤植。

---

## 既有文章搜尋結果

### 🔍 搜尋過程

在整個 Obsidian 知識庫（`wiki/`、`raw/`、`work/`）中，以以下關鍵字搜尋：
- 「做花園」、「花園裡面」、「專題宗旨」
- 「漸進式解構」
- 「skill + 花園」、「skill + 宗旨」
- 「progressive deconstruct」

**結論：找不到**使用者明確提到的「做花園裡面專題宗旨的 SKILL」那篇文章。

### 📍 最接近的既有文章

| # | 檔案路徑 | 標題 | 相關度 |
|---|---------|------|--------|
| 1 | `wiki/concepts/skill-authoring-best-practices.md` | Skill 撰寫方法論 | ★★★★★ — 最直接相關，涵蓋撰寫規範、結構、500 行限制 |
| 2 | `wiki/concepts/agent-skills.md` | Agent Skills — Skill 系統根概念 | ★★★★☆ — 涵蓋三層漸進式揭露、結構規範、LLM 失敗模式 |
| 3 | `wiki/sources/2026-08-17-devops-skill-presentation.md` | 如何寫好一支 SKILL？· DevOps Board 系列 | ★★★★☆ — Cheer 自製簡報，涵蓋設計方法論、安全邊界、組合技 |
| 4 | `wiki/concepts/skill-design-methodology.md` | Skill 設計方法論 — 從人類意圖到 Skill 邊界 | ★★★☆☆ — 設計層方法論，非撰寫層 |
| 5 | `wiki/concepts/knowledge-garden-skill-architecture.md` | 知識花園 Skill 架構 | ★★★☆☆ — 花園 skill 全景圖，非撰寫規範 |
| 6 | `wiki/concepts/content-redesign.md` | Content 設計 Redesign | ★★☆☆☆ — 花園內容設計，非 skill 撰寫 |

---

## 逐 Topic 比對分析

### Topic 1：如何精準觸發（SKILL 的觸發時機/描述要精準）

| 既有文章 | 已涵蓋？ | 內容摘要 |
|---------|---------|---------|
| `skill-authoring-best-practices.md` | ✅ **已涵蓋** | 「描述具體且包含關鍵詞」在檢查清單中；YAML frontmatter `description` ≤1024 字元 |
| `agent-skills.md` | ✅ **已涵蓋** | LLM 失敗模式「選錯 Skill」→ 對應閘門「好的 description（觸發詞寫準，AI 才叫得對人）」 |
| `skill-design-methodology.md` | ✅ **部分涵蓋** | Intention-First 方法論隱含觸發精準度（從人的意圖出發切邊界） |

**判斷**：此 topic 在既有文章中已有涵蓋，但可以考慮：
- 將「觸發詞精準度」從散落各處收攏成一個獨立段落
- 補充影片中的具體技巧：[03:21][03:25] models tend to under trigger，描述應該稍微 oversell（誇大一點）而非 undersell，這點既有文章沒有

**建議動作**：在 `skill-authoring-best-practices.md` 的「描述」章節中，加一小段「觸發精準度」的強調，含「描述寧可稍微誇大也不要太保守」這個實用建議，引用影片來源。

---

### Topic 2：專業知識部分（SKILL 內容要來自實際操作經驗，不能是 AI 生成的；建議 500 行/5000 tokens 以內）

> ⚠️ 已依「影片驗證結果」修正：原心得摘要說的「5000 字」經字幕比對確認是**單位誤譯**，影片原話是「roughly about 5,000 **tokens**」（[07:14]），非字元數。以下表格與建議動作已更正。

| 既有文章 | 已涵蓋？ | 內容摘要 |
|---------|---------|---------|
| `skill-authoring-best-practices.md` | ✅ **500 行已涵蓋** | 「SKILL.md < 500 行」在結構規範與檢查清單中明確列出 |
| `skill-authoring-best-practices.md` | ⚠️ **5000 tokens 未提及** | 僅有 500 行限制，無 token 數建議 |
| `agent-skills.md` | ⚠️ **部分涵蓋，且概念不同** | 提到「三層漸進式揭露」，但影片講的 progressive disclosure（[07:46]）是「超過 500 行拆到 `references/` 子目錄」這個更具體的做法，兩者不能直接等同 |
| `skill-authoring-best-practices.md` | ⚠️ **部分涵蓋** | 提到「簡潔是關鍵：只添加 Claude 尚未擁有的上下文」，暗示專業知識應來自人類 |
| （既有文章均未涵蓋） | ❌ **未涵蓋** | gotchas 段落（[05:10] 影片明講是 skill body 裡最高價值的部分）；Simon Willison 引言（[04:46]，"keep the domain expertise and let the agent do the routine part"） |

**判斷**：此 topic 有幾個**值得新增**的重點：
1. **「不要讓 LLM 代寫 skill 本身」**（非「內容不能含 AI 生成的東西」）：影片原話（[03:41]）是擔心 LLM 代寫會得到 "generic mush"，這跟「專業知識要來自實際經驗」是同一件事的兩種說法，補充時應貼近原意，避免過度簡化成「AI 生成的都不行」。
2. **「5000 tokens」建議**：既有文章只有 500 行限制，無 token 數建議。這是影片給的具體換算，補充時務必寫「tokens」不要寫「字」。
3. **gotchas 段落**：影片明確指出這是 skill body 裡最高價值的部分，建議獨立成一小段強調。
4. **references/ 子目錄按需載入**：這是 progressive disclosure 在影片中的實際用法，跟 `agent-skills.md` 既有的「三層漸進式揭露」是平行但不同的概念，補充時要分開寫，不要合併成同一件事。

**建議動作**：
- 在 `skill-authoring-best-practices.md` 的「簡潔是關鍵」段落中，補充一句：「SKILL 的專業知識內容應來自實際操作經驗與領域知識，避免直接讓 LLM 代寫 skill 本身——容易得到缺乏真實邊界案例與踩雷經驗的空泛內容。」
- 在「SKILL.md < 500 行」旁邊補充「（約 5000 tokens 以內）」作為另一個衡量維度。
- 新增「gotchas 是最高價值段落」的提醒，並視情況引用 Simon Willison 的話。
- 引用影片來源。

---

### Topic 3：Script（腳本）部分

#### 3a：確定性東西應該寫成腳本，不要讓 AI 臨場猜

| 既有文章 | 已涵蓋？ | 內容摘要 |
|---------|---------|---------|
| `skill-authoring-best-practices.md` | ✅ **已涵蓋** | 「可執行腳本的 Skills」章節：「比生成的程式碼更可靠」、「節省 token」、「確保跨使用一致性」 |
| `agent-skills.md` | ✅ **已涵蓋** | 「腳本透過 bash 執行，只有輸出進入 context（腳本程式碼本身永不載入）」 |
| `devops-skill-presentation.md` | ✅ **已涵蓋** | 「Write-back 安全邊界」四步安全門 |

**判斷**：此子topic 在既有文章中已有涵蓋。可以考慮補充影片中的具體案例（例如 IBM 的 business rule 腳本實踐）。

#### 3b：使用任何 SKILL 前都要先掃描，避免夾帶惡意軟體或漏洞

| 既有文章 | 已涵蓋？ | 內容摘要 |
|---------|---------|---------|
| `skill-authoring-best-practices.md` | ❌ **未涵蓋** | 檢查清單無「安全掃描」項目 |
| `agent-skills-enterprise-governance.md` | ⚠️ **間接相關** | 有安全審查流程（5 級風險評估），但那是治理層的組織審查，非「使用前個人掃描」 |
| `agent-skills.md` | ❌ **未涵蓋** | 限制章節無「使用前掃描」建議 |

**判斷**：**全新重點**。既有文章的安全討論集中在「組織治理層」（企業審查流程），但沒有從「個人使用者」角度出發的「使用任何 SKILL 前先掃描腳本」建議。

> ⚠️ 依「影片驗證結果」：影片中 3b 跟 3c 其實是**同一項** best practice（"vet a skill before you run it"，[10:59]），不是兩個獨立重點。且影片引用具體審計數據：近 4,000 個公開 skills 中 **35% 有安全漏洞、13% 有 prompt injection / malware 等嚴重問題**（[11:36]）。補充時建議把 3b/3c 合併為一段，並帶入這組數據增加說服力。

**建議動作**：在 `skill-authoring-best-practices.md` 或 `agent-skills.md` 中新增一段「安全使用守則」，包含：
- 使用第三方 SKILL 前，先掃描其 `scripts/` 目錄下的所有檔案
- 檢查是否有不必要的網路存取、檔案系統存取、或可疑的系統調用
- 引用審計數據（35% 有漏洞、13% 嚴重問題）說明「像對待任何第三方依賴一樣檢查」的必要性
- 這跟企業治理的「事前審查」不同，是「每次使用前」的個人習慣

#### 3c：使用前必須理解 SKILL 裡 script 的整體邏輯跟資源存取權限

| 既有文章 | 已涵蓋？ | 內容摘要 |
|---------|---------|---------|
| `skill-authoring-best-practices.md` | ❌ **未涵蓋** | 無「使用前理解腳本邏輯」的建議 |
| `agent-skills.md` | ⚠️ **間接相關** | 提到腳本「只有輸出進入 context」，但未建議使用者應先理解腳本在做什麼 |
| `agent-skills-enterprise-governance.md` | ⚠️ **間接相關** | 有「8 步審查清單」，但那是治理流程，非使用者日常操作 |

**判斷**：**全新重點**。既有文章假設 SKILL 是可信的（來自官方或已審查），但沒有建議「使用者在執行前應先理解腳本的邏輯與權限」。

**建議動作**：與 3b 合併為「安全使用守則」段落，強調：
- 理解腳本在做什麼（讀一次 scripts/ 下的檔案）
- 理解腳本的資源存取權限（檔案系統、網路、環境變數等）
- 這是一種「知情同意」——你讓 AI 跑一個腳本前，至少要知道它大概在幹嘛

---

## 總結：值得新增的差異清單

| # | 新增重點 | 既有文章狀態 | 建議動作 |
|---|---------|-------------|---------|
| 1 | 觸發精準度收攏 + 「描述寧可稍微誇大」 | 散落多處，無獨立段落 | 在 `skill-authoring-best-practices.md` 加強調段落 |
| 2 | 「避免讓 LLM 代寫 skill 本身」（原「不能是 AI 生成的」，已修正語意） | 未明確聲明 | 在「簡潔是關鍵」段落補充 |
| 3 | **5000 tokens** 建議（原「5000 字」，已修正單位） | 僅有 500 行 | 在結構規範補充 token 數維度 |
| 4 | 使用前掃描 + 理解腳本邏輯（原 3b/3c，已合併為影片實際的單一項目） | 未涵蓋 | 新增「安全使用守則」段落，含 35%/13% 審計數據 |
| 5 | gotchas 段落是 skill body 最高價值部分 | 未涵蓋 | 新增獨立小段強調 |

> 第 1-4 項為原心得摘要內容（經驗證修正），第 5 項為驗證過程中新發現、原心得未提及的重點。

---

## 併入建議

### 方案 A：擴充既有文章（推薦，Cheer 已標記 👍）

在 `wiki/concepts/skill-authoring-best-practices.md` 中新增以下段落：

1. **「觸發精準度」強調段**（在「描述」相關章節中，含「描述寧可稍微誇大」）
2. **「避免讓 LLM 代寫 skill 本身」補充**（在「簡潔是關鍵」段落末尾，原「不能是 AI 生成的」已修正語意）
3. **「5000 tokens」補充**（在「SKILL.md < 500 行」旁邊，原「5000 字」已修正單位）
4. **「安全使用守則」新章節**（在「檢查清單」之前或之後，含 35%/13% 審計數據，3b/3c 合併為一段）
5. **「gotchas 段落」提醒**（新增小段，強調是 skill body 裡最高價值的部分）

### 方案 B：建立新文章

如果影片內容足夠豐富，可建立 `wiki/sources/2026-09-01-ibm-skill-best-practices.md` 作為來源筆記，再從中提取概念更新到既有文章。

### 方案 C：兩者並行

先建來源筆記（方案 B），再從中提取精華更新既有文章（方案 A）。

---

## 待確認事項

1. ~~「做花園裡面專題宗旨的 SKILL」是哪篇文章？~~ **已回答（Cheer，2026-08-22）**：[Agent Skills](https://app.notion.com/p/Agent-Skills-3bc5979e3a8c8121924ef99b09671383?source=copy_link)（Notion）
2. ~~「漸進式解構」出現在哪篇文章？~~ **已驗證，原猜測有誤（2026-09-01）**：影片裡沒有「漸進式解構」這個詞，只提過一次 "progressive disclosure"（[07:46]，指超過 500 行就拆到 `references/` 子目錄按需載入），跟 `agent-skills.md` 的「三層漸進式揭露」是不同概念，兩者不能等同或互引
3. ~~是否要播放影片確認具體內容？~~ **已完成（Pi，2026-09-01）**：字幕逐句比對結果見上方「影片驗證結果」，發現「5000 字」應為「5000 tokens」的單位錯誤，以及數個語意簡化/遺漏
4. ~~選擇哪個併入方案？~~ **已確認（Cheer，2026-08-22）**：**方案 A**（擴充既有文章），已套用「影片驗證結果」的修正
5. ~~是否要把新發現的重點一併補進差異清單？~~ **已確認（Cheer，2026-08-22）**：要。gotchas 段落、35%/13% 安全審計數據、Simon Willison 引言、「描述稍微 oversell」已補進「總結：值得新增的差異清單」（見上方第 1、5 項）與各 Topic 小節

**四項待確認事項全部回答完畢，草稿進入下一階段：開 Claude+Gemini 圓桌會議定稿 → 寫入 Notion 花園 → 雙模型覆核。**

**圓桌會議已完成（2026-08-22）**：Claude + Gemini 3 輪討論，5 項段落文字、插入位置、引用格式全數定稿，無未解決分歧。完整段落草案見 [[.pi/round-table/20260822-231523/synthesis]]（已逐一核對逐字稿時間戳，無捏造）。下一步：把 synthesis.md 的 5 段草案實際寫入 `wiki/concepts/skill-authoring-best-practices.md`。

---

## 相關頁面

- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]] — 最主要的併入目標
- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 次要併入目標
- [[wiki/concepts/skill-design-methodology|Skill 設計方法論]] — 設計層參考
- [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支 SKILL？· DevOps Board 系列]] — 同場景簡報
- [[raw/youtube/2026-09-01-ibm-skill-best-practices|IBM Skill 最佳實踐字幕逐字稿]] — 本次驗證用的原始逐字稿
