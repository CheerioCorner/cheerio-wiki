# AI Agent 學習進度

> 追蹤每次學習的具體成果。

## 最近學習

### 2026-08-20：Pi v0.84.0 更新分析

**學到了什麼**：
1. samplingParams — 模型參數控制（temperature, top_p）
2. thinking_token_budget — CoT 的 token 預算
3. Per-directory context — Agent 設定分層
4. Baseten provider — Model-as-a-Service 生態

**新增 wiki 頁面**：
- [[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 更新分析]]
- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought]]
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]]

**任務**：
- [ ] 嘗試全螢幕模式
- [ ] 測試 AGENTS.override.md
- [ ] 玩 samplingParams

**延伸研究**：
- TUI 設計模式
- Markdown 終端渲染
- Provider 抽象層

---

## 學習統計

| 月份 | 分析版本 | 新增頁面 | 完成任務 |
|------|---------|---------|---------|
| 2026-08 | v0.84.0 | 3 | 0 |

## 學習模式

從 v0.84.0 分析中，我發現了 Pi 團隊的幾個設計模式：

1. **Event-driven** — 用事件驅動 UI 更新（message_update delta）
2. **Layered config** — 支援分層設定（AGENTS.override.md）
3. **Resource budget** — 用預算管理有限資源（thinking_token_budget）
4. **Provider abstraction** — 統一的模型接入介面

這些模式可以應用到自己的 Agent 設計中。

## 標籤

#ai-agent #learning #progress
