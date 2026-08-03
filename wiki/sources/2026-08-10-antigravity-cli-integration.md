---
title: Antigravity CLI 整合 Session
type: source
created: 2026-08-10
updated: 2026-08-10
sources: 1
tags: [antigravity, gemini, cli, multi-agent, integration]
topics: [ai-agent]
canonical: sources/2026-08-10-antigravity-cli-integration
provenance:
  - kind: raw
    path: raw/conversations/2026-08-10-antigravity-cli-integration.md
---

# Antigravity CLI 整合 Session

> 2026-08-10 的實作 session，整合 Antigravity CLI 取代已停用的 Gemini CLI 免費層。

## 背景

- Gemini CLI 免費層已在 2026/6/18 停止服務
- 所有免費/Pro/Ultra 用戶迁移到 Antigravity CLI（封閉原始碼）
- 企業用戶（Gemini Code Assist Standard/Enterprise）仍可使用 Gemini CLI

## 關鍵發現

### Gemini CLI → Antigravity CLI 變化

| 面向 | Gemini CLI | Antigravity CLI |
|------|------------|-----------------|
| 原始碼 | 開源（Apache 2.0） | 封閉原始碼 |
| 語言 | TypeScript | Go |
| 二進位檔名 | `gemini` | `agy` |
| 安裝方式 | `npm install -g @google/gemini-cli` | `irm https://antigravity.google/cli/install.ps1 \| iex` |
| Node.js 依賴 | 需要 | 不需要 |
| 免費配額 | 1000 req/day | 週制（未公開具體限制） |
| 設定檔位置 | `~/.gemini/settings.json` | `~/.gemini/antigravity-cli/settings.json` |

### Antigravity CLI 特色

- 支援多 agent 並行執行
- 與 Antigravity 2.0 共用 agent harness
- 支援 OAuth 認證（Windows Credential Manager）
- 支援 headless 模式：`agy -p "prompt"`
- 支援 JSON 輸出：`--output-format json`

### 免費配額注意事項

- 週制配額（具體限制未公開）
- Community 報告約 2000 行程式碼後會被限制
- 多 agent 並行會快速消耗配額
- 可用 `/usage` 查看目前用量

## 實作結果

### 建立的 Skill

- **gy**（`~/.agents/skills/gy/`）
- 觸發關鍵字：用 Gemini、問 Gemini、agy、gy、/gy
- 功能：Pi orchestrator 啟動 agy → pane 即時可見 → 結束後讀取 log

### 測試結果

```
Input: What is the capital of France?
Output: Paris
Duration: 2.36 seconds
Tokens: 8061 total
Status: SUCCESS
```

## 相關頁面

- [[wiki/entities/pi-agent-dashboard|pi-agent-dashboard]] — Pi Web Dashboard
- [[wiki/concepts/meta-harness|meta-harness]] — Multi-agent orchestration 概念
- [[wiki/entities/hermes-agent|hermes-agent]] — Hermes Agent 架構
