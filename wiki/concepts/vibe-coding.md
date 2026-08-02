---
title: "Vibe Coding — 自然語言驅動的程式碼生成"
type: concept
created: 2026-08-02
updated: 2026-08-05
sources: 1
tags: [vibe-coding, ai-coding, software-engineering, security, application-security]
collection: concepts
topics: [ai-agent]
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

## 最佳實踐

### 安全檢查（不可省略）
- Human code review
- Static and dynamic analysis
- Software composition analysis
- Secrets scanning
- IaC checks

### 輸入/輸出控制
- Prompt hygiene
- Output validation against security policies
- Guardrails 限制高風險操作

### 團隊準備
- Security awareness for AI workflows
- AI limitations training
- Incident response readiness

## 與其他概念的關係

| 概念 | 關係 |
|------|------|
| [[wiki/concepts/ai-coding-workflow]] | 互補：structured workflow 是 vibe coding 的解方 |
| [[wiki/concepts/minimal-agent-philosophy]] | 對比：Pi 的「減法」vs vibe coding 的「加法」 |
| [[wiki/concepts/defect-metrics]] | 相關：vibe coding 改變了缺陷分佈模式 |

## 來源

- [[wiki/sources/2026-08-02-vibe-coding-implications]] — Blackduck 部落格文章
