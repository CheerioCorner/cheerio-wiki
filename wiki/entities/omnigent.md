---
title: Omnigent
type: entity
created: 2026-07-13
updated: 2026-07-30
sources: 1
tags: [meta-harness, orchestration, databricks, open-source, multi-agent, windows-compatibility]
collection: entities
topics: [meta-systems]
canonical: entities/omnigent
---

# Omnigent

> Databricks 開源的 meta-harness，位於現有 AI agents 之上，提供組合（Combine）、控制（Control）、協作（Share）三大能力。

---

## 概述

Omnigent 是一個 **meta-harness（元鞍具）** 層，運行在你已經在使用的 AI agents（Claude Code、Codex、Pi 或自訂 agents）之上，讓它們成為一個更豐富系統中的可互換零件。

- 開源（Apache 2.0）
- 官方 GitHub：https://github.com/omnigent-ai/omnigent
- 官方文件：https://omnigent.ai/
- Quickstart：https://omnigent.ai/quickstart/install

---

## 三大支柱

### Combine（組合）
- 在 Claude Code、Codex、Pi 和自訂 agents 之間無縫切換，只要一行 config 改變
- **Multi-harness authoring**：用 YAML 定義 custom agent，port 到不同 harness
- 支援 terminal-based agents 和 SDK-based agents（OpenAI Agents、Claude Agents SDK）

### Control（控制）
- **Contextual security policies**：動態追蹤 session 狀態做智慧決策
  - 例：下載 npm package 後 → 需要人批才能 git push
  - 例：只能寫入自己建立的檔案
- **Cost policies**：達到 $100 門檻後暫停，詢問是否繼續
- **Strong OS sandbox（OmniBox）**：鎖定 OS 存取、攔截/轉換網路請求（如注入 GitHub token 只到 approved requests）

### Share（協作）
- 即時協作：透過 URL 分享 live agent session
- 隊友可 review 檔案、留言、發送 commands
- 多種介面：web、mobile、Mac OS native app、API

---

## 技術架構

```
┌────────────────────────────────────────┐
│           Omnigent Server              │ ← policies + sharing
├────────────────────────────────────────┤
│          Runner（統一 API）             │ ← 包裝任何 agent
├────────────┬────────────┬──────────────┤
│ Claude Code│   Codex    │   Pi / SDK   │
└────────────┴────────────┴──────────────┘
```

- **Runner**：將任何 agent 包裝在 sandboxed session 中，提供統一的 messages/files in → text streams/tool calls out 介面
- **Server**：提供 contextual policies、cost policies、即時協作功能
- **Cloud execution**：可在本機或 hosted sandbox（Modal、Daytona）上執行

---

## Roadmap

- 整合 `GEPA` — 在 meta-harness 層做自動最佳化（尚未建立 Wiki entity 頁）
- 整合 `MemEx`（programmable scratchpad）和 `RLM`（code-based introspection；尚未建立 Wiki entity 頁）
- Omnigent Server MCP — 讓 agents 可以跨 session 工作
- 更多 harness 支援

---

## 實驗觀察（2026-07-30）

### 平台相容性

| 平台 | 支援狀態 | 備註 |
|------|---------|------|
| Linux | ✅ 支援 | 官方主要支援平台 |
| macOS | ✅ 支援 | 官方支援 |
| Windows | ❌ 不支援 | 目前無 Windows 支援，安裝會失敗 |

> **結論：** 如果你的主要開發環境是 Windows，Omnigent 目前無法直接使用。需要透過 WSL2 或等待官方支援。

### 其他觀察

- 安裝過程需要 Docker（用於 OmniBox sandbox）
- 文件品質不錯，但社群還很早期
- 與 Databricks 生態系整合紧密（Delta Lake、MLflow 等）

## 相關頁面

- [[wiki/concepts/meta-harness]] — 概念總覽頁（含學術版定義 A 與業界版定義 B），**meta-harness 是我們的研究專題，Omnigent 是進入這個領域的一棵樹苗**
- [[wiki/entities/plannotator]] — 另一種 agent 視覺化審閱工具
- [[wiki/concepts/minimal-agent-philosophy]] — Pi 的哲學，與 meta-harness 的加法取徑對比

## 來源

- Databricks Blog：*Introducing Omnigent: A Meta-Harness to Combine, Control and Share Your Agents*（[[../../raw/web/2026-07-13-omnigent-meta-harness-databricks]]）