# Wiki Index

> 維護規則：[[AGENTS|AGENTS.md]] · 目前工作：[[work/current|Current Work]]

> 由 LLM 維護。每次 ingest / lint / 新增頁面後更新。
> 任務：看我就知道這個 wiki 裡有什麼。

---

## 📚 AI Agent 學習系統

> 從「使用工具」到「理解原理」到「自己建造」的學習路徑。

- [[wiki/topics/pi-agent-learning/roadmap|🗺️ 學習路線圖]] — 四階段學習路徑
- [[wiki/topics/pi-agent-learning/progress|📊 學習進度]] — 追蹤每次學習成果
- [[work/designs/pi-agent-learning-system|📋 學習系統設計]] — 完整流程文件

### Pi Agent 更新追蹤

- [[wiki/entities/pi-agent/changelog/v0.84.0|v0.84.0 更新分析]] — 全螢幕 TUI、Mermaid 渲染、samplingParams

### 核心概念

- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought]] — 讓模型「先想再答」
- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — 模型資源分配

---

## 概觀

- **建立日期**：2026-07-11
- **最後更新**：2026-08-08（新增 guizang-ppt-skill + huashu-design）
- **範疇**：AI Agent 框架 / Coding Agent / Pi 生態 / 知識管理
- **Source notes**：26（全部已補 provenance）
- **Canonical collections**：15 concepts、27 entities、4 decisions、3 discussions
- **Project Bundles**：3（入口位於 root `projects/<project-id>/`）

---

## Topics（主題式分類）

> 點進 topic page 看該主題下的 entities 和 concepts。

- [[wiki/topics/ai-agent|🤖 AI Agent]] — Pi、Tau、Hermes、Waku、QwenPaw 等 agent 框架
- [[wiki/topics/pi-agent-learning|📚 AI Agent 學習]] — 從使用到建造的學習路徑
- [[wiki/topics/extension-dev|🔌 Extension 開發]] — Pi extension 生態、OKF、publishing
- [[wiki/topics/meta-systems|🧠 元系統]] — meta-harness 研究
- [[wiki/topics/backend-systems|🔧 後端系統]] — Redis、Caching、Database patterns
- [[wiki/topics/knowledge-mgmt|📚 知識管理]] — Obsidian wiki、Notion 花園、ingest 流程
- [[wiki/topics/skill|🛠️ Skill]] — AI coding skills、workflow

> 🛠️ = 同時屬於多個 topic（見各頁 frontmatter `topics`）

---

## Collections（內容集合）

> 每個 collection 有自己的 README，點進去看完整列表。

- [[wiki/concepts/README|Concepts]] — 可跨專案重用的抽象知識
- [[wiki/entities/README|Entities]] — 人、工具、package 與具體實作（含 [[wiki/entities/open-code-review|OpenCodeReview]] 19.3k ⭐、[[wiki/entities/learning-loop|Learning Loop]]）
- [[wiki/sources/README|Sources]] — 整理後的外部資料與研究紀錄（29 筆，含 [[wiki/sources/2026-08-20-opencode-review-deep-research|OCR Gemini 研究]]、[[wiki/sources/2026-08-21-langgraph-in-10-minutes|LangGraph in 10 Minutes]]、[[wiki/sources/2026-08-08-guizang-ppt-skill|guizang-ppt-skill]]、[[wiki/sources/2026-08-08-huashu-design|huashu-design]]、[[wiki/sources/2026-07-23-plannotator-copilot-setup|Plannotator Copilot 安裝手冊]]）
- [[wiki/decisions/README|Decisions]] — 全域／跨專案已確認選擇
- [[wiki/discussions/README|Discussions]] — 尚未定案的方案與研究問題

---

## Work & Projects

- [[work/current|Current Work]] — 目前工作、下一步與 references
- [[projects/pi-plannotator-auto/index|pi-plannotator-auto]] — ✅ Project OKF Bundle pilot
- [[projects/pi-web-access-zh-tw/index|pi-web-access-zh-tw]] — ⚠️ 需維護
- [[projects/pi-work-tracker/index|pi-work-tracker]] — 📋 取代 pi-todo-journal

---

## 進階式揭露

> 這是 OKF 的核心原則：先看 overview，再深入特定主題。

```
index.md（你這裡）
    ↓
Topic pages（6 個主題入口）
    ↓
Entities / Concepts（具體知識）
    ↓
Sources（原始來源）
```

---

## 待辦 / 缺失概念

- **未獨立成頁**：`pi-ai` / `pi-tui` / `pi-web-ui` / `pi-coding-agent` — 暫收進 [[wiki/entities/pi-mono|pi-mono]]
- **未決**：是否要 ingest Zechner 原 blog 作為上游文獻
- **✅ 已完成**：YouTube 字幕抓取方案（2026-08-03 實測成功）
