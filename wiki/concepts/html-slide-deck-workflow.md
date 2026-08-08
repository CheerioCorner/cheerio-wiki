---
title: HTML Slide Deck 製作工作流
type: concept
created: 2026-08-08
updated: 2026-08-08
sources: 3
tags: [presentation, slides, html, design-system, skill]
topics: [skill]
canonical: concepts/html-slide-deck-workflow
provenance_raw: "raw/web/2026-08-08-plannotator-obsidian-presentation-proposal.md"
---

# HTML Slide Deck 製作工作流

> 用 AI agent 製作高品質 HTML 簡報的完整工作流，整合 guizang-ppt-skill 和 huashu-design 兩套設計系統。

## 一句話

從內容企劃到高品質 HTML slide deck，整合兩套設計系統 + 雙 AI 審閱的工作流。

## 使用的 Skills

| Skill | 用途 | 關鍵檔案 |
|-------|------|----------|
| **guizang-ppt-skill** | 設計系統：主題色、排版、組件、佈局 | `assets/template.html`、`references/themes.md`、`references/layouts.md`、`references/components.md` |
| **huashu-design** | 渲染引擎 + 反 AI slop + 工作流 | `assets/deck_stage.js`、`assets/deck_index.html`、`references/slide-decks.md` |

## 使用的 Tools

| Tool | 用途 |
|------|------|
| **agy (Gemini)** | 內容討論、企劃審閱 |
| **gh copilot** | 企劃審閱（第二意見） |
| **npx skills** | 安裝 skill |
| **web_search** | 生態系研究 |
| **deck_stage.js** | 幻燈片渲染（1920×1080、auto-scale、鍵盤導航） |
| **deck_index.html** | 多檔聚合（概覽網格 + 演示模式） |

## 設計系統（guizang 靛藍瓷主題）

```css
/* 主題色 */
--ink: #0a1f3d;      /* 深靛藍 */
--paper: #f1f3f5;    /* 瓷白 */

/* 字體分工 */
--serif-en: "Playfair Display";  /* 英文標題、數字 */
--serif-zh: "Noto Serif SC";     /* 中文標題、金句 */
--sans-zh: "Noto Sans SC";       /* 正文 */
--mono: "IBM Plex Mono";         /* 標籤、meta */
```

### 組件庫

- **Chrome/Foot**：頂底元資訊條（monospace、uppercase、opacity .5）
- **Kicker**：小節提示（monospace、12px、uppercase）
- **Card**：白底卡片 + 細邊框 + 左側色條
- **Callout**：引用框（border-left + 半透明背景）
- **Flow**：流程圖（flex + arrow + box）
- **Stat**：數字矩陣（serif 大數字 + sans 標籤）
- **Ghost**：巨型背景字（opacity .03、serif 900）
- **Tag**：標籤（mono、border、uppercase）

## 工作流程

```
1. 內容企劃
   ├── 讀取原始資料（raw/）
   ├── 規劃 slide 結構（14 頁為例）
   ├── Gemini 討論 → 修正 → Copilot 審閱
   └── 存入知識庫（source note + index + log）

2. 設計系統設定
   ├── 選擇主題（靛藍瓷 / 墨水經典 / 森林墨）
   ├── 設定字體（serif + sans + mono 三分工）
   └── 確立 grammar（先做 2 個 showcase 頁）

3. 批量製作
   ├── 每頁獨立 HTML → slides/ 目錄
   ├── 使用 deck_stage.js web component
   ├── 套用 guizang 組件（card/callout/flow/ghost）
   └── 遵守 huashu 反 AI slop 規則

4. 聚合與交付
   ├── deck_index.html 聚合 14 頁
   ├── 概覽網格 + 演示模式
   └── 瀏覽器直接打開即可演講
```

## 反 AI Slop 規則（from huashu-design）

| ❌ 不做 | ✅ 做 |
|---------|-------|
| 紫漸變背景 | 單色或微漸變 |
| emoji 圖標裝飾 | 文字標籤或無裝飾 |
| 圓角 + 左 border accent | 細邊框 + 左色條 |
| SVG 畫人臉 | 用文字或 placeholder |
| Inter 做 display | 用 serif 做標題 |
| 每個標題配 icon | 留白 + 排版 |

## 目錄結構

```
project-name/
├── index.html          ← 聚合器（from deck_index.html）
├── deck_stage.js       ← 渲染引擎（from huashu-design）
└── slides/
    ├── 01-cover.html
    ├── 02-problem.html
    ├── ...
    └── 14-references.html
```

## 與其他技能的關係

- 本工作流使用 guizang-ppt-skill 的設計系統，但不使用其 WebGL 背景
- 本工作流使用 huashu-design 的 deck_stage.js，但不使用其動畫系統
- 兩者互補：guizang 提供視覺品質，huashu 提供渲染架構

## 來源

- [[raw/web/2026-08-08-plannotator-obsidian-presentation-proposal|企劃文件]]
- [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill]]
- [[wiki/entities/huashu-design|huashu-design]]

## 相關頁面

- [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill]] — 設計系統來源
- [[wiki/entities/huashu-design|huashu-design]] — 渲染引擎來源
- [[wiki/sources/2026-08-08-plannotator-obsidian-presentation|簡報企劃 source note]]
