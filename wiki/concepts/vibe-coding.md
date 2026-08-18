---
title: "Vibe Coding — 自然語言驅動的程式碼生成"
type: concept
created: 2026-08-02
updated: 2026-08-18
sources: 2
tags: [vibe-coding, ai-coding, software-engineering, security, application-security]
collection: concepts
topics: [ai-agent, ai-development-tools]
canonical: concepts/vibe-coding
---

# Vibe Coding — 自然語言驅動的程式碼生成

> 用自然語言和 AI 工具生成和優化程式碼的開發方式，降低門檻但引入新的安全挑戰。

## 核心概念

**Vibe coding** 是一種開發方式，使用者用自然語言描述意圖，AI 工具生成程式碼。它標誌著程式碼創建方式的根本轉變，讓想法以前所未有的速度從 prototype 到 production。

### 核心矛盾
- ✅ 降低 coding 門檻，讓更多人能「寫 code」
- ❌ 移除了防止不安全程式碼的防護
- ⚠️ 挑戰了 intentional design、modularity、readability 等核心原則

### 角色轉變
開發者的角色從「寫 code」轉向「驗證 AI 輸出的意圖、品質和安全性」：
> "This marks a significant evolution from building code to curating it."

## 關鍵風險

### AI 特有風險
- **Prompt injection and data poisoning**
- **Tool and permission misuse**（Agent 擴權）
- **Insecure code patterns**（AI 複製已知漏洞）
- **Untraceable provenance**（缺乏 commit history）
- **Model supply chain attacks**
- **"Shadow AI" and policy bypass**

### 治理缺口
- 組織採用 AI 工具的速度快於制定政策
- 缺少 approved tool list 和 formal review processes
- 需要新的 standards 來處理 AI code provenance

## 關鍵案例：Self-Approval Loophole

影片《RAD Methodology for AI Vibe Coding》用費用審核 App 舉例：

1. 使用者用 prompt 描述需求：「建立一個費用審核 App」
2. AI 根據 prompt 生成完整的應用程式
3. **問題**：沒有人的 prompt 說過「員工不能審核自己的費用」
4. **結果**：我可以提交 $900 費用然後自己審核通過

這就是為什麼需要 [[wiki/concepts/spec-driven-development|Spec-Driven Development]]——prompt 不夠精確，需要更正式的規格來定義業務規則。

## 最佳實踐

### 安全檢查（不可省略）
- Human code review
- Static and dynamic analysis
- Software composition analysis
- Secrets scanning
- IaC checks
- **Spec-driven development**（規格驅動驗證）

### 輸入/輸出控制
- Prompt hygiene
- Output validation against security policies
- Guardrails 限制高風險操作

### 團隊準備
- Security awareness for AI workflows
- AI limitations training
- Incident response readiness

## RAD 四階段與 Vibe Coding

Vibe coding 可以看作是 [[wiki/concepts/rapid-application-development|RAD 方法論]]的現代實踐：

| RAD 階段 | Vibe Coding 對應 |
|----------|------------------|
| Requirements Planning | Prompt 成為需求文件 |
| User Design | AI 快速生成可運作原型 |
| Construction | AI 持續產出功能代碼 |
| Cut Over | 需要 [[wiki/concepts/spec-driven-development|SDD]] 驗證安全性 |

## 與其他概念的關係

| 概念 | 關係 |
|------|------|
| [[wiki/concepts/rapid-application-development]] | 歷史先例：RAD 的四階段重新可行 |
| [[wiki/concepts/spec-driven-development]] | 補位：SDD 補足 vibe coding 的安全缺陷 |
| [[wiki/concepts/ai-coding-workflow]] | 互補：structured workflow 是 vibe coding 的解方 |
| [[wiki/concepts/minimal-agent-philosophy]] | 對比：Pi 的「減法」vs vibe coding 的「加法」 |
| [[wiki/concepts/defect-metrics]] | 相關：vibe coding 改變了缺陷分佈模式 |

## 來源

- [[wiki/sources/2026-08-02-vibe-coding-implications]] — Blackduck 部落格文章
- [[wiki/sources/2026-08-18-rad-methodology-for-ai-vibe-coding]] — YouTube 影片：RAD Methodology for AI Vibe Coding
