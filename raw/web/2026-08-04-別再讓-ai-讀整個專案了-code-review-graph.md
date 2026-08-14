---
title: "別再讓 AI 讀整個專案了！code-review-graph 用知識圖譜幫你省下 8 倍 Token"
description: "每次讓 AI 做 Code Review 都在燒 Token？code-review-graph 用 Tree-sitter 建構程式碼知識圖譜，透過爆炸半徑分析精準定位變更影響範圍，平均省下 8.2 倍 Token。支援 19 種語言，一鍵整合 Claude Code、Cursor 等主流 AI 編輯器。"
source_url: "https://repoinside.com/tirth8205/code-review-graph"
source_domain: "repoinside.com"
author:
  - "RepoInside"
published: 2026-04-06
clipped: 2026-08-04
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# 別再讓 AI 讀整個專案了！code-review-graph 用知識圖譜幫你省下 8 倍 Token

> Source: [別再讓 AI 讀整個專案了！code-review-graph 用知識圖譜幫你省下 8 倍 Token](https://repoinside.com/tirth8205/code-review-graph)
> Clipped: 2026-08-04

## 專案簡介

你有沒有想過，每次讓 Claude Code 或 Cursor 幫你做 Code Review 時，它其實正在重新讀取你整個專案的原始碼？對於一個幾千個檔案的專案來說，這不只浪費時間，更會燒掉大量的 Token 費用。

**code-review-graph** 是一個開源工具，它透過 Tree-sitter 語法解析器將你的程式碼建構成一張結構化的知識圖譜（Knowledge Graph），儲存在本地的 SQLite 資料庫中。當你修改了某個函式，它不會讓 AI 重新掃描整個專案，而是透過「爆炸半徑分析」（Blast Radius Analysis）精確計算出哪些函式、類別、測試會被影響，只把這些關鍵上下文送給 AI。根據實際測試，這種方法平均可以減少 **8.2 倍** 的 Token 用量。

它支援 19 種程式語言（包含 Python、TypeScript、Go、Rust、Java、Vue、Solidity 等），還能解析 Jupyter Notebook。更重要的是，它透過 MCP（Model Context Protocol）與各大 AI 編輯器無縫整合，包括 Claude Code、Cursor、Windsurf、Zed 等。

## 操作畫面

![compare](https://github.com/tirth8205/code-review-graph/raw/main/diagrams/diagram1_before_vs_after.png)

## 這個專案要解決哪些問題？

- **Token 浪費嚴重** ：AI 輔助工具每次審查程式碼都需要讀取大量檔案，導致 Token 消耗過高、回應速度慢、成本居高不下
- **缺乏精準的變更影響分析** ：修改一個函式後，很難快速知道哪些其他程式碼會被影響，容易遺漏需要一併檢查的相關檔案
- **增量更新困難** ：傳統做法每次都需要重新建立索引，對於大型專案來說等待時間太長
- **跨語言專案的統一分析不易** ：一個專案可能混用多種程式語言，缺少能統一解析並建立跨語言關聯的工具
- **程式碼審查缺乏結構化風險評估** ：現有工具難以自動標示哪些變更風險較高、哪些執行路徑會被影響、哪些地方缺少測試覆蓋
- **新進開發者上手困難** ：大型專案缺乏自動化的架構文件和模組關係圖，新人需要花很長時間才能理解專案結構

## 適合哪些使用者？

- **使用 AI 輔助開發工具的工程師** ：經常使用 Claude Code、Cursor、Windsurf 等 AI 編輯器進行程式碼審查或開發的人
- **技術主管與 Tech Lead** ：需要快速掌握團隊程式碼變更影響範圍、進行 PR Review 的技術管理者
- **大型專案或 Monorepo 維護者** ：管理跨多個子專案、多語言混合的大型程式碼庫的團隊
- **開源專案貢獻者** ：希望快速理解陌生專案架構、找到合適切入點的開發者
- **重視程式碼品質的團隊** ：追求系統化的程式碼審查流程，包括風險評估、測試覆蓋率分析、死碼偵測等
- **對成本敏感的個人開發者或新創團隊** ：希望在使用 AI 輔助工具的同時控制 Token 費用支出

工商服務時間

RepoInside 為 [開源報報](https://www.patreon.com/githubcurator) 的加值服務。開源報報每天透過 Patreon 為付費會員整理一份使用繁體中文的開源專案摘要列表電子報。

[詳細加值服務介紹](https://repoinside.com/)

## 專案資訊

- GitHub 網址： [https://github.com/tirth8205/code-review-graph](https://github.com/tirth8205/code-review-graph)
- 授權條款：MIT License