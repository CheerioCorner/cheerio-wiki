---
title: "How To Use Claude Design To Build Beautiful Sites"
type: source
created: 2026-08-13
updated: 2026-08-14
sources: 1
tags: [claude-design, claude-code, design-workflow, youtube]
topics: [ai-development-tools]
provenance_raw: "raw/youtube/how-to-use-claude-design-to-build-beautiful-sites.md"
provenance_url: "https://youtu.be/bBlY5YOsKN8"
---

# How To Use Claude Design To Build Beautiful Sites

> AI LABS 教學影片，介紹 Claude Design 的五階段工作流程，從 design.md 到實際部署。

## 影片資訊

- **來源**：YouTube（AI LABS 頻道）
- **時長**：18:33
- **字幕**：英文 auto-generated（pytubefix 抓取）
- **Video ID**：bBlY5YOsKN8

## 五階段工作流摘要

### 階段一：Design.md 建立
- design.md 是 Google 定義的品牌設計文件格式，包含色彩、字體等設計參數
- AI coding agent（如 Claude Code）讀取此檔案來確保所有設計 on-brand
- 用 Claude Code + skill 訪談使用者，自動產生符合 Google 標準的 design.md
- 輔助工具：DesignMD.space（視覺化）、getdesign.md（取得品牌範本）、Coolers（色彩生成）

### 階段二：Design System 建立
- 在 Claude Design 中上傳 design.md 作為 asset
- 建立 Design System：一套風格設定（字體、顏色等），確保所有頁面風格一致
- 若不提供 design.md，模型會 fallback 到通用預設風格（AI slop）

### 階段三：Wireframe 製作
- 選擇 Design System → Wireframe 模式
- 描述螢幕數量與結構 → Claude 產生多個方案變體
- 用 Comment 功能微調（選取元素 → 丟評論 → 批次送出）

### 階段四：Prototype 設計
- 選擇 Wireframe 變體 → 要求轉成完整設計
- 保留 Wireframe 結構 + 加入細節（按鈕顏色、texture）
- Comment 微調 → "Polish" 指令美化

### 階段五：Animations 動畫
- 加入 subtle 動畫（scroll into view、text pop-up）
- 保持簡潔，避免過度 springy 的效果

### 交付：Handoff to Claude Code
- 透過 Claude Design MCP 將設計匯出到 Claude Code
- Claude Code 讀取 metadata → 建構真實應用程式
- Deep Review 確保設計一致性與響應式適配
- 連接 Supabase 後端（MCP 連接）→ 部署

## 重要概念

### Effort Level
控制模型思考深度的參數。建議使用 Opus 5 + medium effort，平衡 token 消耗與品質。

### 5 小時 Token Limit
Claude Design 有 5 小時 token 限制，涵蓋所有 Claude 活動（不只是設計）。Heavy usage 會更快用完。

### Anti-slop References
skill 中包含的參考資料，避免 AI 生成的設計落入通用模式（slop），確保設計有品牌個性。

### Claude Design MCP
讓 Claude Design 和 Claude Code 雙向溝通的機制。設計 metadata 雙向傳輸。

## 方法論原則（可遷移、可實測）

> 這支影片表面是「Claude Design 操作教學」，但底層是一組**與工具無關**的 AI 輔助前端開發原則。以下抽出「為什麼這樣做」，附逐字稿時間碼。詳見 [[wiki/discussions/ai-frontend-handoff-methodology|把前端交給 AI 的有效方法論]]。

1. **不是「一句 prompt 生一個網站」，而是分階段流程** — 「a good design doesn't come from just prompting the tool」[00:00]。把設計拆成可檢查的階段，是整套方法的前提。
2. **Spec-first：先給規格檔，agent 動筆前先讀** — design.md「reads that file before it writes anything… never has to guess so it never goes off brand」[03:18]。給 AI 一份先驗規格，等於消除「猜」與「走鐘」。
3. **反 AI-slop：放任 AI 自建就會落入通用預設** — 「falls back to the generic colors and styles… your site ends up looking like every other AI built site」[03:49]。企業要的品牌差異化，正是靠外掛 design.md + anti-slop references 頂住。
4. **漸進保真：先鎖結構、再補細節、最後動效** — Wireframe→Prototype→Animation。理由是成本：「claw design is slow… if the design isn't what you need, you'd have to redo the whole thing」[09:22]。先在便宜的低保真階段收斂。
5. **錨定式回饋 ＞ 純文字 prompt** — 選中元素丟 comment，「sends the exact details of that element… way better than just prompting and letting Claude figure out which area you mean」[10:12]；且「better to group them and send them all at once」[10:38] 省等待與 token。
6. **廉價人審檢查點：生成前先可視化審查** — design.md 機器可讀但人看不懂，用 designmd.space 之類先看，「every one of those rounds costs you tokens」[07:36]。把昂貴的生成往後推，先用便宜的預覽收斂。
7. **AI→AI 交接要驗證，不可盡信轉換** — MCP 匯出後做 deep review，抓到 RWD 破版：「Visually, everything looked good. But there could still be internal issues」[14:01]。視覺過關 ≠ 內部正確。
8. **Skill 與 MCP 的分工** — skill 只是「text telling the agent what to do」、不能動手；要真正操作外部服務需 MCP 或 CLI[16:19]。方法論上：知識（skill）與行動能力（MCP/CLI）要分開理解。
9. **人保留高風險決策** — 資料庫 schema「we'd already pre-made it ourselves… we didn't want the agent working on the structure」[16:19]。把 schema 這種高風險結構握在人手上，AI 只做機械建構。
10. **整套方法論由 token/成本最佳化驅動** — 幾乎每一步的正當理由都是「省 token、減少重做」。這是設計哲學，不只是限制。

⚠️ **素材性質提醒**：本影片是 AI LABS 的行銷型教學（片尾導流其付費社群 AIABS Pro），屬單一廠商、有立場的 n=1 來源。上述原則多為可驗證宣稱，採用前應實測（見研究種子）。

## 涉及工具

| 工具 | 用途 |
|------|------|
| Claude Design | 設計工具（Wireframe → Prototype → Animations） |
| Claude Code | AI Coding Agent，接收設計並建構應用 |
| Supabase | 後端資料庫，透過 MCP 連接 |
| DesignMD.space | 視覺化 design.md |
| getdesign.md | 取得品牌設計範本 |
| Coolers | 色彩調色盤生成 |
| VS Code | 程式碼編輯器 |

## 來源

- [[raw/youtube/how-to-use-claude-design-to-build-beautiful-sites|原始逐字稿]]

## 相關頁面

- [[wiki/discussions/ai-frontend-handoff-methodology|把前端交給 AI 的有效方法論]] — 由本影片抽出的研究種子（可實測假設）
- [[wiki/entities/claude-design|Claude Design]] — 工具實體
- [[wiki/concepts/design-md-format|design.md Format]] — 格式規範概念
- [[wiki/entities/claude-code|Claude Code]] — AI Coding Agent
- [[wiki/concepts/ai-coding-workflow|AI Coding Workflow]] — 結構化 AI 編碼工作流程（互補）
