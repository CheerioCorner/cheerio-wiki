---
title: "HuggingFace: Spark-X2.5-4B Model Card"
type: raw
source_kind: web
url: https://huggingface.co/XHToken/Spark-X2.5-4B
created: 2026-09-04
tags: [llm, spark-x25, huggingface, model-card]
---

# HuggingFace — XHToken/Spark-X2.5-4B

> 以下為 2026-09-04 從 HuggingFace API 與 raw README.md 實際抓取的完整內容。

## 來源 URL

1. https://huggingface.co/api/models/XHToken/Spark-X2.5-4B — API JSON
2. https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/README.md — Model Card 原文
3. https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/config.json — 模型 config
4. https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/modeling_spark.py — 自訂 modeling 原始碼（前 300 行）
5. https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/generation_config.json — 生成參數
6. https://huggingface.co/api/models/XHToken/Spark-X2.5-4B-Base — Base model API
7. https://github.com/XHToken/Spark-X2.5 — GitHub 主頁面 README
8. https://huggingface.co/api/models/XHToken/Spark-X2.5-4B-GGUF/tree/main — GGUF 檔案列表

---

## API 資訊（實際抓取）

```json
{
  "id": "XHToken/Spark-X2.5-4B",
  "private": false,
  "pipeline_tag": "text-generation",
  "library_name": "transformers",
  "tags": ["transformers","safetensors","spark2_5","text-generation","llm","sparkx2_5","conversational","custom_code","base_model:XHToken/Spark-X2.5-4B-Base","base_model:finetune:XHToken/Spark-X2.5-4B-Base","license:apache-2.0","region:us"],
  "downloads": 1514,
  "likes": 162,
  "lastModified": "2026-09-03T05:45:12.000Z",
  "createdAt": "2026-08-24T06:34:34.000Z",
  "config": {
    "architectures": ["Spark2_5ForCausalLM"],
    "auto_map": {
      "AutoConfig": "configuration_spark.Spark2_5Config",
      "AutoModel": "modeling_spark.Spark2_5Model",
      "AutoModelForCausalLM": "modeling_spark.Spark2_5ForCausalLM"
    },
    "model_type": "spark2_5"
  },
  "safetensors": { "parameters": { "BF16": 4112079360 }, "total": 4112079360 },
  "usedStorage": 8234897398
}
```

## Config.json（實際抓取）

```json
{
  "architectures": ["Spark2_5ForCausalLM"],
  "hidden_size": 2560,
  "intermediate_size": 10240,
  "num_hidden_layers": 36,
  "num_attention_heads": 16,
  "num_key_value_heads": 4,
  "head_dim": 256,
  "vocab_size": 131072,
  "max_position_embeddings": 1048576,
  "sliding_window": 512,
  "hidden_act": "gelu",
  "dtype": "bfloat16",
  "rms_norm_eps": 1e-06,
  "tie_word_embeddings": true,
  "gate_attn_act_mode": "sigmoid",
  "headwise_attn_output_gate": true,
  "layer_types": [
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention",
    "sliding_attention","sliding_attention","sliding_attention","full_attention"
  ],
  "rope_parameters": {
    "full_attention": { "partial_rotary_factor": 0.25, "rope_theta": 5000000 },
    "sliding_attention": { "partial_rotary_factor": 1.0, "rope_theta": 10000 }
  }
}
```

## README.md 全文摘要

### 產品定位
- Spark-X2.5-4B 和 Spark-X2.5-1.7B 是 compact, general-purpose language models
- 設計目標：conversation, writing, translation, reasoning, coding, tool use, agentic workflows
- 支援 200+ 語言
- 原生支援最長 1M tokens context window

### 架構
- Hybrid attention architecture：每 4 層中 3 層 sliding-window attention + 1 層 full attention
- Full attention 層的 partial_rotary_factor 為 0.25（只對 25% head_dim 做 RoPE），rope_theta=5000000
- Sliding attention 層的 partial_rotary_factor 為 1.0（完整 RoPE），rope_theta=10000，sliding_window=512
- 頭部門控（headwise_attn_output_gate）：sigmoid gate
- 使用 Gated MLP（SwiGLU-like: gate_proj × up_proj → down_proj，但 activation 為 gelu）

