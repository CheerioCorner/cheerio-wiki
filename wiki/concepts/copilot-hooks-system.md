---
title: "Copilot Hooks 系統 — Lifecycle Callback"
type: concept
created: 2026-08-14
updated: 2026-08-14
sources: 7
tags: [github-copilot, hooks, lifecycle, permissions, auditing, error-handling]
topics: [skill, ai-development-tools]
canonical: concepts/copilot-hooks-system
provenance:
  - kind: raw
    path: "raw/.trash/2026-08-14-working-with-hooks.md"
  - kind: raw
    path: "raw/.trash/2026-08-14-session-hooks.md"
  - kind: raw
    path: "raw/.trash/2026-08-14-pre-tool-use-hook.md"
  - kind: raw
    path: "raw/.trash/2026-08-14-post-tool-use-hook.md"
  - kind: raw
    path: "raw/.trash/2026-08-14-error-handling-hook.md"
  - kind: raw
    path: "raw/web/2026-08-14-session-lifecycle-hooks.md"
  - kind: raw
    path: "raw/.trash/2026-08-14-user-prompt-submitted-hook.md"
---

# Copilot Hooks 系統 — Lifecycle Callback

> Hooks 讓你在 Copilot session 的每個階段插入自訂邏輯：從 session 啟動、使用者 prompt、工具呼叫到 session 結束。

## 7+ 種 Hooks

| Hook | 觸發時機 | 可做什麼 |
|------|---------|---------|
| **`onSessionStart`** | Session 開始（新或恢復） | 注入 context、載入偏好設定 |
| **`onUserPromptSubmitted** | 使用者送出訊息 | 重寫 prompt、加入 context、篩選輸入 |
| **`onPreToolUse`** | 工具執行**前** | 允許/拒絕/修改工具呼叫（權限控制） |
| **`onPostToolUse`** | 工具執行**後**（成功） | 轉換結果、遮蔽密鑰、審計記錄 |
| **`onPostToolUseFailure`** | 工具執行**後**（失敗） | 注入重試指引、記錄失敗 |
| **`onSessionEnd`** | Session 結束 | 清理資源、記錄指標 |
| **`onErrorOccurred`** | 錯誤發生 | 自訂日誌、重試邏輯、Slack 警報 |

## 註冊方式

```typescript
const session = await client.createSession({
    hooks: {
        onSessionStart: async (input, invocation) => { /* ... */ },
        onPreToolUse: async (input) => { /* ... */ },
        onPostToolUse: async (input) => { /* ... */ },
        onErrorOccurred: async (input) => { /* ... */ },
        onSessionEnd: async (input, invocation) => { /* ... */ },
    },
});
```

所有 hooks 都是**可選的**。回傳 `null` 表示使用預設行為。

## 典型使用場景

### 權限控制（onPreToolUse）

```typescript
onPreToolUse: async (input) => {
    if (DESTRUCTIVE_TOOLS.includes(input.toolName)) {
        return { permissionDecision: "ask" };  // 詢問使用者
    }
    return { permissionDecision: "allow" };
}
```

### 審計追蹤

組合 `onSessionStart` + `onUserPromptSubmitted` + `onPreToolUse` + `onPostToolUse` + `onSessionEnd` 建立完整審計軌跡。

### 錯誤處理與重試

```typescript
onErrorOccurred: async (input) => {
    if (input.errorContext === "model_call" && input.recoverable) {
        return { errorHandling: "retry", retryCount: 3 };
    }
    return null;
}
```

### Prompt Enrichment

```typescript
onSessionStart: async (input) => {
    return { additionalContext: `Project: ${pkg.name} v${pkg.version}` };
}
```

## 最佳實踐

1. Hooks 內聯執行——保持快速
2. 回傳 `null` 表示無變更
3. 用 `additionalContext` 而非 `modifiedPrompt`（保留原始意圖）
4. 以 `invocation.sessionId` 為 key 追蹤 per-session 狀態

## 來源

- [[wiki/sources/2026-08-14-working-with-hooks|Working with Hooks]]
- [[wiki/sources/2026-08-14-session-lifecycle-hooks|Session Lifecycle Hooks (含 Session Hooks 概覽)]]
- [[wiki/sources/2026-08-14-pre-tool-use-hook|Pre-tool Use Hook]]
- [[wiki/sources/2026-08-14-post-tool-use-hook|Post-tool Use Hook]]
- [[wiki/sources/2026-08-14-error-handling-hook|Error Handling Hook]]
- [[wiki/sources/2026-08-14-session-lifecycle-hooks|Session Lifecycle Hooks]]
- [[wiki/sources/2026-08-14-user-prompt-submitted-hook|User Prompt Submitted Hook]]

## 相關頁面

- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK]]
- [[wiki/concepts/copilot-custom-agents|Custom Agents]]
- [[wiki/concepts/copilot-agent-loop|Agent Loop]]
