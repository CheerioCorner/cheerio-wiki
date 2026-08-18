---
title: "James Martin"
type: entity
created: 2026-08-18
updated: 2026-08-18
sources: 1
tags: [james-martin, rad, software-engineering, methodology]
topics: [ai-development-tools]
canonical: entities/james-martin
---

# James Martin

> 資訊科技領域作家與先驅，1982 年出版《Application Development Without Programmers》，1991 年形式化提出 RAD（Rapid Application Development）方法論。

## 基本資訊

| 欄位 | 內容 |
|------|------|
| **身份** | 資訊科技作家、方法論提出者 |
| **主要貢獻** | RAD（Rapid Application Development）方法論 |
| **重要著作** | 《Application Development Without Programmers》(1982)、《Rapid Application Development》(1991) |

## 核心貢獻

### 1982：《Application Development Without Programmers》

預見了「無程式員的應用開發」趨勢，這個概念在 44 年後的今天（AI agents、vibe coding）正在成為現實。

### 1991：RAD 方法論

正式形式化 Rapid Application Development，核心主張：
- 偏好**速度**與**迭代開發**，反對 waterfall 的大量前期規劃
- **四階段模型**：Requirements Planning → User Design → Construction → Cut Over
- **核心假設**：使用者在看到東西之前不知道自己要什麼

### 雙半結構

James Martin 的方法論原本就有兩半：
1. **精確描述（Spec）**：系統需要做什麼
2. **程式碼生成（Code Gen）**：根據 spec 生成可運作的程式碼

現代 AI agents 補上了 code gen 半邊，但 spec 半邊需要由 [[wiki/concepts/spec-driven-development|Spec-Driven Development]] 來補足。

## 歷史意義

> "Application Development Without Programmers — 44 years on, that particular request is still waiting on its second sign off because the programmers, they are still very much here. They've just moved in the direction of writing the spec and verifying the result."

這段話總結了 James Martin 的預見與現代實現：
- 程式員仍然在，但角色改變了
- 從「寫 code」轉向「寫 spec + 驗證結果」

## 與現代 AI 開發的關聯

| James Martin 的概念 | 現代 AI 對應 |
|---------------------|-------------|
| RAD 四階段 | Vibe coding 開發流程 |
| Code generators（CASE 工具） | AI agents（agentic AI） |
| Spec 半邊 | Spec-Driven Development |
| Prototype is keeper | AI 生成的原型持續迭代 |

## 來源

- [[wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding]] — YouTube 影片來源

## 相關頁面

- [[wiki/concepts/rapid-application-development]] — RAD 方法論概念頁
- [[wiki/concepts/spec-driven-development]] — Spec-Driven Development 概念頁
- [[wiki/concepts/vibe-coding]] — Vibe Coding 概念頁
