---
title: huashu-design — HTML 原生設計技能
type: entity
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [skill, design, html, prototype, slides, animation, pptx, mp4]
topics: [skill-presentation-design]
canonical: entities/huashu-design
provenance_raw: "raw/web/2026-08-08-alchaincyfhuashu-design-huashu-design.md"
---

# huashu-design — HTML 原生設計技能

> 花叔（@alchaincyf）開發的 HTML 原生設計技能，一句話 prompt 生成高保真原型、幻燈片、動畫，含 20 設計哲學 + 5 維評審 + MP4 導出。MIT 開源。

## 一句話

在 Claude Code / Codex / Cursor 等 agent 中，打一句話拿回一份能交付的設計——App 原型、演講簡報、時間軸動畫、資訊圖、設計方向顧問。

## 核心能力

| 能力 | 交付物 | 典型耗時 |
|------|--------|----------|
| 交互原型（App / Web） | 單文件 HTML · 真 iPhone bezel · 可點擊 · Playwright 驗證 | 10–15 min |
| 演講幻燈片 | HTML deck + 可編輯 PPTX（文本框保留） | 15–25 min |
| 時間軸動畫 | MP4（25/60fps）+ GIF + BGM | 8–12 min |
| 設計變體 | 3+ 並排對比 · Tweaks 即時調參 | 10 min |
| 資訊圖 / 可視化 | 印刷級排版 · PDF/PNG/SVG 導出 | 10 min |
| 設計方向顧問 | 三套邏輯並行 · 直接出 3 版真實視覺 | 5 min |
| 5 維度專家評審 | 雷達圖 + Keep/Fix/Quick Wins | 3 min |

## 核心機制

### 品牌資產協議（5 步硬流程）
涉及具體品牌時強制執行：問 → 搜 → 下載（三條兜底）→ grep 色值 → 寫 `brand-spec.md`。v2 穩定性方差比 v1 低 5 倍。

### 設計方向顧問（Fallback）
需求模糊時觸發三套互補邏輯 subagent：
1. 秒數輪盤（`date +%S` 取秒，20 選 1，打破模型偷選極簡的慣性）
2. 現實參照（世界級獲獎網站/簡報/iOS 原型遷移）
3. 最佳設計師（預算無上限時最適合的工作室哲學）

底層是 **40 種 HTML 原生風格庫**（網頁 20 + PPT 20，純 CSS 無需生圖）。

### Junior Designer 工作流
- 開工前 show 問題清單一次性發給用戶
- HTML 裡先寫 assumptions + placeholders + reasoning comments
- 儘早 show 給用戶（哪怕只是灰色方塊）
- 交付前用 Playwright 肉眼過一遍瀏覽器

### 反 AI slop 規則
避免一眼 AI 的視覺最大公約數（紫漸變 / emoji 圖標 / 圓角+左 border accent / SVG 畫人臉 / Inter 做 display）。

## 安裝

```bash
npx skills add alchaincyf/huashu-design
```

跨 agent 通用——Claude Code、Cursor、Codex、OpenClaw、Hermes 都能裝。

## 與 Claude Design 的關係

| | Claude Design | huashu-design |
|---|---|---|
| 形態 | 網頁產品（瀏覽器裡用） | skill（agent 裡用） |
| 配額 | 訂閱 quota | API 消耗 · 並行跑 agent 不受 quota 限 |
| 交付物 | 畫布內 + 可導 Figma | HTML / MP4 / GIF / 可編輯 PPTX / PDF |
| 複雜動畫 | 有限 | Stage + Sprite 時間軸 · 60fps 導出 |
| 跨 agent | 專屬 Claude.ai | 任意 skill 兼容 agent |

Claude Design 是**更好的圖形工具**，huashu-design 是**讓圖形工具這層消失**。

## 安全

核心鏈路 100% 本地運行，零網路零 key。無 telemetry。

## Limitations

- 不支援圖層級可編輯的 PPTX 到 Figma
- Framer Motion 級別的複雜動畫不行（3D、物理模擬、粒子系統）
- 完全空白的品牌從零設計品質會掉到 60–65 分

## 來源

- [[raw/web/2026-08-08-alchaincyfhuashu-design-huashu-design|Raw — GitHub README]]

## 相關頁面

- [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill]] — 另一款 HTML 原生 PPT 技能
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Agent Skills 集合
- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — AI Agent Skill 開放標準
