---
title: OpenTelemetry — 分散式系統觀測性標準
type: entity
created: 2026-08-25
updated: 2026-08-25
sources: 1
tags: [opentelemetry, observability, tracing, metrics, logs, cncf]
canonical: entities/opentelemetry
topics: [agent-infrastructure, backend-systems]
---

> OpenTelemetry（OTel）是 CNCF 下的開源標準，用於**生成與傳輸**觀測性遥測資料（traces、metrics、logs）。它不是後端——不儲存資料、不畫儀表板；它只負責產生與運送，後端由 Prometheus / Grafana / Datadog 等工具負責。

## 基本資訊

| 項目 | 內容 |
|---|---|
| 組織 | CNCF（Cloud Native Computing Foundation） |
| 授權 | Apache 2.0 |
| 官網 | [opentelemetry.io](https://opentelemetry.io) |
| 核心職責 | 生成 + 傳輸遥測資料（不儲存、不視覺化） |
| 資料格式 | OTLP（OpenTelemetry Protocol）over HTTP 或 gRPC |

## 三大信號（Three Signals）

### 1. Logs（日誌）

- **定義**：帶時間戳的事件訊息，一個事件一行
- **結構化日誌**：JSON 格式，每個欄位可直接查詢（vs 純文字需 regex）
- **成本**：通常是三者中最大筆的帳單
- **最佳實踐**：保留所有 error，sample 成功請求，丟棄 noisy logs

### 2. Metrics（指標）

- **定義**：隨時間聚合的數字
- **三種類型**：
  - **Counter**：只增不減（如：總請求數），用 `rate()` 查詢
  - **Gauge**：可增可減（如：目前進行中的任務數）
  - **Histogram**：將測量值放入 bucket，可跨實例合併計算 P95
- **四大黃金信號**（Google）：Latency、Traffic、Errors、Saturation
- **RED 方法**（Tom Wilkie）：Rate、Errors、Duration（請求視角）
- **USE 方法**（Brendan Gregg）：Utilization、Saturation、Errors（資源視角）
- **Cardinality 陷阱**：不要把高基數欄位（如 user_id）放在 metric labels 上

### 3. Traces（追蹤）

- **定義**：一個請求在系統中完整路徑的記錄
- **Span**：一個工作單位（name、start/end time、attributes、status）
- **Trace**：一組共享同一 trace_id 的 span 樹狀結構
- **Context Propagation**：透過 `traceparent` HTTP header 在服務間傳遞追蹤上下文
- **Span Link**：非 HTTP 呼叫（如 queue）的兩端關聯方式
- **Exemplar**：metric 上的指針，指向一個具體的 trace

## 三大信號的關聯

```
Log ──────┐
          ├── trace_id ──→ 同一個請求的完整故事
Metric ───┘   (exemplar)
```

**Observability 的核心不是擁有三大支柱，而是能為同一個請求在三者之間自由跳轉。**

## OpenTelemetry 架構

```
[App] → API → SDK → OTLP → [Collector] → [Backend]
                                    ├── Tempo (traces)
                                    ├── Loki (logs)
                                    └── Prometheus (metrics)
                                        ↑
                                   (pull-based)
```

### 關鍵組件

| 組件 | 職責 |
|---|---|
| **API** | 開發者寫code時面對的介面 |
| **SDK** | 實際實作，不裝就沒有遥測 |
| **OTLP** | 標準線格式（over HTTP/gRPC） |
| **Collector** | 接收 → 處理（strip 敏感欄位） → 匯出到多個後端 |
| **Semantic Conventions** | 統一屬性命名（如 HTTP method） |
| **Auto-instrumentation** | 零程式碼修改，自動追蹤 HTTP/DB 呼叫 |
| **Manual instrumentation** | 手動加 span 追蹤業務邏輯 |

## Sampling 策略

| 策略 | 原理 | 優點 | 缺點 |
|---|---|---|---|
| **Head Sampling** | 請求開始時決定 keep/drop | 便宜 | 可能丟掉 error trace |
| **Tail Sampling** | 請求結束後決定 | 保留 error/slow | 需要 buffer，成本較高 |

## SLO / SLA / Error Budget

| 術語 | 定義 |
|---|---|
| **SLI** | 你量測的東西（如：booking 成功率） |
| **SLO** | 你的目標（如：99.9%） |
| **SLA** | 寫進合約的目標（違反要賠錢） |
| **Error Budget** | 100% - SLO（如：99.9% → 每月 43 分鐘可當機） |

**Burn Rate**：花費 error budget 的速度。1x = 預算活 30 天；4.4x 持續 1 小時 = 花掉 2%，值得叫人。

## Alerting 原則

- **Page on effects, not causes**：使用者感受到的痛苦（booking 失敗）才叫人，CPU 81% 不叫
- **Short window + Long window**：兩個窗口都同意才觸發，避免誤報
- **如果正確反應是聳肩，就不該觸發**

## eBPF 自動追蹤

- 在 kernel 層觀測所有系統呼叫，零程式碼修改
- 限制：只看到原始系統呼叫，不知道業務語義（如「收了 $18」）
- **混合模式**：eBPF 做廣泛覆蓋 + 手寫 span 加業務上下文

## Continuous Profiling（第四支柱？）

- 對正在執行的程式進行取樣，產生 flame graph
- 可以看到哪一行 code 在燒 CPU
- 成本低（~2% overhead），可在 production 常駐
- 代表工具：Grafana Pyroscope、Parca

## 與 Agent Observability 的關聯

| 概念 | 在 Agent 系統中的應用 |
|---|---|
| **Trace** | 追蹤一個任務在多個 agent 間的執行路徑 |
| **Span** | 每個 agent 的一次工作單位 |
| **Context Propagation** | agent 間傳遞追蹤上下文（如 traceparent） |
| **Metrics** | agent 延遲、token 消耗、任務成功率 |
| **Logs** | agent 的決策紀錄、工具呼叫結果 |
| **SLI/SLO** | agent 任務完成率、回應時間目標 |
| **Exemplar** | 從 metric spike 跳到具體的 agent trace |

## 來源

- [[wiki/sources/2026-08-25-opentelemetry-observability|2026-08-25 System Observability: Logs, Metrics, Traces & OpenTelemetry]]

## 相關頁面

- [[wiki/entities/multi-agent-orchestration-patterns|multi-agent-orchestration-patterns]] — 多 agent 協作的觀測性需求
- [[wiki/entities/langgraph|LangGraph]] — 有 checkpointing 的 agent runtime
- [[wiki/concepts/agent-durability-patterns|agent-durability-patterns]] — 狀態持久化模式
