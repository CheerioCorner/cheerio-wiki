---
title: "Spec-Driven Development (SDD)"
type: concept
created: 2026-08-18
updated: 2026-08-18
sources: 1
tags: [spec-driven-development, sdd, software-engineering, code-quality, security, ai-coding]
topics: [ai-development-tools, ai-agent]
canonical: concepts/spec-driven-development
---

# Spec-Driven Development (SDD)

> 以精確規格（Spec）作為單一事實來源，引導程式碼生成並驗證結果的方法論。在 AI 時代，是補足 vibe coding 安全缺陷的關鍵機制。

## 核心定義

**Spec-Driven Development（SDD，規格驅動開發）**是一種開發方法論，核心原則：
- **精確描述**：用精確的規格定義系統需要做什麼
- **規格即驗證**：生成的程式碼必須通過規格定義的測試
- **單一事實來源（Single Source of Truth）**：規格是唯一的 truth，程式碼是規格的實作

## 為何需要 SDD？

### Vibe Coding 的安全缺陷

Vibe coding（用自然語言和 AI 工具生成程式碼）降低了 coding 門檻，但也移除了防止不安全程式碼的防護。

**關鍵數據**：45% 的 AI 生成程式碼存在安全問題或安全弱點。

### Self-Approval Loophole 案例

以費用審核 App 為例：
1. 使用者用 prompt 描述需求：「建立一個費用審核 App」
2. AI 根據 prompt 生成完整的應用程式
3. **問題**：沒有人的 prompt 說過「員工不能審核自己的費用」
4. **結果**：我可以提交 $900 費用然後自己審核通過

這就是為什麼需要 SDD——prompt 不夠精確，需要更正式的規格來定義業務規則。

## SDD 的核心機制

### 1. 規格撰寫（Spec Writing）

將原型探索的發現寫成正式規格：
- 業務規則（Business Rules）
- 驗收標準（Acceptance Criteria）
- 安全需求（Security Requirements）
- 例如：「沒有人可以審核自己的費用」

### 2. 規格即測試（Spec as Test）

規格定義的規則成為自動化測試：
- 生成的程式碼必須通過這些測試
- 測試失敗 → 程式碼有問題 → 需要修正

### 3. 迭代驗證（Iterative Verification）

- Prototype 教會我們的东西被寫進 spec
- Spec 定義測試
- 生成的程式碼必須通過測試
- 通過後才能進 production

## SDD 與其他方法論的比較

| 方法論 | 規格來源 | 驗證方式 | 適用場景 |
|--------|---------|---------|---------|
| **TDD** | 開發者手寫測試 | 測試通過 = 完成 | 單元測試、功能測試 |
| **BDD** | 行為規格（Given-When-Then） | 行為測試通過 | 使用者行為驗證 |
| **SDD** | 正式規格文件 | 規格定義的測試通過 | AI 生成程式碼驗證 |
| **Vibe Coding** | 自然語言 prompt | 無正式驗證 | 快速原型、探索 |

## RAD 的雙半結構

James Martin 的 RAD 方法論原本就有兩半：
1. **精確描述（Spec）**：系統需要做什麼
2. **程式碼生成（Code Gen）**：根據 spec 生成可運作的程式碼

現代 AI agents 補上了 code gen 半邊，但 spec 半邊需要由 SDD 來補足。

## 實作範例

### 費用審核 App 的 SDD 實作

**規格（Spec）：**
```markdown
## 業務規則
1. 員工可以提交費用報告
2. 費用報告必須由**非提交者**的主管審核
3. 單筆費用超過 $500 需要兩人簽核
4. 審核者不能是費用提交者本人

## 驗收標準
- [ ] 員工 A 提交費用後，員工 A 無法審核自己的費用
- [ ] 員工 B（主管）可以審核員工 A 的費用
- [ ] 單筆 $600 費用需要兩人簽核
```

**測試（Test）：**
```python
def test_self_approval_blocked():
    expense = create_expense(employee="A", amount=900)
    result = approve_expense(expense, approver="A")
    assert result.status == "BLOCKED"
    assert result.reason == "Self-approval not allowed"
```

**生成的程式碼必須通過這些測試。**

## 與其他概念的關係

| 概念 | 關係 |
|------|------|
| [[wiki/concepts/vibe-coding]] | 互補：SDD 補足 vibe coding 的安全缺陷 |
| [[wiki/concepts/rapid-application-development]] | 歷史先例：RAD 的 spec 半邊 |
| [[wiki/concepts/ai-coding-workflow]] | 現代實踐：SDD 是 AI coding workflow 的核心機制 |
| [[wiki/concepts/defect-metrics]] | 相關：SDD 改善了 AI 生成程式碼的缺陷分佈 |

## 來源

- [[wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding]] — YouTube 影片來源（45% 安全問題數據）

## 相關頁面

- [[wiki/concepts/rapid-application-development]] — RAD 方法論
- [[wiki/concepts/vibe-coding]] — Vibe Coding
- [[wiki/entities/james-martin]] — James Martin
