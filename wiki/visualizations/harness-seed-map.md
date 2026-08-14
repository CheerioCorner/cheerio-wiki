---
title: "Harness 視覺地圖 — 種子內部結構"
type: visualization
created: 2026-08-14
updated: 2026-08-14
topics: [ai-agent, meta-systems]
seed: wiki/concepts/harness
notion_map_id: null
---

# 🗺️ Harness 視覺地圖

> 展示 Harness 種子內部的概念結構：核心定義、內部職責、三層分類與具體實例。

## 結構解讀

- **底部 LLM** 是被驅動的對象，不是 harness 本身
- **Coding Agent Harness** 是概念根（本頁面定義的層次）
- **Meta-Harness** 有兩種同名異義：治理/組合型 vs 自動演化/優化型
- **Multi-Agent Orchestration** 是多個 harness 協同工作的上層
- 箭頭方向：`-->` 表示「.builds on / 是其基礎」

## Mermaid

```mermaid
graph TD
    TOP["Multi-Agent Orchestration"]

    subgraph MH["Meta-Harness"]
        GOV["Governance: Omnigent"]
        EVO["Evolution: Stanford"]
    end

    subgraph CAH["Coding Agent Harness"]
        CC["Claude Code"]
        CX["Codex CLI"]
        PA["Pi — earendil-works"]
        HM["Hermes"]
    end

    subgraph CORE["Core Responsibilities"]
        AL["Agentic Loop"]
        TC["Tool Calling"]
        PM["Permissions"]
        CM["Context Management"]
        SM["Session Memory"]
    end

    LLM["LLM / Model"]

    TOP --> GOV
    TOP --> EVO
    GOV --> CC
    GOV --> CX
    EVO --> CC
    EVO --> PA
    CC --> AL
    CC --> TC
    CC --> PM
    CC --> CM
    CC --> SM
    AL --> LLM
    TC --> LLM
    PM --> LLM
    CM --> LLM
    SM --> LLM

    style TOP fill:#1a1a2e,stroke:#e94560,color:#fff
    style MH fill:#16213e,stroke:#0f3460,color:#fff
    style CAH fill:#0f3460,stroke:#533483,color:#fff
    style CORE fill:#1a1a2e,stroke:#e94560,color:#fff
    style LLM fill:#533483,stroke:#e94560,color:#fff
    style GOV fill:#0f3460,stroke:#e94560,color:#fff
    style EVO fill:#0f3460,stroke:#e94560,color:#fff
    style CC fill:#16213e,stroke:#e94560,color:#fff
    style CX fill:#16213e,stroke:#e94560,color:#fff
    style PA fill:#16213e,stroke:#e94560,color:#fff
    style HM fill:#16213e,stroke:#e94560,color:#fff
    style AL fill:#1a1a2e,stroke:#533483,color:#fff
    style TC fill:#1a1a2e,stroke:#533483,color:#fff
    style PM fill:#1a1a2e,stroke:#533483,color:#fff
    style CM fill:#1a1a2e,stroke:#533483,color:#fff
    style SM fill:#1a1a2e,stroke:#533483,color:#fff
```

## 連結

- 種子頁面：[[wiki/concepts/harness]]
- 關聯種子：[[wiki/entities/omnigent]]、[[wiki/entities/claude-code]]、[[wiki/entities/github-copilot]]
- 關聯專題：[[wiki/topics/meta-systems]]、[[wiki/topics/ai-agent]]
