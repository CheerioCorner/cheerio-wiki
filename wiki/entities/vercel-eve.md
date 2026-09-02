---
title: "Vercel Eve — 檔案系統即 Agent 的耐用型框架"
type: entity
created: 2026-09-02
updated: 2026-09-02
sources: 1
tags: [vercel-eve, typescript, agent-framework, durable-execution, file-system-agent]
topics: [agent-runtime-implementations]
canonical: entities/vercel-eve
---

# Vercel Eve — 檔案系統即 Agent 的耐用型框架

> Vercel 於 2026 年 6 月開源 Beta。核心哲學「約定優於配置（Convention over Configuration）」—— Agent 所有原語對應到專案目錄的實體檔案。

> ⚠️ Vercel AI SDK ≠ Vercel Eve。AI SDK 是輕量無狀態的模型整合層；Eve 是高階耐用型 Agent 框架。

## 核心架構

目錄結構即 Agent 定義 `[§3]`：
```
agent/
├── instructions.md      # 系統提示詞（Markdown）
├── agent.ts             # Model 配置（defineAgent）
├── tools/*.ts           # 單檔即單工具（defineTool）
├── skills/*.md          # 模組化知識庫（自動 RAG 檢索）
├── subagents/           # 嵌套子代理
├── channels/            # Slack/Discord 對接
└── connections/         # MCP/外部 API 連線
```

- **Manifest 編譯**：建置時自動遍歷目錄，編譯為 Manifest 清單對接模型 `[§3]`
- **耐用執行**：基於 Vercel Workflow，每一步記錄為 Event Log，確定性重放（Deterministic Replay）在 Cold Start/重啟後無損恢復 `[§3]`
- **安全沙箱**：Firecracker microVM（Vercel）/ Docker（本機） `[§3]`

## 可觀測性

- 部署於 Vercel 後自動啟用「Agent Runs」面板，不需手動配置追蹤代碼 `[§3]`
- 層級 Trace 樹（`ai.eve.turn` → `ai.toolCall`）+ Reasoning 思維鏈 + Token 成本追蹤 `[§3]`
- OTel 導出至 Braintrust/LangSmith/Langfuse `[§3]`

## 多 Agent

- **Hierarchical Subagents**：`agent/subagents/` 目錄定義嵌套子代理，獨立 context 隔離 `[§3]`
- **Slack 審批門禁**：`approval: always()` 觸發暫停（零計算成本），人類核准後從中斷步驟 Resume `[§3]`

## 來源

- [[wiki/sources/2026-09-02-agent-harness-framework-landscape|AI Agent Harness/Framework 全貌盤點]]

## 相關頁面

- [[wiki/entities/langgraph|LangGraph]] — 另一種耐用執行框架（Checkpointer vs Event Log）
