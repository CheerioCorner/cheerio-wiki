---
title: "從模型部署到 Agent Harness：Qwen 3.8 27B 與 DGX Spark 實機示範"
type: source
created: 2026-08-30
updated: 2026-08-30
sources: 1
tags: [local-llm, open-weight-model, agent-harness, dgx-spark, qwen, deepseek, inference-engine, quantization, security-boundary]
topics: [ai-agent, agent-infrastructure, agent-runtime-implementations]
provenance:
  - kind: raw
    path: raw/youtube/2026-08-30-local-llm-agent-harness-dgx-spark.md
    url: https://www.youtube.com/watch?v=7tNIw_gHeSI
---

# 從模型部署到 Agent Harness：Qwen 3.8 27B 與 DGX Spark 實機示範

> **講者：** 胡嘉璽（Josh），嘉崎人智技術長 `[01:44]`
> **時長：** 02:20:16 | Segments: 2052 → 193 paragraphs | Language: zh-TW | Source: API
> **來源：** [[raw/youtube/2026-08-30-local-llm-agent-harness-dgx-spark|YouTube 影片逐字稿]]

## 核心論點

Josh 在保哥的直播分享中，完整示範如何用一台 DGX Spark（13.5 萬台幣）搭配地端模型與開源 harness，跑出可實際工作的 agent 系統。核心論點：**Agent 的上限不是 model，是 harness** `[131:05]`。開源模型（特別是中國模型）已追上閉源 frontier 模型到同一個領先群 `[20:06]`，加上 128GB Unified Memory 的平價硬體出現，讓地端模型從「玩具」變成「可用工具」`[41:58]`。

## 影片結構與重點

### 1. Agent = Model + Harness `[05:23]`
- **Model**：NBA 球員的身高體重、彈跳高度——天分，改變不了 `[05:23]`
- **Harness**：運球技能、三分線技能、團隊配合——訓練出來的技能 `[05:23]`
- 兩者結合才是完整的 agent `[06:46]`
- Model 只佔 agent 架構的一小部分，其餘 90% 是 harness 的責任 `[83:22]`

### 2. 閉源 vs 開放權重模型 `[10:52]`
- **閉源模型**：OpenAI GPT、Anthropic Claude（Haiku/Sonnet/Opus/Fable）、Google Gemini、xAI Grok `[10:52]`
- **成本結構**：美國電費 + NBA 球員級工程師薪水 + NV 顯卡成本 → 反映在 token 定價 `[12:08]`
- **開放權重模型**：90% 收斂到中國 `[16:41]`——DeepSeek、千問、GLM、Kimi K3、MiMo、MiniMax、渾源等上百家 `[16:41]`
- **入場券**：2026 年 3 月羅福莉說入場券是 1TB `[13:54]`，但現在 Kimi K3 2.8T、千問 3 2.4T，實際入場券已升至 2T 以上 `[15:21]`

### 3. 開放權重模型追上閉源的速度 `[18:58]`
- Dario Amodei（Anthropic CEO）3 月說「開放權重追上前端模型要 6-18 個月」`[18:58]`
- 但 2026 年 8 月 Arena.ai 的排行榜顯示：Kimi K3 在 Opus 出來之前長期佔據第一名 `[20:06]`
- 前 12 名中有一半是開放權重模型，前 3 名有 2 名是開放權重 `[20:35]`
- **結論：開放權重模型已正式進入使用階段** `[16:41]`

### 4. 地端模型的必要性 `[38:28]`
- 三個優勢：① 只需服務小群人（幾百人的 agentic workflow）② LLM 不離開公司 ③ 搭配好的 harness 更安全 `[40:00]`
- 適合金融、銀行、保險、證券等注重資安的行業 `[45:28]`
- 已上線應用：翻譯、摘要、影片轉檔、Word/Excel 去識別化 `[45:28]`

### 5. MoE vs Dense 模型 `[23:28]`
- **MoE（Mixture of Experts）**：大腦只取出一部分神經元應付目前狀況，像開車時只注意路況 `[23:28]`
- **Dense**：大腦在開車時不但要注意路況，還要去想古典樂和莎士比亚 `[24:25]`
- DeepSeek V4 是 MoE 模型 `[23:28]`；千問 3.8 27B 是 Dense 模型 `[35:45]`

### 6. KV Cache 優化技術 `[25:41]`
- Attention 機制需要對每個 token 做 QKV 資料庫查詢（模糊查詢）`[27:01]`
- KV 值相乘造成巨大運算負擔 `[28:31]`
- 各種優化技術（CSA、HCA、mHC、MLA）都是為了讓 KV cache 變小 `[28:31]`
- 2027 年（第 10 年）仍沒有能取代 Attention 的架構 `[28:31]`

