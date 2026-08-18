---
title: "Rapid Application Development (RAD)"
type: concept
created: 2026-08-18
updated: 2026-08-18
sources: 1
tags: [rad, rapid-application-development, software-engineering, iterative-development, prototyping]
topics: [ai-development-tools, ai-agent]
canonical: concepts/rapid-application-development
---

# Rapid Application Development (RAD)

> 1991 年 James Martin 提出的軟體開發方法論，強調速度、迭代開發與使用者反饋，核心假設是「使用者在看到東西之前不知道自己要什麼」。

## 核心定義

**RAD（Rapid Application Development）**是一種軟體開發方法論，偏好：
- **速度**：快速交付 working systems
- **迭代開發**：短週期（~90 天）的迭代
- **使用者反饋**：使用者在開發過程中持續影響設計

相對於當時主流的 waterfall 方法論（大量前期規劃），RAD 認為詳細的前期規劃是浪費時間，因為使用者在看到東西之前不知道自己要什麼。

## 四階段模型

```
Requirements Planning → User Design → Construction → Cut Over
       ↓                   ↓              ↓            ↓
    定義問題          建立原型        功能建構      部署上線
```

### Phase 1: Requirements Planning（需求規劃）

輕量級規劃，定義：
- 要解決的問題
- 受影響的使用者
- 想要的功能
- 需要注意的限制

**重點**：不需要詳細規格，因為假設使用者在看到東西之前不知道自己要什麼。

### Phase 2: User Design（使用者設計）

- 團隊快速建立**可點擊的原型**
- 放在真實使用者面前
- 任何錯誤假設會在早期被發現（修復成本低）
- **Prototype 是 Keeper**：會持續建構直到成為實際產品

### Phase 3: Construction（建構）

- 實際應用功能建構
- **短週期迭代**：測試和反饋持續進行，而非等到最後
- Martin 的書提到小團隊在 ~90 天內交付 working systems

### Phase 4: Cut Over（切換）

- 部署到 production
- 資料遷移
- 使用者培訓

## 歷史脈絡

| 年份 | 事件 |
|------|------|
| 1982 | James Martin 出版《Application Development Without Programmers》 |
| 1991 | 正式形式化 RAD 方法論 |
| 1990s | 因 CASE 工具功能不足，RAD 未廣泛流行 |
| 2020s | AI agents 讓 prototyping 變得超快，RAD 四階段重新可行 |

## 為何 RAD 沒流行？

RAD 的 code generators（CASE 工具）在當時無法建構複雜應用。沒有好的程式碼生成器，「快速建立原型」的承諾無法實現。

## 現代復甦：RAD × AI

AI agents 解決了 RAD 當年的痛點：
- **Code gen 半邊**：AI agents 提供了強大的程式碼生成能力
- **Spec 半邊**：需要 spec-driven development 來補足（避免 AI 生成的程式碼有安全問題）

### 四階段的現代對應

| RAD 階段 | AI 時代對應 |
|----------|------------|
| Requirements Planning | Prompt（提示詞）成為需求文件 |
| User Design | AI 根據 prompt 快速生成可運作原型 |
| Construction | AI 持續產出 data schema、workflow logic 等 |
| Cut Over | 需要 spec-driven development 驗證安全性與邏輯 |

## 核心洞察

> "Plan lightly, prototype early, build in short cycles and don't move over into production until the thing has been verified."

這句話總結了 RAD 的精華，也完美描述了現代 AI 開發的最佳實踐。

## 與其他概念的關係

| 概念 | 關係 |
|------|------|
| [[wiki/concepts/vibe-coding]] | 現代實踐：AI 讓 RAD 的四階段重新可行 |
| [[wiki/concepts/spec-driven-development]] | 補位：RAD 的 spec 半邊需要 SDD 來補足 |
| [[wiki/concepts/ai-coding-workflow]] | 歷史先例：RAD 是 AI coding workflow 的歷史先驅 |
| [[wiki/concepts/agentic-ai]] | 執行引擎：AI agents 是 RAD 的 code building machine |

## 來源

- [[wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding]] — YouTube 影片來源
- James Martin, *Rapid Application Development* (1991)
- James Martin, *Application Development Without Programmers* (1982)

## 相關頁面

- [[wiki/entities/james-martin]] — RAD 方法論提出者
- [[wiki/concepts/spec-driven-development]] — Spec-Driven Development
- [[wiki/concepts/vibe-coding]] — Vibe Coding
