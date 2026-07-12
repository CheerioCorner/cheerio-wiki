---
title: Overview
type: overview
created: 2026-07-11
updated: 2026-07-11
sources: 2
tags: [meta]
---

入口頁——這個 wiki 在做什麼、目前進度、整體結構。

## 範疇
**AI Agent 框架 / Coding Agent / Pi 生態**。
起始 metric:2026-07-11 完成首次 ingest,包含兩個來源(同一主題的兩個視角)。

主要對象:
- **[[pi-mono]]** — `badlogic/pi-mono` monorepo(43,777 stars, v0.72.1)
- **[[pi-agent-core]]** — 上列 monorepo 內的 agent runtime(5 檔 / 1,500 行)
- **[[mario-zechner]]** — 作者
- 主要 design philosophies:[[minimal-agent-philosophy]]、[[late-conversion]] 等

## 整體結構
- `raw/` 唯讀原始資料。
- `wiki/` LLM 維護的筆記。
- `AGENTS.md` 規範。

## 當前狀態
- [x] 建立三層架構
- [x] 第一次 ingest(2 個來源,1 個綜合頁)
- [ ] 接下來:linter?第二個主題來源?取向看對話

## 重要點與人為決定
詳見 [[index]] 內「初動判錯」與 [[AGENTS]] 內同等記錄。

## 相關頁面
- [[index]]
- [[log]]
- [[快速開始]]—— 新手必讀的使用指南
- [[minimal-agent-philosophy]]