### 7. MTP（Multi-Token Prediction）`[31:37]`
- 一次預測多個 token，若命中可大幅加速輸出 `[33:03]`
- 必須在訓練時就做，inference engine 也要支援 `[33:03]`
- 千問放上 MTP 後速度提升顯著 `[33:03]`

### 8. Qwen 3.8 27B `[35:45]`
- Dense 模型，hidden size 5120，tokenizer 24 萬 `[35:45]`
- Context window 260K，可開思考模式（不同強度）`[37:03]`
- 完全開源（不只是開放權重）`[37:03]`
- 5090（32GB）可跑 FP8 版本，4090 可跑 NVFP4 `[38:28]`
- FP8 降到 NVFP4 精度只掉約 2 個百分點 `[38:28]`
- **比 DeepSeek 好很多**：DeepSeek 在 128GB 上只能跑 Q2，品質有問題；千問 27B 完全不會漏 `[109:06]`

### 9. 量化（Quantization）`[48:42]`
- **原理**：模型參數中 0.12 與 0.123456 意思差不多，不需要高精度 `[48:42]`
- **FP8**：8 位元量化，記憶體用量 ≈ 參數量（GB ≈ B）`[54:04]`
- **NVFP4**：比 FP8 少一半多一點，精度掉約 2% `[38:28]`
- **QAT（Quantization-Aware Training）**：訓練時就知道要量化 `[50:09]`
- **GGUF**：打包格式，把權重 + tokenizer + config + toolparser 合成一個檔案 `[55:19]`

### 10. 硬體選擇 `[41:58]`
- **DGX Spark**：128GB Unified Memory，CUDA 生態，Linux 系統佔記憶體少 `[41:58]`，上限 3003MHz，建議限 2100MHz 避免熱當 `[100:03]`
- **Mac M-series**：128GB+ 可跑，MLX 模型多，但散熱嚴重 `[118:16]`
- **AMD AI395/495**：128GB，x86 架構，很多 inference engine 只有 x86 `[44:06]`
- **RTX Spark**（即將推出）：Windows on ARM + CUDA `[44:06]`
- **GB300 Desktop**：技嘉 768GB（400+ GB RAM + 272GB VRAM），9.9 萬美金 `[45:28]`

### 11. Inference Engine 比較 `[69:02]`
| Engine | 特色 | 推薦度 |
|--------|------|--------|
| **vLLM** | 參數設定最完整、OpenAI 相容端點、適合正式部署 `[71:40]` | ⭐⭐⭐⭐⭐ |
| **SGLang** | 與 vLLM 類似，有些模型只支援 SGLang `[73:05]` | ⭐⭐⭐⭐ |
| **TRT-LLM** | NV 官方、CUDA 相容性最佳、參數較麻煩 `[71:40]` | ⭐⭐⭐ |
| **llama.cpp** | 老字號、GGUF 格式、模型格式專一 `[69:02]` | ⭐⭐⭐ |
| **Ollama** | 方便、適合測試實驗 `[70:19]` | ⭐⭐⭐ |
| **LM Studio** | GUI 方便、適合桌面環境 `[71:40]` | ⭐⭐⭐ |
| **DFlash** | MLX 架構、Mac 專用、速度特別快 `[69:02]` | ⭐⭐⭐ |

### 12. Harness 安全等級（L1-L7）`[93:22]`
| 等級 | Model 位置 | Harness 位置 | 安全度 |
|------|-----------|-------------|--------|
| **L1** | 雲端（公有） | 本機（無防護） | 🔴 最危險 |
| **L2** | 雲端 | Sandbox（VM/Docker） | 🟡 |
| **L3** | 雲端（可控廠商） | 開源 harness | 🟡 |
| **L4** | 雲端 | 開源 harness + Sandbox | 🟢 |
| **L5** | 私有雲 | Sandbox | 🟢 |
| **L6** | 私有雲 | OpenShell（無法上網） | 🟢🟢 |
| **L7** | 私有雲 | OpenShell + 斷網 | 🟢🟢🟢 最安全 |

### 13. 開源 Harness 比較 `[80:06]`
- **閉源**：Claude Code、Claude Codework、ChatGPT/Codex、Cursor（被 xAI 買）、VS Code + Copilot `[84:45]`
- **開源通用**：OpenClaw（龍蝦）、Hermes Agent `[85:50]`
- **開源極簡**：Pi Agent（4 個工具：read/write/edit/bash）`[87:08]`
- **開源全包**：OpenClaw / Go-Pi（Pi 為原型，預裝常用功能）`[136:36]`
- **退流行原因**：coding agent（Claude Code、Codex）也變強了，功能逐漸覆蓋通用 agent `[84:45]`

