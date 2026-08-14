---
title: "Session lifecycle hooks"
description: "Session lifecycle hooks let you respond to session start and end events. Use them to:"
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle"
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
# Session lifecycle hooks

> Source: [Session lifecycle hooks](https://docs.github.com/en/copilot/how-tos/copilot-sdk/hooks/session-lifecycle)
> Clipped: 2026-08-14

## Session start hook {#session-start}

The `onSessionStart` hook is called when a session begins (new or resumed).

### Hook signature

```typescript
import type { SessionStartHookInput, HookInvocation, SessionStartHookOutput } from "@github/copilot-sdk";
type SessionStartHandler = (
  input: SessionStartHookInput,
  invocation: HookInvocation
) => Promise<SessionStartHookOutput | null | undefined>;
```
```typescript
type SessionStartHandler = (
  input: SessionStartHookInput,
  invocation: HookInvocation
) => Promise<SessionStartHookOutput | null | undefined>;
```

### Input

| Field | Type | Description |
| --- | --- | --- |
| `timestamp` | number | Unix timestamp when the hook was triggered |
| `cwd` | string | Current working directory |
| `source` | `"startup"` \| `"resume"` \| `"new"` | How the session was started |
| `initialPrompt` | string \| undefined | The initial prompt if provided |

### Output

| Field | Type | Description |
| --- | --- | --- |
| `additionalContext` | string | Context to add at session start |
| `modifiedConfig` | object | Override session configuration |

### Examples

#### Add project context at start

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      console.log(\`Session ${invocation.sessionId} started (${input.source})\`);
      
      const projectInfo = await detectProjectType(input.cwd);
      
      return {
        additionalContext: \`
This is a ${projectInfo.type} project.
Main language: ${projectInfo.language}
Package manager: ${projectInfo.packageManager}
        \`.trim(),
      };
    },
  },
});
```

#### Handle session resume

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      if (input.source === "resume") {
        // Load previous session state
        const previousState = await loadSessionState(invocation.sessionId);
        
        return {
          additionalContext: \`
Session resumed. Previous context:
- Last topic: ${previousState.lastTopic}
- Open files: ${previousState.openFiles.join(", ")}
          \`.trim(),
        };
      }
      return null;
    },
  },
});
```

#### Load user preferences

```typescript
const session = await client.createSession({
  hooks: {
    onSessionStart: async () => {
      const preferences = await loadUserPreferences();
      
      const contextParts = [];
      
      if (preferences.language) {
        contextParts.push(\`Preferred language: ${preferences.language}\`);
      }
      if (preferences.codeStyle) {
        contextParts.push(\`Code style: ${preferences.codeStyle}\`);
      }
      if (preferences.verbosity === "concise") {
        contextParts.push("Keep responses brief and to the point.");
      }
      
      return {
        additionalContext: contextParts.join("\n"),
      };
    },
  },
});
```

## Session end hook {#session-end}

The `onSessionEnd` hook is called when a session ends.

### Hook signature

```typescript
type SessionEndHandler = (
  input: SessionEndHookInput,
  invocation: HookInvocation
) => Promise<SessionEndHookOutput | null | undefined>;
```

### Input

| Field | Type | Description |
| --- | --- | --- |
| `timestamp` | number | Unix timestamp when the hook was triggered |
| `cwd` | string | Current working directory |
| `reason` | string | Why the session ended (see below) |
| `finalMessage` | string \| undefined | The last message from the session |
| `error` | string \| undefined | Error message if session ended due to error |

#### End reasons

| Reason | Description |
| --- | --- |
| `"complete"` | Session completed normally |
| `"error"` | Session ended due to an error |
| `"abort"` | Session was aborted by user or code |
| `"timeout"` | Session timed out |
| `"user_exit"` | User explicitly ended the session |

### Output

| Field | Type | Description |
| --- | --- | --- |
| `suppressOutput` | boolean | Suppress the final session output |
| `cleanupActions` | string\[\] | List of cleanup actions to perform |
| `sessionSummary` | string | Summary of the session for logging/analytics |

### Examples

#### Track session metrics

```typescript
const sessionStartTimes = new Map<string, number>();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionStartTimes.set(invocation.sessionId, input.timestamp);
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      const startTime = sessionStartTimes.get(invocation.sessionId);
      const duration = startTime ? input.timestamp - startTime : 0;
      
      await recordMetrics({
        sessionId: invocation.sessionId,
        duration,
        endReason: input.reason,
      });
      
      sessionStartTimes.delete(invocation.sessionId);
      return null;
    },
  },
});
```

#### Clean up resources

```typescript
const sessionResources = new Map<string, { tempFiles: string[] }>();

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionResources.set(invocation.sessionId, { tempFiles: [] });
      return null;
    },
    onSessionEnd: async (input, invocation) => {
      const resources = sessionResources.get(invocation.sessionId);
      
      if (resources) {
        // Clean up temp files
        for (const file of resources.tempFiles) {
          await fs.unlink(file).catch(() => {});
        }
        sessionResources.delete(invocation.sessionId);
      }
      
      console.log(\`Session ${invocation.sessionId} ended: ${input.reason}\`);
      return null;
    },
  },
});
```

#### Save session state for resume

```typescript
const session = await client.createSession({
  hooks: {
    onSessionEnd: async (input, invocation) => {
      if (input.reason !== "error") {
        // Save state for potential resume
        await saveSessionState(invocation.sessionId, {
          endTime: input.timestamp,
          cwd: input.cwd,
          reason: input.reason,
        });
      }
      return null;
    },
  },
});
```

#### Log session summary

```typescript
const sessionData: Record<string, { prompts: number; tools: number; startTime: number }> = {};

const session = await client.createSession({
  hooks: {
    onSessionStart: async (input, invocation) => {
      sessionData[invocation.sessionId] = { 
        prompts: 0, 
        tools: 0, 
        startTime: input.timestamp 
      };
      return null;
    },
    onUserPromptSubmitted: async (_, invocation) => {
      sessionData[invocation.sessionId].prompts++;
      return null;
    },
    onPreToolUse: async (_, invocation) => {
      sessionData[invocation.sessionId].tools++;
      return { permissionDecision: "allow" };
    },
    onSessionEnd: async (input, invocation) => {
      const data = sessionData[invocation.sessionId];
      console.log(\`
Session Summary:
  ID: ${invocation.sessionId}
  Duration: ${(input.timestamp - data.startTime) / 1000}s
  Prompts: ${data.prompts}
  Tool calls: ${data.tools}
  End reason: ${input.reason}
      \`.trim());
      
      delete sessionData[invocation.sessionId];
      return null;
    },
  },
});
```

## Agent stop hook {#agent-stop}

The agent stop hook runs when the top-level agent naturally reaches the end of a turn. It is separate from `onSessionEnd`: the session remains active, and the hook can request another agent turn.

| Language | Handler |
| --- | --- |
| Node.js / TypeScript | `onAgentStop` |
| Python | `on_agent_stop` |
| Go | `OnAgentStop` |
| .NET | `OnAgentStop` |
| Rust | `on_agent_stop` |
| Java | `setOnAgentStop` |

### Input

The public member names follow each language's casing conventions:

| Meaning | Node.js / Python | Go /.NET | Rust | Java |
| --- | --- | --- | --- | --- |
| Why the agent stopped, such as `end_turn` | `stopReason` | `StopReason` | `stop_reason` | `getStopReason()` |
| Path to the on-disk session transcript | `transcriptPath` | `TranscriptPath` | `transcript_path` | `getTranscriptPath()` |
| Whether an earlier block decision already forced this continuation | `stopHookActive` | `StopHookActive` | `stop_hook_active` | `getStopHookActive()` |

### Output

Return no output to let the agent stop. Return a block decision to enqueue another user message and continue:

```json
{
  "decision": "block",
  "reason": "Run the final validation and fix any failures."
}
```

Use the active-stop member listed above to avoid repeatedly blocking an agent that has already continued because of this hook. The runtime also caps consecutive block decisions.

## Best practices

1. **Keep `onSessionStart` fast** - Users are waiting for the session to be ready.
2. **Handle all end reasons** - Don't assume sessions end cleanly; handle errors and aborts.
3. **Clean up resources** - Use `onSessionEnd` to free any resources allocated during the session.
4. **Store minimal state** - If tracking session data, keep it lightweight.
5. **Make cleanup idempotent** - `onSessionEnd` might not be called if the process crashes.