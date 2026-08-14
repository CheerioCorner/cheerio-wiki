---
title: "Default setup (bundled CLI)"
description: "The Node.js and .NET SDKs include the Copilot CLI as a dependency—your app ships with everything it needs, with no extra installation or configuration required."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/bundled-cli"
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
# Default setup (bundled CLI)

> Source: [Default setup (bundled CLI)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/bundled-cli)
> Clipped: 2026-08-14

The Python SDK recommends a one-time download step after installation:

```bash
python -m copilot download-runtime
```

This downloads the matching runtime and caches it locally. If you skip this step, the SDK will attempt to download it automatically on first use as a fallback.

**Best for:** Most applications—desktop apps, standalone tools, CLI utilities, prototypes, and more.

## How it works

When you install the SDK, the Copilot runtime is included automatically (Node.js,.NET) or downloaded via `python -m copilot download-runtime` (Python). The SDK starts it as a child process and communicates over stdio. There's nothing extra to configure.

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-38223/mw-1440/images/help/copilot/copilot-sdk/setup-bundled-cli-diagram-0.webp)

**Key characteristics:**

- CLI binary is included with the SDK—no separate install needed
- The SDK manages the CLI version to ensure compatibility
- Users authenticate through your app (or use env vars / BYOK)
- Sessions are managed per-user on their machine

## Quick start

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();

const session = await client.createSession({ model: "gpt-5.4" });
const response = await session.sendAndWait({ prompt: "Hello!" });
console.log(response?.data.content);

await client.stop();
```

## Authentication strategies

You need to decide how your users will authenticate. Here are the common patterns:

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-70830/mw-1440/images/help/copilot/copilot-sdk/setup-bundled-cli-diagram-1.webp)

### Option A: user's signed-in credentials (simplest)

The user signs in to the CLI once, and your app uses those credentials. No extra code needed—this is the default behavior.

```typescript
const client = new CopilotClient();
// Default: uses signed-in user credentials
```

### Option B: token via environment variable

Ship your app with instructions to set a token, or set it programmatically:

```typescript
const client = new CopilotClient({
    env: {
        COPILOT_GITHUB_TOKEN: getUserToken(),  // Your app provides the token
    },
});
```

### Option C: BYOK (no GitHub auth needed)

If you manage your own model provider keys, users don't need GitHub accounts at all:

```typescript
const client = new CopilotClient();

const session = await client.createSession({
    model: "gpt-5.4",
    provider: {
        type: "openai",
        baseUrl: "https://api.openai.com/v1",
        apiKey: process.env.OPENAI_API_KEY,
    },
});
```

See the **[BYOK (bring your own key)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/byok)** for full details.

## Session management

Apps typically want named sessions so users can resume conversations:

```typescript
const client = new CopilotClient();

// Create a session tied to the user's project
const sessionId = \`project-${projectName}\`;
const session = await client.createSession({
    sessionId,
    model: "gpt-5.4",
});

// User closes app...
// Later, resume where they left off
const resumed = await client.resumeSession(sessionId);
```

Session state persists at `~/.copilot/session-state/{sessionId}/`.

## When to move on

| Need | Next Guide |
| --- | --- |
| Users signing in with GitHub accounts | [GitHub OAuth setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth) |
| Run on a server instead of user machines | [Backend services setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/backend-services) |
| Use your own model keys | [BYOK (bring your own key)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/byok) |

## Next steps

- **[BYOK (bring your own key)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/byok)**: Use your own model provider keys
- **[Session resume and persistence](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/session-persistence)**: Advanced session management
- **[Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started)**: Build a complete app