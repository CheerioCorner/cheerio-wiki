---
title: Content 設計 Redesign
type: concept
created: 2026-08-08
updated: 2026-08-08
sources: 1
tags: [skill, knowledge-garden, content, architecture]
topics: [knowledge-mgmt, skill-cases-and-comparisons]
canonical: concepts/content-redesign
---

> 知識花園 Content 設計重構：從「套模板」到「針對性報告」。經 Copilot + Gemini 三輪討論確立。

---

## 核心問題

1. **只有種子** — 研究專題和視覺地圖的 content 是附帶的
2. **模板綁定成長階段** — 🌱/🌿/🌳 決定了內容結構，本末倒置
3. **缺少主觀層面** — 只有客觀事實，沒有人的決策、想法、判斷
4. **缺少 roadmap** — 沒有「下一步要往哪裡走」

---

## 新設計方向

```
content = 針對該標的物的專屬報告
         + 客觀事實（從來源提取）
         + 主觀現實（人的決策、想法、判斷）
         + roadmap（下一步延伸）
```

不是「套模板」，而是「針對這個題目，產出一份有觀點的報告」。

---

## 四個共識（Copilot + Gemini 一致同意）

### 共識 1：四層通用骨架

所有標的物（種子、研究專題、視覺地圖）的 content 都用同一個結構：

| 層 | 內容 | 說明 |
|----|------|------|
| 第一層 | 標的識別與中繼資料 | 類型、階段、標籤、關聯 |
| 第二層 | 客觀事實層 | 定義、規格、參考資料 |
| 第三層 | 主觀現實層 | 個人經驗、決策脈絡、思維模型 |
| 第四層 | 延伸路線圖 | 下一步行動、未解問題 |

### 共識 2：成長階段 ≠ 模板

🌱/🌿/🌳 只是成熟度狀態屬性，不決定內容結構。內容結構由標的物類型（種子/專題/地圖）決定。

### 共識 3：研究專題 ≠ 種子

| 標的物 | 本質 | 生命週期 | 重點 |
|--------|------|---------|------|
| 種子 | 無窮演進的知識節點 | 發現→理解→體系化 | 認知堆疊與維護 |
| 研究專題 | 目標驅動的有限收斂過程 | 📋→🔬→📚 | 假說驗證、有明確邊界與收斂終點 |

專題收斂（📚）時，會產出或更新種子和視覺地圖，形成良性輪轉。

### 共識 4：視覺地圖 Database Schema

視覺地圖是一個**索引/註冊表**，不是存放地圖內容的地方。實際內容存在子頁面。

| 欄位 | 類型 | 說明 |
|------|------|------|
| 頁面 | Title | 地圖名稱 |
| 類型 | Select | 專題地圖 / 種子地圖 |
| 關聯種子 | Relation → 種子 DB | 這張圖描述哪顆種子 |
| 關聯專題 | Relation → 專題 DB | 這張圖描述哪個專題 |
| 建立時間 | Created time | 自動 |
| 更新時間 | Last edited time | 自動 |

---

## 對 page-content skill 的影響

需要大改：
1. **移除模板綁定** — 不再按 🌱/🌿/🌳 選模板
2. **改為按標的物類型** — 種子/專題/地圖各有自己的 content 結構
3. **加入主觀現實層** — 目前完全缺少
4. **加入 roadmap** — 每份報告都要有下一步
5. **視覺地圖** — Database schema 需要手動加欄位

---

## 討論過程

- Round 1：確認 redesign 方向、三種標的物差異
- Round 2：確認四個共識
- Copilot 補充：加版本時戳、provenance
- Gemini 補充：用獨立 map_bindings 關聯表做多型綁定

---

## 相關頁面

- [[wiki/concepts/knowledge-garden-skill-architecture|知識花園 Skill 架構]]
- [[wiki/entities/knowledge-garden|knowledge-garden]] — 花園 manifest
