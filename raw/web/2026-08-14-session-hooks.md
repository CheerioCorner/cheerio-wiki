---
title: "Session hooks"
description: "Hooks allow you to intercept and customize the behavior of Copilot sessions at key points in the conversation lifecycle. Use hooks to:"
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/hooks-overview"
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
# Session hooks

> Source: [Session hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/hooks-overview)
> Clipped: 2026-08-14

- **Control tool execution** - approve, deny, or modify tool calls
- **Transform results** - modify tool outputs before they're processed
- **Add context** - inject additional information at session start
- **Handle errors** - implement custom error handling
- **Audit and log** - track all interactions for compliance

## Available hooks

| Hook | Trigger | Use Case |
| --- | --- | --- |
| [Pre-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/pre-tool-use) | Before a tool executes | Permission control, argument validation |
| [Post-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/post-tool-use) | After a tool executes (success only) | Result transformation, logging |
| [Post-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/post-tool-use#failure-variant) | After a tool execution whose result was a failure | Inject retry guidance, log failures |
| [User prompt submitted hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/user-prompt-submitted) | When user sends a message | Prompt modification, filtering |
| [Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle#session-start) | Session begins | Add context, configure session |
| [Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle#session-end) | Session ends | Cleanup, analytics |
| [Error handling hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/error-handling) | Error happens | Custom error handling |
| [Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle#agent-stop) | Top-level agent naturally stops | Validate completion or request another turn |

## Quick start

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      console.log(\`Tool called: ${input.toolName}\`);
      // Allow all tools
      return { permissionDecision: "allow" };
    },
    onPostToolUse: async (input) => {
      console.log(\`Tool result: ${JSON.stringify(input.toolResult)}\`);
      return null; // No modifications
    },
    onSessionStart: async (input) => {
      return { additionalContext: "User prefers concise answers." };
    },
  },
});
```

## Hook invocation context

Every hook receives an `invocation` parameter with context about the current session:

| Field | Type | Description |
| --- | --- | --- |
| `sessionId` | string | The ID of the current session |

This allows hooks to maintain state or perform session-specific logic.

## Common patterns

### Logging all tool calls

```typescript
const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      console.log(\`[${new Date().toISOString()}] Tool: ${input.toolName}, Args: ${JSON.stringify(input.toolArgs)}\`);
      return { permissionDecision: "allow" };
    },
    onPostToolUse: async (input) => {
      console.log(\`[${new Date().toISOString()}] Result: ${JSON.stringify(input.toolResult)}\`);
      return null;
    },
  },
});
```

### Blocking dangerous tools

```typescript
const BLOCKED_TOOLS = ["shell", "bash", "exec"];

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      if (BLOCKED_TOOLS.includes(input.toolName)) {
        return {
          permissionDecision: "deny",
          permissionDecisionReason: "Shell access is not permitted",
        };
      }
      return { permissionDecision: "allow" };
    },
  },
});
```

### Adding user context

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async () => {
      const userPrefs = await loadUserPreferences();
      return {
        additionalContext: \`User preferences: ${JSON.stringify(userPrefs)}\`,
      };
    },
  },
});
```

## Hook guides

- **[Pre-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/pre-tool-use)** - Control tool execution permissions
- **[Post-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/post-tool-use)** - Transform tool results
- **[User prompt submitted hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/user-prompt-submitted)** - Modify user prompts
- **[Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle)** - Session start and end
- **[Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle#agent-stop)** - Validate completion before the agent stops
- **[Error handling hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/error-handling)** - Custom error handling