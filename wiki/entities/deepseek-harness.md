---
title: "DeepSeek Harness — Cordis 微核心編碼 Agent 平台"
type: entity
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [deepseek-harness, cordis, agent-harness, event-driven, plugin-architecture]
topics: [ai-development-tools, coding-agent, hooks]
canonical: entities/deepseek-harness
provenance:
  - kind: raw
    path: "raw/deep-research/rc-20260825-001/research-report.md"
---

# DeepSeek Harness — Cordis 微核心編碼 Agent 平台

> DeepSeek Harness 是 DeepSeek 推出的開源編碼 Agent 平台，基於 Cordis 微核心框架，採用「一切皆插件」的設計哲學。目前處於 Developer Preview 階段。

## 核心架構

### Cordis 微核心
- **一切皆插件**：模型適配、工具註冊、會話持久化、沙盒提供者等 major subsystems 都以插件形式掛載於共享 context 中
- **時空可組合性**：當 Hook 插件被卸載（unmount）時，系統會自動逆向執行其記錄的 cleanup 邏輯，乾淨撤銷所有副作用
- **不可變事件流**：Turn 與 Step 的思考與工具結果視為不可變事件流，支援 Resume、Fork 與 Replay

### 生命週期事件（Cordis Events）

| 事件 | 觸發時機 | 說明 |
|------|---------|------|
| `session/turn/start` | 工作階段/回合開始 | 初始化 session context |
| `session/message/user` | 使用者訊息 | 事件流自動包含 |
| `session/tool/before` | 工具執行前 | 攔截點，可阻斷執行 |
| `session/tool/result` | 工具執行後 | 含成功與失敗結果 |
| `session/turn/end` | 回合結束 | 清理資源 |
| `session/permission/change` | 權限變更 | 錯誤/環境異動事件 |

## Hook 機制

### 實作方式：TypeScript Cordis 插件

```typescript
import type { Context } from "@deepseek-ai/cordis";

export const name = "corporate-safety-gate";
export const inject = ["tools", "sessions"]; // 依賴注入其他服務

export function apply(ctx: Context) {
  // 監聽工具執行前事件
  ctx.on("session/tool/before", async (toolCall, session) => {
    if (toolCall.name === "bash" && toolCall.args.command.includes("rm -rf")) {
      throw new Error("DSH Policy: Dangerous command rejected!");
    }
  });
}
```

### 啟用插件（YAML 設定）

```yaml
# cordis.patch.yml
plugins:
  corporate-safety-gate: mounted
```

## 優勢

1. **時空可組合性**：插件卸載時自動逆向執行 cleanup，不留 leftovers
2. **不可變事件流與 Trajectory trace**：可追溯每次 Action 的具體 Plugin 來源
3. **跨生態通用適配**：透過 `oh-my-dsh` 散佈版，能自動發現並複用 Cursor、Claude、Copilot 的 Skills 與 Hooks

## 限制

1. **高度不穩定**：Developer Preview 階段，隨時會有破壞性變更
2. **學習曲線陡峭**：必須深入理解 Cordis 微核心事件與依賴注入才能開發 Hook
3. **第三方生態不成熟**：缺乏充足的文檔，社群生態仍在發展中

## 與其他 Harness 的比較

| 面向 | DeepSeek Harness | Claude Code | GitHub Copilot |
|------|------------------|-------------|----------------|
| **事件廣度** | Cordis 微核心事件 | 25+ 種事件 | 7+ 種事件 |
| **上下文干預深度** | 深（可重寫 messages） | 中（additionalContext） | 低（additionalContext） |
| **異步能力** | 由 Cordis 框架控制 | 原生 async: true | 同步阻塞 |
| **Fail-Safe 設計** | 由插件控制 | Fail-Closed（command）/ Fail-Open（HTTP） | Fail-Closed（command）/ Fail-Open（HTTP） |

## 來源

- [[wiki/sources/2026-08-25-coding-agent-hooks-comparison|五大 Coding Agent Harness Hook 機制比較研究]]

## 相關頁面

- [[wiki/concepts/harness|Harness — LLM 的驅動層]]
- [[wiki/topics/hooks|Hooks]]
- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級]]