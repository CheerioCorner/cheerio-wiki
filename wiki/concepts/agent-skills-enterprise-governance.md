---
title: "Agent Skills 企業治理"
type: concept
created: 2026-08-14
updated: 2026-08-17
sources: 2
tags: [skill, enterprise, governance, security, lifecycle]
topics: [skill]
canonical: concepts/agent-skills-enterprise-governance
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-企業級-skills.md"
---

# Agent Skills 企業治理

> 企業大規模導入 Agent Skills 的安全審查、部署評估、生命週期管理與組織策略。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的基礎層。**

## 安全審查框架

### 5 級風險評估

在核准部署前，根據風險指標評估每個 Skill：

| 風險指標 | 關注程度 | 說明 |
|---------|---------|------|
| 程式碼執行 | 🔴 高 | 腳本以完整環境存取權限執行 |
| 指令操縱 | 🔴 高 | 可能繞過安全控制 |
| MCP 伺服器引用 | 🔴 高 | 存取範圍擴展到 Skill 之外 |
| 網路存取模式 | 🔴 高 | 潛在資料外洩途徑 |
| 硬編碼憑證 | 🔴 高 | 機密暴露在 Git 歷史和 context |
| 檔案系統存取 | 🟡 中 | 可能存取非預期資料 |
| 工具呼叫 | 🟡 中 | 審查執行了哪些操作 |

### 8 步審查檢查清單

1. 閱讀所有 Skill 目錄內容
2. 驗證腳本行為與聲明目的相符（沙箱執行）
3. 檢查對抗性指令（忽略安全規則、隱藏操作）
4. 檢查網路存取模式（http/requests/curl/fetch）
5. 驗證沒有硬編碼憑證
6. 識別所有工具和命令引用
7. 確認外部 URL 重新導向目的地
8. 驗證沒有資料外洩模式

> ⚠️ 絕不要在未經完整稽核的情況下部署來自不受信任來源的 Skills。

## 部署前評估

### 5 個評估維度

| 維度 | 衡量內容 | 失敗範例 |
|------|---------|---------|
| 觸發準確性 | 正確查詢啟動、無關查詢不啟動 | 每次提到試算表都觸發 |
| 隔離行為 | 單獨運作正確 | 引用不存在的檔案 |
| 共存性 | 不降低其他 Skills 效能 | 描述過廣，搶走觸發 |
| 指令遵循 | Claude 準確遵循指令 | 跳過驗證步驟 |
| 輸出品質 | 產生正確有用的結果 | 格式錯誤或資料遺失 |

### 評估要求

- 每個 Skill 提交 **3–5 個代表性查詢**
- 涵蓋：應觸發 / 不應觸發 / 模糊邊緣案例
- 在組織使用的模型上測試（Haiku/Sonnet/Opus）

## 生命週期管理（6 階段）

```
規劃 → 建立與審查 → 測試 → 部署 → 監控 → 迭代或棄用
```

1. **規劃**：識別重複性高、容易出錯的工作流程
2. **建立與審查**：遵循最佳實踐 + 安全審查清單 + 評估套件（職責分離）
3. **測試**：隔離評估 + 共存評估
4. **部署**：Skills API 上傳 + 內部登錄
5. **監控**：使用模式追蹤 + 定期重新評估
6. **迭代或棄用**：新版本需完整評估套件通過

### Skill SDLC（開發者視角）

> 2026-08-17 新增。從開發者角度的完整 8 階段生命週期，與上述組織治理流程互補。

| 階段 | 做什麼 | 對應工具/方法 |
|------|--------|-------------|
| 1. 需求 | 反問逼出真意圖 | grill-me |
| 2. 分析 | 12 條意圖起手 | [[wiki/concepts/skill-design-methodology|Intention-First]] |
| 3. 設計 | 流程圖即是規格 | Decision Tree |
| 4. 開發 | 產出 skill 骨架 | skill-creator |
| 5. 測試 | 真實環境真的跑 | plannotator UI |
| 6. 部署 | 統一發佈的管道 | GitHub Enterprise |
| 7. 版控 | 留下每次修改紀錄 | branch / PR / tag |
| 8. 迭代 | 回到需求，重新開始 | memory feedback |

**核心**：打造 Skill 的生命週期，跟打造軟體的生命週期，本質上沒有不同；差別只在最後產出的，從程式碼變成了 **skill 資產**。

> 與 [[wiki/concepts/skill-authoring-best-practices#寫入安全模式write-back-safety-gate|Write-back Safety Gate]] 的關係：SDLC 的「測試」階段應包含寫入安全驗證（dry-run → 人類確認 → confirm → 讀回驗證）。

## 大規模組織策略

### 召回限制

- 每個 Skill 的中繼資料在系統提示中**競爭注意力**
- Skills 過多 → Claude 可能選錯或錯過
- **每請求最多 8 個 Skills**（API 限制）
- 超過時考慮整合窄範圍 Skills 為廣範圍 Skills

### 從具體開始，之後再整合

```
開始：formatting-sales-reports + querying-pipeline-data + updating-crm-records
整合：sales-operations（當評估確認效能相當時）
```

### 命名與編目

為每個 Skill 維護內部登錄：

- **目的**：支援的工作流程
- **擁有者**：維護團隊/個人
- **版本**：目前部署版本
- **相依性**：MCP 伺服器、套件、外部服務
- **評估狀態**：最後評估日期和結果

### 基於角色的套件

- **銷售團隊**：CRM 操作、管道報告、提案產生
- **工程**：程式碼審查、部署工作流程、事件回應
- **財務**：報告產生、資料驗證、稽核準備

## 發佈與版本控制

- **原始碼控制**：Git 追蹤（歷史 + PR 審查 + 回滾）
- **基於 API 的發佈**：工作區範圍共享
- **版本策略**：生產固定版本 / 開發用 latest / 回滾計畫
- **完整性驗證**：校驗和 + 簽署提交
- **跨介面**：Git 為唯一真實來源，各介面分別同步

## 來源

- [[wiki/sources/2026-08-14-agent-skills-enterprise-deployment|企業級部署指南]] — Anthropic 官方企業文件

## 相關頁面

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — 上層導航
- [[wiki/entities/anthropic-agent-skills|Anthropic Agent Skills]] — Skill 系統本體
- [[wiki/concepts/skill-authoring-best-practices|Skill 撰寫方法論]]
- [[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]] — 企業 AI 治理層
