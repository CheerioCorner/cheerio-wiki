---
type: topic
title: Agent Infrastructure
topic: agent-infrastructure
created: 2026-08-08
updated: 2026-08-08
---

# Agent Infrastructure

> 與 Agent Infrastructure 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/a2a-protocol|A2A（Agent2Agent Protocol）]] — Agent 間通訊的開放協定，讓不同 agent 能互相發現、溝通與協作。
- [[wiki/entities/agentskills-io-standard|agentskills.io — AI Agent Skill 開放標準]] — 一個包含**指令、腳本、資源**的可移植工作流模塊。 🛠️
- [[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway — 企業 AI 治理層]] — 企業級 AI 閘道，在前端應用程式、AI Agents 與企業後端 IT 生態系之間搭建安全、受控且靈活的橋樑。
- [[wiki/entities/dgx-spark|NVIDIA DGX Spark — 128GB Unified Memory 的地端 AI 工作站]] — NVIDIA 推出的平價地端 AI 工作站，128GB Unified Memory，讓地端模型從「玩具」變「可用工具」。 🛠️
- [[wiki/entities/llm-d|LLM-D — Kubernetes 上的 AI 推理智慧路由器]] 🛠️
- [[wiki/entities/lsp|LSP (Language Server Protocol)]] — 微軟提出的語言伺服器協定，將程式語言語意分析從編輯器中解耦，實現 $M \times N \rightarrow M + N$ 的架構革新。 🛠️
- [[wiki/entities/mcp-authorization|MCP Authorization — OAuth 2.1 授權框架]] — MCP 使用標準化的 OAuth 2.1 授權流程保護伺服器上的敏感資源與操作，支援一般 OAuth、Client Credentials 與企業託管授權三種模式。 🛠️
- [[wiki/entities/mcp-model-context-protocol|MCP (Model Context Protocol)]] — Anthropic 發起的開源標準，讓 AI 應用程式透過統一協定連接外部系統——就像 AI 世界的 USB-C 介面。 🛠️
- [[wiki/entities/mcp-registry|MCP Registry — 官方伺服器註冊表]] — MCP Registry 是官方的 MCP Server 註冊表，支援多種套件格式（npm、PyPI、Docker 等），提供伺服器發現、審查政策與自動化發佈流程。 🛠️
- [[wiki/entities/tree-sitter|Tree-sitter]] — 高效能 Parser Generator，支援毫秒級增量解析，是現代 Code Intelligence 的核心基礎設施。 🛠️
- [[wiki/entities/vllm|vLLM — 高吞吐量 LLM 推理引擎]] 🛠️
- [[wiki/entities/opentelemetry|OpenTelemetry — 分散式系統觀測性標準]] — CNCF 開源標準，用於生成與傳輸 traces/metrics/logs。OTLP 協議、Collector 架構、Sampling 策略、SLO/SLA/Error Budget。 🛠️
- [[wiki/entities/agent-security|Agent Security — AI Agent 安全基礎]] — Prompt Injection 防禦、Agent 權限邊界、多 Agent 信任模型、Sandbox 隔離。 🛠️
- [[wiki/entities/ai-threat-modeling|AI System Threat Modeling — AI 系統威脅建模]] — STRIDE × AI Agent、Attack Surface 分類、威脅建模工具。 🛠️

## Concepts

