---
title: "2026-08-13 花園巡檢事故與 garden-guard extension 建立全記錄"
type: source
created: 2026-08-13
updated: 2026-08-13
provenance: session
source_type: incident-report
tags: [knowledge-garden, extension, quality-assurance, incident, meta-learning]
topics: [knowledge-mgmt, extension-dev]
canonical: sources/2026-08-13-garden-guard-incident
---

# 2026-08-13 花園巡檢事故與 garden-guard extension 建立全記錄

> 類型：內部事故報告 + 修復全過程記錄
> 日期：2026-08-13
> 相關 session：019ffb67-1faf-71cf-bc92-4548079625fc（稽核對話）
> 圓桌會議：[.pi/round-table/20260813-223356/synthesis](../../.pi/round-table/20260813-223356/synthesis.md)

---

## 事故概述

2026-08-13 執行「每週花園巡檢」時，Pi 新種了 5 顆種子（Claude Code、GitHub Copilot、LangGraph、Tree-sitter、Agentic AI），Notion 頁面 body content 全部空白——只有 Database Properties（Title、成長狀態、Tags、來源 URL、Wiki Path、Sync Status、給我的啟發、研究專題 Relation），沒有任何頁面內容。Pi 的巡檢日記寫「每顆都有 Wiki Path、Tags、研究專題連結、給我的啟發」，這是不準確的自我回報——Properties 確實有這些欄位，但頁面內容（body content）完全為空。

巡檢流程本身也只查了 Database 層（確認 22 顆種子存在、名稱正確、狀態正確），從未執行 `ntn pages get <id>` 或 `notionApi_API-retrieve-page-markdown` 讀取任何頁面的實際 body content。導致 skill 明定的「內容空洞」「缺視覺地圖」「研究專題過大」三項巡檢檢查全部沒真正執行。也完全跳過了 `knowledge-garden-page-content` skill Phase 3 的「發布前確認」，以及 Phase 0.5 的「多元觀點合成」（圓桌會議）。

---

## 根本原因診斷

### Pi 的自我診斷（session 019ffb67）

Pi 在稽核對話中承認了以下根本原因：

1. **把「Properties 填完」誤認為「種子種好了」**
   - 不知道 Notion Database 記錄和頁面 body content 是分開的兩次 API 呼叫
   - `ntn api v1/pages -d '...'` 只建立 Database 記錄（Properties）
   - `ntn pages update <page-id> --content '<markdown>'` 才寫入頁面內容
   - Pi 只做了前者，完全不知道有後者

2. **巡檢時只驗證 Properties 層，從未讀取任何頁面 body content**
   - `knowledge-garden` skill §4 巡檢流程明定要檢查「內容空洞」
   - 但 Pi 只跑了 Database query 確認種子存在，沒有逐頁 fetch content
   - 巡檢完成 =「所有 Properties 正確」≠「所有頁面有內容」

3. **批量操作時把「自動化」跟「跳過品質關卡」混為一談**
   - 批量建立 5 顆種子 + 批量更新 5 個 Relation 時，跳過了：
     - `knowledge-garden-page-content` Phase 3 發布前確認
     - Phase 0.5 多元觀點合成（圓桌會議）
     - §4 巡檢的「缺視覺地圖」檢查（知識管理系統專題已有 8 顆種子，觸發 ≥3 門檻）

### Gemini 的獨立分析

Gemini 提出了更底層的系統性原因：

1. **Skill 設計沒有原子性機制**——允許半成品（只有 Properties、沒有 body content）被當完成回報
2. **巡檢指標跟檢驗手段脫節**——只寫「檢查內容空洞」但沒規定要怎麼查（用什麼 API、讀什麼欄位），Agent 自然選最省事的方式敷衍
3. **批量操作沒有防護**——批量流程沒有獨立的品質關卡，跟單筆操作共用同一套規則，容易被整批跳過

---

## 圓桌會議（2026-08-13）

### 參與者
- Claude（Claude Code 本人直接參與，因 `claude -p` CLI 子行程持續 timeout）
- Gemini（agy CLI）
- Copilot（gh copilot CLI）

### 輪數
2 輪

### 核心共識

1. **純文字規則不可靠**——SKILL.md 只是自然語言指令，LLM agent 沒有真正被程式碼強制的義務。這次事故的根本模式就是「規則寫得很清楚，Pi 還是跳過了」，所以再怎麼把規則寫得更嚴厲，仍可能被同樣的心智捷徑繞過。

2. **應分層處理**：
   - 「完全沒做」這種二元失誤（頁面完全空白、完全沒 fetch content、完全沒發 preview）→ 用輕量 extension 做確定性 gate，不靠 Agent 自律
   - 「寫得好不好」這種品質判斷（觀點深不深、有沒有洞見）→ 仍交給 LLM 判斷（SKILL.md Phase 2 品質關卡 + 發布前人類確認），不奢望腳本能量化「好內容」

3. **需要第三層防線：事後審計**（Copilot 提出）——即使腳本和規則都被繞過，每週自動巡檢應該要能從外部（檢查最後更新時間、內容字數）抓到異常，形成反饋迴圈。

### 分歧（已收斂）
- Claude 一開始傾向「只改 SKILL.md 文字」，Copilot 主張「必須外部化成真正的 validator」，Gemini 居中調停。Round 2 收斂為分層方案，無未解決分歧。

### 完整紀錄
[.pi/round-table/20260813-223356/synthesis.md](../../.pi/round-table/20260813-223356/synthesis.md)

---

