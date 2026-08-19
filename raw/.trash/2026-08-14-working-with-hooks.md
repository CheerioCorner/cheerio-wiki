---
title: "Working with hooks"
description: "Hooks let you plug custom logic into every stage of a Copilot session—from the moment it starts, through each user prompt and tool call, to the moment it ends. This guide walks through practical use cases so you can ship permissions, auditing, notifications, and more without modifying the core agent behavior."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/hooks"
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
# Working with hooks

> Source: [Working with hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/hooks)
> Clipped: 2026-08-14

## Overview

A hook is a callback you register once when creating a session. The SDK invokes it at a well-defined point in the conversation lifecycle, passes contextual input, and optionally accepts output that modifies the session's behavior.

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-34198/mw-1440/images/help/copilot/copilot-sdk/features-hooks-diagram-0.webp)

| Hook | When it fires | What you can do |
| --- | --- | --- |
| [Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle#session-start) | Session begins (new or resumed) | Inject context, load preferences |
| [User prompt submitted hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/user-prompt-submitted) | User sends a message | Rewrite prompts, add context, filter input |
| [Pre-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/pre-tool-use) | Before a tool executes | Allow / deny / modify the call |
| [Post-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/post-tool-use) | After a tool returns (success only) | Transform results, redact secrets, audit |
| [Post-tool use hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/post-tool-use#failure-variant) | After a tool returns a failure | Inject retry guidance, log failures |
| [Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle#session-end) | Session ends | Clean up, record metrics |
| [Error handling hook](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/error-handling) | An error is raised | Custom logging, retry logic, alerts |

All hooks are **optional** —register only the ones you need. Returning `null` (or the language equivalent) from any hook tells the SDK to continue with default behavior.

## Registering hooks

Pass a `hooks` object when you create (or resume) a session. Every example below follows this pattern.

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
await client.start();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      /* ... */
    },
    onPreToolUse: async (input, invocation) => {
      /* ... */
    },
    onPostToolUse: async (input, invocation) => {
      /* ... */
    },
    // ... add only the hooks you need
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Use case: permission control

Use `onPreToolUse` to build a permission layer that decides which tools the agent may run, what arguments are allowed, and whether the user should be prompted before execution.

### Allow-list a safe set of tools

```typescript
const READ_ONLY_TOOLS = ["read_file", "glob", "grep", "view"];

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      if (!READ_ONLY_TOOLS.includes(input.toolName)) {
        return {
          permissionDecision: "deny",
          permissionDecisionReason: \`Only read-only tools are allowed. "${input.toolName}" was blocked.\`,
        };
      }
      return { permissionDecision: "allow" };
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Restrict file access to specific directories

```typescript
const ALLOWED_DIRS = ["/home/user/projects", "/tmp"];

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      if (["read_file", "write_file", "edit"].includes(input.toolName)) {
        const filePath = (input.toolArgs as { path: string }).path;
        const allowed = ALLOWED_DIRS.some((dir) => filePath.startsWith(dir));

        if (!allowed) {
          return {
            permissionDecision: "deny",
            permissionDecisionReason: \`Access to "${filePath}" is outside the allowed directories.\`,
          };
        }
      }
      return { permissionDecision: "allow" };
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Ask the user before destructive operations

```typescript
const DESTRUCTIVE_TOOLS = ["delete_file", "shell", "bash"];

const session = await client.createSession({
  hooks: {
    onPreToolUse: async (input) => {
      if (DESTRUCTIVE_TOOLS.includes(input.toolName)) {
        return { permissionDecision: "ask" };
      }
      return { permissionDecision: "allow" };
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

Returning `"ask"` delegates the decision to the user at runtime—useful for destructive actions where you want a human in the loop.

## Use case: auditing and compliance

Combine `onPreToolUse`, `onPostToolUse`, and the session lifecycle hooks to build a complete audit trail that records every action the agent takes.

### Structured audit log

```typescript
interface AuditEntry {
  timestamp: Date;
  sessionId: string;
  event: string;
  toolName?: string;
  toolArgs?: unknown;
  toolResult?: unknown;
  prompt?: string;
}

const auditLog: AuditEntry[] = [];

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      auditLog.push({
        timestamp: input.timestamp,
        sessionId: invocation.sessionId,
        event: "session_start",
      });
      return null;
    },
    onUserPromptSubmitted: async (input, invocation) => {
      auditLog.push({
        timestamp: input.timestamp,
        sessionId: invocation.sessionId,
        event: "user_prompt",
        prompt: input.prompt,
      });
      return null;
    },
    onPreToolUse: async (input, invocation) => {
      auditLog.push({
        timestamp: input.timestamp,
        sessionId: invocation.sessionId,
        event: "tool_call",
        toolName: input.toolName,
        toolArgs: input.toolArgs,
      });
      return { permissionDecision: "allow" };
    },
    onPostToolUse: async (input, invocation) => {
      auditLog.push({
        timestamp: input.timestamp,
        sessionId: invocation.sessionId,
        event: "tool_result",
        toolName: input.toolName,
        toolResult: input.toolResult,
      });
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      auditLog.push({
        timestamp: input.timestamp,
        sessionId: invocation.sessionId,
        event: "session_end",
      });

      // Persist the log — swap this with your own storage backend
      await fs.promises.writeFile(
        \`audit-${invocation.sessionId}.json\`,
        JSON.stringify(auditLog, null, 2),
      );
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Redact secrets from tool results

```typescript
const SECRET_PATTERNS = [
  /(?:api[_-]?key|token|secret|password)\s*[:=]\s*["']?[\w\-\.]+["']?/gi,
];

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      if (typeof input.toolResult !== "string") return null;

      let redacted = input.toolResult;
      for (const pattern of SECRET_PATTERNS) {
        redacted = redacted.replace(pattern, "[REDACTED]");
      }

      return redacted !== input.toolResult
        ? { modifiedResult: redacted }
        : null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Use case: notifications and sounds

Hooks fire in your application's process, so you can trigger any side-effect—desktop notifications, sounds, Slack messages, or webhook calls.

### Desktop notification on session events

```typescript
import notifier from "node-notifier"; // npm install node-notifier

const session = await client.createSession({
  hooks: {
    onSessionEnd: async (input, invocation) => {
      notifier.notify({
        title: "Copilot Session Complete",
        message: \`Session 8
 finished (${input.reason}).\`,
      });
      return null;
    },
    onErrorOccurred: async (input) => {
      notifier.notify({
        title: "Copilot Error",
        message: input.error.slice(0, 200),
      });
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Play a sound when a tool finishes

```typescript
import { exec } from "node:child_process";

const session = await client.createSession({
  hooks: {
    onPostToolUse: async (input) => {
      // macOS: play a system sound after every tool call
      exec("afplay /System/Library/Sounds/Pop.aiff");
      return null;
    },
    onErrorOccurred: async () => {
      exec("afplay /System/Library/Sounds/Basso.aiff");
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Post to Slack on errors

```typescript
const SLACK_WEBHOOK_URL = process.env.SLACK_WEBHOOK_URL!;

const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input, invocation) => {
      if (!input.recoverable) {
        await fetch(SLACK_WEBHOOK_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({
            text: \`🚨 Unrecoverable error in session \\`8
\\`:\n\\`\\`\\`${input.error}\\`\\`\\`\`,
          }),
        });
      }
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Use case: prompt enrichment

Use `onSessionStart` and `onUserPromptSubmitted` to automatically inject context so users don't have to repeat themselves.

### Inject project metadata at session start

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input) => {
      const pkg = JSON.parse(
        await fs.promises.readFile("package.json", "utf-8"),
      );
      return {
        additionalContext: [
          \`Project: ${pkg.name} v${pkg.version}\`,
          \`Node: ${process.version}\`,
          \`Working directory: ${input.workingDirectory}\`,
        ].join("\n"),
      };
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Expand shorthand commands in prompts

```typescript
const SHORTCUTS: Record<string, string> = {
  "/fix": "Find and fix all errors in the current file",
  "/test": "Write comprehensive unit tests for this code",
  "/explain": "Explain this code in detail",
  "/refactor": "Refactor this code to improve readability",
};

const session = await client.createSession({
  hooks: {
    onUserPromptSubmitted: async (input) => {
      for (const [shortcut, expansion] of Object.entries(SHORTCUTS)) {
        if (input.prompt.startsWith(shortcut)) {
          const rest = input.prompt.slice(shortcut.length).trim();
          return { modifiedPrompt: rest ? \`${expansion}: ${rest}\` : expansion };
        }
      }
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Use case: error handling and recovery

The `onErrorOccurred` hook gives you a chance to react to failures—whether that means retrying, notifying a human, or gracefully shutting down.

### Retry transient model errors

```typescript
const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input) => {
      if (input.errorContext === "model_call" && input.recoverable) {
        return {
          errorHandling: "retry",
          retryCount: 3,
          userNotification: "Temporary model issue — retrying…",
        };
      }
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

### Friendly error messages

```typescript
const FRIENDLY_MESSAGES: Record<string, string> = {
  model_call: "The AI model is temporarily unavailable. Please try again.",
  tool_execution: "A tool encountered an error. Check inputs and try again.",
  system: "A system error occurred. Please try again later.",
};

const session = await client.createSession({
  hooks: {
    onErrorOccurred: async (input) => {
      return {
        userNotification: FRIENDLY_MESSAGES[input.errorContext] ?? input.error,
      };
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Use case: session metrics

Track how long sessions run, how many tools are invoked, and why sessions end—useful for dashboards and cost monitoring.

```typescript
const metrics = new Map<
  string,
  { start: Date; toolCalls: number; prompts: number }
>();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      metrics.set(invocation.sessionId, {
        start: input.timestamp,
        toolCalls: 0,
        prompts: 0,
      });
      return null;
    },
    onUserPromptSubmitted: async (_input, invocation) => {
      metrics.get(invocation.sessionId)!.prompts++;
      return null;
    },
    onPreToolUse: async (_input, invocation) => {
      metrics.get(invocation.sessionId)!.toolCalls++;
      return { permissionDecision: "allow" };
    },
    onSessionEnd: async (input, invocation) => {
      const m = metrics.get(invocation.sessionId)!;
      const durationSec =
        (input.timestamp.getTime() - m.start.getTime()) / 1000;

      console.log(
        \`Session 8
: \` +
          \`${durationSec.toFixed(1)}s, ${m.prompts} prompts, \` +
          \`${m.toolCalls} tool calls, ended: ${input.reason}\`,
      );

      metrics.delete(invocation.sessionId);
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Combining hooks

Hooks compose naturally. A single `hooks` object can handle permissions **and** auditing **and** notifications—each hook does its own job.

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input) => {
      console.log(\`[audit] session started in ${input.workingDirectory}\`);
      return { additionalContext: "Project uses TypeScript and Vitest." };
    },
    onPreToolUse: async (input) => {
      console.log(\`[audit] tool requested: ${input.toolName}\`);
      if (input.toolName === "shell") {
        return { permissionDecision: "ask" };
      }
      return { permissionDecision: "allow" };
    },
    onPostToolUse: async (input) => {
      console.log(\`[audit] tool completed: ${input.toolName}\`);
      return null;
    },
    onErrorOccurred: async (input) => {
      console.error(\`[alert] ${input.errorContext}: ${input.error}\`);
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      console.log(
        \`[audit] session 8
 ended: ${input.reason}\`,
      );
      return null;
    },
  },
  onPermissionRequest: async () => ({ kind: "approve-once" }),
});
```

## Best practices

1. **Keep hooks fast.** Every hook runs inline—slow hooks delay the conversation. Offload heavy work (database writes, HTTP calls) to a background queue when possible.
2. **Return `null` when you have nothing to change.** This tells the SDK to proceed with defaults and avoids unnecessary object allocation.
3. **Be explicit with permission decisions.** Returning `{ permissionDecision: "allow" }` is clearer than returning `null`, even though both allow the tool.
4. **Don't swallow critical errors.** It's fine to suppress recoverable tool errors, but always log or alert on unrecoverable ones.
5. **Use `additionalContext` instead of `modifiedPrompt` when possible.** Appending context preserves the user's original intent while still guiding the model.
6. **Scope state by session ID.** If you track per-session data, key it on `invocation.sessionId` and clean up in `onSessionEnd`.