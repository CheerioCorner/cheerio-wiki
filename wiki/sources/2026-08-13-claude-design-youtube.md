---
title: "How To Use Claude Design To Build Beautiful Sites"
type: source
created: 2026-08-13
updated: 2026-08-13
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

- [[wiki/entities/claude-design|Claude Design]] — 工具實體
- [[wiki/concepts/design-md-format|design.md Format]] — 格式規範概念
- [[wiki/entities/claude-code|Claude Code]] — AI Coding Agent