- [[wiki/concepts/agent-extensibility-hierarchy|Agent 擴充架構層級 — 從 Skill、Hook 到 Extension 的能力邊界]] — 當 Skill 不足以控制流程，下一層是什麼？系統化比較 Skill / Hook / Extension 的能力光譜與適用場景。**屬於 [[wiki/concepts/agent-skills|Agent Skills 根概念]] 的擴充層。** 🛠️
- [[wiki/concepts/agent-skills|Agent Skills — Skill 系統根概念]] — Skill 是**可重複使用、基於檔案系統的模組化能力單元**，為 AI Agent 提供特定領域的專業知識，將通用代理轉變為專家。 🛠️
- [[wiki/concepts/agents-md-format|AGENTS.md — Agent 專案配置規範]] — 放在專案根目錄的 markdown 文字檔，每次 agent 開始工作時自動讀取，用來定義專案的行為規則。類似 README，但寫給 agent 看。 🛠️
- [[wiki/concepts/code-graph|Code Graph]] — 跨檔案、跨模組的代碼語意圖譜，結合 AST、控制流圖、資料流圖與全域符號表，是現代 Code Intelligence 與 AI RAG 的核心基礎。 🛠️
- [[wiki/concepts/agent-security-levels|Agent 安全等級 — 從 L1 到 L7 的防護層級]] — 根據 model 和 harness 的部署位置，定義從「最危險」到「最安全」的七個防護層級。核心原則：危險的地方絕對不會是模型，最危險的是 harness。 🛠️
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference — 為什麼 AI 需要 GPU 而不是 CPU]] 🛠️
- [[wiki/concepts/local-llm-deployment|Local LLM 部署 — 從雲端到地端的基礎設施選擇]] — 將開放權重模型安裝在自有硬體上執行的基礎設施策略，涵蓋硬體選擇、量化計算、安全考量。 🛠️
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture — 從 Token 到 KV Cache 的完整推理流程]] — 💡 這就是為什麼短問題回應快、長 Prompt 要等很久才蹦出第一個字——長 Prompt 的 Prefill 階段需要更多時間。 🛠️
- [[wiki/concepts/mcp-clients|MCP Clients — 建置與操作]] — MCP Client 是維持與 MCP Server 連線的元件，負責為 MCP Host 取得上下文。每個 Client 維持與對應 Server 的專用連線。 🛠️
- [[wiki/concepts/mcp-protocol-versioning|MCP Protocol Versioning — 版本控制與演進]] — MCP 使用 `YYYY-MM-DD` 格式的字串版本識別碼，表示最後一次向後不相容變更的日期，並透過功能生命週期管理 deprecation。 🛠️
- [[wiki/concepts/mcp-servers|MCP Servers — 建置與操作]] — MCP Server 是向 MCP Client 提供上下文的程式，透過暴露 tools、resources、prompts 三種原語讓 AI 應用程式存取外部系統。 🛠️
- [[wiki/concepts/model-sharding|Model Sharding — 多 GPU 切分巨型模型的兩種策略]] — Chatty talk 放盒子裡（同機器 NVLink），Light talk 放盒子間（跨機器網路）`[34:07]` 🛠️
- [[wiki/concepts/prompt-caching|Prompt Caching — LLM 輸入端 KV Cache 重用機制]] 🛠️
- [[wiki/concepts/sub-agents-pattern|Sub-agents — 子 agent 委派模式]] — 主 agent 將複雜任務拆解，分派給多個子 agent 在獨立 context window 中執行，完成後回傳結果。 🛠️
- [[wiki/concepts/webmcp|WebMCP — 網頁原生的 Agent-Ready 工具暴露機制]] — WebMCP 讓網站在頁面內直接宣告 agent-ready 工具，透過 browser API 暴露給 AI agent，不需額外的 MCP server。工具生命週期與頁面生命週期綁定，不同頁面暴露不同工具。 🛠️

## Sources

