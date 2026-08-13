---
title: Claude Design
type: entity
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [claude-design, anthropic, design-tool, wireframe, prototype]
topics: [ai-development-tools]
canonical: entities/claude-design
---

# Claude Design

> Anthropic 的 AI 設計工具，讓使用者透過簡單 prompt 建立 Wireframe、Prototype 與動畫，並可透過 MCP 匯出到 Claude Code 實作。

## 核心定位

Claude Design 是一個基於 Claude 模型的設計工具，提供直覺式介面，讓非技術人員也能透過 prompt 建立高品質設計。可用於網站、投影片、文件與動畫。

## 支援模型

| 模型 | 特性 | 建議用途 |
|------|------|---------|
| Opus 5 | 最強能力，token 消耗高 | 設計任務（medium effort） |
| Haiku | 省 token，需更多修正 | 簡單設計草稿 |
| Fable | 最高能力，限額使用 | 複雜設計任務 |

建議設定 **medium effort level** 平衡品質與 token 消耗。

## 五階段工作流

```
design.md → Design System → Wireframe → Prototype → Animations → Handoff
```

### 1. Design.md 建立
- 先建立 [[wiki/concepts/design-md-format|design.md]] 品牌設計文件
- 用 Claude Code + skill 訪談自動產生
- 確保所有後續設計 on-brand

### 2. Design System 建立
- 上傳 design.md 作為 asset
- 建立字體、顏色等風格設定
- 確保多頁面一致性

### 3. Wireframe 製作
- 選擇 Design System → Wireframe 模式
- 描述螢幕數量與結構
- 產生多個變體方案
- 用 Comment 功能微調

### 4. Prototype 設計
- 選擇 Wireframe 變體 → 轉成完整設計
- 加入按鈕顏色、texture 等細節
- "Polish" 指令美化

### 5. Animations 動畫
- 加入 subtle 動畫（scroll into view、text pop-up）
- 保持簡潔，避免過度效果

## MCP Handoff（設計交付）

Claude Design 透過 MCP Protocol 與 [[wiki/entities/claude-code|Claude Code]] 雙向溝通：

1. 在 Claude Design 中匯出設計
2. Claude Code 讀取 metadata 與 Design System
3. 建構真實應用程式
4. Deep Review 確保品質（響應式適配、設計一致性）
5. 連接後端（如 Supabase）→ 部署

## 限制

- **5 小時 Token Limit**：所有 Claude 活動共用（不只是設計）
- **Token Hungry**：Heavy usage 會更快用完限額
- **響應式問題**：Claude Design 建立的設計在不同螢幕尺寸上可能有問題，需 Deep Review 修正

## 參考來源

- [[wiki/sources/2026-08-13-claude-design-youtube|How To Use Claude Design To Build Beautiful Sites]] — 五階段完整教學
- [Claude Design 官方文件](https://docs.anthropic.com/en/docs/claude-design)

## 相關頁面

- [[wiki/entities/claude-code|Claude Code]] — 接收設計的 AI Coding Agent
- [[wiki/concepts/design-md-format|design.md Format]] — 品牌設計文件格式
- [[wiki/entities/mcp-model-context-protocol|MCP]] — 設計交付使用的 Protocol
