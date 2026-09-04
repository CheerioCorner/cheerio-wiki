---
title: "HuggingFace: Spark-X2.5-4B Model Card 查證"
type: source
created: 2026-09-04
updated: 2026-09-04
sources: 8
tags: [llm, spark-x25, huggingface, model-card, on-device, agentic, hybrid-attention]
topics: [ai-agent, agent-infrastructure]
provenance:
  - kind: external
    url: https://huggingface.co/api/models/XHToken/Spark-X2.5-4B
  - kind: external
    url: https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/README.md
  - kind: external
    url: https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/config.json
  - kind: external
    url: https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/modeling_spark.py
  - kind: external
    url: https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/generation_config.json
  - kind: external
    url: https://github.com/XHToken/Spark-X2.5
  - kind: external
    url: https://huggingface.co/api/models/XHToken/Spark-X2.5-4B-Base
  - kind: external
    url: https://huggingface.co/api/models/XHToken/Spark-X2.5-4B-GGUF
---

# HuggingFace: Spark-X2.5-4B Model Card 查證

> 2026-09-04 從 HuggingFace API、README、config.json、modeling 原始碼、GitHub repo 實際抓取並查證的完整資料。對應 entity 頁面：[[wiki/entities/spark-x25-4b|Spark-X2.5-4B]]。

## 查證來源一覽

| # | 來源 | URL | 關鍵擷取 |
|---|------|-----|---------|
| 1 | HuggingFace API | https://huggingface.co/api/models/XHToken/Spark-X2.5-4B | 參數量 4,112,079,360（BF16），架構 `Spark2_5ForCausalLM`（custom_code），5 shards，8.2GB，Apache 2.0，1514 downloads / 162 likes，base model 為 `XHToken/Spark-X2.5-4B-Base` |
| 2 | Model Card README | https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/README.md | 產品定位（compact general-purpose）、20T tokens pretrain、Hybrid Attention 架構說明、MOPD post-training、完整 benchmark 表格、SGLang/vLLM/MLX/Ollama/LM Studio Quickstart |
| 3 | config.json | https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/config.json | hidden_size=2560、intermediate_size=10240、36 layers、16 attention heads、4 KV heads（GQA）、head_dim=256、vocab=131072、max_position=1048576、sliding_window=512、36 層 layer_types（9 組 3 sliding + 1 full）、RoPE 差異化配置 |
| 4 | modeling_spark.py | https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/modeling_spark.py | 自訂架構：Spark2_5RMSNorm、Spark2_5MLP（SwiGLU-like with GELU activation）、Spark2_5Attention（headwise sigmoid gate、sliding window conditional on layer_type）、partial_rotary_factor 差異化 RoPE（full=0.25, sliding=1.0） |
| 5 | generation_config.json | https://huggingface.co/XHToken/Spark-X2.5-4B/raw/main/generation_config.json | temperature=1.0, top_p=0.95, top_k=-1, repetition_penalty=1.0（官方推薦參數） |
| 6 | Base model API | https://huggingface.co/api/models/XHToken/Spark-X2.5-4B-Base | 同為 4.11B BF16，54 downloads，tags 含 en/zh |
| 7 | GitHub 主 Repo | https://github.com/XHToken/Spark-X2.5 | 8 個 XHToken repos（llama.cpp/sglang/vllm fork、Spark-plugin、Spark-MLX-LLM、LlamaFactory fork、community）；支援 Huawei Ascend A2/A3/950DT |
| 8 | GGUF Repo | https://huggingface.co/api/models/XHToken/Spark-X2.5-4B-GGUF | 單檔 BF16 GGUF（Spark-X2.5-4B.gguf，8.23GB），3282 downloads（比主模型更高） |

## API 原始擷取

```json
{
  "id": "XHToken/Spark-X2.5-4B",
  "private": false,
  "pipeline_tag": "text-generation",
  "library_name": "transformers",
  "tags": ["transformers","safetensors","spark2_5","text-generation","llm",
    "sparkx2_5","conversational","custom_code",
    "base_model:XHToken/Spark-X2.5-4B-Base",
    "base_model:finetune:XHToken/Spark-X2.5-4B-Base",
    "license:apache-2.0","region:us"],
  "downloads": 1514,
  "likes": 162,
  "createdAt": "2026-08-24T06:34:34.000Z",
  "lastModified": "2026-09-03T05:45:12.000Z",
  "safetensors": { "parameters": { "BF16": 4112079360 }, "total": 4112079360 },
  "usedStorage": 8234897398
}
```

## Config 架構細節

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

## Benchmark 擷取（Thinking Mode）

### Agent

