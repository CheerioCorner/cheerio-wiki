---
title: "混合式意圖路由器 — 確定性邏輯 + LLM 輔助的四層架構"
type: concept
created: 2026-09-01
updated: 2026-09-01
sources: 3
tags: [hybrid-architecture, intent-router, llm, deterministic, devops]
topics: [agent-infrastructure, ai-agent]
canonical: concepts/hybrid-intent-router
---

# 混合式意圖路由器 — 確定性邏輯 + LLM 輔助的四層架構

> **成長階段：** 🌱 種子期（圓桌會議共識，尚未實作驗證）

## 一句話定義

**混合式意圖路由器**是一種將確定性邏輯（Regex / Embedding）與 LLM 語意理解分層使用的架構，讓 LLM 只負責「理解自由文字」這一部分，其餘的路由、驗證、執行全部交給確定性程式碼。

## 為什麼重要

1. **降低 LLM 準確率依賴**：小模型（7B）的 tool calling 穩定性不足，混合架構讓確定性邏輯兜底
2. **提升可靠性**：多步驟串接的錯誤率是乘法累積（單步 90% × 4 跳 ≈ 65%），減少 LLM 參與的步驟數能大幅降低整体錯誤率
3. **安全邊界清晰**：LLM 永遠不直接執行 shell、不取得秘密、不跳過 confirm
4. **漸進式升級**：可以從純 Regex 開始，逐步加入 Embedding → LLM，不需要一步到位

## 四層架構

```
┌─────────────────────────────────────────────────────┐
│ 使用者自然語言輸入                                    │
└──────────────────────┬──────────────────────────────┘
                       ▼
┌─────────────────────────────────────────────────────┐
│ 第一層：確定性路由                                    │
│ - Regex / 關鍵字匹配                                 │
│ - Skill metadata 查表                                │
│ - Embedding 相似度（可選）                            │
│ → 高信心命令直接跳到第四層                             │
└──────────────────────┬──────────────────────────────┘
                       ▼（低信心時）
┌─────────────────────────────────────────────────────┐
│ 第二層：LLM 意圖解析                                 │
│ - 只輸出嚴格 JSON（intent + arguments + confidence）  │
│ - 使用 GBNF grammar 或 format:json 約束輸出           │
│ - 7B 模型足夠勝任                                   │
└──────────────────────┬──────────────────────────────┘
                       ▼
┌─────────────────────────────────────────────────────┐
│ 第三層：Policy / Schema 驗證                         │
│ - Skill 是否存在                                     │
│ - 參數是否完整（JSON Schema / Zod）                   │
│ - 操作類型（read-only vs mutation）                   │
│ - 是否需要人類確認                                   │
│ - Server / Collection / Auth 是否可用                │
└──────────────────────┬──────────────────────────────┘
                       ▼
┌─────────────────────────────────────────────────────┐
│ 第四層：執行                                         │
│ - 既有 .mjs 腳本                                    │
│ - Dry-run → 人類確認 → --confirm → 讀回驗證          │
│ - LLM 不在這一層參與                                 │
└─────────────────────────────────────────────────────┘
```

## 實作要點

### 第一層：確定性路由
- 維護一份 skill metadata 索引（名稱、觸發詞、參數 schema、操作類型）
- Regex 處理明確命令（如「看我目前的工作」→ `devops-board-work-status`）
- 可選加入 Embedding 相似度（如 `bge-small-zh-v1.5`，<150MB，<30ms）

### 第二層：LLM 意圖解析
- 模型：Qwen2.5-Coder-7B-Instruct Q4_K_M
- 輸出約束：JSON Schema + GBNF grammar
- 只負責：理解自由文字語意、填補缺失參數、產出結構化意圖

### 第三層：Policy 驗證
- 使用 JSON Schema 或 Zod 驗證 LLM 輸出
- 查表確認 skill 存在、參數完整、操作許可
- 決定是否需要 dry-run + 人類確認

### 第四層：執行
- 呼叫既有 .mjs 腳本（帶正確參數）
- 變更操作強制 dry-run → 人類確認 → 執行 → 讀回驗證
- 全程記入 audit log

## 與其他概念的關係

- 站在 [[wiki/concepts/local-llm-deployment|Local LLM 部署]] 之上——了解硬體限制後選擇模型
- 為 [[wiki/concepts/agent-security-levels|Agent 安全等級]] 提供架構層面的實現
- 與 [[wiki/concepts/copilot-agent-loop|Copilot Agent Loop]] 互補——Copilot 是 UI 層，本架構是後端邏輯層

## 來源

- [[wiki/sources/2026-09-01-local-llm-uhd770-research|Local LLM UHD 770 研究]]
- [[wiki/sources/2026-09-01-local-llm-round-table-synthesis|圓桌會議紀要]]
