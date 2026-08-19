---
title: "Pre-tool use hook"
description: "The onPreToolUse hook is called before a tool executes. Use it to:"
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/pre-tool-use"
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
# Pre-tool use hook

> Source: [Pre-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/pre-tool-use)
> Clipped: 2026-08-14

## Hook signature

```typescript
import type { PreToolUseHookInput, HookInvocation, PreToolUseHookOutput } from "@github/copilot-sdk";
type PreToolUseHandler = (
  input: PreToolUseHookInput,
  invocation: HookInvocation
) => Promise<PreToolUseHookOutput | null | undefined>;
```
```typescript
type PreToolUseHandler = (
  input: PreToolUseHookInput,
  invocation: HookInvocation
) => Promise<PreToolUseHookOutput | null | undefined>;
```

## Input

| Field | Type | Description |
| --- | --- | --- |
| `timestamp` | number | Unix timestamp when the hook was triggered |
| `cwd` | string | Current working directory |
| `toolName` | string | Name of the tool being called |
| `toolArgs` | object | Arguments passed to the tool |

## Output

Return `null` or `undefined` to allow the tool to execute with no changes. Otherwise, return an object with any of these fields:

| Field | Type | Description |
| --- | --- | --- |
| `permissionDecision` | `"allow"` \| `"deny"` \| `"ask"` | Whether to allow the tool call |
| `permissionDecisionReason` | string | Explanation shown to user (for deny/ask) |
| `modifiedArgs` | object | Modified arguments to pass to the tool |
| `additionalContext` | string | Extra context injected into the conversation |
| `suppressOutput` | boolean | If true, tool output won't appear in conversation |

### Permission decisions

| Decision | Behavior |
| --- | --- |
| `"allow"` | Tool executes normally |
| `"deny"` | Tool is blocked, reason shown to user |
| `"ask"` | User is prompted to approve (interactive mode) |

### Skipping permission prompts for trusted custom tools

If you define a custom tool that is safe to run without prompting, set `skipPermission: true` on the tool definition. Use this for trusted, app-owned tools whose inputs are already constrained by your application; use `onPreToolUse` when you need per-call policy checks or argument validation.

```typescript
const getWeather = defineTool("get_weather", {
  description: "Get weather for a location.",
  parameters: {
    type: "object",
    properties: { location: { type: "string" } },
    required: ["location"],
  },
  skipPermission: true,
  handler: async ({ location }) => ({ forecast: \`Sunny in ${location}\` }),
});
```

## Examples

### Allow all tools (logging only)

```typescript
const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input, invocation) => {
      console.log(\`[${invocation.sessionId}] Calling ${input.toolName}\`);
      console.log(\`  Args: ${JSON.stringify(input.toolArgs)}\`);
      return { permissionDecision: "allow" };
    },
  },
});
```

### Block specific tools

```typescript
const BLOCKED_TOOLS = ["shell", "bash", "write_file", "delete_file"];

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      if (BLOCKED_TOOLS.includes(input.toolName)) {
        return {
          permissionDecision: "deny",
          permissionDecisionReason: \`Tool '${input.toolName}' is not permitted in this environment\`,
        };
      }
      return { permissionDecision: "allow" };
    },
  },
});
```

### Modify tool arguments

```typescript
const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      // Add a default timeout to all shell commands
      if (input.toolName === "shell" && input.toolArgs) {
        const args = input.toolArgs as { command: string; timeout?: number };
        return {
          permissionDecision: "allow",
          modifiedArgs: {
            ...args,
            timeout: args.timeout ?? 30000, // Default 30s timeout
          },
        };
      }
      return { permissionDecision: "allow" };
    },
  },
});
```

### Restrict file access to specific directories

```typescript
const ALLOWED_DIRECTORIES = ["/home/user/projects", "/tmp"];

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      if (input.toolName === "read_file" || input.toolName === "write_file") {
        const args = input.toolArgs as { path: string };
        const isAllowed = ALLOWED_DIRECTORIES.some(dir => 
          args.path.startsWith(dir)
        );
        
        if (!isAllowed) {
          return {
            permissionDecision: "deny",
            permissionDecisionReason: \`Access to '${args.path}' is not permitted. Allowed directories: ${ALLOWED_DIRECTORIES.join(", ")}\`,
          };
        }
      }
      return { permissionDecision: "allow" };
    },
  },
});
```

### Suppress verbose tool output

```typescript
const VERBOSE_TOOLS = ["list_directory", "search_files"];

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      return {
        permissionDecision: "allow",
        suppressOutput: VERBOSE_TOOLS.includes(input.toolName),
      };
    },
  },
});
```

### Add context based on tool

```typescript
const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      if (input.toolName === "query_database") {
        return {
          permissionDecision: "allow",
          additionalContext: "Remember: This database uses PostgreSQL syntax. Always use parameterized queries.",
        };
      }
      return { permissionDecision: "allow" };
    },
  },
});
```

## Best practices

1. **Always return a decision** - Returning `null` allows the tool, but being explicit with `{ permissionDecision: "allow" }` is clearer.
2. **Provide helpful denial reasons** - When denying, explain why so users understand:
	```typescript
	return {
	  permissionDecision: "deny",
	  permissionDecisionReason: "Shell commands require approval. Please describe what you want to accomplish.",
	};
	```
3. **Be careful with argument modification** - Ensure modified args maintain the expected schema for the tool.
4. **Consider performance** - Pre-tool hooks run synchronously before each tool call. Keep them fast.
5. **Use `suppressOutput` judiciously** - Suppressing output means the model won't see the result, which may affect conversation quality.