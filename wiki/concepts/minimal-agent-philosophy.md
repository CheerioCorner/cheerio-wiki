---
title: Minimal Agent Philosophy — 「減法大於加法」
type: concept
created: 2026-07-11
updated: 2026-07-11
sources: 2
tags: [concept, pi, philosophy]
collection: concepts
topics: [agent-architecture]
canonical: concepts/minimal-agent-philosophy
---

> 兩篇來源、兩個視角,**落到同一個結論**:agent 框架的價值不取決於堆了多少功能,而取決於它**拒絕排除了多少東西**。
>
> 該哲學可以總結為:**「自主 agent = LLM + tools + 一個迴圈」,其餘都是鍋。**

## 證據出處

### A 篇([[[../../raw/web/2026-02-10-pi-agent-core-design|2026-02-10-pi-agent-core-design]]])
- 「An autonomous agent is just an LLM + tools + a loop.」— 直接引用 Zechner
- 「前沿模型已經被 RL 訓練得足夠理解『編碼 Agent』是什麼。你不需要 10,000 token 的系統提示詞。」
- 實證:Pi Agent Core **5 檔 /約 1,500 行**,**4 個工具**(read / write / edit / bash),**系統提示 + 工具定義 < 1000 token**,在 Terminal-Bench 2.0 與 Codex / Cursor / Windsurf 同列榜。

### B 篇([2026-05-02-pi-mono-framework-tw])
- 「如果你在意的是 agent CLI 可不可以延伸成自己的工具鏈、可不可以接不同模型、可不可以把 workflow 做成 package、skill 或 extension,那它很值得仔細研究。」
- 「adapt pi to your workflows, not the other way around.」
- 5 個產品判斷、除 #3(provider 完備)外全部偏向「不內建,由擴充補充」。

## 「不做清單」的全貌
| 不做 | 替代方案 |
|---|---|
| 無 MCP | CLI + README 透過 `bash` 按需載入 |
| 無 sub-agents | 「黑盒中的黑盒」;改用 `bash` 自我呼叫 |
| 無 plan mode | 用 `PLAN.md` 文件替代,可版本控制、跨會話共享 |
| 無 permission popups | 「安全劇場」(security theater) |
| 無 maxSteps | 迴圈自然結束(原句:「我從來沒找到需要 maxSteps 的用例」) |
| 無 built-in to-dos | — |

→ 這是兩份來源拼接出來的。**繼續増接同類來源時,先檢查這張清單是否迭代表**。

## 為什麼這能成立

三個理由(由 A 篇技術細節 + B 篇產品取徑拼出):

1. **前沿模型已 RL 成熟**:它們理解「coding agent 是什麼」,**不需要 10000 token 軟糗**——所以工具不要堆。
2. **可觀測性的話,全押在「看得見」的設計上**:三層事件生命週期、partial message 就地更新、`bash` 自我呼叫所產出的 output 都是可見——所以不需要 sub-agent 來負擔複雜性。
3. **擴充邊界在 runtime 之外**:Skills / Extensions / Packages 把「你怎麼工作」的決定推給使用者。

## 何時「最小代理」應該是錯的

不適用於:
- 嚴格安全沙箱(互相矛盾——Pi 選擇 YOLO by default，不做權限檢查)
- 複雜多 Agent 編排(原文明言「與此立場相悖」)
- 需要即裝即用、絕低學習成本的消費產品(B 篇明示不適)

## 相關概念（散見於其他頁面）
- [[wiki/entities/pi-agent-core]] — 5 檔架構、三層事件生命週期、雙層 while 迴圈、steering/follow-up 雙 queue
- [[wiki/concepts/late-conversion]] — TS Declaration Merging 實作的「最晚轉換」型別策略
- [[wiki/entities/pi-mono]] — 「刻意不做」清單（無 MCP、無 sub-agents、無 plan mode、無權限檢查、無 maxSteps）

## 相關頁面
- Entities:[[wiki/entities/pi-mono]]、[[wiki/entities/pi-agent-core]]、[[wiki/entities/mario-zechner]]
- Concepts:[[wiki/concepts/vibe-coding]] — 對比：Pi 的「減法」vs vibe coding 的「加法」
- Sources:[[wiki/sources/2026-02-10-pi-agent-core-design]] / [[wiki/sources/2026-05-02-pi-mono-framework-tw]]
