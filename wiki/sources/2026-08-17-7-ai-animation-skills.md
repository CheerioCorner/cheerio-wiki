---
title: "7 個用 AI 做動畫的 Skills（Debug Tuboshu）"
type: source
created: 2026-08-17
updated: 2026-08-17
sources: 1
tags: [ai-video, ai-animation, motion-design, text-to-video, code-based-animation]
topics: [ai-development-tools]
provenance_raw: "raw/youtube/2026-08-17-7-ai-animation-skills.md"
provenance_url: "https://www.youtube.com/shorts/8k4WhKuA3OA"
---

# 7 個用 AI 做動畫的 Skills（Debug Tuboshu）

> 來源：[YouTube Shorts — 7 個用 AI 做動畫的 Skills](https://www.youtube.com/shorts/8k4WhKuA3OA)
> 頻道：Debug Tuboshu | 上傳日期：2026-08-16 | 觀看數：1,891 | 時長：53 秒
> 字幕來源：Whisper base model（auto-generated）
> 查證日期：2026-08-17 | 查證方式：GitHub API 搜尋 + HTTP URL 驗證

## 影片摘要

53 秒短片快速列出 7 種可用於 AI 動畫／影片生成的工具或手法，涵蓋文字轉影片、網頁動效、Logo 動畫、程式化動畫、電影運鏡、故事動畫與手感動效。

## 查證結果：7 個工具逐項核實

### 1. HyperFriends（黑卷開發） → ✅ 核實：hyperframes（HeyGen）

- **逐字稿原文**：「你只要寫網頁,它就把你變成影片」
- **實際工具名**：hyperframes
- **開發者**：HeyGen（https://github.com/heygen-com/hyperframes）
- **GitHub Stars**：41,244
- **核心用途**：撰寫 HTML 網頁，自動渲染成影片，專為 AI Agent 設計
- **使用情境**：
  - 產品展示影片：把產品頁面 HTML 直接轉成動態展示短片
  - 廣告素材批量製作：用程式化方式生成多版本 A/B 測試廣告
  - 教學影片：將文件/教程網頁轉為旁白配音的教學影片

### 2. G7 → ✅ 核實：GSAP（GreenSock Animation Platform）

- **逐字稿原文**：「網頁上那種會符、會彈有質感的動效,就是靠它交AI寫出來的」
- **實際工具名**：GSAP (GreenSock Animation Platform)
- **開發者**：GreenSock（https://github.com/greensock/GSAP / https://gsap.com）
- **GitHub Stars**：27,769
- **核心用途**：JavaScript 網頁動畫 Library，高效能、跨瀏覽器的專業級動效引擎
- **使用情境**：
  - 網頁互動動效：滾動觸發、懸浮彈跳、頁面轉場等高質感動畫
  - 品牌官網：打造有質感的品牌動態視覺體驗
  - 數據視覺化動畫：圖表、數據 Dashboard 的流暢動態呈現

### 3. Pixel 2 模式 → ✅ 核實：pixel2motion

- **逐字稿原文**：「你丟一張Logo 圖給它,它就會自動變成一段會動的開場」
- **實際工具名**：pixel2motion
- **開發者**：nolangz（https://github.com/nolangz/pixel2motion）
- **GitHub Stars**：1,962
- **核心用途**：AI Logo 動畫 Skill，將靜態 Logo 圖片轉為流暢的 SVG 動畫、HTML 動態展示、GIF/影片
- **使用情境**：
  - 品牌開場動畫：Logo 動態揭幕影片，用於影片片頭或簡報開場
  - 社群媒體素材：將品牌 Logo 轉為動態 GIF 用於貼文
  - 簡報動態元素：讓簡報中的 Logo 有呼吸感的微動畫

### 4. Remotion → ✅ 核實：Remotion

- **逐字稿原文**：「它是用寫程式的方式做影片,最適合做數字、圖表這種動畫」
- **實際工具名**：Remotion
- **開發者**：Remotion Dev（https://github.com/remotion-dev/remotion / https://remotion.dev）
- **GitHub Stars**：56,520
- **核心用途**：用 React 程式碼製作影片，資料驅動的程式化影片生成
- **使用情境**：
  - 數據年報影片：將年度數據自動轉為動態圖表動畫
  - 個人化影片：根據用戶資料批量生成個人化問候/推薦影片
  - 產品演示：用程式碼精準控制時間軸，製作高品質產品功能展示

### 5. Video Shrugcraft → ✅ 核實：video-shotcraft

- **逐字稿原文**：「它準備了一百多種現成的電影鏡頭,你把網站畫面丟進去」
- **實際工具名**：video-shotcraft
- **開發者**：Vincentwei1021（https://github.com/Vincentwei1021/video-shotcraft）
- **GitHub Stars**：5,245
- **核心用途**：AI 影片 Skill，內建 152 種電影運鏡模板，把網站畫面轉為有電影感的產品廣告片
- **使用情境**：
  - 產品宣傳片：將 SaaS 產品介面轉為電影感宣傳短片
  - App Store 展示影片：用專業運鏡展示 App 操作流程
  - 投資人演示：把 demo 網站轉為高質感的 pitch 影片

### 6. Story2 Hand Zone → ✅ 核實：story-to-handdrawn-video

- **逐字稿原文**：「打一段中文故事,它變成首會日記動畫」
- **實際工具名**：story-to-handdrawn-video
- **開發者**：gnipbao（https://github.com/gnipbao/story-to-handdrawn-video）
- **GitHub Stars**：1,399
- **核心用途**：Agent Skill，將中文故事文字或圖片序列轉為手繪日記風格動畫（無聲 MP4）
- **使用情境**：
  - 兒童故事繪本：把童話故事轉為手繪風格動畫影片
  - 品牌故事敘事：用溫暖手繪風呈現品牌創辦故事
  - 教育內容：將歷史故事/成語故事轉為生動的動畫教材

### 7. 手感動效 → ⚠️ 部分核實：breathing-dots-tutorial（教學專案）

- **逐字稿原文**：「它交的是手感,畫面上這片會呼吸,有節奏的點證」
- **找到的相關專案**：breathing-dots-tutorial（https://github.com/mattrossman/breathing-dots-tutorial）
- **GitHub Stars**：130
- **說明**：這是一個教學專案（非獨立工具），展示如何用 React Three Fiber 製作呼吸感點陣動畫。影片中提到的「手感」指的是這類有機質感的視覺效果，通常由 GSAP 或 Three.js 等底層動畫引擎實現。
- **使用情境**：
  - 品牌視覺：有呼吸感的點陣背景，傳達有機、自然的品牌調性
  - 載入動畫：替代傳統 loading spinner，用節奏感動畫降低等待焦慮
  - 藝術展覽：互動式數位藝術装置的視覺元素

## 來源連結

| # | 逐字稿名稱 | 實際工具 | GitHub URL |
|---|-----------|---------|-----------|
| 1 | HyperFriends | hyperframes | https://github.com/heygen-com/hyperframes |
| 2 | G7 | GSAP | https://github.com/greensock/GSAP |
| 3 | Pixel 2 模式 | pixel2motion | https://github.com/nolangz/pixel2motion |
| 4 | Remotion | Remotion | https://github.com/remotion-dev/remotion |
| 5 | Video Shrugcraft | video-shotcraft | https://github.com/Vincentwei1021/video-shotcraft |
| 6 | Story2 Hand Zone | story-to-handdrawn-video | https://github.com/gnipbao/story-to-handdrawn-video |
| 7 | 手感動效 | breathing-dots-tutorial | https://github.com/mattrossman/breathing-dots-tutorial |

## 相關頁面

- [[wiki/concepts/ai-animation-generation-approaches|AI 動畫生成手法分類]]
- [[wiki/entities/hyperframes|hyperframes]]
- [[wiki/entities/gsap|GSAP]]
- [[wiki/entities/pixel2motion|pixel2motion]]
- [[wiki/entities/remotion|Remotion]]
- [[wiki/entities/video-shotcraft|video-shotcraft]]
- [[wiki/entities/story-to-handdrawn-video|story-to-handdrawn-video]]
