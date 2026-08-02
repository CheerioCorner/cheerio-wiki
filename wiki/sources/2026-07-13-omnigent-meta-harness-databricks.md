---
title: "Omnigent: Databricks 開源 Meta-Harness"
type: source
created: 2026-07-13
updated: 2026-08-05
sources: 1
tags: [meta-harness, orchestration, databricks, open-source, multi-agent, security, collaboration]
collection: sources
topics: [meta-systems]
canonical: sources/2026-07-13-omnigent-meta-harness-databricks
provenance:
  - kind: raw
    path: "raw/web/2026-07-13-omnigent-meta-harness-databricks.md"
---

# Omnigent: Databricks 開源 Meta-Harness

> Databricks 官方部落格文章，宣布開源 Omnigent — 一個位於現有 AI agents 之上的 meta-harness 層。

## 重點摘要

- **核心論點：** Agent engineering 的 frontier 正在向上移動一層，單一 model + 單一 harness 已不是最佳解
- **三大支柱：** Combine（組合）、Control（控制）、Share（協作）
- **技術架構：** Runner（統一 API）+ Server（policies + sharing）+ Cloud execution
- **授權：** Apache 2.0 開源
- **平台：** 僅支援 Linux/macOS，**不支援 Windows**

## 關鍵發現

### Combine（組合）
- 一行 config 切換 Claude Code、Codex、Pi、自訂 agents
- Multi-harness authoring：YAML 定義 custom agent，port 到不同 harness
- 支援 terminal-based 和 SDK-based agents

### Control（控制）
- **Contextual security policies：** 動態追蹤 session 狀態做智慧決策
- **Cost policies：** 達到 threshold 後暫停詢問
- **Strong OS sandbox（OmniBox）：** 鎖定 OS 存取、攔截/轉換網路請求

### Share（協作）
- 透過 URL 分享 live agent session
- 隊友可 review 檔案、留言、發送 commands

## 與現有知識的關係

- 補充 [[wiki/entities/omnigent]] entity 頁面的技術細節
- Omnigent 與 [[wiki/concepts/meta-harness]] 同屬「定義 B（orchestration layer）」的實作
- 比 OmniAgent 更強調 security policies 和 collaboration

## 來源

- Databricks Blog: [Introducing Omnigent: A Meta-Harness to Combine, Control and Share Your Agents](https://www.databricks.com/blog/introducing-omnigent-meta-harness-combine-control-and-share-your-agents)
- GitHub: https://github.com/omnigent-ai/omnigent
- Docs: https://omnigent.ai/
