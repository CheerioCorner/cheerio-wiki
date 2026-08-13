---
title: "design.md Format"
type: concept
created: 2026-08-13
updated: 2026-08-13
sources: 1
tags: [design-md, brand-design, format-spec, google]
topics: [ai-development-tools]
canonical: concepts/design-md-format
---

# design.md Format

> Google 定義的品牌設計文件格式，讓 AI coding agent 在產生設計前先讀懂品牌規範，確保所有輸出 on-brand。

## 什麼是 design.md？

design.md 是一份結構化的品牌設計文件，包含：

- **品牌色彩**（Brand Colors）— 主色、輔色、強調色
- **字體規範**（Typography）— 標題字體、正文字體、字級
- **視覺風格**（Visual Style）— 圓角、陰影、間距
- **設計原則**（Design Principles）— 品牌調性、設計語言

AI coding agent 在執行設計任務前會讀取此檔案，因此不需要每次 prompt 都重複描述品牌規範。

## 為什麼重要？

### 避免 AI Slop
若不提供 design.md，Claude Design 等工具會 fallback 到通用預設風格，導致「所有 AI 生成的設計看起來都一樣」。design.md 提供了差異化的基礎。

### 確保一致性
多頁面專案中，design.md 作為單一事實來源（Single Source of Truth），確保所有頁面遵循相同的視覺語言。

### 節省 Token
一次建立 design.md，後續所有設計任務都能自動套用，不需要每次重複描述品牌需求。

## 建立方式

### 方式一：透過 Claude Code + Skill
用設計規劃 skill 訪談使用者，自動產生符合 Google 標準的 design.md。

### 方式二：從既有品牌複製
使用 [getdesign.md](https://getdesign.md) 取得知名品牌的 design.md 範本，再修改為自己的品牌。

### 方式三：手動撰寫
依照 Google 的格式規範自行撰寫。

## 輔助工具

| 工具 | 用途 |
|------|------|
| DesignMD.space | 視覺化 design.md 內容 |
| getdesign.md | 取得品牌設計範本 |
| Coolers | 色彩調色盤生成與視覺化 |

## 在工作流中的位置

```
design.md（本概念）
    ↓ 輸入到
Claude Design → Design System → Wireframe → Prototype → Animations
    ↓ 匯出到
Claude Code → 真實應用程式
```

## 參考來源

- [[wiki/sources/2026-08-13-claude-design-youtube|How To Use Claude Design To Build Beautiful Sites]]
- [Google Design Systems](https://design.google/)

## 相關頁面

- [[wiki/entities/claude-design|Claude Design]] — 使用 design.md 的設計工具
- [[wiki/entities/claude-code|Claude Code]] — 讀取 design.md 的 AI Coding Agent