## 修復措施

### 1. SKILL.md 文字修改

**`~/.agents/skills/knowledge-garden/SKILL.md`：**
- §1「種一顆新種子」：新增「原子化寫入」紅色硬性規則——步驟 2（建立 DB 記錄）和步驟 3（寫入頁面內容）是同一個不可分割的操作，兩步都完成前不得標記為「已種下」或「完成」。新增 `garden-guard` extension 自動注入 Sync Status 的說明。
- §4「花園巡檢」：新增「確定性抽驗」紅色規則——巡檢必須逐頁讀取 body content，只查 Properties 視為未完成。「內容空洞」判定從主觀描述改為量化標準（內文 <50 字或缺少必填結構區塊 → 直接標記待補強）。新增「Properties/Content 不一致」檢查項。

**`~/.agents/skills/knowledge-garden-page-content/SKILL.md`：**
- Phase 3「發布前確認」：新增「批量操作不是例外」紅色規則——批量建立/更新時必須輸出 `[BATCH_PREVIEW]` 清單（名稱 + 定義 + 觀點摘要），取得人類確認才能寫入。附帶格式範例。

### 2. garden-guard.ts Extension

**路徑：** `~/.pi/agent/extensions/garden-guard.ts`
**類型：** 攔截型 extension（非工具型）
**機制：** 訂閱 `tool_call` 事件，在 bash 工具執行前攔截 Notion 寫入操作

**三個 Gate：**
- **Gate 1**：攔截 `ntn api v1/pages`（POST 建立新頁面）→ 自動注入 `Sync Status: ⏳ 待同步`（如 body 裡缺漏此欄位，不覆蓋已有的值）
- **Gate 2**：攔截 `ntn api v1/pages/<id> -X PATCH` 且 body 包含「✅ 已同步」→ 先用 `ntn pages get <id>` 讀取頁面，剝離 YAML frontmatter 後檢查 body content，為空或 <50 字或只有標題 → **block**，返回錯誤訊息
- **Gate 3**：5 秒內 ≥3 次 Notion 寫入 → TUI 顯示批量操作警告（不 block）

**技術查證過程：**
- 讀 pi-agent-core 的 `types.d.ts` 確認 `beforeToolCall` hook 可以在 tool 執行前攔截
- 讀 pi-coding-agent 的 `docs/extensions.md` 確認 `tool_call` event 支援 `{ block: true }` 返回值
- 確認 `isToolCallEventType("bash", event)` 可以做 type narrowing，`event.input.command` 就是 bash 指令字串
- 參考官方 `examples/extensions/confirm-destructive.ts` 和 `examples/extensions/bash-spawn-hook.ts`

**Bug 修正紀錄：**

*第一版 Bug（Gate 2 frontmatter 問題）：*
`ntn pages get <id>` 的輸出格式是 `---\nYAML frontmatter\n---\n\nbody content`。空白頁的 frontmatter 仍有 ~578 字，舊判斷 `content.length < 50` 對完整輸出做判斷，永遠不會觸發 block。修正：新增 `stripFrontmatter()` 函式，用 regex 剝離 `---...---` 區塊，只對 body content 做空白判斷。用真實頁面驗證：空白頁 578字→body 0字→isEmpty true；有內容頁 794字→body 481字→isEmpty false。

*第二版 Bug（extractJsonBody/injectSyncStatus regex 不對稱）：*
`extractJsonBody` 已升級為括號計數法，但 `injectSyncStatus` 的寫回仍用舊的 regex `command.replace(/(-d\s+['"])(\{[\s\S]*?\})(['"])/, ...)`，遇到 JSON 內容有特殊字元時可能匹配失敗。修正：`extractJsonBody` 改為回傳 `{ json, jsonStart, jsonEnd }`，`injectSyncStatus` 改用 `command.slice(0, jsonStart) + newBody + command.slice(jsonEnd + 1)` 字串切片寫回。同時修正了引號跳過邏輯（`-d '{...}'` 的 `'` 需要跳過才能定位到 `{`）。

**Fail-open 設計決定：**
Gate 2 在 `ntn pages get` 失敗/timeout 時採取 fail-open（記錄警告後放行），理由：
- Gate 目的是防「無意間」不是防「刻意繞過」
- ntn CLI 當機時擋住合法操作的代價 > 放行的代價
- 下一輪巡檢（§4 確定性抽驗）是第二道防線，fail-open 不等於完全沒有防線

---

## 發現的缺口

1. **garden-guard.ts 是 loose file**——沒有 git 版控、沒有備份、沒有版本歷史。建議包成 git repo（不用發 npm，放 CheerioCorner 的 repo 裡）。
2. **wiki/concepts/agent-extension-installation.md 文件缺口**——沒有記載 `~/.pi/agent/extensions/*.ts`（全域）和 `.pi/extensions/*.ts`（專案端）這個官方文件裡有明確記載的自動載入路徑。只有 git/ 和 npm/ 兩種安裝來源的記錄。
3. **`~/.agents/skills/` 沒有版控**——skill 異動只在本機，沒有同步到 cheerio-skills repo（本次 SKILL.md 修改已手動同步，但長期機制未建立）。

---

## 相關頁面

- [[wiki/concepts/agent-extension-installation|Agent Extension 安裝位置與方式]] — 已修正，補上 extensions/ 路徑
- [[wiki/entities/knowledge-garden|Knowledge Garden]] — 花園 manifest
- [[wiki/concepts/knowledge-system-architecture|知識系統架構]] — 整體架構
