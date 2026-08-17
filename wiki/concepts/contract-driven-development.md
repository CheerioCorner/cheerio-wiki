---
type: concept
date: 2026-08-17
tags: [ai-agent, frontend, workflow, design-system]
related: [wiki/sources/2026-08-17-frontend-ai-roundtable, wiki/concepts/context-decay]
---

# Contract-Driven Development（契約驅動開發）

> 用結構化契約作為 AI 模型間交接的唯一真相來源，取代自然語言聊天的不確定性。

## 定義

在多 AI 模型協作的前端開發流程中，用一份版本化的結構化文件（如 `design-contract.yaml`）記錄產品需求、設計決策、技術約束與驗收標準，作為每次模型交接的唯一真相來源。

## 為什麼需要

1. **自然語言交接有衰減**：每次跨模型傳遞資訊，決策脈絡會逐漸遺失
2. **被否決的方案會重複嘗試**：沒有記錄「為什麼不選其他方案」，下一棒會重新踩坑
3. **缺乏可驗證的交付標準**：沒有一致的 acceptance criteria，品質參差不齊

## 契約應包含的欄位

```yaml
product_goal:           # 產品目標
user_flows:             # 使用者流程
design_system:          # 設計系統（色彩、字體、間距）
routes:                 # 路由規劃
components:             # 元件定義（Props、Events）
technical_constraints:  # 技術約束
acceptance_criteria:    # 驗收標準
rejected_alternatives:  # 被否決的替代方案（含原因）
open_questions:         # 未解決的問題
artifacts:              # 產出物清單
validation_status:      # 驗證狀態
```

## 實作流程

1. **探索期**（Gemini 主導）：產出初始契約 + 專案骨架
2. **實作期**（Claude 主導）：基於契約實作核心元件，更新契約
3. **交付期**（Codex 主導）：執行驗收測試，確認契約條件全部滿足

## 關鍵原則

- **每次交接都先更新契約，再修改程式碼**
- **交付標準以「契約 + 可運行產品 + 驗收結果」為準**
- **契約是活文件，不是一次性交付物**
