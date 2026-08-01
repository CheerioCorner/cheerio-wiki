---
title: 使用指南
type: guide
created: 2026-07-11
updated: 2026-08-01
sources: 8
tags: [meta]
---

入口頁——這個 wiki 在做什麼、怎麼用、目前進度。

## 這是什麼？

一個為 LLM agent 設計的知識庫。原始資料進來，結構化 wiki 出來，query 答案可以存回 wiki，讓知識持續累積。

## 範疇

**AI Agent 框架 / Coding Agent / Pi 生態 / 知識管理**

主要對象：
- **[[topics/ai-agent/pi-mono|pi-mono]]** — Pi monorepo 架構
- **[[topics/ai-agent/pi-agent-core|pi-agent-core]]** — agent runtime
- **[[topics/extension-dev/plannotator|plannotator]]** — 視覺化審閱工具
- **[[topics/meta-systems/meta-harness|meta-harness]]** — 我們的研究專題

## 怎麼用？

### 新手入門
1. 讀 [[快速開始]] — 三種收集資料的方法
2. 讀 [[index]] — 看目前有什麼知識

### 日常操作
- **看到好文章** → Web Clipper 到 `raw/` → 說「ingest」
- **有問題** → 說「查 wiki：xxx」
- **好答案** → 說「存到 wiki」
- **定期維護** → 說「lint wiki」

### 進階操作
- **開始新專案** → 在 `wiki/projects/` 建立子資料夾
- **學到新東西** → 建立主題頁到 `wiki/topics/`
- **重要學習** → 種進 Notion 花園

## 目前結構

```
wiki/
├── topics/           # 主題式（按領域分類）
│   ├── ai-agent/     # AI Agent 相關
│   ├── extension-dev/ # Extension 開發
│   ├── meta-systems/ # 元系統
│   └── knowledge-mgmt/ # 知識管理
├── projects/         # 專案維護
├── sources/          # 來源筆記
├── canvas/           # 視覺化圖
├── index.md          # 索引
├── log.md            # 時間日誌
├── guide.md          # 本頁
└── 快速開始.md       # 新手入門
```

## 當前狀態

- [x] 建立三層架構（2026-07-11）
- [x] 完成 8 個來源的 ingest
- [x] 建立 24 個 wiki 頁面
- [x] 建立 7 筆日記（從 archive 回填）
- [x] Wiki 結構重構：topics/ 主題式（2026-08-01）
- [x] 系統全面盤點與優化閉環設計（2026-08-01）
- [ ] Notion 花園連線修復（待完成）
- [ ] YouTube 字幕抓取方案（待研究）

## 重要里程碑

| 日期 | 事件 |
|------|------|
| 2026-07-11 | 系統建立 + 首次 ingest + pi-web-access-zh-tw 本地化 |
| 2026-07-13 | Omnigent 研究 + meta-harness 概念整理 |
| 2026-07-18 | Pi 資源完整盤點 |
| 2026-07-30 | pi-plannotator-auto 發佈到 GitHub |
| 2026-07-31 | Package Publishing 流程建立 + OKF 學習 |
| 2026-08-01 | OKF Extension 開發 + 系統整理 + 優化閉環設計 |

## 相關頁面

- [[index]] — 內容索引（按主題分類）
- [[快速開始]] — 新手必讀的使用指南
- [[log]] — 時間日誌（append-only）
