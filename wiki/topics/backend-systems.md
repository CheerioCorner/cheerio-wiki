---
type: topic
title: Backend Systems
topic: backend-systems
created: 2026-08-10
updated: 2026-08-10
---

# Backend Systems

> 與 Backend Systems 相關的技術實體、抽象概念與研究來源。

## Entities

- [[wiki/entities/llm-d|LLM-D — Kubernetes 上的 AI 推理智慧路由器]] 🛠️
- [[wiki/entities/vllm|vLLM — 高吞吐量 LLM 推理引擎]] 🛠️

## Concepts

- [[wiki/concepts/gpu-architecture-for-ai-inference|GPU Architecture for AI Inference — 為什麼 AI 需要 GPU 而不是 CPU]] 🛠️
- [[wiki/concepts/llm-serving-architecture|LLM Serving Architecture — 從 Token 到 KV Cache 的完整推理流程]] — 💡 這就是為什麼短問題回應快、長 Prompt 要等很久才蹦出第一個字——長 Prompt 的 Prefill 階段需要更多時間。 🛠️
- [[wiki/concepts/model-sharding|Model Sharding — 多 GPU 切分巨型模型的兩種策略]] — Chatty talk 放盒子裡（同機器 NVLink），Light talk 放盒子間（跨機器網路）`[34:07]` 🛠️
- [[wiki/concepts/prompt-caching|Prompt Caching — LLM 輸入端 KV Cache 重用機制]] 🛠️
- [[wiki/concepts/redis|Redis]] — In-memory key-value 資料庫，μs 級延遲，適合快取、Session、即時排行、Rate Limiting。

## Sources

- [[wiki/sources/2026-08-03-redis-acl|Redis ACL（Access Control List）完整指南]] — 來源：[Redis ACL](https://redis.io/docs/latest/operate/oss_and_stack/management/security/acl/)
- [[wiki/sources/2026-08-03-redis-cluster-architecture|Redis Enterprise Cluster Architecture]] — 來源：[Redis Enterprise Cluster Architecture](https://redis.io/technology/redis-enterprise-cluster-architecture/)
- [[wiki/sources/2026-08-03-redis-eviction-policy|Redis Eviction Policy 完整指南]] — 來源：[Redis Eviction Policy](https://redis.io/docs/latest/operate/rs/databases/memory-performance/eviction-policy/)
- [[wiki/sources/2026-08-03-redis-licenses|Redis 授權指南 — 企業導入必讀]] — 來源：[Redis Licenses](https://redis.io/legal/licenses/)
- [[wiki/sources/2026-08-03-redis-memory-optimization|Redis Memory Optimization 策略]] — 來源：[Redis Memory Optimization](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/memory-optimization/)
- [[wiki/sources/2026-08-03-redis-performance-tuning|Redis Performance Tuning 最佳實踐]] — 來源：[Redis Performance Tuning Best Practices](https://redis.io/faq/doc/1mebipyp1e/performance-tuning-best-practices)
- [[wiki/sources/2026-08-03-redis-security-practices|Redis Security Best Practices]] — 來源：[Redis Recommended Security Practices](https://redis.io/docs/latest/operate/rs/security/recommended-security-practices/)
- [[wiki/sources/2026-08-03-redis-sentinel-client-spec|Redis Sentinel Client Spec — 客戶端整合規範]] — 來源：[Redis Sentinel Client Spec](https://redis.io/docs/latest/develop/reference/sentinel-clients/)
- [[wiki/sources/2026-08-09-redis-caching-patterns|Redis Design Patterns for Caching and Session Management]] — 來源：Artem Khrienov (2026-03-05) — Medium 26 分鐘長文，涵蓋 Redis 數據結構、緩存模式、Session 管理、Rate Limiting 與最佳實踐。
- [[wiki/sources/2026-08-21-understanding-ai-infrastructure-gpus-vllm-kubernetes|Understanding AI Infrastructure: GPUs, vLLM, and Kubernetes]] — Duration: 00:51:00 | Segments: 219 | Language: en | Source: API (auto-generated) 🛠️
- [[wiki/sources/2026-08-24-npu-deep-research|NPU 在 AI 基礎設施架構中的角色 — 深度研究]] — Gemini Deep Research 執行的深度研究報告（job rc-20260824-001），涵蓋 54 筆過濾後來源，主題：NPU 在 AI 基礎設施架構中扮演什麼角色、為什麼 AI 時代需要 NPU。 🛠️
- [[wiki/sources/2026-08-27-alejandro-ao-prompt-caching|Prompt Caching Explained — Alejandro AO — Agent 設計與成本實作]] — 之前已經看過 GPU 作為 LLM Server 如何進行 AI 運算的基礎原理，看完這兩支影片後，更能理解 Prompt Caching 和傳統應用系統的 Caching 不同之處，也更明白這在節省 Token 上背後的原理是什麼。 🛠️
- [[wiki/sources/2026-08-27-ibm-technology-prompt-caching|What is Prompt Caching? | IBM Technology — 概念解說]] — 之前已經看過 GPU 作為 LLM Server 如何進行 AI 運算的基礎原理，看完這兩支影片後，更能理解 Prompt Caching 和傳統應用系統的 Caching 不同之處，也更明白這在節省 Token 上背後的原理是什麼。 🛠️

## Others

- [[wiki/discussions/npu-role-in-ai-infrastructure|NPU 在 AI 基礎設施架構中的角色]] — 影片中並沒有提到 NPU。那 NPU 在整個 AI 基礎設施架構中扮演什麼角色？它是什麼樣的運作，跟為什麼說它更適合 AI 時代？為什麼現在除了 CPU、GPU 之外，還需要 NPU？ 🛠️

## 相關 Topics
