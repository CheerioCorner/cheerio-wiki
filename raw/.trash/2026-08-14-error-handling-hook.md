---
title: "Error handling hook"
description: "The onErrorOccurred hook is called when errors occur during session execution. Use it to:"
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/error-handling"
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
# Error handling hook

> Source: [Error handling hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/error-handling)
> Clipped: 2026-08-14

## Hook signature

```typescript
import type { ErrorOccurredHookInput, HookInvocation, ErrorOccurredHookOutput } from "@github/copilot-sdk";
type ErrorOccurredHandler = (
  input: ErrorOccurredHookInput,
  invocation: HookInvocation
) => Promise<ErrorOccurredHookOutput | null | undefined>;
```
```typescript
type ErrorOccurredHandler = (
  input: ErrorOccurredHookInput,
  invocation: HookInvocation
) => Promise<ErrorOccurredHookOutput | null | undefined>;
```

## Input

| Field | Type | Description |
| --- | --- | --- |
| `timestamp` | number | Unix timestamp when the error occurred |
| `cwd` | string | Current working directory |
| `error` | string | Error message |
| `errorContext` | string | Where the error occurred: `"model_call"`, `"tool_execution"`, `"system"`, or `"user_input"` |
| `recoverable` | boolean | Whether the error can potentially be recovered from |

## Output

Return `null` or `undefined` to use default error handling. Otherwise, return an object with:

| Field | Type | Description |
| --- | --- | --- |
| `suppressOutput` | boolean | If true, don't show error output to user |
| `errorHandling` | string | How to handle: `"retry"`, `"skip"`, or `"abort"` |
| `retryCount` | number | Number of times to retry (if errorHandling is `"retry"`) |
| `userNotification` | string | Custom message to show the user |

## Examples

### Basic error logging

```typescript
const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input, invocation) => {
      console.error(\`[${invocation.sessionId}] Error: ${input.error}\`);
      console.error(\`  Context: ${input.errorContext}\`);
      console.error(\`  Recoverable: ${input.recoverable}\`);
      return null;
    },
  },
});
```

### Send errors to monitoring service

```typescript
import { captureException } from "@sentry/node"; // or your monitoring service

const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input, invocation) => {
      captureException(new Error(input.error), {
        tags: {
          sessionId: invocation.sessionId,
          errorContext: input.errorContext,
        },
        extra: {
          error: input.error,
          recoverable: input.recoverable,
          cwd: input.cwd,
        },
      });
      
      return null;
    },
  },
});
```

### User-friendly error messages

```typescript
const ERROR_MESSAGES: Record<string, string> = {
  "model_call": "There was an issue communicating with the AI model. Please try again.",
  "tool_execution": "A tool failed to execute. Please check your inputs and try again.",
  "system": "A system error occurred. Please try again later.",
  "user_input": "There was an issue with your input. Please check and try again.",
};

const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input) => {
      const friendlyMessage = ERROR_MESSAGES[input.errorContext];
      
      if (friendlyMessage) {
        return {
          userNotification: friendlyMessage,
        };
      }
      
      return null;
    },
  },
});
```

### Suppress non-critical errors

```typescript
const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input) => {
      // Suppress tool execution errors that are recoverable
      if (input.errorContext === "tool_execution" && input.recoverable) {
        console.log(\`Suppressed recoverable error: ${input.error}\`);
        return { suppressOutput: true };
      }
      return null;
    },
  },
});
```

### Add recovery context

```typescript
const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input) => {
      if (input.errorContext === "tool_execution") {
        return {
          userNotification: \`
The tool failed. Here are some recovery suggestions:
- Check if required dependencies are installed
- Verify file paths are correct
- Try a simpler approach
          \`.trim(),
        };
      }
      
      if (input.errorContext === "model_call" && input.error.includes("rate")) {
        return {
          errorHandling: "retry",
          retryCount: 3,
          userNotification: "Rate limit hit. Retrying...",
        };
      }
      
      return null;
    },
  },
});
```

### Track error patterns

```typescript
interface ErrorStats {
  count: number;
  lastOccurred: number;
  contexts: string[];
}

const errorStats = new Map<string, ErrorStats>();

const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input, invocation) => {
      const key = \`${input.errorContext}:50
\`;
      
      const existing = errorStats.get(key) || {
        count: 0,
        lastOccurred: 0,
        contexts: [],
      };
      
      existing.count++;
      existing.lastOccurred = input.timestamp;
      existing.contexts.push(invocation.sessionId);
      
      errorStats.set(key, existing);
      
      // Alert if error is recurring
      if (existing.count >= 5) {
        console.warn(\`Recurring error detected: ${key} (${existing.count} times)\`);
      }
      
      return null;
    },
  },
});
```

### Alert on critical errors

```typescript
const CRITICAL_CONTEXTS = ["system", "model_call"];

const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input, invocation) => {
      if (CRITICAL_CONTEXTS.includes(input.errorContext) && !input.recoverable) {
        await sendAlert({
          level: "critical",
          message: \`Critical error in session ${invocation.sessionId}\`,
          error: input.error,
          context: input.errorContext,
          timestamp: new Date(input.timestamp).toISOString(),
        });
      }
      
      return null;
    },
  },
});
```

### Combine with other hooks for context

```typescript
const sessionContext = new Map<string, { lastTool?: string; lastPrompt?: string }>();

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input, invocation) => {
      const ctx = sessionContext.get(invocation.sessionId) || {};
      ctx.lastTool = input.toolName;
      sessionContext.set(invocation.sessionId, ctx);
      return { permissionDecision: "allow" };
    },
    
    onUserPromptSubmitted: async (input, invocation) => {
      const ctx = sessionContext.get(invocation.sessionId) || {};
      ctx.lastPrompt = input.prompt.substring(0, 100);
      sessionContext.set(invocation.sessionId, ctx);
      return null;
    },
    
    onErrorOccurred: async (input, invocation) => {
      const ctx = sessionContext.get(invocation.sessionId);
      
      console.error(\`Error in session ${invocation.sessionId}:\`);
      console.error(\`  Error: ${input.error}\`);
      console.error(\`  Context: ${input.errorContext}\`);
      if (ctx?.lastTool) {
        console.error(\`  Last tool: ${ctx.lastTool}\`);
      }
      if (ctx?.lastPrompt) {
        console.error(\`  Last prompt: ${ctx.lastPrompt}...\`);
      }
      
      return null;
    },
  },
});
```

## Best practices

1. **Always log errors** - Even if you suppress them from users, keep logs for debugging.
2. **Categorize errors** - Use `errorType` to handle different errors appropriately.
3. **Don't swallow critical errors** - Only suppress errors you're certain are non-critical.
4. **Keep hooks fast** - Error handling shouldn't slow down recovery.
5. **Provide helpful context** - When errors occur, `additionalContext` can help the model recover.
6. **Monitor error patterns** - Track recurring errors to identify systemic issues.