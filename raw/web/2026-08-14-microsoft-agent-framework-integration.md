---
title: "Microsoft agent framework integration"
description: "Use the Copilot SDK as an agent provider inside the Microsoft Agent Framework (MAF) to compose multi-agent workflows alongside Azure OpenAI, Anthropic, and other providers."
source_url: "https://docs.github.com/en/copilot/how-tos/copilot-sdk/integrations/microsoft-agent-framework"
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
# Microsoft agent framework integration

> Source: [Microsoft agent framework integration](https://docs.github.com/en/copilot/how-tos/copilot-sdk/integrations/microsoft-agent-framework)
> Clipped: 2026-08-14

## Overview

The Microsoft Agent Framework is the unified successor to Semantic Kernel and AutoGen. It provides a standard interface for building, orchestrating, and deploying AI agents. Dedicated integration packages let you wrap a Copilot SDK client as a first-class MAF agent—interchangeable with any other agent provider in the framework.

| Concept | Description |
| --- | --- |
| **Microsoft Agent Framework** | Open-source framework for single- and multi-agent orchestration in.NET and Python |
| **Agent provider** | A backend that powers an agent (Copilot, Azure OpenAI, Anthropic, etc.) |
| **Orchestrator** | A MAF component that coordinates agents in sequential, concurrent, or handoff workflows |
| **A2A protocol** | Agent-to-Agent communication standard supported by the framework |

## Prerequisites

Before you begin, ensure you have:

- A working [Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started) in your language of choice
- A GitHub Copilot subscription (Individual, Business, or Enterprise)
- The Copilot CLI installed or available via the SDK's bundled CLI

## Installation

Install the Copilot SDK alongside the MAF integration package for your language:

```shell
dotnet add package GitHub.Copilot.SDK
dotnet add package Microsoft.Agents.AI.GitHub.Copilot --prerelease
```

## Basic usage

Wrap the Copilot SDK client as a MAF agent with a single method call. The resulting agent conforms to the framework's standard interface and can be used anywhere a MAF agent is expected.

```csharp
using GitHub.Copilot;
using Microsoft.Agents.AI;

await using var copilotClient = new CopilotClient();
await copilotClient.StartAsync();

// Wrap as a MAF agent
AIAgent agent = copilotClient.AsAIAgent();

// Use the standard MAF interface
string response = await agent.RunAsync("Explain how dependency injection works in ASP.NET Core");
Console.WriteLine(response);
```

## Adding custom tools

Extend your Copilot agent with custom function tools. Tools defined through the standard Copilot SDK are automatically available when the agent runs inside MAF.

```csharp
using GitHub.Copilot;
using Microsoft.Extensions.AI;
using Microsoft.Agents.AI;

// Define a custom tool
AIFunction weatherTool = CopilotTool.DefineTool(
    (string location) => $"The weather in {location} is sunny with a high of 25°C.",
    factoryOptions: new AIFunctionFactoryOptions
    {
        Name = "GetWeather",
        Description = "Get the current weather for a given location.",
    }
);

await using var copilotClient = new CopilotClient();
await copilotClient.StartAsync();

// Create agent with tools
AIAgent agent = copilotClient.AsAIAgent(new AIAgentOptions
{
    Tools = new[] { weatherTool },
});

string response = await agent.RunAsync("What's the weather like in Seattle?");
Console.WriteLine(response);
```

You can also use Copilot SDK's native tool definition alongside MAF tools:

```typescript
import { CopilotClient, defineTool } from "@github/copilot-sdk";

const getWeather = defineTool("GetWeather", {
    description: "Get the current weather for a given location.",
    parameters: {
        type: "object",
        properties: {
            location: { type: "string", description: "City name" },
        },
        required: ["location"],
    },
    handler: async ({ location }: { location: string }) =>
        \`The weather in ${location} is sunny, 25°C.\`,
});

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5.4",
    tools: [getWeather],
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});

await session.sendAndWait({ prompt: "What's the weather like in Seattle?" });
```

## Multi-agent workflows

The primary benefit of MAF integration is composing Copilot alongside other agent providers in orchestrated workflows. Use the framework's built-in orchestrators to create pipelines where different agents handle different steps.

### Sequential workflow

Run agents one after another, passing output from one to the next:

```csharp
using GitHub.Copilot;
using Microsoft.Agents.AI;
using Microsoft.Agents.AI.Orchestration;

await using var copilotClient = new CopilotClient();
await copilotClient.StartAsync();

// Copilot agent for code review
AIAgent reviewer = copilotClient.AsAIAgent(new AIAgentOptions
{
    Instructions = "You review code for bugs, security issues, and best practices. Be thorough.",
});

// Azure OpenAI agent for generating documentation
AIAgent documentor = AIAgent.FromOpenAI(new OpenAIAgentOptions
{
    Model = "gpt-5.4",
    Instructions = "You write clear, concise documentation for code changes.",
});

// Compose in a sequential pipeline
var pipeline = new SequentialOrchestrator(new[] { reviewer, documentor });

string result = await pipeline.RunAsync(
    "Review and document this pull request: added retry logic to the HTTP client"
);
Console.WriteLine(result);
```

### Concurrent workflow

Run multiple agents in parallel and aggregate their results:

```csharp
using GitHub.Copilot;
using Microsoft.Agents.AI;
using Microsoft.Agents.AI.Orchestration;

await using var copilotClient = new CopilotClient();
await copilotClient.StartAsync();

AIAgent securityReviewer = copilotClient.AsAIAgent(new AIAgentOptions
{
    Instructions = "Focus exclusively on security vulnerabilities and risks.",
});

AIAgent performanceReviewer = copilotClient.AsAIAgent(new AIAgentOptions
{
    Instructions = "Focus exclusively on performance bottlenecks and optimization opportunities.",
});

// Run both reviews concurrently
var concurrent = new ConcurrentOrchestrator(new[] { securityReviewer, performanceReviewer });

string combinedResult = await concurrent.RunAsync(
    "Analyze this database query module for issues"
);
Console.WriteLine(combinedResult);
```

## Streaming responses

When building interactive applications, stream agent responses to show real-time output. The MAF integration preserves the Copilot SDK's streaming capabilities.

```csharp
using GitHub.Copilot;
using Microsoft.Agents.AI;

await using var copilotClient = new CopilotClient();
await copilotClient.StartAsync();

AIAgent agent = copilotClient.AsAIAgent(new AIAgentOptions
{
    Streaming = true,
});

await foreach (var chunk in agent.RunStreamingAsync("Write a quicksort implementation in C#"))
{
    Console.Write(chunk);
}
Console.WriteLine();
```

You can also stream directly through the Copilot SDK without MAF:

```typescript
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5.4",
    streaming: true,
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});

session.on("assistant.message_delta", (event) => {
    process.stdout.write(event.data.deltaContent ?? "");
});

await session.sendAndWait({ prompt: "Write a quicksort implementation in TypeScript" });
```

## Configuration reference

### MAF agent options

| Property | Type | Description |
| --- | --- | --- |
| `Instructions` / `instructions` | `string` | System prompt for the agent |
| `Tools` / `tools` | `AIFunction[]` / `list` | Custom function tools available to the agent |
| `Streaming` / `streaming` | `bool` | Enable streaming responses |
| `Model` / `model` | `string` | Override the default model |

### Copilot SDK options (passed through)

All standard [Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started) options are still available when creating the underlying Copilot client. The MAF wrapper delegates to the SDK under the hood:

| SDK Feature | MAF Support |
| --- | --- |
| Custom tools (`DefineTool` / `AIFunctionFactory`) | ✅ Merged with MAF tools |
| MCP servers | ✅ Configured on the SDK client |
| Custom agents / sub-agents | ✅ Available within the Copilot agent |
| Infinite sessions | ✅ Configured on the SDK client |
| Model selection | ✅ Overridable per agent or per call |
| Streaming | ✅ Full delta event support |

## Best practices

### Choose the right level of integration

Use the MAF wrapper when you need to compose Copilot with other providers in orchestrated workflows. If your application only uses Copilot, the standalone SDK is simpler and gives you full control:

```typescript
// Standalone SDK — full control, simpler setup
import { CopilotClient } from "@github/copilot-sdk";

const client = new CopilotClient();
const session = await client.createSession({
    model: "gpt-5.4",
    onPermissionRequest: async () => ({ kind: "approve-once" }),
});
const response = await session.sendAndWait({ prompt: "Explain this code" });
```

### Keep agents focused

When building multi-agent workflows, give each agent a specific role with clear instructions. Avoid overlapping responsibilities:

```typescript
// ❌ Too vague — overlapping roles
const agents = [
    { instructions: "Help with code" },
    { instructions: "Assist with programming" },
];

// ✅ Focused — clear separation of concerns
const agents = [
    { instructions: "Review code for security vulnerabilities. Flag SQL injection, XSS, and auth issues." },
    { instructions: "Optimize code performance. Focus on algorithmic complexity and memory usage." },
];
```

### Handle errors at the orchestration level

Wrap agent calls in error handling, especially in multi-agent workflows where one agent's failure shouldn't block the entire pipeline:

```csharp
try
{
    string result = await pipeline.RunAsync("Analyze this module");
    Console.WriteLine(result);
}
catch (AgentException ex)
{
    Console.Error.WriteLine($"Agent {ex.AgentName} failed: {ex.Message}");
    // Fall back to single-agent mode or retry
}
```

## See also

- [Build your first Copilot-powered app](https://docs.github.com/en/copilot/how-tos/copilot-sdk/getting-started): initial Copilot SDK setup
- [Custom agents and sub-agent orchestration](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/custom-agents): define specialized sub-agents within the SDK
- [Custom skills](https://docs.github.com/en/copilot/how-tos/copilot-sdk/features/skills): reusable prompt modules
- [Microsoft Agent Framework documentation](https://learn.microsoft.com/en-us/agent-framework/agents/providers/github-copilot): official MAF docs for the Copilot provider
- [Blog: Build AI Agents with GitHub Copilot SDK and Microsoft Agent Framework](https://devblogs.microsoft.com/semantic-kernel/build-ai-agents-with-github-copilot-sdk-and-microsoft-agent-framework/)