- [[wiki/sources/2026-08-04-code-review-graph|code-review-graph：用知識圖譜幫 AI Code Review 省下 8 倍 Token]] — 來源：[別再讓 AI 讀整個專案了！code-review-graph 用知識圖譜幫你省下 8 倍 Token](https://repoinside.com/tirth8205/code-review-graph) 🛠️
- [[wiki/sources/2026-08-04-rust-analyzer|rust-analyzer — Rust 語言的 LSP 實作]] — 來源：[rust-analyzer Introduction](https://rust-analyzer.github.io/book/)
- [[wiki/sources/2026-08-07-ai-related-seeds|Notion AI 相關種子 — agentskills.io + Axway AI Gateway]] — 來源：Notion「任何當下 → AI相關」頁面 🛠️
- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]] — Duration: 00:51:00 | Segments: 219 | Language: en | Source: API (auto-generated) 🛠️
- [[wiki/sources/2026-08-30-local-llm-agent-harness-dgx-spark|從模型部署到 Agent Harness：Qwen 3.8 27B 與 DGX Spark 實機示範]] — 胡嘉璽（Josh）完整示範地端模型 + 開源 harness 的實機操作，涵蓋 MoE vs Dense、量化、Inference Engine 比較、安全等級 L1-L7。 🛠️
- [[wiki/sources/2026-08-24-npu-deep-research|NPU 在 AI 基礎設施架構中的角色 — 深度研究]] — Gemini Deep Research 執行的深度研究報告（job rc-20260824-001），涵蓋 54 筆過濾後來源，主題：NPU 在 AI 基礎設施架構中扮演什麼角色、為什麼 AI 時代需要 NPU。 🛠️
- [[wiki/sources/2026-08-24-webmcp-youtube|Make your website agent ready with WebMCP]] — 來源：[YouTube — Make your website agent ready with WebMCP](https://www.youtube.com/watch?v=FARxSG_EY98) 🛠️
- [[wiki/sources/2026-08-27-alejandro-ao-prompt-caching|Prompt Caching Explained — Alejandro AO — Agent 設計與成本實作]] — 之前已經看過 GPU 作為 LLM Server 如何進行 AI 運算的基礎原理，看完這兩支影片後，更能理解 Prompt Caching 和傳統應用系統的 Caching 不同之處，也更明白這在節省 Token 上背後的原理是什麼。 🛠️
- [[wiki/sources/2026-08-27-ibm-technology-prompt-caching|What is Prompt Caching? | IBM Technology — 概念解說]] — 之前已經看過 GPU 作為 LLM Server 如何進行 AI 運算的基礎原理，看完這兩支影片後，更能理解 Prompt Caching 和傳統應用系統的 Caching 不同之處，也更明白這在節省 Token 上背後的原理是什麼。 🛠️
- [[wiki/sources/2026-08-25-opentelemetry-observability|System Observability: Logs, Metrics, Traces & OpenTelemetry]] — YouTube 影片：從 ride-share 後端的演化過程完整介紹觀測性三大支柱、OpenTelemetry、SLO/SLA、alerting、eBPF、continuous profiling。 🛠️
- [[wiki/sources/2026-08-30-agent-security-basics|Agent Security 基礎深度研究]] — Prompt Injection 防禦、Agent 權限邊界、多 Agent 信任模型、Sandbox 隔離。 🛠️
- [[wiki/sources/2026-08-30-enterprise-api-security|Enterprise API Security 深度研究]] — OAuth 2.0、Azure AD、API Key 管理、DLP。 🛠️
- [[wiki/sources/2026-08-30-ai-threat-modeling|AI Threat Modeling 深度研究]] — STRIDE × AI Agent、Attack Surface 分類、威脅建模工具。 🛠️
- [[wiki/sources/2026-08-30-skill-supply-chain-security|Skill Supply Chain Security 深度研究]] — Skill 供應鏈風險、權限控管、Sandbox 隔離、生態系安全比較。 🛠️
- [[wiki/sources/2026-09-04-ibm-agentic-ai-five-terms|5 Terms You Need to Know About Agentic AI — IBM Technology]] — IBM Technology 出品，主講 Martin Keen。10 分鐘內拆解 front-end AI agent 底下的五個核心組件。 🛠️

## Others

- [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]] — 影片中並沒有提到 NPU。那 NPU 在整個 AI 基礎設施架構中扮演什麼角色？它是什麼樣的運作，跟為什麼說它更適合 AI 時代？為什麼現在除了 CPU、GPU 之外，還需要 NPU？ 🛠️

## 相關 Topics
