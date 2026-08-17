---
title: "AI 動畫生成手法分類"
type: concept
created: 2026-08-17
updated: 2026-08-17
sources: 1
tags: [ai-video, ai-animation, motion-design]
topics: [ai-development-tools]
---

# AI 動畫生成手法分類

從一支 53 秒 YouTube 短片整理出的 7 種 AI 動畫／影片生成手法分類，已逐一查證核實真實工具名稱與用途。

## 分類方式

依輸入類型可分為 7 類：

### 1. 網頁轉影片（HTML → Video）

**核實工具**：[[wiki/entities/hyperframes|hyperframes]]（HeyGen）— 41,244 stars
- **核心用途**：撰寫 HTML 網頁，自動渲染成影片
- **適用情境**：社群行銷短片批次生成、個人化年度數據回顧短影音、AI Agent 自動化原型影片演示

### 2. 網頁動效生成（Web Animation）

**核實工具**：[[wiki/entities/gsap|gsap-skills]]（GreenSock）— 13,682 stars
- **核心用途**：教 AI Coding Agent 正確使用 GSAP 製作專業級網頁動畫
- **適用情境**：品牌官網捲動視差自動化開發、複雜向量圖標無縫變形動畫、微互動物理反饋

### 3. Logo 動畫（Logo → Motion）

**核實工具**：[[wiki/entities/pixel2motion|pixel2motion]] — 1,962 stars
- **核心用途**：AI Logo 動畫 Skill，靜態 Logo 轉流暢 SVG/HTML/GIF 動畫
- **適用情境**：企業品牌網站開場動畫、影音創作者動態片頭、互動式網頁向量圖示

### 4. 程式碼驅動影片（Code → Video）

**核實工具**：[[wiki/entities/remotion|Remotion]] — 56,520 stars
- **核心用途**：用 React 程式碼製作影片，資料驅動的程式化影片生成
- **適用情境**：金融報表數據故事短片、個人化里程碑影片（Spotify Wrapped 風格）、社群賽事即時排行動畫

### 5. 電影運鏡模板（Cinematic Templates）

**核實工具**：[[wiki/entities/video-shotcraft|video-shotcraft]] — 5,245 stars
- **核心用途**：152 種電影運鏡模板，網站畫面轉電影感廣告片
- **適用情境**：SaaS 產品官網宣傳短片、電商商品詳情頁動態展示、新創募資 Demo 快速原型

### 6. 故事轉手繪動畫（Story → Hand-drawn）

**核實工具**：[[wiki/entities/story-to-handdrawn-video|story-to-handdrawn-video]] — 1,399 stars
- **核心用途**：中文故事文字轉手繪日記風格動畫
- **適用情境**：知識科普與情感故事自媒體短片、兒童有聲繪本動態化、品牌起源故事行銷

### 7. 有機質感動效（Organic Motion）

**參考專案**：breathing-dots-tutorial — 130 stars（⚠️ 教學專案，非獨立工具）
- **核心用途**：呼吸感點陣動畫，由 GSAP/Three.js 等底層引擎實現
- **適用情境**：高階品牌官網沉浸式 WebGL 背景、正念冥想 App 視覺導引、音訊頻譜可視化

## 工具關聯圖

```
HTML 網頁 ──→ hyperframes ──→ 影片
CSS/JS 動效 ──→ gsap-skills ──→ 網頁動畫
Logo 圖片 ──→ pixel2motion ──→ Logo 動畫
React 程式碼 ──→ Remotion ──→ 程式化影片
網站畫面 ──→ video-shotcraft ──→ 電影感廣告
中文故事 ──→ story-to-handdrawn-video ──→ 手繪動畫
視覺特效 ──→ GSAP/Three.js ──→ 有機質感動效
```

## 來源

- [[wiki/sources/2026-08-17-7-ai-animation-skills|7 個用 AI 做動畫的 Skills（Debug Tuboshu）]]

## 相關頁面

- [[wiki/topics/ai-development-tools|AI Development Tools]]