### 訓練方法
- Pretrain：約 20T tokens，來源為 web pages, books, academic publications, code, encyclopedic materials
- Long-context：專門的訓練階段，數百 billion tokens，序列長度延伸至 1M
- Post-training：
  1. Supervised fine-tuning（建立 instruction following、structured generation、task-completion）
  2. Large-scale RL（language understanding, reasoning, programming, tool-augmented agentic behavior, instruction following）
  3. MOPD（Multi-Objective Policy Distillation）：將多個 domain-specialized teacher policies 整合成單一模型
- 在 Huawei Ascend 集群上訓練

### Benchmark（thinking mode 下評測）

#### Agent
| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B |
|-----------|--------------|------------|------------|
| BFCL-V4 | 65.1 | 50.3* | 36.9 |
| τ²-bench | 75.1 | 79.9* | 42.2* |
| τ³-bench | **30.4** | 6.7 | 10.1 |
| MCP-Atlas | **54.6** | 40.8* | 15.0* |
| MCP-Mark | **14.2** | 12.5 | – |
| Workspace Bench | **31.2** | 21.3 | – |
| VitaBench2.0 | **25.2** | 18.2 | 4.8 |
| BrowseComp | **40.9** | 14.3 | 8.3 |

#### Code
| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B |
|-----------|--------------|------------|------------|
| SWE-Bench Pro | **44.4** | 29.4* | 4.0* |
| SWE-Bench Verified | 41.6 | 38.8* | 14.0* |
| SWE-Bench Multilingual | **53.3** | 27.7 | – |
| SciCode | 34.7 | 24.0 | 27.5 |

#### Math
| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B |
|-----------|--------------|------------|------------|
| Gaokao 2026 | 133.4 | 130.3 | 102.4 |
| AIME 2026 | **90.7** | 83.0 | 42.5* |
| HMMT Feb 2026 | **81.2** | 69.7 | 34.2 |
| IMO-AnswerBench | **74.2** | 68.5 | 26.9 |

#### General & Knowledge
| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B |
|-----------|--------------|------------|------------|
| IFEval | 93.0 | 89.8* | 45.3 |
| IFBench | **75.0** | 59.2 | 44.0* |
| AA-LCR | 56.3 | 57.0* | 34.7 |
| HLE | 12.3 | 8.6 | 3.9 |
| GPQA | 67.4 | 67.2 | 54.5 |

### License
- Apache 2.0

### Citation
```bibtex
@misc{sparkx2.5,
    title  = {Spark-X2.5 4B&1.7B: Pushing the Limits of Agentic Capabilities in On-Device Models},
    author = {SparkLLM Team},
    year   = {2026}
}
```

## GGUF 版本

- HuggingFace: https://huggingface.co/XHToken/Spark-X2.5-4B-GGUF
- 檔案：Spark-X2.5-4B.gguf（BF16 轉換，8.23 GB）
- 下載量：3282（比主模型的 1514 還高）
- 需要使用 XHToken 的 llama.cpp fork：https://github.com/XHToken/llama.cpp

## XHToken GitHub Repos

- https://github.com/XHToken/Spark-X2.5 — 主 repo
- https://github.com/XHToken/Spark-plugin — vLLM 的 Spark2.5 plugin
- https://github.com/XHToken/Spark-MLX-LLM — MLX 原生支援（無需 GGUF 轉換）
- https://github.com/XHToken/llama.cpp — llama.cpp fork（支援 Spark2.5）
- https://github.com/XHToken/sglang — SGLang fork
- https://github.com/XHToken/vllm — vLLM fork
- https://github.com/XHToken/LlamaFactory — LLaMA-Factory fork（微調用）

## XHToken 全部模型

| Model | Downloads | Likes |
|-------|-----------|-------|
| Spark-X2.5-4B | 1514 | 162 |
| Spark-X2.5-1.7B | 1128 | 54 |
| Spark-X2.5-4B-GGUF | 3282 | 27 |
| Spark-X2.5-1.7B-GGUF | 1627 | 15 |
| Spark-X2.5-1.7B-Base | 260 | 6 |
| Spark-X2.5-4B-Base | 54 | 6 |
