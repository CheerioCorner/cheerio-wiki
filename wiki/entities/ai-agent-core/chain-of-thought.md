---
title: "Chain-of-thought (CoT)"
type: entity
created: 2026-08-06
updated: 2026-08-06
sources: 0
tags: [chain-of-thought, cot, reasoning, inference, llm]
topics: [ai-agent]
canonical: entities/ai-agent-core/chain-of-thought
---

# Chain-of-thought (CoT)

> 讓模型「先想再答」的推理技術。

## 一句話解釋

模型在回答問題前，先把推理過程寫出來，然後基於這個過程給出最終答案。

## 為什麼重要

```
問題：如果一個球和一個球拍共 $1.10，球拍比球貴 $1.00，球多少錢？

沒 CoT：$0.10（錯誤！）
有 CoT：
  1. 設球 = x
  2. 球拍 = x + 1.00
  3. x + (x + 1.00) = 1.10
  4. 2x + 1.00 = 1.10
  5. 2x = 0.10
  6. x = 0.05
  答案：$0.05（正確！）
```

## 實作方式

### 1. Prompt-based CoT（最簡單）

```
Let's think step by step.
```

在 prompt 中加入這句話，模型就會自動展開推理過程。

### 2. thinking_token_budget（vLLM 方式）

在模型層級預留 token 給思考：

```json
{
  "model": "my-model",
  "thinking_token_budget": 2048
}
```

模型會用這 2048 tokens 進行內部推理，然後用剩餘 tokens 輸出答案。

### 3. Extended Thinking（Claude 方式）

Claude 的 extended thinking 功能，讓模型可以「思考」更久：

```json
{
  "thinking": {
    "type": "enabled",
    "budget_tokens": 10000
  }
}
```

## 與 Agent 的關係

| Agent 功能 | CoT 如何幫助 |
|-----------|-------------|
| Tool use | 先想清楚要用什麼工具，再執行 |
| Planning | 先想出步驟，再逐一執行 |
| Debugging | 先分析錯誤原因，再修復 |
| Code review | 先理解程式碼意圖，再評估 |

## 延伸閱讀

- [[wiki/entities/ai-agent-core/token-budget|Token 預算]] — CoT 的資源限制
- [[wiki/entities/pi-agent/changelog/v0.84.0|Pi v0.84.0]] — thinking_token_budget 實作
- [[wiki/concepts/meta-harness|Meta Harness]] — Agent 架構中的推理位置

## 自己動手做

如果要建立自己的 Agent 並加入 CoT：

1. **簡單版**：在 system prompt 加入「請先思考再回答」
2. **進階版**：用 vLLM 部署，設定 `thinking_token_budget`
3. **完整版**：實作 two-pass — 第一次生成思考，第二次生成答案

## 標籤

#ai-agent #reasoning #llm #inference
