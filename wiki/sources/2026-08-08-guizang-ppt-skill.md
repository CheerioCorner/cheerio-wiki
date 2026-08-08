---
title: "guizang-ppt-skill — 歸藏 HTML PPT 技能 GitHub README"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
provenance_raw: "raw/web/2026-08-08-guizang-ppt-skill.md"
tags: [skill, ppt, html, slides, design, presentation, guizang]
topics: [skill]
canonical: sources/2026-08-08-guizang-ppt-skill
---

> 來源：[op7418/guizang-ppt-skill GitHub README](https://github.com/op7418/guizang-ppt-skill)
> Clipped：2026-08-08

## 一句話

歸藏（@op7418）開發的 AI agent 簡報技能，用單文件 HTML 生成橫向翻頁 PPT，內建電子雜誌（Style A）和瑞士國際主義（Style B）雙視覺系統。

## 重點摘要

### 雙視覺系統
- **Style A 電子雜誌**：10 種佈局、5 套主題色（墨水經典/靛藍瓷/森林墨/牛皮紙/沙丘），像 *Monocle* 雜誌
- **Style B 瑞士國際主義**：22 種鎖定版式、4 套錨點色（克萊因藍/檸檬黃/檸檬綠/安全橙），16 列 grid、直角色塊、1px 髮絲線
- 不允許自定義 hex 值，保護美學比給自由更重要

### 核心特色
- 橫向翻頁：鍵盤/滾輪/觸屏/ESC 索引
- 演講者模式：雙窗口觀眾屏、當前/下一頁 16:9 預覽、演講備註、計時排練、自動翻頁、雷射筆、圈選、一鍵黑/白屏
- Codex 配圖：GPT-Image 2.0 / GPT-M 2.0 生成紀實照片、資訊圖、UI 情境圖
- 多平台封面：公眾號 21:9、分享卡 1:1、小紅書 3:4、視頻號
- 低性能靜態模式：按 `B` 關閉 WebGL
- 單文件 HTML：不需建構、不需伺服器

### 瑞士風設計規範
- 22 個具名版式（S01–S22），不能臨時發明頁面結構
- 圖文底對齊、圖片槽位綁定（data-image-slot）
- 強校驗腳本：`validate-swiss-deck.mjs`、`validate-presenter-mode.mjs`

### 演講者模式
- 雙窗口觀眾屏同步
- 宮格選頁、結構化備註
- 排練模式記錄每頁實際時長
- 激光筆、圈選、一鍵黑屏/白屏
- 演前檢查覆蓋彈窗、全屏、字體、圖片/視頻

### 適合 / 不適合
- ✅ 線下分享、行業內部講話、私享會、AI 產品發布、demo day
- ❌ 大段表格資料、培訓課件、多人協作編輯

### 平台
Claude Code ✅、Codex ✅、Cursor 可用、WorkBuddy 適配中、普通 Chatbot 不推薦

### 贊助
360 安全龍蝦、Kimi work、Cola Skill 金牌贊助 + 真格 Token Grant

## 與其他技能的關係

- 與 huashu-design 同屬「HTML 原生設計技能」賽道
- 歸藏更專注於 PPT/簡報場景，huashu-design 覆蓋更廣（原型/動畫/MP4/評審）
- 都遵循 agentskills.io 安裝標準

## 相關頁面

- [[wiki/entities/guizang-ppt-skill|guizang-ppt-skill]] — entity 頁面
- [[wiki/entities/huashu-design|huashu-design]] — 同賽道另一技能
- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — Skill 開放標準
