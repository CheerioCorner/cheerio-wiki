---
title: "Spark-X2.5-4B — 華為 Ascend 訓練的 On-Device Agentic 模型"
type: entity
created: 2026-09-04
updated: 2026-09-04
sources: 1
tags: [llm, spark-x25, on-device, agentic, hybrid-attention, chinese-model]
topics: [ai-agent, agent-infrastructure]
canonical: entities/spark-x25-4b
provenance:
  - kind: external
    url: https://huggingface.co/XHToken/Spark-X2.5-4B
---

# Spark-X2.5-4B

> SparkLLM Team 推出的 4.1B 參數 Dense 模型，以 Agentic 能力為核心賣點，在同量級開源模型中 agent/code/math 數項 benchmark 領先。使用華為 Ascend 集群訓練，Apache 2.0 授權。

## 基本規格

| 屬性 | 數值 |
|------|------|
| 參數量 | 4.11B（BF16） |
| Hidden Size | 2560 |
| Intermediate Size | 10240 |
| Layers | 36 |
| Attention Heads | 16 |
| KV Heads | 4（GQA） |
| Head Dim | 256 |
| Vocab Size | 131,072 |
| Context Window | 原生 1M tokens（1,048,576） |
| Sliding Window | 512（sliding attention 層） |
| 模型檔案大小 | ~8.2 GB（BF16 safetensors，5 shards） |
| GGUF 檔案大小 | ~8.23 GB（BF16 GGUF） |
| 授權 | Apache 2.0（可商用） |
| 架構代號 | Spark2_5ForCausalLM（custom_code，非 transformers 內建） |
| Base Model | XHToken/Spark-X2.5-4B-Base |

## 架構特色：Hybrid Attention

Spark-X2.5 最核心的架構設計是**混合式注意力機制**，每 4 層為一組：

| 層類型 | 數量 | 滑動窗口 | RoPE 配置 |
|--------|------|---------|-----------|
| Sliding Attention | 3 層 | 512 tokens | full partial_rotary_factor=1.0, rope_theta=10000 |
| Full Attention | 1 層 | 無限 | partial_rotary_factor=0.25, rope_theta=5,000,000 |

**設計理念**：在效能、推論效率、KV-cache 大小三者之間取得平衡。大多數層只看局部上下文（sliding window），少數全注意力層負責長距離資訊傳遞。相比純 full attention，顯著降低長 context 的計算開銷。

**其他架構細節**：
- 頭部門控（Headwise Attention Output Gate）：sigmoid gate，per-head 控制 attention output
- Gated MLP：gate_proj × up_proj → down_proj，activation 為 GELU
- tied word embeddings（輸入與輸出共享 embedding）
- 使用 Qwen3 相容的 chat template 和 reasoning parser

## 訓練方法

### Pretrain
- 約 20T tokens
- 資料來源：web pages, books, academic publications, code, encyclopedic materials
- 透過 data-mixture studies 確定 math/logic/code 等高價值領域的取樣權重
- 專門的 long-context 訓練階段：數百 billion tokens，序列長度延伸至 1M

### Post-training
1. **Supervised Fine-Tuning**：精選語料，建立 instruction following、structured generation、task-completion
2. **Large-Scale RL**：跨多個能力域（language understanding, reasoning, programming, tool-augmented agentic behavior, instruction following）
3. **MOPD（Multi-Objective Policy Distillation）**：將多個 domain-specialized teacher policies 整合成單一可部署模型

### 訓練環境
- 華為 Ascend 集群
- 開源了 Ascend A2/A3/950DT 的部署指引

## Benchmark 表現（Thinking Mode）

與同量級主要競爭者比較（4B 級別）：

### Agent 領域（Spark-X2.5-4B 的強項）

| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B | 勝出 |
|-----------|:------------:|:----------:|:----------:|:----:|
| τ³-bench | **30.4** | 6.7 | 10.1 | Spark |
| MCP-Atlas | **54.6** | 40.8* | 15.0* | Spark |
| MCP-Mark | **14.2** | 12.5 | – | Spark |
| Workspace Bench | **31.2** | 21.3 | – | Spark |
| VitaBench2.0 | **25.2** | 18.2 | 4.8 | Spark |
| BrowseComp | **40.9** | 14.3 | 8.3 | Spark |
| BFCL-V4 | 65.1 | 50.3* | 36.9 | Spark（Qwen3.5-9B 最高） |

### Code 領域

| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B | 勝出 |
|-----------|:------------:|:----------:|:----------:|:----:|
| SWE-Bench Pro | **44.4** | 29.4* | 4.0* | Spark |
| SWE-Bench Multilingual | **53.3** | 27.7 | – | Spark |
| SWE-Bench Verified | 41.6 | 38.8* | 14.0* | Gemma4-12B(44.2)* |
| SciCode | 34.7 | 24.0 | 27.5 | Gemma4-12B(39.8) |

