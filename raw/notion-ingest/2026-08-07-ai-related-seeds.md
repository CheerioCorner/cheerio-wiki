# Notion AI相關 Ingest — 待寫入花園的種子

> 來源：Notion「任何當下 → AI相關」頁面
> ingest 日期：2026-08-07
> 狀態：wiki 已完成，花園待寫入

## 種子 A：agentskills.io — AI Agent Skill 開放標準

**成長階段：** 🌱 種子期
**Tags：** 🔬 種子
**來源 URL：** https://agentskills.io/
**給我的啟發？：** Skill 開放標準讓跨平台成為可能。Pi 的 skill 系統已經走在前面，但格式標準化是趨勢。差異就是成長的空間。
**Wiki GitHub：** https://github.com/CheerioCorner/cheerio-wiki/blob/master/wiki/entities/agentskills-io-standard.md

### 種子故事

從 Notion「任何當下 → AI相關 → Skill」頁面發現的開放標準。agentskills.io 定義了跨平台 Skill 格式，讓同一個 Skill 可以在 GitHub Copilot、Claude Code、Gemini 等多個 AI 工具間流通。

### 比對分析

| 概念 | 來源的說法 | 我的做法 |
|------|-----------|---------|
| Skill 是可移植模組 | 指令+腳本+資源的組合 | Pi 的 `~/.agents/skills/` 結構一致 |
| 跨平台是趨勢 | 支援 Copilot/Claude/Gemini | Pi 目前是專用格式 |
| 分發 | `npx skills` Registry | 本地安裝/npm |
| 安全 | 建議企業審查 | 信任本地檔案 |

### 成長計畫

- [ ] 比較 Pi skill 格式 vs agentskills.io 標準的差異
- [ ] 評估 `npx skills` 的安全模型
- [ ] 關注社群中跨平台 Skill 的實際案例

---

## 種子 B：Axway Amplify AI Gateway — 企業 AI 治理層

**成長階段：** 🌱 種子期
**Tags：** 🔬 種子
**來源 URL：** https://www.axway.com/en/products/ai-gateway
**給我的啟發？：** 企業 AI 治理的核心是「集中化」：統一端點、集中安全、Token 審核。個人 Agent 雖不需要那麼重的治理，但 MCP Proxy 和 Semantic Caching 的概念值得關注。
**Wiki GitHub：** https://github.com/CheerioCorner/cheerio-wiki/blob/master/wiki/entities/axway-amplify-ai-gateway.md

### 種子故事

從 Notion「任何當下 → AI相關 → AI Gateway」頁面的 19 張截圖，透過 agy + Gemini 視覺分析提取出完整的企業 AI 治理入門教材。涵蓋 LLM/MCP/Agent 基礎概念、企業風險案例、Axway AI Gateway 架構與四大使用場景。

### 比對分析

| 概念 | 來源的做法 | 我的做法 |
|------|-----------|---------|
| MCP 是 AI 的 USB-C | 標準化工具整合 | Pi 已支援 MCP |
| AI 需要治理層 | 集中控管安全+成本 | Pi 有 token budget 但無集中治理 |
| 安全模型 | Gateway 層 RBAC + Guardrails | 信任本地 |
| 成本控管 | Token Quota + Budgets（集中式） | Token Budget（session 級） |
| Agent 管理 | Agent Registry + Designer | 本地 agent 配置 |

### 成長計畫

- [ ] 比較 Axway AI Gateway vs Omnigent 的治理方法
- [ ] 研究 MCP Proxy 在個人 Agent 中的應用
- [ ] 關注 Semantic Caching / Semantic Routing 技術
