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

## 影片摘要

53 秒短片快速列出 7 種可用於 AI 動畫／影片生成的工具或手法，涵蓋文字轉影片、網頁動效、Logo 動畫、程式化動畫、電影運鏡、故事動畫與手感動效。

## 雙模型交叉驗證說明

本頁工具名稱與用途經過以下流程查證：

1. **Pi 初步查證**（2026-08-17）：使用 GitHub API 搜尋 + HTTP URL 驗證，找到 6 個匹配工具 + 1 個教學專案
2. **Gemini 獨立查證**（2026-08-17）：獨立進行 web search，結果與 Pi 高度一致（6 項同意，第 7 項一致判定為「教學範例非獨立工具」）
3. **Claude URL 驗證**：逐一 HTTP 驗證所有 URL，全部回 200

**關鍵修正**：#2 工具從 `greensock/GSAP`（動畫庫主倉）修正為 `greensock/gsap-skills`（官方 AI 技能包，13,682 stars），更貼合逐字稿「教 AI 寫出來的」語境。

**已排除捏造連結**：Gemini 引用的 Codrops 教學文章 `https://tympanus.net/codrops/2021/01/26/breathing-dots-tutorial/` 經 Claude 實測回 404，確認不存在，已排除。

## 查證結果：7 個工具逐項核實

### 1. HyperFriends（黑卷開發） → ✅ 核實：hyperframes（HeyGen）`[00:04]`

- **逐字稿原文**：「你只要寫網頁,它就把你變成影片」`[00:06]`
- **實際工具名**：hyperframes
- **開發者**：HeyGen（https://github.com/heygen-com/hyperframes）
- **GitHub Stars**：41,244
- **核心用途**：撰寫 HTML 網頁，自動渲染成影片，專為 AI Agent 設計
- **使用情境**：
  - 社群行銷短片批次生成：同一個產品頁面 HTML，AI 自動產生多版本 15/30/60 秒短片
  - 個人化年度數據回顧短影音：用戶數據填入 HTML 模板，自動渲染成個人化回顧影片
  - AI Coding Agent 自動化原型影片演示：Agent 開發完功能後，自動把功能頁面渲染成 demo 影片

### 2. G7 → ✅ 核實：gsap-skills（GreenSock AI 技能包）`[00:11]`

- **逐字稿原文**：「網頁上那種會浮、會彈有質感的動效,就是靠它教 AI 寫出來的」`[00:14]`
- **實際工具名**：gsap-skills（基於 GSAP 動畫庫打造的 AI 技能包）
- **開發者**：GreenSock（https://github.com/greensock/gsap-skills）
- **GitHub Stars**：13,682（GSAP 主庫 27,769 stars）
- **建立日期**：2026-03-04
- **核心用途**：教 AI Coding Agent 正確使用 GSAP 製作專業級網頁動畫
- **使用情境**：
  - 品牌官網捲動視差自動化開發：AI 根據設計稿自動生成 GSAP ScrollTrigger 捲動動畫
  - 複雜向量圖標無縫變形動畫：SVG path morphing 動效，圖標之間流暢過渡
  - 微互動物理反饋：按鈕懸浮彈跳、卡片拖拽回彈、頁面轉場滑入等有「手感」的互動動效

### 3. Pixel 2 模式 → ✅ 核實：pixel2motion `[00:16]`

- **逐字稿原文**：「你丟一張Logo 圖給它,它就會自動變成一段會動的開場」`[00:18]`
- **實際工具名**：pixel2motion
- **開發者**：nolangz（https://github.com/nolangz/pixel2motion）
- **GitHub Stars**：1,962
- **核心用途**：AI Logo 動畫 Skill，將靜態 Logo 圖片轉為流暢的 SVG 動畫、HTML 動態展示、GIF/影片
- **使用情境**：
  - 企業品牌網站開場動畫：官網首頁 Hero Section 的 Logo 動態揭幕效果
  - 影音創作者動態片頭：YouTube / 抖音頻道片頭 Logo 動畫
  - 互動式網頁向量圖示：導覽列、CTA 按鈕的 Logo 微動畫

### 4. Remotion → ✅ 核實：Remotion `[00:22]`

- **逐字稿原文**：「它是用寫程式的方式做影片,最適合做數字、圖表這種動畫」`[00:23]`
- **實際工具名**：Remotion
- **開發者**：Remotion Dev（https://github.com/remotion-dev/remotion / https://remotion.dev）
- **GitHub Stars**：56,520
- **核心用途**：用 React 程式碼製作影片，資料驅動的程式化影片生成
- **使用情境**：
  - 金融報表數據故事短片：每季財報數字自動轉為動態圖表動畫
  - 個人化里程碑影片（Spotify Wrapped 風格）：根據用戶年度數據自動批次渲染個人化回顧影片
  - 社群賽事即時排行動畫：比賽進行中，即時將分數/排名數據渲染為動態排行動畫

