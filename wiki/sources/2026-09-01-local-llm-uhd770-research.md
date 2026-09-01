---
title: "Local LLM 在 Intel UHD 770 + 32GB RAM 環境下的選擇研究"
type: source
created: 2026-09-01
updated: 2026-09-01
sources: 200
tags: [local-llm, quantization, intel-uhd-770, gguf, copilot, devops, ttft]
topics: [agent-infrastructure, ai-agent]
canonical: sources/2026-09-01-local-llm-uhd770-research
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260901-001/research-report.md
  - kind: raw
    path: raw/deep-research/rc-20260901-001/spec.json
---

# Local LLM 在 Intel UHD 770 + 32GB RAM 環境下的選擇研究

> **來源：** Gemini Notebook 深度研究（200 筆來源），2026-09-01
> **Job ID：** `rc-20260901-001`
> **Notebook：** https://notebooklm.google.com/notebook/b8e1d221-6784-454f-a20d-cb536253134f

## 一句話摘要

在 Intel UHD 770 內顯 + 32GB RAM 的 Windows 11 商務桌上型電腦上，透過量化（GGUF Q4_K_M）可以讓 7B-14B 模型流暢運行，但多步驟 DevOps skill 編排任務需要混合式架構（確定性路由 + LLM 輔助），而非全交給 LLM。

## 硬體環境

- **機型：** HP Pro Mini 400 G9 Desktop PC
- **CPU：** Intel Core i5-14500T（14 核 20 緒，6P+8E，**不支援 AVX-512**）
- **RAM：** 32GB DDR5 4800 MT/s
- **GPU：** Intel UHD 770（內顯，32 EU，**共享記憶體上限 16GB**）
- **儲存：** 512GB SSD

## 核心發現

### 1. 量化策略 × 模型選擇

#### GPU 全卸載路線（上限 16GB VRAM）
- 7B Q4-Q8：全部能裝入，空間充裕
- 14B Q4_K_M (~9GB)：可 100% 裝入 GPU，**甜點選擇**
- 14B Q5_K_M (~10.2GB)：可全卸載
- 14B Q8_0 (~15-16GB)：耗盡 VRAM，不建議

#### CPU-only 路線（上限 32GB RAM）
- 32B Q4_K_M (~20.5GB)：可跑但只能 CPU-only，速度 ~8-12 tokens/sec
- 32B Q5_K_M (~24GB)：不建議，剩餘記憶體過少

### 2. TTFT 優化
- **Prompt Caching**（`--cache-prompt`）：多輪對話 TTFT 降毫秒級
- **KV Cache 量化**（`q8_0`）：記憶體減半
- **Flash Attention**：降低長 context 佔用
- **執行緒綁 P-Core**（`--threads 6`）：避開慢速 E-Core
- **BIOS IGD Aperture Size 調最大**：避免驅動崩潰

### 3. GitHub Copilot 整合
- **VS Code**：v1.99 起支援 BYOK，直接選 Ollama 模型
- **Copilot CLI**：`COPILOT_PROVIDER_BASE_URL` + `COPILOT_MODEL` + `COPILOT_OFFLINE=true`
- **LiteLLM Proxy**：進階整合，轉發任意 OpenAI-compatible API
- **Continue.dev**：開源替代方案

### 4. 運行方案比較

| 方案 | 易用性 | TTFT | 適合場景 |
|---|---|---|---|
| Ollama | ⭐⭐⭐⭐⭐ | 中等 | 日常首選 |
| llama.cpp + OpenVINO | ⭐ | 極快 | 最在乎 TTFT |
| llama.cpp + Vulkan | ⭐⭐ | 中等 | 省心 GPU 加速 |
| LM Studio | ⭐⭐⭐⭐⭐ | 慢 | 探索模型 |
| GPT4All | ⭐⭐⭐⭐ | 中下 | 低配穩定 |

## 圓桌會議共識（Claude + Gemini + Codex）

經過三方圓桌討論，對研究報告的修正：

1. **模型降級**：報告推薦的 14B/32B 不適合直接作為起點，應改為 **7B 起步**
2. **評測錯位**：Aider 分數（73.7）不適用於評估 DevOps skill 編排能力
3. **混合架構**：應採用「確定性路由 + LLM 輔助」的四層架構
4. **LLM 定位**：副駕駛 / 翻譯員，而非握方向盤的人
5. **Copilot BYOK**：可以進入 PoC，但不應未經測試就作為核心編排器

## 與其他頁面的關係

- 更新 [[wiki/concepts/local-llm-deployment|Local LLM 部署]]：新增低階硬體（UHD 770）的實務經驗
- 對應 [[wiki/concepts/hybrid-intent-router|混合式意圖路由器]]：四層架構的具體實作
- 連接 [[wiki/entities/github-copilot|GitHub Copilot]]：BYOK 整合方式
