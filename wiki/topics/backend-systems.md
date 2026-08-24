---
type: topic
title: Backend Systems
topic: backend-systems
created: 2026-08-10
updated: 2026-08-10
---

# Backend Systems

> 後端系統設計模式：快取策略、Session 管理、資料庫架構的導航頁。

## Concepts

- [[wiki/concepts/redis|Redis]] — In-memory key-value 資料庫，μs 級延遲 🛠️
- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference]] — 為什麼 AI 需要 GPU 而不是 CPU 🛠️
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture]] — 從 Token 到 KV Cache 的完整推理流程 🛠️
- [[wiki/concepts/model-sharding|Model Sharding]] — 多 GPU 切分巨型模型的兩種策略 🛠️

## Entities

- [[wiki/entities/vllm|vLLM]] — 高吞吐量 LLM 推理引擎 🛠️
- [[wiki/entities/llm-d|LLM-D]] — Kubernetes 上的 AI 推理智慧路由器 🛠️

## Sources — AI Infrastructure

- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]] — Mumshad Mannambeth 的 AI 基礎設施完整課程
- [[wiki/sources/2026-08-24-npu-deep-research|NPU 在 AI 基礎設施架構中的角色 — 深度研究]] — Gemini Deep Research，54 筆過濾後來源 🛠️

## Sources — Redis

- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns for Caching and Session Management]] — 快取模式、Session、Rate Limiting 🛠️
- [[wiki/sources/2026-08-03-redis-cluster-architecture|Redis Enterprise Cluster Architecture]] — 叢架架構、Sharding、HA 99.999% 🛠️
- [[wiki/sources/2026-08-03-redis-eviction-policy|Redis Eviction Policy 完整指南]] — 10 種淘汰策略 🛠️
- [[wiki/sources/2026-08-03-redis-memory-optimization|Redis Memory Optimization 策略]] — 記憶體優化技巧 🛠️
- [[wiki/sources/2026-08-03-redis-performance-tuning|Redis Performance Tuning 最佳實踐]] — 效能調校要點 🛠️
- [[wiki/sources/2026-08-03-redis-security-practices|Redis Security Best Practices]] — 部署安全、叢集安全、資料庫安全 🛠️
- [[wiki/sources/2026-08-03-redis-acl|Redis ACL 完整指南]] — 存取控制列表、權限管理 🛠️
- [[wiki/sources/2026-08-03-redis-sentinel-client-spec|Redis Sentinel Client Spec]] — 客戶端整合規範、服務發現 🛠️
- [[wiki/sources/2026-08-03-redis-licenses|Redis 授權指南]] — RSALv2/SSPLv1/AGPLv3 三選一授權 🛠️
