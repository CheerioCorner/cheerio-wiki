---
title: morphir-dotnet
type: entity
created: 2026-08-02
updated: 2026-08-05
sources: 1
tags: [morphir, dotnet, finos, enterprise, ir, domain-driven]
collection: entities
topics: [agent-research]
canonical: entities/morphir-dotnet
---

# morphir-dotnet

> FINOS 開發的 .NET binding for Morphir ecosystem，提供 IR（intermediate representation）和 tooling。

## 一句話

morphir-dotnet 是 Morphir 生態系的 .NET 綁定，提供 domain model、IR codecs、CLI tooling 和 AI agent 貢獻指南。

## 核心資訊

| 項目 | 內容 |
|------|------|
| 官方 | https://morphir.finos.org/ |
| GitHub | https://github.com/finos/morphir-dotnet |
| 技術棧 | C# 14, .NET 10, F#, TypeScript（dev tooling）|
| 授權 | FINOS policies |
| CI | dotnet build/test/format, npm ci |

## 架構

```
morphir-dotnet/
├── src/
│   ├── Morphir           # C# CLI/host
│   └── Morphir.Core      # Core domain model and IR definition
├── docs/spec/            # IR specification and JSON/OpenAPI schemas
├── tests/                # TUnit, Reqnroll, contract tests
├── scripts/              # format/test/contract/codegen utilities
└── .agents/              # AI agent guidance (skills, QA, AOT)
```

## 核心特色

### Skills 系統
專業化的 AI agent skills，跨所有主流 coding agents 可用：
- **QA Tester** — test plan design, regression testing, coverage monitoring
- **AOT Guru** — single-file trimmed executables, size optimization
- **Release Manager** — release lifecycle, version selection
- **Technical Writer** — Hugo/Docsy, Mermaid/PlantUML

### TDD 強制
Red-Green-Refactor 是必須的開發流程，BDD-first for features。

### CLI 日誌規則
stdout = data, stderr = diagnostics。絕不把 log 寫到 stdout。

### CLA 合規
禁止 AI assistant 作為 co-author，允許 commit body 中的 attribution。

## 設計原則

- **Immutability-first：** push effects to edges
- **ADTs：** make illegal states unrepresentable; avoid nulls
- **Strong testing：** TUnit + Verify snapshots + Reqnroll BDD + property-based
- **Domain is pure；side effects in adapters**

## 與我們的關係

- AGENTS.md 結構完整，可作為撰寫 agent 指南的參考
- Skills 系統的 professionalized design 值得學習
- TDD 強制流程與 mattpocock/skills 的 tdd skill 一致

## 來源

- [[wiki/sources/2026-08-02-morphir-dotnet-agents]] — AI Agent 貢獻指南
