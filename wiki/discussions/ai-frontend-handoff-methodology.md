---
title: "把前端交給 AI 的有效方法論（研究種子）"
type: discussion
created: 2026-08-14
updated: 2026-08-14
sources: 1
tags: [ai-frontend, methodology, handoff, design-workflow, research-seed]
topics: [ai-development-tools]
status: draft
---

> 研究問題：**在開發前端時，怎麼把工作交給 AI 才「有效」？** 尤其著眼於企業推動——一套可複製、可驗證、能守住品牌與品質的前端 AI 協作方法論。這是一顆研究種子（尚未定案），第一份素材是 [[wiki/sources/2026-08-13-claude-design-youtube|Claude Design 五階段工作流影片]]，但目標不綁單一工具。

## 為什麼值得做（企業角度）

- 前端是「客戶第一眼」的門面，AI 生成又最容易落入 **AI-slop（千篇一律的通用外觀）**——企業要的是品牌差異化，這正是痛點。
- 「把設計/前端交給 AI」目前多靠個人手感，缺一套**可教、可稽核、可規模化**的流程。若能沉澱出方法論 + 一組驗證過的實務，對團隊導入價值高。
- 影片裡大量步驟其實是**可遷移原則**（見來源筆記的「方法論原則」段），而且**很多可以我們自己實測**——適合做成「假設→實驗→結論」的研究。

## 核心假設（可實測）

以下每條抽自影片宣稱，設計成可驗證的實驗。H = Hypothesis。

| # | 假設 | 影片依據 | 如何驗證 | 量測指標 |
|---|------|---------|---------|---------|
| H1 | 先給 **design.md / 設計規格檔**，再讓 agent 生成，比純 prompt 更 on-brand、返工更少 | [03:18] | A/B：同需求，一組有 spec 檔一組沒有，各跑 N 次 | on-brand 命中率、修改輪數、token 用量 |
| H2 | 提供 **anti-slop references** 能顯著降低「通用 AI 外觀」 | [05:51][03:49] | 盲評：有/無 anti-slop 的產出，找人分辨「像不像 AI 做的」 | 盲評「像 AI」比例、設計相似度 |
| H3 | **漸進保真**（先 wireframe 鎖結構再做完整設計）比直接生成完整頁，總成本更低 | [09:22] | 兩條路徑做同一頁，記錄到「可接受成品」的總 token 與時間 | 總 token、總時長、重做次數 |
| H4 | **錨定式回饋**（選中元素+comment）比等值的純文字 prompt 更準、更省 | [10:12] | 同一組修改需求，用 comment vs 純文字各做一輪 | 一次到位率、修改輪數、token |
| H5 | **批次送回饋** 比逐條送，總等待/成本更低 | [10:38] | 5 條修改：一次送 vs 逐條送 | 總時長、總 token |
| H6 | **生成前先可視化審查 spec**（如 designmd.space）能減少昂貴的生成重跑 | [07:36] | 有/無「生成前預覽」兩流程比較 | 生成次數、達標前總 token |
| H7 | **AI→AI 交接後做 deep review** 能抓到視覺看不出的問題（如 RWD 破版） | [14:01] | 交接後一組做 deep review 一組不做，測多螢幕尺寸 | 缺陷數（RWD/a11y）、逃逸到人工的缺陷 |
| H8 | **人先定 schema/高風險結構、AI 只做建構**，比全交給 AI 更穩 | [16:19] | 資料模型由人預先定 vs 交給 agent，比較後續修正量 | schema 返工、資料相關 bug 數 |

> 註：H1–H8 多數以 Claude Design/Claude Code 為情境提出，但假設本身應設計成**工具無關**，最好在 ≥2 種工具鏈上重跑，才能判斷是「方法論」還是「某工具特性」。

## 待釐清（open questions）

- 這些原則在**團隊/企業**情境（多人協作、design system 治理、交付驗收）如何落地？影片只示範單人。
- design.md（Google 格式）與既有企業 design token / Figma 流程如何整合？是取代還是並存？
- 「effort level / 模型選擇」對成本–品質曲線的實際影響？影片主張 Opus 5 + medium，需自測。
- 交接與驗證環節能否自動化成 CI 檢查（RWD、a11y、design-spec 對齊）？

## 下一步

1. 從 H1–H8 挑 1–2 條**最便宜、最能立刻做**的先跑（建議 H1、H4）。
2. 補充第二、三份素材（非 AI LABS 立場的來源），避免 n=1 偏誤。
3. 成熟後，依「花園寫入」規則推進：**先開圓桌會議 → Claude + Gemini 品質確認 → 才寫入 Notion 花園**（本地大腦這頁先當草稿孵化）。

## 來源

- [[wiki/sources/2026-08-13-claude-design-youtube|How To Use Claude Design To Build Beautiful Sites]]（原始逐字稿 [[raw/youtube/how-to-use-claude-design-to-build-beautiful-sites|link]]）

## 相關頁面

- [[wiki/concepts/ai-coding-workflow|AI Coding Workflow]] — 結構化 AI 編碼工作流程（後端/整體，本頁聚焦前端交接）
- [[wiki/concepts/design-md-format|design.md Format]] — spec-first 的規格檔格式
- [[wiki/entities/claude-design|Claude Design]] — 第一份素材使用的工具
