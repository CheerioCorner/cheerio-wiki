# Pi Agent 更新學習系統

> 從「使用工具」到「理解原理」到「自己建造」的學習路徑。

## 🎯 最終目標

**建立自己的 AI Agent** — 不一定是 Pi Agent，可能是一個 framework，但透過學習 Pi 的設計，理解 Agent 的核心原理。

## 🔄 學習迴路（每次更新時執行）

```
┌─────────────────────────────────────────────────────────┐
│  1. 偵測更新                                              │
│     → 檢查 CHANGELOG.md                                   │
│     → 識別新功能 / Breaking Changes / 修正                 │
├─────────────────────────────────────────────────────────┤
│  2. 分析學習                                              │
│     → 這個功能是什麼？（白話解釋）                           │
│     → 為什麼要更新？（解決什麼問題）                         │
│     → 我可以學到什麼？（背後的原理）                         │
│     → 跟我有什麼關係？（對我的工作影響）                     │
├─────────────────────────────────────────────────────────┤
│  3. 存入大腦                                              │
│     → 更新 wiki entities / concepts                       │
│     → 記錄到 log.md                                       │
│     → 更新 index.md 連結                                  │
├─────────────────────────────────────────────────────────┤
│  4. 任務規劃                                              │
│     → 建立學習任務（在 current.md）                         │
│     → 設定下一步（next:）                                  │
│     → 標籤分類（#ai-agent / #extension / #skills）         │
├─────────────────────────────────────────────────────────┤
│  5. 實作練習                                              │
│     → 動手玩新功能                                         │
│     → 寫 demo / sample                                    │
│     → 記錄實作心得                                         │
├─────────────────────────────────────────────────────────┤
│  6. 延伸研究                                              │
│     → 這個功能涉及什麼技術？                               │
│     → 有什麼延伸議題可以研究？                              │
│     → 跟哪些 wiki 頁面有關聯？                             │
└─────────────────────────────────────────────────────────┘
```

## 📋 更新分析模板

每次分析 Pi Agent 更新時，填寫以下模板：

```markdown
# Pi Agent vX.Y.Z 更新分析

## 版本資訊
- 版本：vX.Y.Z
- 日期：YYYY-MM-DD
- 重要性：⭐⭐⭐⭐⭐（1-5 顆星）

## 新功能摘要
| 功能 | 一句話解釋 | 對我的價值 |
|------|-----------|-----------|
| XXX  | ...       | ...       |

## Breaking Changes（如果有）
| 改動 | 影響 | 需要做什麼 |
|------|------|-----------|
| XXX  | ...  | ...       |

## 我可以學到的
### 技術原理
- 這個功能背後的技術是什麼？

### 設計理念
- 為什麼 Pi 團隊要這樣設計？

### 可迁移的知識
- 這些知識可以用在哪裡？

## 任務規劃
- [ ] 實作任務 1：...
- [ ] 實作任務 2：...

## 延伸研究
- 議題 1：...
- 議題 2：...
- 相關 wiki 頁面：...
```

## 📂 知識庫結構

```
wiki/
├── entities/
│   ├── pi-agent/           # Pi Agent 整體
│   │   ├── changelog/      # 每個版本的分析
│   │   │   ├── v0.84.0.md
│   │   │   ├── v0.83.0.md
│   │   │   └── ...
│   │   ├── features/       # 功能拆解
│   │   │   ├── fullscreen-tui.md
│   │   │   ├── mermaid-rendering.md
│   │   │   └── ...
│   │   └── architecture/   # 架構理解
│   │       ├── session-model.md
│   │       ├── provider-system.md
│   │       └── ...
│   ├── ...
│
├── concepts/
│   ├── ai-agent/           # AI Agent 核心概念
│   │   ├── chain-of-thought.md
│   │   ├── token-budget.md
│   │   ├── tool-use.md
│   │   └── ...
│   ├── ...
│
└── topics/
    ├── pi-agent-learning/  # 學習路徑追蹤
    │   ├── roadmap.md      # 學習路線圖
    │   ├── progress.md     # 學習進度
    │   └── notes.md        # 學習筆記
    └── ...
```

## 🎓 學習路線圖

### Phase 1：理解使用（現在）
- [ ] 熟悉 Pi Agent 的日常操作
- [ ] 理解 skill 系統的運作方式
- [ ] 掌握 knowledge base 管理流程

### Phase 2：理解原理（進行中）
- [ ] 理解 Provider 系統（模型如何接入）
- [ ] 理解 Session 模型（對話如何管理）
- [ ] 理解 Tool 系統（工具如何運作）
- [ ] 理解 Extension 系統（擴充如何運作）

### Phase 3：理解架構（下一步）
- [ ] Pi Agent 的整體架構
- [ ] Harness 的設計模式
- [ ] Agent 與 Model 的分離

### Phase 4：動手建造（最終目標）
- [ ] 選擇一個 Agent Framework
- [ ] 建立最小可行 Agent
- [ ] 加入 Tool 系統
- [ ] 加入 Session 管理
- [ ] 加入 Extension 機制

## 🔗 相關資源

- Pi Agent CHANGELOG: `C:/Users/User/AppData/Roaming/npm/node_modules/@earendil-works/pi-coding-agent/CHANGELOG.md`
- Pi Agent Docs: `C:/Users/User/AppData/Roaming/npm/node_modules/@earendil-works/pi-coding-agent/docs/`
- Pi Agent Examples: `C:/Users/User/AppData/Roaming/npm/node_modules/@earendil-works/pi-coding-agent/examples/`
- Wiki Index: `[[wiki/index]]`
- Work Tracker: `[[work/current]]`
