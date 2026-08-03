---
title: "QwenPaw: Your Personal AI Assistant — YouTube 介紹影片"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
provenance:
  - kind: raw
    path: raw/youtube/qwenpaw-personal-ai-assistant.md
tags: [qwenpaw, ai-agent, agent-os, security, local-deploy, youtube]
collection: sources
topics: [ai-agent]
canonical: sources/2026-08-08-qwenpaw-youtube-intro
provenance_raw: raw/youtube/qwenpaw-personal-ai-assistant.md
provenance_url: https://youtu.be/QE6FVnNcd3Y
---

> 來源：[YouTube — QwenPaw: Your Personal AI Assistant](https://youtu.be/QE6FVnNcd3Y)
> 原始字幕：[[raw/youtube/qwenpaw-personal-ai-assistant|raw transcript]]
> Language: zh-TW | Duration: 6:44

## 一句話

QwenPaw 是 AgentScope 開發的本地 AI 助理，以 Agent OS 架構 + 安全沙箱為核心，GitHub 近三萬星。

## 重點摘要

### 問題意識

現有 AI 助理三大痛點：
1. **資料隱私與雲端鎖定** — 對話/檔案上傳第三方，有隱私外洩風險
2. **擴充性不足** — 功能固定，難以客製化工作流程
3. **安全性疑慮** — 執行本地程式碼如同開後門，LLM 幻覺可能產生惡意指令

### 核心架構：Agent OS

三大支柱：
- **Resources** — 透明獨立的檔案工作區
- **Governance** — Tool Guard + File Guard 存取規則引擎
- **Sandbox** — OS 核心層級隔離（Linux Bubblewrap 等）

### Scroll Context + ReMe

- 三層記憶體系統，完整保留對話歷史（不破壞性摘要）
- ReMe 長期記憶檢索，Agent 永不遺忘

### 應用場景

- **開發者**：Coding Mode（三欄 Web IDE），讀/編輯/審查程式碼
- **營運人員**：排程任務，自動抓取數據、產生報告、推送到 DingTalk/Discord
- **技術愛好者**：Skills + Plugin 生態，打造個人自動化工作流

### 社群反應

正面：易用性、多通道整合、強大文件處理、主動記憶系統
顾虑：外掛生態早期、阿里巴巴色彩可能導致 Qwen 生態鎖定

### 產業影響

- 將 AI Agent 安全性從可選功能拉高到核心架構層次
- 可能迫使其他 Agent 框架重新檢視安全模型
- 基於 AgentScope 2.0 + ACP，未來有演進為多代理協作系統的潛力

## 來源

- [[raw/youtube/qwenpaw-personal-ai-assistant|Raw transcript — YouTube QE6FVnNcd3Y]]

## 相關頁面

- [[wiki/entities/qwenpaw|QwenPaw]]
- [[wiki/sources/2026-08-02-qwenpaw-github-readme|GitHub README]]
