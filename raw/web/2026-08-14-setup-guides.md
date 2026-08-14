---
title: "Setup guides"
description: "These guides walk you through configuring the Copilot SDK for your specific use case—from personal side projects to production platforms serving thousands of users."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/choosing-a-setup-path"
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
# Setup guides

> Source: [Setup guides](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/choosing-a-setup-path)
> Clipped: 2026-08-14

## Architecture at a glance

Every Copilot SDK integration follows the same core pattern: your application talks to the SDK, which communicates with the Copilot CLI over JSON-RPC. What changes across setups is **where the CLI runs**, **how users authenticate**, and **how sessions are managed**.

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-53272/mw-1440/images/help/copilot/copilot-sdk/setup-choosing-a-setup-path-diagram-0.webp)

The setup guides below help you configure each layer for your scenario.

## Who are you?

### 🧑💻 Hobbyist

You're building a personal assistant, side project, or experimental app. You want the simplest path to getting Copilot in your code.

**Start with:**

1. **[Default setup (bundled CLI)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/bundled-cli)** —The SDK includes the CLI automatically—just install and go
2. **[Local CLI setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/local-cli)** —Use your own CLI binary or running instance (advanced)

### 🏢 Internal app developer

You're building tools for your team or company. Users are employees who need to authenticate with their enterprise GitHub accounts or org memberships.

**Start with:**

1. **[GitHub OAuth setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth)** —Let employees sign in with their GitHub accounts
2. **[Backend services setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/backend-services)** —Run the SDK in your internal services

**If scaling beyond a single server:**

1. **[Multi-tenancy and server deployments](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/multi-tenancy)** —Configure SDK options for multi-user server mode
2. **[Scaling and multi-tenancy](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/scaling)** —Handle multiple users and services

### 🚀 App developer (ISV)

You're building a product for customers. You need to handle authentication for your users—either through GitHub or by managing identity yourself.

**Start with:**

1. **[GitHub OAuth setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth)** —Let customers sign in with GitHub
2. **[BYOK (bring your own key)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/byok)** —Manage identity yourself with your own model keys
3. **[Backend services setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/backend-services)** —Power your product from server-side code

**For production:**

1. **[Multi-tenancy and server deployments](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/multi-tenancy)** —Use `mode: "empty"`, per-session tokens, and isolated runtime state
2. **[Scaling and multi-tenancy](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/scaling)** —Serve many customers reliably

### 🏗️ Platform developer

You're embedding Copilot into a platform—APIs, developer tools, or infrastructure that other developers build on. You need fine-grained control over sessions, scaling, and multi-tenancy.

**Start with:**

1. **[Backend services setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/backend-services)** —Core server-side integration
2. **[Multi-tenancy and server deployments](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/multi-tenancy)** —SDK-level isolation, per-session auth, and shared runtime options
3. **[Scaling and multi-tenancy](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/scaling)** —Session isolation, horizontal scaling, persistence

**Depending on your auth model:**

1. **[GitHub OAuth setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth)** —For GitHub-authenticated users
2. **[BYOK (bring your own key)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/byok)** —For self-managed identity and model access

## Decision matrix

Use this table to find the right guides based on what you need to do:

| What you need | Guide |
| --- | --- |
| Getting started quickly | [Default setup (bundled CLI)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/bundled-cli) |
| Use your own CLI binary or server | [Local CLI setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/local-cli) |
| Users sign in with GitHub | [GitHub OAuth setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth) |
| Use your own model keys (OpenAI, Azure, and more) | [BYOK (bring your own key)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/byok) |
| Azure BYOK with Managed Identity (no API keys) | [Azure managed identity with BYOK](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/azure-managed-identity) |
| Run the SDK on a server | [Backend services setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/backend-services) |
| Configure SDK options for concurrent users | [Multi-tenancy and server deployments](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/multi-tenancy) |
| Serve multiple users / scale horizontally | [Scaling and multi-tenancy](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/scaling) |

## Configuration comparison

![Diagram: Flowchart showing the described process.](https://docs.github.com/assets/cb-128123/mw-1440/images/help/copilot/copilot-sdk/setup-choosing-a-setup-path-diagram-1.webp)

## Prerequisites

All guides assume you have:

- **One of the SDKs** installed (Node.js, Python, and.NET SDKs include the CLI automatically):

If you're brand new, start with the **[Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started)** first, then come back here for production configuration.

## Next steps

Pick the guide that matches your situation from the [decision matrix](#decision-matrix) above, or start with the persona description closest to your role.