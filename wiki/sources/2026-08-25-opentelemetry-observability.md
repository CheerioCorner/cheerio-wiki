---
title: "System Observability: Logs, Metrics, Traces & OpenTelemetry"
type: source
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [opentelemetry, observability, youtube, tutorial]
canonical: sources/2026-08-25-opentelemetry-observability
topics: [agent-infrastructure, backend-systems]
provenance:
  - kind: raw
    path: raw/youtube/observability-opentelemetry-logs-metrics-traces.md
---

> 來源：[YouTube — System Observability: Logs, Metrics, Traces & OpenTelemetry](https://youtu.be/umm-MyCl3Q4)（35:51，764 segments，auto-generated English captions）

## 影片摘要

用一個 ride-share 後端從單體到微服務的演化過程，完整介紹系統觀測性的三大支柱（logs、metrics、traces）、OpenTelemetry 標準、SLO/SLA/Error Budget、alerting 原則、eBPF 自動追蹤、continuous profiling、RUM，以及 incident response 流程。

### 核心概念

**Monitoring vs Observability**：Monitoring 是你知道要問什麼問題才建的儀表板（known unknowns）；Observability 是你能回答你沒預先想到的問題（unknown unknowns）。

**三大支柱的關聯**：Observability 的核心不是擁有 logs/metrics/traces，而是能為**同一個請求**在三者之間自由跳轉（correlation via trace_id）。

### 三大信號

1. **Logs**：結構化日誌（JSON）讓每個欄位可查詢。成本通常是最大筆帳單。
2. **Metrics**：Counter（只增不減）、Gauge（可增可減）、Histogram（bucket 聚合，可算 P95）。四大黃金信號：Latency、Traffic、Errors、Saturation。
3. **Traces**：一個請求的完整路徑。Span 是工作單位，trace_id 串接所有 span。Context propagation 透過 `traceparent` header 在服務間傳遞。

### Sampling

- **Head Sampling**：便宜但可能丟掉 error trace
- **Tail Sampling**：保留 error/slow，但需要 buffer

### SLO/SLA/Error Budget

- SLI（你量測的）→ SLO（你的目標）→ SLA（合約承諾）
- Error Budget = 100% - SLO。99.9% → 每月 43 分鐘可當機。
- Burn Rate：4.4x 持續 1 小時 = 花掉 2% budget，值得叫人。

### Alerting 原則

- Page on effects, not causes（使用者痛苦才叫人）
- Short + Long window 雙窗口驗證
- 如果正確反應是聳肩，就不該觸發

### 進階主題

- **eBPF**：kernel 層自動追蹤，零程式碼修改，但不知道業務語義
- **Continuous Profiling**：flame graph 看到哪一行 code 在燒 CPU（~2% overhead）
- **RUM（Real User Monitoring）**：Core Web Vitals（LCP、INP、CLS）
- **Service Map**：從 traces 自動生成，不手畫架構圖

### 完整技術棧

```
[App] → OpenTelemetry SDK → OTLP → [Collector]
                                      ├── Tempo (traces)
                                      ├── Loki (logs)
                                      └── Prometheus (metrics)
                                           ↑
                                      [Grafana] ← 統一視覺化
```

### 與 Agent 系統的關聯

這支影片雖然是講微服務觀測性，但概念直接適用於多 agent 系統：
- Trace 追蹤一個任務在多個 agent 間的執行路徑
- Metrics 追蹤 agent 延遲、token 消耗、任務成功率
- SLI/SLO 定義 agent 服務品質目標
- Alerting 原則（page on effects）適用於 agent 失敗偵測

## 陳述級溯源

- 「Observability means you can understand the system from the outside and ask new questions about how it's behaving without shipping new code first」`[02:19]`
- 「A metric is a number aggregated over time」`[04:03]`
- 「A histogram drops each measurement into a bucket so you can add the buckets up across every instance and read one P95 for the whole fleet」`[05:13]`
- 「OpenTelemetry is not a backend, and that's the single biggest thing people get wrong about it」`[14:34]`
- 「Service name is the one field OpenTelemetry insists you set, and leaving it out is why telemetry shows up as unknown service」`[16:24]`
- 「Observability isn't three separate pillars. It's the ability to correlate them for a single request」`[35:16]`

## 相關頁面

- [[wiki/entities/opentelemetry|opentelemetry]] — entity 頁面
- [[wiki/entities/multi-agent-orchestration-patterns|multi-agent-orchestration-patterns]] — 多 agent 協作的觀測性需求