| Benchmark | Spark-X2.5-4B | Spark-X2.5-1.7B | Qwen3.5-9B | Qwen3.5-4B | Gemma4-12B | Gemma4-E4B |
|-----------|:------------:|:---------------:|:----------:|:----------:|:----------:|:----------:|
| BFCL-V4 | 65.1 | 46.9 | 66.1* | 50.3* | 37.4 | 36.9 |
| τ²-bench | 75.1 | 65.3 | 79.1* | 79.9* | 69.0* | 42.2* |
| τ³-bench | **30.4** | 20.1 | 9.3 | 6.7 | 13.3 | 10.1 |
| MCP-Atlas | **54.6** | 23.4 | 47.4* | 40.8* | 30.5* | 15.0* |
| MCP-Mark | **14.2** | 2.3 | 13.4 | 12.5 | – | – |
| Workspace Bench | **31.2** | 18.9 | 25.5 | 21.3 | – | – |
| VitaBench2.0 | **25.2** | 8.3 | 15.6 | 18.2 | 12.4 | 4.8 |
| BrowseComp | **40.9** | 29.7 | 8.3 | 14.3 | 10.0 | 8.3 |

### Code

| Benchmark | Spark-X2.5-4B | Qwen3.5-9B | Qwen3.5-4B | Gemma4-12B | Gemma4-E4B |
|-----------|:------------:|:----------:|:----------:|:----------:|:----------:|
| SWE-Bench Pro | **44.4** | 33.8* | 29.4* | 21.9* | 4.0* |
| SWE-Bench Verified | 41.6 | 53.1* | 38.8* | **44.2*** | 14.0* |
| SWE-Bench Multilingual | **53.3** | 43.3 | 27.7 | 32.5* | – |
| SciCode | 34.7 | 32.7* | 24.0 | **39.8** | 27.5 |

### Math

| Benchmark | Spark-X2.5-4B | Qwen3.5-9B | Qwen3.5-4B | Gemma4-12B | Gemma4-E4B |
|-----------|:------------:|:----------:|:----------:|:----------:|:----------:|
| Gaokao 2026 | 133.4 | **135.5** | 130.3 | 130.6 | 102.4 |
| AIME 2026 | **90.7** | 88.2 | 83.0 | 82.1* | 42.5* |
| HMMT Feb 2026 | **81.2** | 70.8 | 69.7 | 65.6 | 34.2 |
| IMO-AnswerBench | **74.2** | 69.8 | 68.5 | 57.2 | 26.9 |

### General & Knowledge

| Benchmark | Spark-X2.5-4B | Qwen3.5-9B | Qwen3.5-4B | Gemma4-12B | Gemma4-E4B |
|-----------|:------------:|:----------:|:----------:|:----------:|:----------:|
| IFEval | 93.0 | 91.5* | 89.8* | **94.8** | 45.3 |
| IFBench | **75.0** | 64.5 | 59.2 | 73.5* | 44.0* |
| AA-LCR | 56.3 | **63.0*** | 57.0* | 55.3* | 34.7 |
| HLE | 12.3 | 14.3 | 8.6 | **13.1** | 3.9 |
| GPQA | 67.4 | **77.2** | 67.2 | 72.8 | 54.5 |

> \* 表示來自公開 model card/paper 的結果。`–` 表示尚無公開分數。

## XHToken GitHub Repos 擷取

| Repo | 說明 |
|------|------|
| XHToken/Spark-X2.5 | 主 repo：模型系列介紹、benchmark、Quickstart |
| XHToken/llama.cpp | llama.cpp fork，支援 Spark2.5 架構（Ollama/LM Studio 透過此 fork） |
| XHToken/sglang | SGLang fork |
| XHToken/vllm | vLLM fork |
| XHToken/Spark-plugin | vLLM 的 Spark2.5 plugin（Ascend NPU 用） |
| XHToken/Spark-MLX-LLM | MLX 原生支援（Apple Silicon / Linux CPU / CUDA） |
| XHToken/LlamaFactory | LLaMA-Factory fork（微調用） |
| XHToken/community | 開源社群 |

## XHToken 全部模型下載量

| Model | Downloads | Likes |
|-------|-----------|-------|
| Spark-X2.5-4B | 1514 | 162 |
| Spark-X2.5-1.7B | 1128 | 54 |
| **Spark-X2.5-4B-GGUF** | **3282** | 27 |
| Spark-X2.5-1.7B-GGUF | 1627 | 15 |
| Spark-X2.5-1.7B-Base | 260 | 6 |
| Spark-X2.5-4B-Base | 54 | 6 |

## 模型卡原文重點摘要

- 「two compact, general-purpose language models designed to make capable AI more practical, efficient, and accessible」
- 「achieving leading results among open-source models of comparable size」
- 「native context windows of up to 1M tokens, and support for more than 200 languages」
- 「hybrid attention architecture that combines one full-attention layer with three sliding-window attention layers」
- 「deeply integrated with popular agent harnesses, including Codex, Claude Code, OpenClaw, and Hermes」
- 「trained on Huawei Ascend clusters」
- 「Large-scale reinforcement learning and post-training techniques such as MOPD」
- License: Apache 2.0（可商用）
- Citation: SparkLLM Team, 2026

## 相關頁面

- [[wiki/entities/spark-x25-4b|Spark-X2.5-4B]] — entity 頁面（架構、benchmark、硬體需求、適用場景）
- [[wiki/entities/qwen-3-8-27b|Qwen 3.8 27B]] — 同為中國 Dense 模型的對照
- [[wiki/entities/deepseek-v4|DeepSeek V4]] — MoE 架構的中國模型
- [[wiki/sources/2026-09-04-huggingface-spark-x25-4b|raw 來源]] — 原始查證資料
