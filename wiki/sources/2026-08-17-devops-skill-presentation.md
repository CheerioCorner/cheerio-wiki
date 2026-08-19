---
title: "如何寫好一支 SKILL？· DevOps Board 系列（簡報來源筆記）"
type: source
created: 2026-08-17
updated: 2026-08-17
sources: 1
tags: [skill, design-methodology, devops, presentation, eva-air]
topics: [skill-system, skill-presentation-design, ai-development-tools]
canonical: sources/2026-08-17-devops-skill-presentation
provenance:
  - kind: local
    path: "Report/devops-skill-presentation/"
    description: "Cheer 自製 31 頁 HTML 簡報，非外部來源。2026-08 系統架構課分享會用。"
---

# 如何寫好一支 SKILL？· DevOps Board 系列（簡報來源筆記）

> Cheer 自製 31 頁簡報，以 Azure DevOps Board 系列 13 支 skill 為案例，講解複雜系統的 Skill 設計方法論。2026-08 系統架構課分享會用。

## 簡報結構（5 幕 31 頁）

| 幕 | 頁數 | 主題 |
|---|------|------|
| 前導 | P01-P06 | 封面 → 常見作法 → Skill 基本結構 → 複雜系統痛點 → DevOps 複雜度 |
| Part 1 | P07-P11 | 從一句人話到可切的邊界：北極星案例 → Event Storming → 切邊界 → 理論認祖歸宗 |
| Part 2 | P12-P17 | 技術不是起點，意圖才是起點：Intention-First → 小型決策樹 → 12 條 Human 意圖 → 完整決策樹 → Domain Know-how |
| Part 3 | P18-P19 | 先證明它能做事：Demo |
| Part 4 | P20-P28 | 從會用 Skill 到會做 Skill：Meta-Skills Loop → 安全邊界 → 撰寫技巧 → 通用規格 → 組合技 → SDLC → 限制 → Beyond Skill |
| 結尾 | P29-P31 | 可帶走的心法 → Q&A → References |

## 關鍵新概念（既有 Agent Skills 專題未涵蓋）

### 1. Intention-First 設計哲學（P13）
- 分析順序：Human Intention → Event → Boundary → Skill
- 先問「為什麼要更新」「這次代表什麼狀態變化」，再問「有哪些 API」
- 核心：系統設計要從人的意圖開始往下拆

### 2. 12 條 Human 意圖分類（P15）
- 六大類別：Collection(2) / Project(2) / Team(2) / Board(2) / Work(3) / Analytics(3)
- 不是需求規格文件，是人類用自然語言把「要做的事」與領域脈絡講清楚

### 3. 決策樹方法（P16）
- 「流程圖，就是規格」
- 每個葉節點 = 一支 skill 的邊界
- 12 意圖 + 1 獨立 Auth = 13 支 skill

### 4. Domain Know-how 地基概念（P17）
- 三層堆疊：地基（Domain Know-how）→ 規格（意圖決策樹）→ 成品（13 支 Skills）
- 「流程圖不是起點，領域知識才是」
- 地基不穩，上面的流程圖與 skill 都會塌

### 5. Event Storming 拆邊界（P09-P10）
- 把「發生的事」攤在時間軸上
- 動詞不一樣的地方（寫 vs 讀），就是邊界
- 對應 DDD 的 Bounded Context / 單一職責 / Design-First

### 6. Meta-Skills Loop（P21）
- Skills Help Skills：做 Skill 的也是 Skill
- 迴圈：grill-me（反問）→ find-skills（找現成）→ skill-creator（生第一版）→ plannotator（視覺化對齊）→ feedback/memory（沉澱經驗）

### 7. Write-back 安全邊界（P22）
- 四步安全門：--dry-run → 人類確認 → --confirm → 讀回驗證
- 三級分類：要（明確事實）/ 小心（動到別人資料）/ 不要（AI 推測幻想）
- 核心：不可逆的事，人類永遠先看過一眼

### 8. Skill 組合技（P25）
- 複雜情境需要 skill 鏈式協作
- 範例：work-query → wiki → analytics → work-maintainer

### 9. Skill SDLC（P26）
- 完整 8 階段：需求 → 分析 → 設計 → 開發 → 測試 → 部署 → 版控 → 迭代
- 對應 P05 的六個痛點

### 10. Skill 的限制（P27）
- LLM 四大失敗模式：選錯 skill / 誤解意圖 / 跳過步驟 / 幻覺
- 對應四道閘門：好的 description / 安全門與確認點 / 真實環境測試 / 必要時交給 Hook/Extension

### 11. Beyond Skill（P28）
- Skill：讓 AI 變懂，提供知識與流程（被動，要靠人類記得叫）
- Hook：事件驅動，攔截、檢查、強制規則（主動，不必人類每次記得叫）
- Agent/Extension：把角色、權限、工具封裝成執行單元（平台層能力延伸）

## 與既有 Agent Skills 專題的關係

- 既有專題聚焦「怎麼寫」（best practices）和「怎麼用」（API）、「怎麼管」（governance）
- 簡報補齊的是更上游的「怎麼決定邊界」（設計方法論）和更下游的「碰到限制之後呢」（Beyond Skill）
- 兩者完美互補，無衝突

## 來源

- 本機簡報：`Report/devops-skill-presentation/`（31 個 HTML slide + README）
- 非外部 URL，Cheer 自製簡報
