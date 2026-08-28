---
type: topic
title: Agent Infrastructure
topic: agent-infrastructure
created: 2026-08-08
updated: 2026-08-08
---

# Agent Infrastructure

> AI Agent 技術基礎設施：協定、語言工具、企業治理的導航頁。

## Entities

- [[wiki/entities/mcp-model-context-protocol|MCP]] — Model Context Protocol，AI Agent 工具整合標準 🛠️
- [[wiki/entities/mcp-authorization|MCP Authorization]] — OAuth 2.1 授權框架 🛠️
- [[wiki/entities/mcp-registry|MCP Registry]] — 官方伺服器註冊表 🛠️
- [[wiki/entities/mcp-extensions|MCP Extensions]] — 擴充機制與支援矩陣 🛠️
- [[wiki/entities/a2a-protocol|A2A]] — Agent2Agent Protocol，Agent 間通訊標準 🆕
- [[wiki/entities/lsp|LSP]] — Language Server Protocol，語言伺服器標準
- [[wiki/entities/tree-sitter|Tree-sitter]] — 增量解析器，程式碼結構分析
- [[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]] — 企業 AI 治理層，MCP + Guardrails + Agent 管理 🛠️
- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — AI Agent Skill 開放標準，跨平台相容 🛠️
- [[wiki/entities/vllm|vLLM]] — 高吞吐量 LLM 推理引擎 🛠️
- [[wiki/entities/llm-d|LLM-D]] — Kubernetes 上的 AI 推理智慧路由器 🛠️

## Concepts

- [[wiki/concepts/mcp-servers|MCP Servers]] — 伺服器端建置與除錯 🛠️
- [[wiki/concepts/mcp-clients|MCP Clients]] — 客戶端建置與 elicitation 🛠️
- [[wiki/concepts/mcp-protocol-versioning|MCP Protocol Versioning]] — 版本控制與功能生命週期 🛠️
- [[wiki/concepts/webmcp|WebMCP]] — 網頁原生 Agent-Ready 工具暴露機制 🆕
- [[wiki/concepts/agents-md-format|AGENTS.md]] — Agent 專案配置規範 🆕
- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — Skill 系統定義、三層漸進式揭露、基礎/進階導航 🛠️
- [[wiki/concepts/sub-agents-pattern|Sub-agents]] — 子 agent 委派模式 🆕
- [[wiki/concepts/code-graph|Code Graph]] — AI Code Assistant 的核心基礎設施 🛠️
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] — 為什麼 AI 需要 GPU 而不是 CPU 🛠️
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] — 從 Token 到 KV Cache 的完整推理流程 🛠️
- [[wiki/concepts/prompt-caching|Prompt Caching]] — LLM 輸入端 KV Cache 重用機制 🛠️
- [[wiki/concepts/model-sharding|Model Sharding]] — 多 GPU 切分巨型模型的兩種策略 🛠️

## Discussions

- [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]] — 深度研究已完成（2026-08-24），涵蓋硬體架構、異質運算、LLM 推理資源失衡

## Sources

- [[wiki/sources/mcp-official-docs-42|MCP 官方文件彙整（42 篇）]] — MCP 協定完整規範 🛠️
- [[wiki/sources/mcp-sdks-and-tooling|MCP SDKs & Tooling]] — 官方 SDK 與開發工具 🛠️
- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]] — AI Code Assistant 核心基礎 🛠️
- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF + LSP + CodeGraph AI Agent 研究]] — 三者整合分析
- [[wiki/sources/2026-08-04-code-review-graph|code-review-graph]] — Tree-sitter 知識圖譜做 Code Review，省 8.2 倍 Token 🛠️
- [[wiki/sources/2026-08-04-rust-analyzer|rust-analyzer]] — Rust LSP 實作，高效能代碼分析 🛠️
- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]] — AI 基礎設施完整課程 🛠️
- [[wiki/sources/2026-08-27-ibm-technology-prompt-caching|What is Prompt Caching? | IBM Technology]] — Prompt Caching 概念解說
- [[wiki/sources/2026-08-27-alejandro-ao-prompt-caching|Prompt Caching Explained — Alejandro AO]] — Agent 設計與成本實作 🛠️
- [[wiki/sources/2026-08-24-npu-deep-research|NPU 在 AI 基礎設施架構中的角色 — 深度研究]] — Gemini Deep Research，54 筆過濾後來源 🛠️
- [[wiki/sources/2026-09-04-ibm-agentic-ai-five-terms|5 Terms You Need to Know About Agentic AI — IBM Technology]] — Agentic AI 五大核心術語 🆕
