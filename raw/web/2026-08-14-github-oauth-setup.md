---
title: "GitHub OAuth setup"
description: "Let users authenticate with their GitHub accounts to use Copilot through your application. This supports individual accounts, organization memberships, and enterprise identities."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth"
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
# GitHub OAuth setup

> Source: [GitHub OAuth setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth)
> Clipped: 2026-08-14

**Best for:** Multi-user apps, internal tools with org access control, SaaS products, apps where users have GitHub accounts.

## How it works

You create a GitHub OAuth App (or GitHub App), users authorize it, and you pass their access token to the SDK. Copilot requests are made on behalf of each authenticated user, using their Copilot subscription.

![Diagram: Sequence diagram showing the described process.](https://docs.github.com/assets/cb-73919/mw-1440/images/help/copilot/copilot-sdk/setup-github-oauth-diagram-0.webp)

**Key characteristics:**

- Each user authenticates with their own GitHub account
- Copilot usage is billed to each user's subscription
- Supports GitHub organizations and enterprise accounts
- Your app never handles model API keys—GitHub manages everything

## Architecture

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-70248/mw-1440/images/help/copilot/copilot-sdk/setup-github-oauth-diagram-1.webp)

## Step 1: create a GitHub OAuth app

1. Go to **GitHub Settings → Developer Settings → OAuth Apps → New OAuth App** (or for organizations: **Organization Settings → Developer Settings**)
2. Fill in:
	- **Application name**: Your app's name
		- **Homepage URL**: Your app's URL
		- **Authorization callback URL**: Your OAuth callback endpoint (e.g., `https://yourapp.com/auth/callback`)
3. Note your **Client ID** and generate a **Client Secret**

> **GitHub App vs OAuth App:** Both work. GitHub Apps offer finer-grained permissions and are recommended for new projects. OAuth Apps are simpler to set up. The token flow is the same from the SDK's perspective.

## Step 2: implement the OAuth flow

Your application handles the standard GitHub OAuth flow. Here's the server-side token exchange:

```typescript
// Server-side: Exchange authorization code for user token
async function handleOAuthCallback(code: string): Promise<string> {
    const response = await fetch("https://github.com/login/oauth/access_token", {
        method: "POST",
        headers: {
            "Content-Type": "application/json",
            Accept: "application/json",
        },
        body: JSON.stringify({
            client_id: process.env.GITHUB_CLIENT_ID,
            client_secret: process.env.GITHUB_CLIENT_SECRET,
            code,
        }),
    });

    const data = await response.json();
    return data.access_token; // gho_xxxx or ghu_xxxx
}
```

## Step 3: pass the token to the SDK

Create an SDK client for each authenticated user, passing their token:

```typescript
import { CopilotClient } from "@github/copilot-sdk";

// Create a client for an authenticated user
function createClientForUser(userToken: string): CopilotClient {
    return new CopilotClient({
        gitHubToken: userToken,
        useLoggedInUser: false,  // Don't fall back to CLI login
    });
}

// Usage
const client = createClientForUser("gho_user_access_token");
const session = await client.createSession({
    sessionId: \`user-${userId}-session\`,
    model: "gpt-5.4",
});

const response = await session.sendAndWait({ prompt: "Hello!" });
```

## Enterprise and organization access

GitHub OAuth naturally supports enterprise scenarios. When users authenticate with GitHub, their org memberships and enterprise associations come along.

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-76870/mw-1440/images/help/copilot/copilot-sdk/setup-github-oauth-diagram-2.webp)

### Verify organization membership

After OAuth, check that the user belongs to your organization:

```typescript
async function verifyOrgMembership(
    token: string,
    requiredOrg: string
): Promise<boolean> {
    const response = await fetch("https://api.github.com/user/orgs", {
        headers: { Authorization: \`Bearer ${token}\` },
    });
    const orgs = await response.json();
    return orgs.some((org: any) => org.login === requiredOrg);
}

// In your auth flow
const token = await handleOAuthCallback(code);
if (!await verifyOrgMembership(token, "my-company")) {
    throw new Error("User is not a member of the required organization");
}
const client = createClientForUser(token);
```

### Enterprise managed users (EMU)

For GitHub Enterprise Managed Users, the flow is identical—EMU users authenticate through GitHub OAuth like any other user. Their enterprise policies (IP restrictions, SAML SSO) are enforced by GitHub automatically.

```typescript
// No special SDK configuration needed for EMU
// Enterprise policies are enforced server-side by GitHub
const client = new CopilotClient({
    gitHubToken: emuUserToken,  // Works the same as regular tokens
    useLoggedInUser: false,
});
```

## Supported token types

| Token Prefix | Source | Works? |
| --- | --- | --- |
| `gho_` | OAuth user access token | ✅ |
| `ghu_` | GitHub App user access token | ✅ |
| `github_pat_` | Fine-grained personal access token | ✅ |
| `ghp_` | Classic personal access token | ❌ (deprecated) |

## Token lifecycle

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-46786/mw-1440/images/help/copilot/copilot-sdk/setup-github-oauth-diagram-3.webp)

**Important:** Your application is responsible for token storage, refresh, and expiration handling. The SDK uses whatever token you provide—it doesn't manage the OAuth lifecycle.

### Token refresh pattern

```typescript
async function getOrRefreshToken(userId: string): Promise<string> {
    const stored = await tokenStore.get(userId);

    if (stored && !isExpired(stored)) {
        return stored.accessToken;
    }

    if (stored?.refreshToken) {
        const refreshed = await refreshGitHubToken(stored.refreshToken);
        await tokenStore.set(userId, refreshed);
        return refreshed.accessToken;
    }

    throw new Error("User must re-authenticate");
}
```

## Multi-user patterns

### One client per user (recommended)

Each user gets their own SDK client with their own token. This provides the strongest isolation.

```typescript
const clients = new Map<string, CopilotClient>();

function getClientForUser(userId: string, token: string): CopilotClient {
    if (!clients.has(userId)) {
        clients.set(userId, new CopilotClient({
            gitHubToken: token,
            useLoggedInUser: false,
        }));
    }
    return clients.get(userId)!;
}
```

### Shared CLI with per-request tokens

For a lighter resource footprint, you can run a single external CLI server and pass tokens per session. See [Backend services setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/backend-services) for this pattern.

## Limitations

| Limitation | Details |
| --- | --- |
| **Copilot subscription required** | Each user needs an active Copilot subscription |
| **Token management is your responsibility** | Store, refresh, and handle expiration |
| **GitHub account required** | Users must have GitHub accounts |
| **Rate limits per user** | Subject to each user's Copilot rate limits |