### 14. Pi Agent 特色 `[87:08]`
- 極簡化：只有 4 個工具（read/write/edit/bash）`[87:08]`
- 沒有 MCP、沒有 Sub-agent，全部用 extension 裝起來 `[87:08]`
- 模組化像樂高積木，需要的裝上去、不需要的拿走 `[114:15]`
- Token efficiency 比其他好很多 `[115:24]`
- TUI 可顯示圖片（Mac）`[117:33]`

### 15. 實機示範：去識別化（De-ID）`[91:40]`
- **案例**：銀行稽核專員需要去識別化客戶資料 `[91:40]`
- **最危險場景（L1）**：閉源 harness + 雲端模型，3 分鐘後資安部電話就來 `[99:43]`
- **安全場景**：Pi Agent + Qwen 27B 地端模型，完全不出網路 `[99:43]`
- **最安全場景（L6/L7）**：Pi Agent + Qwen 27B 在 OpenShell 沙箱中，連 Google/GitHub 都出不去 `[103:26]`
- 27B 模型對 1000 筆 Excel 資料做去識別化，效果跟雲端模型一樣好 `[110:29]`

### 16. 用 Claude Code 架設推論引擎 `[73:46]`
- 把 Hugging Face model card 貼給 Claude Code，它會幫你用 Docker 架起來 `[73:46]`
- CLAUDE.md 規定「所有服務都用 Docker」`[74:50]`
- 重要原則：docker start/stop 方便切換模型、開放 OpenAI endpoint 給區域網路、指定 toolparser `[76:32]`

## Cheer 的想法

> 來自 [[raw/conversations/2026-08-30-local-llm-thoughts|看完地端 AI 模型追上閉源巨頭影片的想法]]

### 想法摘要

1. **開源模型追上閉源的速度驚人**：Dario Amodei 說要 6-18 個月，但實際上可能更快。Kimi K3 在 Opus 出來前長期佔據榜首
2. **混合運用 LLM 是未來方向**：閉源 + 開源模型各自有適用場景
3. **硬體設備評估**：DGX Spark、Mac、AMD AI395/495、RTX Spark 等選擇
4. **成本考量**：地端模型的營運成本遠低於 API 訂閱
5. **安全邊界**：不同 harness + 模型組合有不同的安全等級
6. **跟個人專案的關聯**：W-074 架構設計、W-080 ADO Agent 都可以從中學習

### Cheer 的完整原文

> 這部影片主要講到目前地端 AI 模型以驚人的速度追上閉源巨頭模型，也帶動了 Local LLM 跟在本地建置 Local LLM 的相關趨勢。我覺得混合運用 LLM 的方式將會是未來的方向。無論是對公司內部還是我個人來說，我都會想在兩邊有所投入，並評估個人或公司需不需要採購不同情境所需要的硬體設備，將其成本納入考量。因為接下來我會用 AI 來創造更多我的想像（包括我們的專案），所以收集這些資訊滿重要的。

## 與既有知識的關聯

- [[wiki/concepts/harness|Harness — LLM 的驅動層]]：本影片進一步拆解 harness 的安全等級（L1-L7），是對 harness 概念的實務補充
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]]：影片補充了 UMA 架構（DGX Spark、Mac）的實際使用經驗，與既有 GPU 比較形成互補
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]]：影片從使用者角度比較各種 inference engine，是對架構概念的實務落地
- [[wiki/entities/vllm|vLLM]]：影片確認 vLLM 是「正式部署」的首選推論引擎
- [[wiki/entities/hermes-agent|Hermes Agent]]：影片指出 Hermes 和 OpenClaw 逐漸退流行，因為 coding agent 變強了
- [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 角色討論]]：影片提到中國模型在受限硬體（H100、昇騰 910C）上做架構優化
- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|AI Infrastructure 影片]]：同一講者（胡嘉璽）之前的分享，從基礎設施角度介紹 vLLM 與 Kubernetes

## 關鍵引用

- 「Agent 的上限不是 model，是 harness」`[131:05]`
- 「開放權重模型要追上前端模型要 6 到 18 個月」——Dario Amodei，2026 年 3 月 `[18:58]`
- 「現在你要進入模型市場，你的入場券是 1TB」——羅福莉（小米），2026 年 3 月 `[13:54]`
- 「我們要的不是快，我們要的是可用」——DGX Spark 的定位 `[41:58]`
- 「危險的地方絕對不會是模型，一個 agent 最危險絕對是 harness」`[83:22]`
- 「能用 CPU 解決的事情就不要用 GPU 解決，因為 GPU 是 non-deterministic」`[131:05]`
