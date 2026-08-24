---
title: "NPU 在 AI 基礎設施架構中的角色 — 深度研究"
type: source
created: 2026-08-24
updated: 2026-08-24
sources: 54
tags: [npu, ai-chip, edge-ai, hardware, quantization, dataflow-architecture, pim]
topics: [backend-systems, agent-infrastructure]
provenance:
  - kind: raw
    path: raw/deep-research/rc-20260824-001/research-report.md
---

# NPU 在 AI 基礎設施架構中的角色 — 深度研究

> Gemini Deep Research 執行的深度研究報告（job rc-20260824-001），涵蓋 54 筆過濾後來源，主題：NPU 在 AI 基礎設施架構中扮演什麼角色、為什麼 AI 時代需要 NPU。

## 來源筆記

這份報告是 Cheer 為了回填 [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]] 而執行的深度研究（W-2026-08-069）。原始問題來自 Mumshad Mannambeth 的 AI Infrastructure 課程中「完全沒有提到 NPU」的缺口。

## 研究方法

- 工具：Gemini Deep Research（NotebookLM，`nlm` CLI）
- 品質過濾：62 → 54 筆（移除 8 筆低品質來源）
- 品質過濾移除的來源類型：商業行銷頁面、產品推廣頁面、大會宣傳頁面
- 來源分類/重新命名：48 筆成功
- Profile：personal
- 深度：deep
- 來源偏好：mixed（學術 + 產業 + 媒體）

## 三大章節摘要

### 1. NPU 在異質運算架構中的角色定位

在高度異質化的 AI 基礎設施架構中，CPU、GPU、TPU 與 NPU 扮演互補的角色：

- **CPU**：通用型處理器，負責作業系統啟動、通用邏輯控制、資料預處理、分詞（Tokenization）與採樣（Sampling）等分支複雜的串行任務
- **GPU**：數千個並行核心，極度擅長高吞吐量的數據並行計算，在 AI **訓練（Training）**、微調以及高度可編程的並行任務上扮演主導角色
- **TPU**：由 Google 專門為雲端 TensorFlow/JAX 張量計算設計的客製化 ASIC 晶片
- **NPU**：專門為**深度學習神經網路推理（Inference）**進行極致硬體優化的專用硬體加速器，以超低功耗與極低延遲在邊緣端或本地端不間斷地加速 AI 模型運行

### 2. NPU 核心硬體架構與關鍵技術

NPU 之所以能在神經網路推理上實現超越 CPU 與 GPU 數十至上百倍的能效比，源自其在晶片設計上對神經網路特性的極致客製化：

**脈動陣列（Systolic Array）**：幾乎所有 NPU、TPU 晶片的核心。資料像心臟泵血一樣在相鄰 PE（Processing Element）之間有節奏地傳遞。以**權重駐留（Weight Stationary, WS）**為例，模型權重預先加載到各個 PE 的暫存器中保持不動，而輸入激活值在 PE 之間水平流動複用，實現 N² 個 MAC 運算在極少外部記憶體存取下完成。

**靈活的資料流與可重構技術**：除 WS 外，還有輸出駐留（OS）、輸入駐留（IS）與行駐留（RS）等模式。近代頂尖 NPU 整合了**可重構脈動陣列技術**，透過 multiplexer 控制，在編譯期或運行時調整邏輯矩陣的形狀及資料流模式。

**低精度量化與混合精度計算**：NPU 高度優化了 INT8、INT4、FP16 與 FP8 等低精度運算。INT8 量化能將權重縮小 4 倍，MAC 運算速度可提升 4 倍以上，精度損失通常小於 1%。最新協同設計支援 INT4（用於權重與 KV-Cache）與 FP8（用於激活值與注意力分數）的混合精度量化。

**極致的片上緩存與存內計算（PIM）**：NPU 內部將大量晶粒面積轉為堆疊超高速的片上 SRAM 緩衝區（如 Apple ANE 的 192MB 統一 SRAM 暫存）。NPU-PIM 異質加速系統將輕量級計算單元直接置於 DRAM 旁，實現近數據計算。

### 3. 為什麼 AI 時代（尤其是大模型時代）需要 NPU

**解決 LLM 推理 Prefill 與 Decode 的階段性資源失衡**：LLM 推理包含 Prefill（計算密集型）與 Decode（記憶體頻寬受限型）兩個階段。NPU 透過大容量片上 SRAM 緩存、動態預取與高效率 DMA 控制器，能以極佳的能效處理 GEMV 解碼任務。FlexNPU 虛擬化技術實現「動態 Prefill-Decode 共存調度」，可將系統整體吞吐量提高 26.33%，首字延遲降低達 92.75% 以上。

**本地/邊緣端部署的熱保護與功耗續航問題**：旗艦手機 GPU/CPU 在連續執行 LLM 推理時，會因為 junction 溫度急速飆升觸發 DVFS 熱保護機制降頻，導致生成速度衰減 40% 到 50%。專用 NPU（如 Hailo-10H）在低於 2W 超低功耗下，能維持完全不降頻的完美熱穩定運行。

**Windows 11 Copilot+ PC 本地生態門檻與模型管線化協同**：微軟 Copilot+ PC 必須配備至少 40+ TOPS 的 NPU。透過 Windows ML 與 DirectML 架構，NPU 與 GPU 實現「模型管線化」異質協同——將 Stable Diffusion 交給 GPU、Yolov8 與 RCAN 交給 NPU，總推理延遲從 179.65 秒縮短至 16.57 秒。

## 與既有頁面的差異

本報告相較於既有的 [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]]（2026-08-21，8 筆來源）：

- **不推翻任何既有結論**
- **補充的深度**：
  - 資料流架構拆解為 WS/OS/IS/RS 四種具體變體（既有只籠統提「資料流架構」）
  - 低精度量化策略（INT8/INT4/FP8 混合精度）——既有完全沒提
  - PIM 存內計算與片上 SRAM 緩存設計——既有完全沒提
  - 邊緣端熱降頻對持續推理效能的實際衝擊——既有只講功耗沒講降頻
  - Windows Copilot+ PC 生態門檻與模型管線化協同——產業落地新素材

## 參考來源

54 筆來源經 Gemini Notebook 品質過濾與重新命名，完整清單見 `raw/deep-research/rc-20260824-001/research-report.md`。

## 相關頁面

- [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]] — 原始討論頁面（本報告用於回填）
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] — GPU 的完整角色
- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]] — 原始影片來源
