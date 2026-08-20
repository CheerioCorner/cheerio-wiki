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
- [[wiki/entities/lsp|LSP]] — Language Server Protocol，語言伺服器標準
- [[wiki/entities/tree-sitter|Tree-sitter]] — 增量解析器，程式碼結構分析
- [[wiki/entities/axway-amplify-ai-gateway|Axway Amplify AI Gateway]] — 企業 AI 治理層，MCP + Guardrails + Agent 管理 🛠️
- [[wiki/entities/agentskills-io-standard|agentskills-io-standard]] — AI Agent Skill 開放標準，跨平台相容 🛠️
- [[wiki/entities/vllm|vLLM]] — 高吞吐量 LLM 推理引擎 🛠️
- [[wiki/entities/llm-d|LLM-D]] — Kubernetes 上的 AI 推理智慧路由器 🛠️

## Concepts

- [[wiki/concepts/agent-skills|Agent Skills 根概念]] — Skill 系統定義、三層漸進式揭露、基礎/進階導航 🛠️
- [[wiki/concepts/code-graph|Code Graph]] — AI Code Assistant 的核心基礎設施 🛠️
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] — 為什麼 AI 需要 GPU 而不是 CPU 🛠️
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] — 從 Token 到 KV Cache 的完整推理流程 🛠️
- [[wiki/concepts/model-sharding|Model Sharding]] — 多 GPU 切分巨型模型的兩種策略 🛠️

## Discussions

- [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]] — 🔴 開放問題：NPU vs GPU 在 AI 推理中的定位

## Sources

- [[wiki/sources/2026-08-04-lsp-code-graph-research|LSP 與 Code Graph 技術深度研究]] — AI Code Assistant 核心基礎 🛠️
- [[wiki/sources/2026-08-04-okf-lsp-codegraph-ai-agent-research|OKF + LSP + CodeGraph AI Agent 研究]] — 三者整合分析
- [[wiki/sources/2026-08-04-code-review-graph|code-review-graph]] — Tree-sitter 知識圖譜做 Code Review，省 8.2 倍 Token 🛠️
- [[wiki/sources/2026-08-04-rust-analyzer|rust-analyzer]] — Rust LSP 實作，高效能代碼分析 🛠️
- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]] — AI 基礎設施完整課程 🛠️
