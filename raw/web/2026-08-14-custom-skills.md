---
title: "Custom skills"
description: "Skills are reusable prompt modules that extend Copilot's capabilities. Load skills from directories to give Copilot specialized abilities for specific domains or workflows."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/skills"
source_domain: "github.com"
author:
published:
clipped: 2026-08-14
type: "raw-source"
source_kind: "web"
immutable: "true"
tags:
  - "raw"
  - "web-clip"
---
# Custom skills

> Source: [Custom skills](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/skills)
> Clipped: 2026-08-14

## Overview

A skill is a named directory containing a `SKILL.md` file—a markdown document that provides instructions to Copilot. When loaded, the skill's content is injected into the session context.

Skills allow you to:

- Package domain expertise into reusable modules
- Share specialized behaviors across projects
- Organize complex agent configurations
- Enable/disable capabilities per session

## Loading skills

Specify directories containing skills when creating a session:

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5.4",
    skillDirectories: [
        "./skills/code-review",
        "./skills/documentation",
    ],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});

// Copilot now has access to skills in those directories
await session.sendAndWait({ prompt: "Review this code for security issues" });
```

## Disabling skills

Disable specific skills while keeping others active:

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills"],
    disabledSkills: ["experimental-feature", "deprecated-tool"],
});
```

## Skill directory structure

Each skill is a named subdirectory containing a `SKILL.md` file:

```
skills/
├── code-review/
│   └── SKILL.md
└── documentation/
    └── SKILL.md
```

The `skillDirectories` option points to the parent directory (e.g., `./skills`). The CLI discovers all `SKILL.md` files in immediate subdirectories.

### SKILL.md format

A `SKILL.md` file is a markdown document with optional YAML frontmatter:

```markdown
---
name: code-review
description: Specialized code review capabilities
---

# Code Review Guidelines

When reviewing code, always check for:

1. **Security vulnerabilities** - SQL injection, XSS, etc.
2. **Performance issues** - N+1 queries, memory leaks
3. **Code style** - Consistent formatting, naming conventions
4. **Test coverage** - Are critical paths tested?

Provide specific line-number references and suggested fixes.
```

The frontmatter fields:

- **`name`**: The skill's identifier (used with `disabledSkills` to selectively disable it). If omitted, the directory name is used.
- **`description`**: A short description of what the skill does.

The markdown body contains the instructions that are injected into the session context when the skill is loaded.

## Configuration options

### SessionConfig skill fields

| Language | Field | Type | Description |
| --- | --- | --- | --- |
| Node.js | `skillDirectories` | `string[]` | Directories to load skills from |
| Node.js | `disabledSkills` | `string[]` | Skills to disable |
| Python | `skill_directories` | `list[str]` | Directories to load skills from |
| Python | `disabled_skills` | `list[str]` | Skills to disable |
| Go | `SkillDirectories` | `[]string` | Directories to load skills from |
| Go | `DisabledSkills` | `[]string` | Skills to disable |
| .NET | `SkillDirectories` | `List<string>` | Directories to load skills from |
| .NET | `DisabledSkills` | `List<string>` | Skills to disable |

## Best practices

1. **Organize by domain** - Group related skills together (e.g., `skills/security/`, `skills/testing/`)
2. **Use frontmatter** - Include `name` and `description` in YAML frontmatter for clarity
3. **Document dependencies** - Note any tools or MCP servers a skill requires
4. **Test skills in isolation** - Verify skills work before combining them
5. **Use relative paths** - Keep skills portable across environments

## Combining with other features

### Skills + custom agents

Skills listed in an agent's `skills` field are **eagerly preloaded** —their full content is injected into the agent's context at startup, so the agent has access to the skill instructions immediately without needing to invoke a skill tool. Skill names are resolved from the session-level `skillDirectories`.

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills/security"],
    customAgents: [{
        name: "security-auditor",
        description: "Security-focused code reviewer",
        prompt: "Focus on OWASP Top 10 vulnerabilities",
        skills: ["security-scan", "dependency-check"],
    }],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Skills + MCP servers

Skills can complement MCP server capabilities:

```typescript
const session = await client.createSession({
    skillDirectories: ["./skills/database"],
    mcpServers: {
        postgres: {
            type: "local",
            command: "npx",
            args: ["-y", "@modelcontextprotocol/server-postgres"],
            tools: ["*"],
        },
    },
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Troubleshooting

### Skills not loading

1. **Check path exists** - Verify the skill directory path is correct and contains subdirectories with `SKILL.md` files
2. **Check permissions** - Ensure the SDK can read the directory
3. **Check SKILL.md format** - Verify the markdown is well-formed and any YAML frontmatter uses valid syntax
4. **Enable debug logging** - Set `logLevel: "debug"` to see skill loading logs

### Skill conflicts

If multiple skills provide conflicting instructions:

- Use `disabledSkills` to exclude conflicting skills
- Reorganize skill directories to avoid overlaps

## See also

- [Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started#create-custom-agents) - Define specialized AI personas
- [Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started#step-4-add-a-custom-tool) - Build your own tools
- [Using MCP servers with the GitHub Copilot SDK](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/mcp) - Connect external tool providers