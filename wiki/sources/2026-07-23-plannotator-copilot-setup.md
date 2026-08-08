---
title: "Plannotator IT 安裝手冊：GitHub Copilot CLI 整合"
type: source
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [plannotator, copilot, installation, windows, setup-guide]
topics: [extension-dev, coding-agent]
canonical: sources/2026-07-23-plannotator-copilot-setup
provenance_raw: "raw/web/2026-07-23-plannotator-copilot-setup-manual.md"
---

# Plannotator IT 安裝手冊：GitHub Copilot CLI 整合

> 來源：PDF 安裝手冊（2026-07-23），描述 Plannotator 與 GitHub Copilot CLI 在 Windows 上的整合安裝流程。

## 一句話

這份安裝手冊教你如何在 Windows 上把 Plannotator 裝起來，並與 GitHub Copilot CLI 整合，讓 Copilot 的 plan mode 能在瀏覽器中標注審閱。

## 核心流程

### 三步快速安裝

1. **安裝 Plannotator** — `irm https://plannotator.ai/install.ps1 | iex`
2. **停用 Share** — 設定 `PLANNOTATOR_SHARE=disabled`（環境變數或 config.json）
3. **安裝 Copilot CLI 插件** — `/plugin marketplace add backnotprop/plannotator` + `/plugin install plannotator-copilot@plannotator`

### 完整設定（12 步，3 階段）

| 階段 | 步驟 | 內容 |
|------|------|------|
| 基礎安裝 | 1-4 | 安裝 Plannotator → 停用 Share → 安裝插件 → 確認 App PATH |
| 整合測試 | 5 | Shift+Tab 進入 plan mode → 瀏覽器標注 → exit_plan_mode 發送 |
| 進階整合 | 6-12 | 與 Obsidian 整合、slash commands、進階設定 |

### Slash Commands

| 命令 | 功能 |
|------|------|
| `/plannotator-review` | 審查 PR |
| `/plannotator-annotate <file>` | 標註 Markdown / HTML / TXT |
| `/plannotator-last` | 標註 Copilot 最後回應 |

## 關鍵設計

### 架構原理

```
Copilot CLI/App → localhost → Plannotator → 瀏覽器 UI → 人類標注 → 發送 Prompt
```

Plannotator 在 Copilot 的 plan mode 中攔截 AI 請求，人類在瀏覽器中完成標注後才發送給 AI。

### Ask AI 功能

- 支援 Anthropic、OpenAI、GitHub Copilot 作為 AI provider
- 支援 Claude、Codex、Pi、OpenCode 等 agent
- 透過 localhost 通訊，不會將資料送往外部 telemetry
- 功能包括：Plan Review、Code Review、Anote

## 常見問題

| 問題 | 解決方案 |
|------|----------|
| plannotator 不在 PATH 中 | 確認 `AppData\Local\plannotator\` 在 PATH 中 |
| /plugin install 失敗 | 確認 Copilot CLI 已正確安裝，嘗試 PowerShell 或 VS Code |
| VS Code 整合 | 從 Marketplace 安裝 `backnotprop.plannotator-webview` |

## 來源

- [[raw/web/2026-07-23-plannotator-copilot-setup-manual|原始 PDF 文字提取]]
- 官方文件：https://plannotator.ai/docs
- GitHub：https://github.com/backnotprop/plannotator

## 相關頁面

- [[wiki/entities/plannotator|Plannotator]]
- [[wiki/topics/extension-dev|Extension Dev]]
