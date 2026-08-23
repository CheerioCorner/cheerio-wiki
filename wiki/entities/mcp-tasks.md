---
title: MCP Tasks — 長任務擴充機制
type: entity
created: 2026-08-23
updated: 2026-08-23
sources: 1
tags: [mcp, tasks, long-running, async, polling]
topics: [extension-dev, mcp-ecosystem]
canonical: entities/mcp-tasks
provenance_raw:
  - "raw/web/2026-08-22-Tasks.md"
---

# MCP Tasks — 長任務擴充機制

> MCP Tasks 讓伺服器為長時間運行的請求返回持久控制代碼，客戶端可輪詢進度、提供輸入並在重連後取得結果，而非阻塞連線。

## 為什麼需要 Tasks？

直接阻塞連線有以下問題：
- **無長-lived 連線**：阻塞會佔用連線，許多客戶端和中介有超時限制
- **崩潰恢復**：task ID 是持久控制代碼，客戶端斷線後可用同一 ID 恢復
- **進度可見性**：Tasks 攜帶狀態中繼資料（`working`、`input_required`、`completed`、`failed`、`cancelled`）
- **中途互動**：任務需要輸入時進入 `input_required` 狀態，客戶端透過 `tasks/update` 回應
- **伺服器主導**：伺服器決定是否為請求建立 task `Tasks`

## 工作流程

```
1. 能力協商 → Client 包含 io.modelcontextprotocol/tasks
2. Task 建立 → Server 回傳 CreateTaskResult（taskId, 狀態, TTL, 輪詢間隔）
3. 輪詢 → Client 呼叫 tasks/get(taskId)
4. 中途輸入 → 狀態變為 input_required，Client 透過 tasks/update 回應
5. 完成 → 狀態變為 completed，result 欄位包含最終結果
6. 取消 → Client 隨時可送 tasks/cancel（合作式取消）
```

`Tasks`

## 狀態

| 狀態 | 說明 |
|------|------|
| `working` | 任務執行中 |
| `input_required` | 需要使用者輸入 |
| `completed` | 任務完成，result 可用 |
| `failed` | 任務失敗，error 可用 |
| `cancelled` | 任務已取消 |

## 適用場景

- CI pipeline、批次處理、模型訓練等長時間運行操作
- 需要使用者審批的工作流程
- 需要跨重連保持狀態的操作 `Tasks`

## 相關頁面

- [[wiki/entities/mcp-extensions|MCP Extensions]] — 擴充機制總覽
- [[wiki/entities/mcp-model-context-protocol|MCP]] — 協定總覽
- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端開發

## 來源

- [[raw/web/2026-08-22-Tasks|Tasks]]
