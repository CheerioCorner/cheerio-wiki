---
title: "IBM YouTube 影片 Skill 五項最佳實踐 — 補充草稿"
type: draft
created: 2026-09-01
updated: 2026-09-01
tags: [skill, best-practices, youtube, draft, supplement]
topics: [skill-system]
status: draft — 待人類覆核
provenance:
  - kind: youtube
    url: "https://youtu.be/qYNs80FKIVc?si=ONtRQtqXU73YXi8K"
    description: "IBM Martin Keen & Bri Kopecki 講解開發 AI Agent Skills 的五項最佳實踐"
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
- 補充影片中可能提到的更具體案例（例如 IBM 的觸發詞設計實踐）

**建議動作**：在 `skill-authoring-best-practices.md` 的「描述」章節中，加一小段「觸發精準度」的強調，引用影片來源。

---

### Topic 2：專業知識部分（SKILL 內容要來自實際操作經驗，不能是 AI 生成的；建議 500 行/5000 字以內）

| 既有文章 | 已涵蓋？ | 內容摘要 |
|---------|---------|---------|
| `skill-authoring-best-practices.md` | ✅ **500 行已涵蓋** | 「SKILL.md < 500 行」在結構規範與檢查清單中明確列出 |
| `skill-authoring-best-practices.md` | ⚠️ **5000 字未提及** | 僅有 500 行限制，無字數建議 |
| `agent-skills.md` | ⚠️ **部分涵蓋** | 提到「三層漸進式揭露」確保 context 效率，但未明確說「不能是 AI 生成的」 |
| `skill-authoring-best-practices.md` | ⚠️ **部分涵蓋** | 提到「簡潔是關鍵：只添加 Claude 尚未擁有的上下文」，暗示專業知識應來自人類 |

**判斷**：此 topic 有兩個**值得新增**的重點：
1. **「不能是 AI 生成的」**：既有文章強調「簡潔」和「只添加 AI 不知道的」，但沒有明確說「SKILL 的專業知識內容必須來自實際操作經驗，不能是 AI 自己編的」。這是更強烈的聲明。
2. **「5000 字」建議**：既有文章只有 500 行限制，無字數建議。5000 字是一個實用的補充指標（一行約 10 字，500 行 ≈ 5000 字，兩者一致但角度不同）。

**建議動作**：
- 在 `skill-authoring-best-practices.md` 的「簡潔是關鍵」段落中，補充一句：「SKILL 的專業知識內容應來自實際操作經驗與領域知識，而非 AI 生成——AI 生成的內容缺乏真實世界的邊界案例與踩雷經驗。」
- 在「SKILL.md < 500 行」旁邊補充「（約 5000 字以內）」作為另一個衡量維度。
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

**建議動作**：在 `skill-authoring-best-practices.md` 或 `agent-skills.md` 中新增一段「安全使用守則」，包含：
- 使用第三方 SKILL 前，先掃描其 `scripts/` 目錄下的所有檔案
- 檢查是否有不必要的網路存取、檔案系統存取、或可疑的系統調用
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
| 1 | 觸發精準度收攏 | 散落多處，無獨立段落 | 在 `skill-authoring-best-practices.md` 加強調段落 |
| 2 | 「不能是 AI 生成的」 | 未明確聲明 | 在「簡潔是關鍵」段落補充 |
| 3 | 5000 字建議 | 僅有 500 行 | 在結構規範補充字數維度 |
| 4 | 使用前掃描腳本（防惡意） | 未涵蓋 | 新增「安全使用守則」段落 |
| 5 | 使用前理解腳本邏輯與權限 | 未涵蓋 | 與 #4 合併 |

---

## 併入建議

### 方案 A：擴充既有文章（推薦）

在 `wiki/concepts/skill-authoring-best-practices.md` 中新增以下段落：

1. **「觸發精準度」強調段**（在「描述」相關章節中）
2. **「不能是 AI 生成的」補充**（在「簡潔是關鍵」段落末尾）
3. **「5000 字」字數建議**（在「SKILL.md < 500 行」旁邊）
4. **「安全使用守則」新章節**（在「檢查清單」之前或之後）

### 方案 B：建立新文章

如果影片內容足夠豐富，可建立 `wiki/sources/2026-09-01-ibm-skill-best-practices.md` 作為來源筆記，再從中提取概念更新到既有文章。

### 方案 C：兩者並行

先建來源筆記（方案 B），再從中提取精華更新既有文章（方案 A）。

---

## 待確認事項

1. **「做花園裡面專題宗旨的 SKILL」是哪篇文章？** 目前找不到，需人類確認路徑或標題
2. **「漸進式解構」出現在哪篇文章？** 搜尋未果，可能是指 `agent-skills.md` 的「三層漸進式揭露（Progressive Disclosure）」，但兩者語意不同
3. **是否要播放影片確認具體內容？** 本草稿基於使用者心得描述，未實際播放影片驗證
4. **選擇哪個併入方案？** A / B / C，需人類決定

---

## 相關頁面

- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]] — 最主要的併入目標
- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 次要併入目標
- [[wiki/concepts/skill-design-methodology|Skill 設計方法論]] — 設計層參考
- [[wiki/sources/2026-08-17-devops-skill-presentation|如何寫好一支 SKILL？· DevOps Board 系列]] — 同場景簡報
