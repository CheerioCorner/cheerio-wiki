---
title: guizang-ppt-skill — AI Agent HTML PPT 技能
type: entity
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [skill, ppt, html, slides, design, presentation]
topics: [skill]
canonical: entities/guizang-ppt-skill
provenance_raw: "raw/web/2026-08-08-guizang-ppt-skill.md"
---

# guizang-ppt-skill — AI Agent HTML PPT 技能

> 歸藏（@op7418）開發的 AI agent 簡報技能，生成單文件 HTML 橫向翻頁 PPT、配圖和多平台封面，內建演講者模式。

## 一句話

在 Claude Code / Codex 等 agent 環境中，用一句話 prompt 生成高品質 HTML 橫向翻頁簡報，支援兩套視覺系統、配圖生成和完整演講者工具。

## 雙視覺系統

### Style A：電子雜誌 × 電子墨水
- 適合敘事、觀點、分享、個人風格表達
- 10 種佈局：封面、章節、數據大字報、圖文、圖片網格、Pipeline、對比等
- 5 套主題色：墨水經典、靛藍瓷、森林墨、牛皮紙、沙丘
- 視覺參考：*Monocle* 雜誌版式

### Style B：瑞士國際主義
- 適合事實、產品、分析、方法論表達
- 22 種鎖定版式：Cover、Statement、KPI Tower、Loop Diagram、Duo Compare、Image Hero、Closing Manifesto 等
- 4 套錨點色：克萊因藍 IKB、檸檬黃、檸檬綠、安全橙
- 網格鎖定：16 列 grid、直角色塊、1px 髮絲線、無陰影無漸變無圓角
- 強校驗：版式、圖片槽位、標題對齊、SVG 問題攔截

## 核心能力

| 能力 | 說明 |
|------|------|
| 橫向翻頁 | 鍵盤 ←→ / 滾輪 / 觸屏滑動 / 底部圓點 / ESC 索引 |
| Codex 配圖 | GPT-Image 2.0 / GPT-M 2.0 生成紀實照片、資訊圖、流程圖、UI 情境圖 |
| 多平台封面 | 公眾號 21:9、分享卡 1:1、小紅書 3:4、視頻號橫版 |
| 演講者模式 | 雙窗口觀眾屏、當前/下一頁 16:9 預覽、演講備註、計時排練、自動翻頁、雷射筆、圈選 |
| 低性能靜態模式 | 按 `B` 關閉 WebGL/canvas 動畫 |
| 單文件 HTML | 不需建構、不需伺服器，瀏覽器直接打開 |

## 安裝

```bash
npx skills add https://github.com/op7418/guizang-ppt-skill --skill guizang-ppt-skill
```

或手動：`git clone https://github.com/op7418/guizang-ppt-skill.git ~/.claude/skills/guizang-ppt-skill`

## 使用流程

1. 選擇風格（A 電子雜誌 / B 瑞士國際主義）
2. 需求澄清（7 問清單）
3. 拷貝模板
4. 填充內容（主題節奏表 + 佈局骨架）
5. 可選配圖
6. 生成演講備註
7. 自檢（`checklist.md` P0 級問題必須全過）
8. 預覽 → 排練 → 迭代

## 適合 / 不適合

- ✅ 線下分享、行業內部講話、私享會、AI 產品發布、demo day、帶強烈個人風格的演講
- ❌ 大段表格資料、培訓課件（資訊密度不夠）、需要多人協作編輯

## 平台支持

| 平台 | 狀態 |
|------|------|
| Claude Code | ✅ 原生 Skill 工作流 |
| Codex | ✅ 適合生成 PPT + 圖片生成 |
| Cursor / 其他本地 Agent | 可用 |
| WorkBuddy | 適配中 |
| 普通 Chatbot | 不推薦 |

## 贊助

360 安全龍蝦、Kimi work、Cola Skill 金牌贊助 + 真格 Token Grant 支持。

## 設計原則

1. 克制優於炫技
2. 結構優於裝飾
3. 圖片是第一公民
4. 配圖只做素材
5. 節奏靠 hero 頁
6. 低性能可退場
7. 術語統一
8. 瑞士風必須守版式

## 來源

- [[raw/web/2026-08-08-guizang-ppt-skill|Raw — GitHub README]]

## 相關頁面

- [[wiki/entities/huashu-design|huashu-design]] — 另一款 HTML 原生設計技能
- [[wiki/entities/mattpocock-skills|mattpocock-skills]] — AI Coding Agent Skills 集合
- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — AI Agent Skill 開放標準
