---
title: "huashu-design — 花叔 HTML 原生設計技能 GitHub README"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
provenance:
  - kind: raw
    path: raw/web/2026-08-08-alchaincyfhuashu-design-huashu-design.md
tags: [skill, design, html, prototype, slides, animation, pptx, mp4, huashu]
topics: [skill-presentation-design]
canonical: sources/2026-08-08-huashu-design
---

> 來源：[alchaincyf/huashu-design GitHub README](https://github.com/alchaincyf/huashu-design)
> Clipped：2026-08-08

## 一句話

花叔（@alchaincyf）開發的 HTML 原生設計技能，一句話 prompt 生成高保真原型、幻燈片、動畫，含 20 設計哲學 + 5 維評審 + MP4 導出，MIT 開源。

## 重點摘要

### 核心能力
| 能力 | 交付物 | 耗時 |
|------|--------|------|
| 交互原型 | 單文件 HTML · 真 iPhone bezel · 可點擊 | 10–15 min |
| 演講幻燈片 | HTML deck + 可編輯 PPTX | 15–25 min |
| 時間軸動畫 | MP4（25/60fps）+ GIF + BGM | 8–12 min |
| 設計變體 | 3+ 並排對比 · Tweaks 即時調參 | 10 min |
| 資訊圖 | 印刷級排版 · PDF/PNG/SVG | 10 min |
| 設計方向顧問 | 三套邏輯並行 · 3 版真實視覺 | 5 min |
| 5 維度評審 | 雷達圖 + Keep/Fix/Quick Wins | 3 min |

### 核心機制

**品牌資產協議（5 步硬流程）**：問 → 搜 → 下載（三條兜底）→ grep 色值 → 寫 `brand-spec.md`。v2 穩定性方差比 v1 低 5 倍。

**設計方向顧問（Fallback）**：需求模糊時觸發三套互補邏輯 subagent：
1. 秒數輪盤（`date +%S` 取秒，20 選 1，打破極簡慣性）
2. 現實參照（世界級獲獎網站遷移）
3. 最佳設計師（頂級工作室哲學）
底層 40 種 HTML 原生風格庫（網頁 20 + PPT 20）。

**Junior Designer 工作流**：先 show 問題清單 → assumptions/placeholders → 儘早 show → 迭代 → Playwright 驗證。

**反 AI slop 規則**：避免紫漸變/emoji 圖標/圓角+左 border accent/SVG 畫人臉/Inter 做 display。

### 安裝
```bash
npx skills add alchaincyf/huashu-design
```
跨 agent 通用：Claude Code、Cursor、Codex、OpenClaw、Hermes。

### 與 Claude Design 的比較
- Claude Design = 更好的圖形工具（瀏覽器 GUI）
- huashu-design = 讓圖形工具這層消失（agent 對話）
- huashu-design 支援複雜動畫（Stage + Sprite 時間軸、60fps）和跨 agent

### 安全
核心鏈路 100% 本地運行，零網路零 key。無 telemetry。

### Limitations
- 不支援圖層級可編輯的 PPTX 到 Figma
- 3D/物理模擬/粒子系統超出邊界
- 空白品牌從零設計品質 60–65 分

### Showcases
- �鸚鵡進化史網站（三套設計方向顧問實戰）
- 「聊聊 skill」PM after-party 演講 deck（13 頁 HTML deck）
- FanBox（Coding Agent 駕駛艙）三套界面皮膚

## 與其他技能的關係

- 與 guizang-ppt-skill 同屬「HTML 原生設計技能」賽道
- huashu-design 覆蓋更廣（原型/動畫/MP4/評審），guizang-ppt-skill 更專注 PPT 場景
- 兩者都是 MIT 開源、跨 agent 通用

## 相關頁面

- [[wiki/entities/huashu-design|huashu-design]] — entity 頁面
- [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill]] — 同賽道另一技能
- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — Skill 開放標準
