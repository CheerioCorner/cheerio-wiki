---
title: "morphir-dotnet AGENTS.md — AI Agent 貢獻指南"
type: source
created: 2026-08-02
updated: 2026-08-05
sources: 1
tags: [morphir, dotnet, agents-md, ai-coding-agent, tdd, finos, enterprise]
collection: sources
topics: [ai-agent, skill]
canonical: sources/2026-08-02-morphir-dotnet-agents
---

# morphir-dotnet AGENTS.md — AI Agent 貢獻指南

> FINOS morphir-dotnet 專案的 AGENTS.md，展示企業級 .NET 專案如何組織 AI agent 指南。

## 重點摘要

- **定位：** .NET binding for Morphir ecosystem，提供 IR（intermediate representation）和 tooling
- **AGENTS.md 規模：** 極其完整，涵蓋 18 個章節，從 project overview 到 agent execution rules
- **核心特色：** 專業化的 skills 系統（QA Tester、AOT Guru、Release Manager、Technical Writer）
- **TDD 強制：** Red-Green-Refactor 是必須的開發流程

## 關鍵設計

### Multi-Agent 支援
| Agent | 召喚方式 |
|-------|----------|
| Claude Code | `@skill {skill-name}` |
| GitHub Copilot | 自然語言 + skill name |
| Cursor | `.cursorrules` auto-trigger |
| Windsurf | 自然語言（auto-discovery）|
| JetBrains AI | 自然語言 |

### Skills 系統
- **QA Tester：** test plan design, regression testing, coverage monitoring
- **AOT Guru：** single-file trimmed executables, AOT readiness, size optimization
- **Release Manager：** release lifecycle, changelog management, version selection
- **Technical Writer：** Hugo/Docsy, Mermaid/PlantUML, style guide

### TDD 工作流程
1. **RED：** 先寫失敗的 test
2. **GREEN：** 寫最少的 code 讓 test 通過
3. **REFACTOR：** 改善 code，保持 test 通過
- BDD-first：新功能先寫 feature files
- Snapshot testing（Verify）+ Property-based testing（FsCheck）

### CLI 日誌規則
- **CRITICAL：** CLI tools 絕對不能把 log 寫到 stdout
- stdout = data, stderr = diagnostics
- 用 Serilog configured with `standardErrorFromLevel: LogEventLevel.Verbose`

### CLA 合規
- **禁止：** `Co-Authored-By: Claude` 或任何 AI assistant 作為 co-author
- **允許：** commit body 中的 attribution `🤖 Generated with [Claude Code]`
- 有自動修復腳本 `remove-claude-coauthor.fsx`

## 與我們的關係

- AGENTS.md 的結構可作為我們撰寫 agent 指南的參考
- Skills 系統設計（professionalized skills + cross-agent accessibility）值得學習
- TDD 強制流程與 mattpocock/skills 的 tdd skill 一致
- CLA 合規問題提醒我們在開源專案中注意 AI attribution

## 來源

- GitHub: https://github.com/finos/morphir-dotnet/blob/main/AGENTS.md
- Morphir: https://morphir.finos.org/