### 5. Video Shrugcraft → ✅ 核實：video-shotcraft `[00:30]`

- **逐字稿原文**：「它準備了一百多種現成的電影鏡頭,你把網站畫面丟進去」`[00:31]`
- **實際工具名**：video-shotcraft
- **開發者**：Vincentwei1021（https://github.com/Vincentwei1021/video-shotcraft）
- **GitHub Stars**：5,245
- **核心用途**：AI 影片 Skill，內建 152 種電影運鏡模板，把網站畫面轉為有電影感的產品廣告片
- **使用情境**：
  - SaaS 產品官網宣傳短片：把產品首頁 Demo 畫面套用電影運鏡，30 秒內產生宣傳短片
  - 電商商品詳情頁動態展示：商品頁自動截取 + 套用滑軌/推拉/環繞鏡頭
  - 新創募資 Demo 快速原型：Product Hunt / YC Demo Day 前，快速把原型網站轉為有電影感的 demo 影片

### 6. Story2 Hand Zone → ✅ 核實：story-to-handdrawn-video `[00:38]`

- **逐字稿原文**：「打一段中文故事,它變成首會日記動畫」`[00:39]`
- **實際工具名**：story-to-handdrawn-video
- **開發者**：gnipbao（https://github.com/gnipbao/story-to-handdrawn-video）
- **GitHub Stars**：1,399
- **核心用途**：Agent Skill，將中文故事文字或圖片序列轉為手繪日記風格動畫（無聲 MP4）
- **使用情境**：
  - 知識科普與情感故事自媒體短片：將科普文案或人生故事轉為手繪動畫短片
  - 兒童有聲繪本動態化：將童話故事文字自動產生手繪風格動畫影片
  - 品牌起源故事行銷：將品牌創辦故事轉為溫暖手繪風動畫

### 7. 手感動效 → ⚠️ 部分核實：breathing-dots-tutorial（教學專案，非獨立工具）`[00:43]`

- **逐字稿原文**：「它交的是手感,畫面上這片會呼吸,有節奏的點證」`[00:45]`
- **找到的相關專案**：breathing-dots-tutorial（https://github.com/mattrossman/breathing-dots-tutorial）
- **GitHub Stars**：130
- **說明**：⚠️ 這是教學專案（非獨立工具），展示如何用 React Three Fiber 製作呼吸感點陣動畫。影片中提到的「手感」指的是這類有機質感的視覺效果，通常由 GSAP 或 Three.js 等底層動畫引擎實現。
- **使用情境**：
  - 高階品牌官網沉浸式 WebGL 背景：有呼吸感的點陣背景，傳達有機、自然的品牌調性
  - 正念冥想 App 視覺導引：配合呼吸節奏的視覺動畫，引導使用者放鬆
  - 音訊頻譜可視化：將音樂節拍轉為有機質感的動態視覺

## 來源連結

| # | 逐字稿名稱 | 實際工具 | GitHub URL |
|---|-----------|---------|-----------|
| 1 | HyperFriends | hyperframes | https://github.com/heygen-com/hyperframes |
| 2 | G7 | gsap-skills | https://github.com/greensock/gsap-skills |
| 3 | Pixel 2 模式 | pixel2motion | https://github.com/nolangz/pixel2motion |
| 4 | Remotion | Remotion | https://github.com/remotion-dev/remotion |
| 5 | Video Shrugcraft | video-shotcraft | https://github.com/Vincentwei1021/video-shotcraft |
| 6 | Story2 Hand Zone | story-to-handdrawn-video | https://github.com/gnipbao/story-to-handdrawn-video |
| 7 | 手感動效 | breathing-dots-tutorial | https://github.com/mattrossman/breathing-dots-tutorial |

## 相關頁面

- [[wiki/concepts/ai-animation-generation-approaches|AI 動畫生成手法分類]]
- [[wiki/entities/hyperframes|hyperframes]]
- [[wiki/entities/gsap|gsap-skills]]
- [[wiki/entities/pixel2motion|pixel2motion]]
- [[wiki/entities/remotion|Remotion]]
- [[wiki/entities/video-shotcraft|video-shotcraft]]
- [[wiki/entities/story-to-handdrawn-video|story-to-handdrawn-video]]
