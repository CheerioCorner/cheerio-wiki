---
title: "ConardLi/garden-skills — GitHub repo"
description: "ConardLi 維護的 5 個子 skill 集合，只有 web-design-engineer 屬於前端視覺設計範疇（協作式：宣告設計系統後等待使用者確認才動工），其餘 4 個（beautiful-article、web-video-presentation、kb-retriever、gpt-image-2）屬內容/簡報/檢索/圖片生成，與前端設計主題較無直接關係。"
url: https://github.com/ConardLi/garden-skills
source_domain: github.com
author:
  - ConardLi
published:
clipped: 2026-08-26
type: raw-web
source_kind: web
immutable: "true"
tags:
  - raw
  - web-clip
  - ai-frontend
  - agent-skill
---
# ConardLi/garden-skills

> Source: [github.com/ConardLi/garden-skills](https://github.com/ConardLi/garden-skills)
> Clipped: 2026-08-26（AI 結構化研究報告，README 與 5 份 SKILL.md 皆從 WebFetch/raw.githubusercontent.com 實際 fetch）
> ⚠️ 提醒：此 repo 名稱 `garden-skills` 與本知識庫既有的 Notion「知識花園」（[[wiki/entities/knowledge-garden|knowledge-garden]]）純屬同名巧合，內容完全無關，不可混淆。

## 摘要

ConardLi 維護的 Claude Code / Cursor / Codex 等 AI coding agent 精選 skill 集合。MIT license，約 10.7k stars，142 commits——真實、持續更新中的 repo。

## 結構：5 個子 skill（`skills/<name>/SKILL.md`）

| Skill | 用途 | Trigger（逐字引用） |
|---|---|---|
| `web-design-engineer` | 建置/重新設計精緻的瀏覽器渲染視覺產物（頁面、儀表板、原型、簡報、動畫、資料視覺化） | "Use for visual front-end creation, design-system exploration, design critique, or explicit browser acceptance / QA of a web artifact. Not for back-end, CLI, non-visual coding..." |
| `beautiful-article` | 把 URL/PDF/DOCX/Markdown/截圖轉成單檔可離線分享的 HTML 文章（非 app） | "把 URL/PDF/DOCX/文章做成网页文章... 'render this as a beautiful web article'... 只生成文章，不生成后台、表单、dashboard、产品原型或通用 Web App。" |
| `web-video-presentation` | 把文章/口播稿轉成點擊驅動的 16:9「像影片」網頁簡報，可選 TTS 合成 | "用网页做视频（动态 PPT 但不像 PPT）、把口播稿/文章变成可交互的解说、为 B 站/YouTube 录屏教程" |
| `kb-retriever` | 對本機知識庫目錄（md/PDF/xlsx）做分層索引檢索/QA，漸進式 grep+Read，PDF/Excel 讀取前強制先讀 reference | — |
| `gpt-image-2` | GPT-Image-2 prompt 工程 + 生成/編輯，三種模式（本機生成/委派給 host 圖片工具/純 prompt 顧問），80+ 模板橫跨 18 類別 | — |

## 核心規則（僅 `web-design-engineer` 屬前端視覺設計範疇，逐字/接近逐字引用）

- 「Avoid AI clichés (purple→pink gradients, emoji-as-icons, left-border accent cards, Inter as display font)... 'average of training data'—all brands averaged into none.」
- 「Bold type contrast (4–6× ratio is normal).」
- 選 token 前先問四個定位問題：敘事角色 / 觀看距離 / 視覺溫度 / 容量檢查。
- **宣告完整設計系統（色彩、字型、間距、圓角、陰影、動效）後等待使用者確認才動工**——這是它與其他自主生成型 skill（taste-skill、hallmark 等）最大的差異：協作式而非全自動。
- 出貨前檢查清單：禁止 `scrollIntoView`、要求 `text-wrap: pretty`、只能用已宣告的設計系統色彩、涵蓋所有互動狀態、禁止填充/捏造內容。
- 動效優先序：CSS transition 優先（80% 情境）→ React state/`requestAnimationFrame` → 自訂 timeline；除非使用者要求，否則避免 Framer Motion/GSAP。

## 定位（供比較用）

僅 `web-design-engineer` 與本次比較主題相關，且因「先宣告後等確認」的協作模式而與其他自主型 anti-slop skill 形成互補（適合結對開發 session，而非批次自動產出）。其餘 4 個 skill 屬不同領域，記錄於此僅為完整性，非前端設計比較的核心對象。

## 驗證備註

repo 真實存在、公開；所有引用內容皆直接 fetch 取得，未憑空杜撰。