### Math 領域

| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B | 勝出 |
|-----------|:------------:|:----------:|:----------:|:----:|
| AIME 2026 | **90.7** | 83.0 | 42.5* | Spark |
| HMMT Feb 2026 | **81.2** | 69.7 | 34.2 | Spark |
| IMO-AnswerBench | **74.2** | 68.5 | 26.9 | Spark |
| Gaokao 2026 | 133.4 | 130.3 | 102.4 | Qwen3.5-9B(135.5) |

### General & Knowledge

| Benchmark | Spark-X2.5-4B | Qwen3.5-4B | Gemma4-E4B | 勝出 |
|-----------|:------------:|:----------:|:----------:|:----:|
| IFBench | **75.0** | 59.2 | 44.0* | Spark |
| IFEval | 93.0 | 89.8* | 45.3 | Gemma4-12B(94.8) |
| HLE | 12.3 | 8.6 | 3.9 | Qwen3.5-9B(14.3) |
| GPQA | 67.4 | 67.2 | 54.5 | Qwen3.5-9B(77.2) |

> \* 表示來自公開 model card/paper 的結果。所有評測均在 thinking mode 下進行。

**總結**：在 4B 級別中，Spark-X2.5-4B 在 Agent（尤其 τ³-bench、MCP-Atlas、BrowseComp）、Code（SWE-Bench Pro/Multilingual）、Math（AIME、HMMT、IMO-AnswerBench）均領先同量級競爭者。在 GPQA、HLE 等純知識類 benchmark 上略遜於 Qwen3.5-4B。

## 硬體需求與部署

### VRAM/RAM 最低需求
- **BF16 原始模型**：模型本體 ~8.2 GB，加上 KV cache 和 framework overhead，最低建議 **10-12 GB VRAM**
- **1M context window**：若要完整使用 1M context，KV cache 會非常大，需大幅增加 VRAM 或減少 context-length
- **官方建議**：vLLM 預設 `gpu-memory-utilization=0.7`；SGLang 預設 `mem-fraction-static=0.8`

### 量化版本
- **GGUF BF16**：Spark-X2.5-4B-GGUF（8.23 GB），需使用 XHToken 的 llama.cpp fork
- **GGUF 低精度量化**：頁面上目前只提供 BF16 GGUF，未看到 Q4/Q8 等其他量化選項（截至 2026-09-04）
- **MLX**：透過 Spark-MLX-LLM 直接跑原始 HF checkpoint（無需 GGUF 轉換），支援 Apple Silicon / Linux CPU / NVIDIA CUDA

### CPU-only 是否可行
- MLX 支援 Linux CPU（`pip install -e '.[cpu]'`），技術上可行但速度會很慢
- 未有官方 CPU-only benchmark 數據
- **不建議**作為日常使用方案

### 推薦硬體等級
- **最低可用**：NVIDIA RTX 3060 12GB（BF16，short context）
- **舒適使用**：NVIDIA RTX 4090 24GB / RTX 5090 32GB
- **完整 1M context**：需要多張 GPU 或大幅量化
- **Huawei Ascend**：官方提供 A2/A3/950DT 完整部署指引

### 推理框架支援
| 框架 | 支援方式 |
|------|---------|
| SGLang | 官方 Docker image（NVIDIA + Ascend） |
| vLLM | 官方 Docker image + XHToken Spark-plugin |
| llama.cpp | XHToken fork（Ollama / LM Studio 透過此 fork） |
| MLX | Spark-MLX-LLM（原生 HF checkpoint） |
| Ollama | 透過 XHToken llama.cpp fork 建置 |
| LM Studio | 透過 XHToken llama.cpp fork 替換 runtime |

## LLM 本身限制

### Context Window
- 原生支援 1M tokens，但 KV-cache 大小隨 context 長度線性增長
- 官方 Quickstart 中提到「This setting requires sufficient device memory; reduce --context-length when necessary」
- 實際可用 context 長度取決於硬體

### 已知限制
- **Custom code required**：架構不是 transformers 內建的，需要 `trust-remote-code=True`
- **需要 XHToken fork**：llama.cpp、vLLM、SGLang 都需要使用 XHToken 的 fork 版本，非主流主線版本直接支援
- **推理框架支援成熟度**：相較於 Qwen、Llama 等主流模型被所有框架原生支援，Spark-X2.5 目前仍依賴 fork
- **無多模態**：純文字模型，不支援圖片/影片輸入
- **無公開的訓練資料詳細說明**：只提到 web pages/books/code 等大類，未公開具體資料集
- **Benchmark 星號**：部分競爭者的 benchmark 結果帶星號（\*），表示來自對方公開的 model card，可能有測試環境差異

