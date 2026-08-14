---
title: "Local CLI setup"
description: "Use a specific CLI binary instead of the SDK's automatic CLI management. This is an advanced option—you supply the CLI path explicitly, and you are responsible for ensuring version compatibility with the SDK."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/local-cli"
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
# Local CLI setup

> Source: [Local CLI setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/local-cli)
> Clipped: 2026-08-14

**Use when:** You need to pin a specific CLI version, or work with the Go SDK (which does not include a CLI automatically).

## How it works

By default, the Node.js, Python, and.NET SDKs include their own CLI dependency (see [Default setup (bundled CLI)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/bundled-cli)). If you need to override this—for example, to use a system-installed CLI—you can use the `Connection` option.

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-43872/mw-1440/images/help/copilot/copilot-sdk/setup-local-cli-diagram-0.webp)

**Key characteristics:**

- You explicitly provide the CLI binary path
- You are responsible for CLI version compatibility with the SDK
- Authentication uses the signed-in user's credentials from the system keychain (or env vars)
- Communication happens over stdio

## Configuration

### Using a local CLI binary

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient({
    cliPath: "/usr/local/bin/copilot",
});

const session = await client.createSession({ model: "gpt-5.4" });
const response = await session.sendAndWait({ prompt: "Hello!" });
console.log(response?.data.content);

await client.stop();
```

## Additional options

```typescript
const client = new CopilotClient({
    cliPath: "/usr/local/bin/copilot",

    // Set log level for debugging
    logLevel: "debug",

    // Pass extra CLI arguments
    cliArgs: ["--log-dir=/tmp/copilot-logs"],

    // Set working directory
    cwd: "/path/to/project",
});
```

## Using environment variables

Instead of the keychain, you can authenticate via environment variables. This is useful for CI or when you don't want interactive login.

```bash
# Set one of these (in priority order):
export COPILOT_GITHUB_TOKEN="gho_xxxx"   # Recommended
export GH_TOKEN="gho_xxxx"               # GitHub CLI compatible
export GITHUB_TOKEN="gho_xxxx"           # GitHub Actions compatible
```

The SDK picks these up automatically—no code changes needed.

## Managing sessions

Sessions default to ephemeral. To create resumable sessions, provide your own session ID:

```typescript
// Create a named session
const session = await client.createSession({
    sessionId: "my-project-analysis",
    model: "gpt-5.4",
});

// Later, resume it
const resumed = await client.resumeSession("my-project-analysis");
```

Session state is stored locally at `~/.copilot/session-state/{sessionId}/`.

## Limitations

| Limitation | Details |
| --- | --- |
| **Version compatibility** | You must ensure your CLI version is compatible with the SDK |
| **Single user** | Credentials are tied to whoever signed in to the CLI |
| **Local only** | The CLI runs on the same machine as your app |
| **No multi-tenant** | Can't serve multiple users from one CLI instance |

## Next steps

- **[Default setup (bundled CLI)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/bundled-cli)**: Use the SDK's built-in CLI (recommended for most use cases)
- **[Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started)**: Build a complete interactive app
- **[Authentication](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/authenticate)**: All auth methods in detail