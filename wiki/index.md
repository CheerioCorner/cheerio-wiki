# Wiki Index

> 由 LLM 維護。每次 ingest / lint / 新增頁面後更新。

> 任務:看我就知道這個 wiki 裡有什麼。

---

## 概觀

- **建立日期**  :2026-07-11
- **範疇**      :AI Agent 框架 / Coding Agent / Pi 生態
- **總來源數**  :3
- **總頁面數**  :10
- **新手入口**  :[[快速開始]]—— 三種收集資料的方法與基本操作

---

## Sources(來源筆記)

- [[2026-02-10-pi-agent-core-design]] — 知乎王鹏LLM,針對 `packages/agent/src/` 五支檔的應用碼級解剖 — 來源數 1
- [[2026-05-02-pi-mono-framework-tw]] — ai-chain.tw 繁中長文,從整個 monorepo 與代理人生態看 Pi — 來源數 1
- [[2026-07-11-plannotator-research]] — GitHub repo + 官網研究,功能摘要與自定義開發評估 — 來源數 1

---

## Entities

- [[pi-mono]] — `badlogic/pi-mono` monorepo + 5 個子 package。
- [[pi-agent-core]] — agent runtime,5 檔 / 1,500 行,深度抽象解析。
- [[mario-zechner]] — 作者。「An autonomous agent is just an LLM + tools + a loop.」
- [[plannotator]] — AI coding agent 視覺化審閱工具，支援 9 種 agent（Pi、Claude Code、Copilot 等）。

(以下子 package 本階段不獨立成頁·以 section 形式收錄在 [[pi-mono]]:[[pi-ai]]、[[pi-coding-agent]]、[[pi-tui]]、[[pi-web-ui]])

---

## Concepts

- [[late-conversion]] — TS Declaration Merging 實作的「最晚轉換」型別策略。
- [[minimal-agent-philosophy]] — 「減法大於加法」哲學,2 來源、兩視角交點。
- [[meta-harness]] — 對 harness 的再抽象/最佳化層，有學術（自動演化 harness）與業界（多 agent orchestration）兩種定義。

(以下為階段性「tag-on-page」概念,未獨立開頁。靈記則睨[[pi-agent-core]]或 [[pi-mono]]:
[[minimal-agent-loop]] / [[observability-layer]] / [[steering-followup]] / [[yolo-by-default]] / [[provider-abstraction]] / [[declaration-merging]])

---

## Comparisons

(目前尚無)

> 計劃中:[[coding-agent-comparison]]—— Claude Code / Codex / Pi 五維對比表,由 A 篇供給。等待人類匯入後再開。

---

## Synthesis

- [[minimal-agent-philosophy]] — 跨兩來源的綜合。

---

## 待辦 / 缺失概念

- **不獨立出任 page**:`pi-ai` / `pi-tui` / `pi-web-ui` / `pi-coding-agent` ——暫收進 [[pi-mono]]·未來 lobe 需求開出獨立頁。
- **未決**:是否要 ingest Zechner 原 blog「What I learned building an opinionated and minimal coding agent」(2025-11-30)作為 A 篇的上游文獻。