### 訓練資料偏見
- 頁面未公開具體訓練資料分佈
- 支援 200+ 語言，但未揭露各語言的佔比
- 中文 benchmark（Gaokao）有特別列出，暗示中文能力可能是重點

## 與主流 LLM 的差異

### vs Qwen3.5-4B
| 維度 | Spark-X2.5-4B | Qwen3.5-4B |
|------|--------------|------------|
| 架構 | Hybrid attention（3 sliding + 1 full per 4 layers） | 標準 Transformer（推測） |
| Context | 原生 1M | 未明確（Qwen3 系列通常 128K-256K） |
| Agent 能力 | 顯著領先（τ³-bench 30.4 vs 6.7） | 較弱 |
| Code 能力 | SWE-Bench Pro 44.4 vs 29.4 | 較弱 |
| Math 能力 | AIME 90.7 vs 83.0 | 略弱 |
| GPQA/HLE | 67.4/12.3 vs 67.2/8.6 | 略強或持平 |
| 生態系成熟度 | 需 fork | 原生支援所有主流框架 |
| 訓練環境 | 華為 Ascend | NVIDIA GPU |

### vs Gemma4-E4B
| 維度 | Spark-X2.5-4B | Gemma4-E4B |
|------|--------------|------------|
| Agent | 全面領先 | 較弱 |
| Code | SWE-Bench Pro 44.4 vs 4.0 | 顯著落後 |
| Math | AIME 90.7 vs 42.5 | 顯著落後 |
| IFEval | 93.0 vs 45.3 | 顯著落後 |
| 整體 | 各項大幅領先 | 在 4B 級別表現不佳 |

### 設計理念差異
1. **Agent-first 設計**：Spark-X2.5 是目前少數以 agent 能力為核心訴求的小模型，benchmark 選擇（τ³-bench、MCP-Atlas、BrowseComp、Workspace Bench）都偏向 agent 場景
2. **Hybrid Attention 效率**：不同於標準 Transformer 的全注意力或純 sliding window，混合式設計在長 context 效率上有結構性優勢
3. **華為 Ascend 生態**：這是少數以 Ascend NPU 為一級部署目標的開源模型，對中國市場有特殊意義
4. **MOPD 訓練**：Multi-Objective Policy Distillation 是其 post-training 的關鍵技術，將多個專門化的 teacher policy 整合成一個模型

## 適用場景

### 適合
- **Agent / Tool Use 場景**：這是 Spark-X2.5 的核心強項，τ³-bench、MCP-Atlas 等 agent benchmark 領先同量級
- **程式碼生成與修改**：SWE-Bench Pro/Multilingual 表現優異
- **數學推理**：AIME、HMMT、IMO-AnswerBench 均領先
- **長 context 應用**：原生 1M tokens，適合需要處理大量文件的場景
- **邊緣裝置 / On-Device**：4B 參數量在合理硬體範圍內
- **中文任務**：Gaokao 2026 表現不錯（133.4），暗示中文能力經過特別優化
- **多語言場景**：支援 200+ 語言

### 不適合
- **需要最大生態系相容性的場景**：需要 XHToken fork，不如 Qwen/Llama 被主流框架原生支援
- **純知識問答 / GPQA**：在 GPQA 上與 Qwen3.5-4B 持平但不如更大模型
- **多模態需求**：不支援圖片/影片
- **需要極高精度的生產環境**：4B 模型在複雜推理上仍有天花板
- **企業合規需求**：雖然 Apache 2.0 可商用，但華為 Ascend 訓練背景可能在某些企業環境需要額外評估

## 社群連結

- Slack: https://join.slack.com/t/tokenspark/shared_invite/zt-432qf8l2f-5~dLyXv8uETr0P0UuC07nw
- Discord: https://discord.gg/kTDE2Hg8aw
- YouTube: https://www.youtube.com/@SparkLLM
- X (Twitter): https://x.com/sparkllm
- Zhihu: https://www.zhihu.com/people/zhiikz7qh7m
- GitHub: https://github.com/XHToken/Spark-X2.5

## 相關頁面

- [[wiki/entities/qwen-3-8-27b|Qwen 3.8 27B]] — 同為中國開源 Dense 模型，但 27B 規格
- [[wiki/entities/deepseek-v4|DeepSeek V4]] — MoE 架構的中國模型
- [[wiki/entities/vllm|vLLM]] — Spark-X2.5 透過 XHToken fork 支援的推理引擎
- [[wiki/concepts/local-llm-deployment|Local LLM 部署]] — 地端模型部署策略
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] — 推理服務架構
- [[wiki/sources/2026-09-04-huggingface-spark-x25-4b|來源筆記]] — 原始查證資料
