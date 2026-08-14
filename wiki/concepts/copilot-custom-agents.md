---
title: "Copilot Custom Agents — Sub-agent Orchestration"
type: concept
created: 2026-08-14
updated: 2026-08-14
sources: 2
tags: [github-copilot, custom-agents, sub-agent, orchestration, skills, mcp]
topics: [skill, ai-development-tools]
canonical: concepts/copilot-custom-agents
provenance:
  - kind: raw
    path: "raw/web/2026-08-14-custom-agents-and-sub-agent-orchestration.md"
  - kind: raw
    path: "raw/web/2026-08-14-custom-skills.md"
---

# Copilot Custom Agents — Sub-agent Orchestration

> 定義具有 scoped tools/prompts 的輕量級 agent，runtime 自動委派為 sub-agent，在隔離 context 中執行。

## Custom Agent 定義

每個 agent 有：
- **`name`**：唯一識別碼
- **`prompt`**：獨立 system prompt
- **`tools`**：受限的工具集（`null` = 全部工具）
- **`mcpServers`**：per-agent MCP server
- **`skills`**：per-agent 預載入 skills（eagerly preloaded）
- **`infer`**：是否允許 runtime 自動選取（default: `true`）
- **`model`** / **`reasoningEffort`**：per-agent 模型覆蓋

## Sub-agent 委派流程

1. **Intent matching**：runtime 分析使用者 prompt，匹配 agent 的 name/description
2. **Agent selection**：若匹配且 `infer: true`，runtime 選取 agent
3. **Isolated execution**：sub-agent 在隔離 context 中執行
4. **Event streaming**：`subagent.started/completed/failed/selected/deselected` 串流回 parent
5. **Result integration**：sub-agent 輸出整合進 parent response

## Custom Skills 系統

與 Anthropic Agent Skills 格式兼容的 SKILL.md：

```
skills/
├── code-review/
│   └── SKILL.md
└── documentation/
    └── SKILL.md
```

### 載入方式

- **Session 層**：`skillDirectories` 參數載入，`disabledSkills` 禁用
- **Agent 層**：agent 的 `skills` 陣列會 **eagerly preload**（完整內容注入 context）
- **繼承**：sub-agents **不繼承** parent 的 skills

### SKILL.md 格式

```markdown
---
name: code-review
description: Specialized code review capabilities
---
# Code Review Guidelines
[指令內容]
```

## Agent-Exclusive Tools

`defaultAgent.excludedTools` 可將特定工具從預設 agent 隱藏，強制委派給 sub-agent：

```typescript
defaultAgent: {
    excludedTools: ["analyze-codebase"],  // 預設 agent 看不到此工具
},
customAgents: [{
    name: "researcher",
    tools: ["analyze-codebase"],          // sub-agent 擁有此工具
}]
```

## 搭配 MCP Server

每個 custom agent 可有自己的 MCP server：

```typescript
customAgents: [{
    name: "db-analyst",
    mcpServers: {
        "database": { command: "npx", args: ["-y", "@modelcontextprotocol/server-postgres"] },
    },
}]
```

## 來源

- [[wiki/sources/2026-08-14-custom-agents-and-sub-agent-orchestration|Custom Agents]]
- [[wiki/sources/2026-08-14-custom-skills|Custom Skills]]

## 相關頁面

- [[wiki/entities/github-copilot-sdk|GitHub Copilot SDK]]
- [[wiki/concepts/copilot-agent-loop|Agent Loop]]
- [[wiki/concepts/copilot-fleet-mode|Fleet Mode]]
- [[wiki/concepts/copilot-hooks-system|Hooks 系統]]
