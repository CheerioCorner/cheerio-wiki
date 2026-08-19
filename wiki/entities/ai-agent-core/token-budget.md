---
title: "Token 預算"
type: entity
created: 2026-08-06
updated: 2026-08-06
sources: 0
tags: [token, budget, context-window, llm, resource-management]
topics: [agent-memory-context]
canonical: entities/ai-agent-core/token-budget
---

# Token 預算

> 模型的「記憶力」和「思考力」都是有限的，要學會分配。

## 一句話解釋

每個模型每次回覆能處理的 token 數量有上限，你需要決定多少用於輸入、多少用於思考、多少用於輸出。

## Token 是什麼

```
"Hello, world!" = 4 tokens
"人工智慧" = 2-3 tokens（取決於 tokenizer）
一頁英文文件 ≈ 500 tokens
一頁中文文件 ≈ 300-400 tokens
```

Token 不等於字元或字數，而是模型處理文字的基本單位。

## 預算分配模型

```
總預算：4096 tokens
├── Input（輸入）
│   ├── System prompt: 500 tokens
│   ├── 歷史對話: 2000 tokens
│   └── 用戶訊息: 500 tokens
├── Thinking（思考，可選）
│   └── CoT 推理: 1000 tokens
└── Output（輸出）
    └── 最終回答: 1096 tokens
```

## 為什麼重要

| 問題 | 原因 | 解決方案 |
|------|------|---------|
| 回答被截斷 | 輸出 token 用完 | 增加 max_tokens 或減少輸入 |
| 回答品質差 | 沒有 CoT 空間 | 預留 thinking_token_budget |
| 歷史遺忘 | 上下文超出限制 | 用 compaction 壓縮歷史 |
| 成本過高 | token 用太多 | 優化 prompt、減少不必要的內容 |

## 實際應用

### 1. 設定 max_tokens

```json
{
  "model": "gpt-4",
  "max_tokens": 4096
}
```

限制模型最多生成 4096 tokens。

### 2. 設定 thinking_token_budget（vLLM）

```json
{
  "model": "my-model",
  "thinking_token_budget": 2048
}
```

預留 2048 tokens 給思考，其餘給輸出。

### 3. Context Window 管理

```
Context Window = 128K tokens
├── 歷史對話: 80K（需要 compaction）
├── 當前對話: 20K
└── 預留輸出: 28K
```

## 與 Agent 的關係

| Agent 場景 | Token 預算挑戰 |
|-----------|---------------|
| 長對話 | 歷史對話超出 context window |
| 工具呼叫 | 工具結果佔用大量 token |
| Code review | 大段程式碼需要分析 |
| 文件生成 | 輸出需要大量 token |

## 自己動手做

如果要建立自己的 Agent：

1. **了解限制**：每個模型的 context window 不同
2. **監控使用量**：追蹤每次對話用了多少 token
3. **實作 compaction**：當歷史太長時，自動壓縮
4. **預留空間**：確保 CoT 和輸出有足夠 token

## 延伸閱讀

- [[wiki/entities/ai-agent-core/chain-of-thought|Chain-of-thought]] — 需要預算的思考
- [[wiki/entities/pi-agent/changelog/v0.84.0|Pi v0.84.0]] — thinking_token_budget
- [[wiki/concepts/context-management|Context 管理]] — 如何處理超長對話

## 標籤

#ai-agent #llm #token #context-window
