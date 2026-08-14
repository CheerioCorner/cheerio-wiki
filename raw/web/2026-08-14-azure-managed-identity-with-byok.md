---
title: "Azure managed identity with BYOK"
description: "The GitHub Copilot SDK's BYOK (bring your own key) supports static API keys, but Azure deployments often use Managed Identity (Microsoft Entra ID) instead of long-lived keys. The GitHub Copilot SDK is designed to compose with the Azure Identity SDK for maximum flexibility. Supply a bearer token provider callback that can fetch fresh tokens on demand using an Azure Identity SDK API."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/azure-managed-identity"
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
# Azure managed identity with BYOK

> Source: [Azure managed identity with BYOK](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/azure-managed-identity)
> Clipped: 2026-08-14

This guide shows how to use Azure Identity SDK APIs to authenticate with Microsoft Foundry models through the GitHub Copilot SDK. Most languages use `DefaultAzureCredential`; Rust uses `DeveloperToolsCredential` locally and `ManagedIdentityCredential` in Azure.

## How it works

Microsoft Foundry's OpenAI-compatible endpoint (`https://<resource-name>.openai.azure.com/openai/v1/`) accepts bearer tokens from Microsoft Entra ID in place of static API keys. This guide uses a token provider callback so the GitHub Copilot SDK runtime can request fresh tokens on demand.

Using Python as an example, the flow is:

1. Configure `DefaultAzureCredential` for your environment.
2. Pass a callback, in `bearer_token_provider` of the BYOK provider configuration, that uses `DefaultAzureCredential` to obtain a token for the `https://ai.azure.com/.default` scope.
3. Let the GitHub Copilot SDK request fresh tokens on demand through that callback.

![Diagram: Sequence diagram showing the described process.](https://docs.github.com/assets/cb-81006/mw-1440/images/help/copilot/copilot-sdk/setup-azure-managed-identity-diagram-0.webp)

## Code samples

### Prerequisites

Install the Azure Identity and GitHub Copilot SDK packages for your language:

```bash
dotnet add package GitHub.Copilot.SDK
dotnet add package Azure.Core
```

### Use a token provider callback

Use this approach when you want the GitHub Copilot SDK runtime to request fresh tokens on demand through a callback that you provide. The Azure Identity SDK handles token caching and refresh timing.

Here are language-specific implementations:

```csharp
using Azure.Core;
using Azure.Identity;
using GitHub.Copilot;

DefaultAzureCredential credential = new(
    DefaultAzureCredential.DefaultEnvironmentVariableName);
await using CopilotClient client = new();
string foundryUrl = Environment.GetEnvironmentVariable("FOUNDRY_RESOURCE_URL")!;

await using CopilotSession session = await client.CreateSessionAsync(new SessionConfig
{
    Model = "gpt-5.5",
    Provider = new ProviderConfig
    {
        Type = "openai",
        BaseUrl = $"{foundryUrl}/openai/v1/",
        BearerTokenProvider = async _ =>
        {
            AccessToken token = await credential.GetTokenAsync(
                new TokenRequestContext(["https://ai.azure.com/.default"]));
            return token.Token;
        },
        WireApi = "responses",
    },
});

AssistantMessageEvent? response = await session.SendAndWaitAsync(
    new MessageOptions { Prompt = "Hello from Managed Identity!" });
Console.WriteLine(response?.Data.Content);
```

## Environment configuration

| Variable | Description | Example |
| --- | --- | --- |
| `AZURE_TOKEN_CREDENTIALS` | When running in **Azure**, set it to `ManagedIdentityCredential`. When running **locally**, set it to either `dev` or a developer tool credential name, such as `AzureCliCredential`. | `ManagedIdentityCredential` |
| `AZURE_CLIENT_ID` | *Optional.* When running in **Azure**, set this to the client ID of a User-assigned Managed Identity when using `ManagedIdentityCredential`. If not set, Azure uses the System-assigned Managed Identity. | `11111111-2222-3333-4444-555555555555` |
| `FOUNDRY_RESOURCE_URL` | Your Microsoft Foundry resource URL | `https://<my-resource>.openai.azure.com` |

No API key environment variable is needed—authentication is handled by Azure Identity credentials. In.NET, Go, Java, Python, and TypeScript, `DefaultAzureCredential` automatically supports:

- **Managed Identity** (System-assigned or User-assigned): for Azure-hosted apps
- **Azure CLI** (`az login`): for local development
- **Environment variables** (`AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_CLIENT_SECRET`): for service principals
- **Workload Identity**: for Kubernetes

In.NET, Go, Java, Python, and TypeScript, `ManagedIdentityCredential` reads `AZURE_CLIENT_ID` to select a User-assigned Managed Identity. Rust is an exception in this guide.

In Rust, use `DeveloperToolsCredential` for local development and `ManagedIdentityCredential` when running in Azure. For other languages, see the `DefaultAzureCredential` documentation for the full credential chain:

- [.NET](https://aka.ms/azsdk/net/identity/credential-chains#defaultazurecredential-overview)
- [Go](https://aka.ms/azsdk/go/identity/credential-chains#defaultazurecredential-overview)
- [Java](https://aka.ms/azsdk/java/identity/credential-chains#defaultazurecredential-overview)
- [Python](https://aka.ms/azsdk/python/identity/credential-chains#defaultazurecredential-overview)
- [TypeScript](https://aka.ms/azsdk/js/identity/credential-chains#defaultazurecredential-overview)

## When to use this pattern

| Scenario | Recommendation |
| --- | --- |
| Azure-hosted app with Managed Identity | ✅ Use this pattern |
| App with existing Microsoft Entra service principal | ✅ Use this pattern |
| Local development with `az login` | ✅ Use this pattern |
| Non-Azure environment with static API key | Use [BYOK (bring your own key)](https://docs.github.com/en/copilot/how-tos/copilot-sdk/auth/byok) |
| GitHub Copilot subscription available | Use [GitHub OAuth setup](https://docs.github.com/en/copilot/how-tos/copilot-sdk/setup/github-oauth) |