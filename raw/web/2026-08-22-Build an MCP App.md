---
title: Build an MCP App
description: Getting started guide for building interactive UI applications with MCP Apps
url: https://modelcontextprotocol.io/extensions/apps/build
source_domain: modelcontextprotocol.io
author:
published:
clipped: 2026-08-22
type: raw-web
source_kind: web
immutable: "true"
tags:
  - raw
  - web-clip
  - mcp
  - mcp-apps
---
# Build an MCP App

> Source: [Build an MCP App](https://modelcontextprotocol.io/extensions/apps/build)
> Clipped: 2026-08-22

## Prerequisites

You’ll need [Node.js](https://nodejs.org/en/download) 18 or higher. Familiarity with [MCP tools](https://modelcontextprotocol.io/specification/latest/server/tools) and [resources](https://modelcontextprotocol.io/specification/latest/server/resources) is recommended since MCP Apps combine both primitives. Experience with the [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk) will help you better understand the server-side patterns.

## Getting started

The fastest way to create an MCP App is using an AI coding agent with the MCP Apps skill. If you prefer to set up a project manually, skip to [Manual setup](#manual-setup).

### Using an AI coding agent

AI coding agents with Skills support can scaffold a complete MCP App project for you. Skills are folders of instructions and resources that your agent loads when relevant. They teach the AI how to perform specialized tasks like creating MCP Apps.

The `create-mcp-app` skill includes architecture guidance, best practices, and working examples that the agent uses to generate your project.

### Manual setup

If you’re not using an AI coding agent, or prefer to understand the setup process, follow these steps.

## Building an MCP App

Let’s build a simple app that displays the current server time. This example demonstrates the full pattern: registering a tool with UI metadata, serving the bundled HTML as a resource, and building a UI that communicates with the server.

### Server implementation

The server needs to do two things: register a tool that includes the `_meta.ui.resourceUri` field, and register a resource handler that serves the bundled HTML. Here’s the complete server file:

```typescript
// server.ts
console.log("Starting MCP App server...");

import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import {
  registerAppTool,
  registerAppResource,
  RESOURCE_MIME_TYPE,
} from "@modelcontextprotocol/ext-apps/server";
import cors from "cors";
import express from "express";
import fs from "node:fs/promises";
import path from "node:path";

const server = new McpServer({
  name: "My MCP App Server",
  version: "1.0.0",
});

// The ui:// scheme tells hosts this is an MCP App resource.
// The path structure is arbitrary; organize it however makes sense for your app.
const resourceUri = "ui://get-time/mcp-app.html";

// Register the tool that returns the current time
registerAppTool(
  server,
  "get-time",
  {
    title: "Get Time",
    description: "Returns the current server time.",
    inputSchema: {},
    _meta: { ui: { resourceUri } },
  },
  async () => {
    const time = new Date().toISOString();
    return {
      content: [{ type: "text", text: time }],
    };
  },
);

// Register the resource that serves the bundled HTML
registerAppResource(
  server,
  resourceUri,
  resourceUri,
  { mimeType: RESOURCE_MIME_TYPE },
  async () => {
    const html = await fs.readFile(
      path.join(import.meta.dirname, "dist", "mcp-app.html"),
      "utf-8",
    );
    return {
      contents: [
        { uri: resourceUri, mimeType: RESOURCE_MIME_TYPE, text: html },
      ],
    };
  },
);

// Expose the MCP server over HTTP
const expressApp = express();
expressApp.use(cors());
expressApp.use(express.json());

expressApp.post("/mcp", async (req, res) => {
  const transport = new StreamableHTTPServerTransport({
    sessionIdGenerator: undefined,
    enableJsonResponse: true,
  });
  res.on("close", () => transport.close());
  await server.connect(transport);
  await transport.handleRequest(req, res, req.body);
});

expressApp.listen(3001, (err) => {
  if (err) {
    console.error("Error starting server:", err);
    process.exit(1);
  }
  console.log("Server listening on http://localhost:3001/mcp");
});
```

Let’s break down the key parts:

- **`resourceUri`**: The `ui://` scheme tells hosts this is an MCP App resource. The path structure is arbitrary.
- **`registerAppTool`**: Registers a tool with the `_meta.ui.resourceUri` field. When the host calls this tool, the UI is fetched and rendered, and the tool result is passed to it upon arrival.
- **`registerAppResource`**: Serves the bundled HTML when the host requests the UI resource.
- **Express server**: Exposes the MCP server over HTTP on port 3001.

### UI implementation

The UI consists of an HTML page and a TypeScript module that uses the `App` class to communicate with the host. Here’s the HTML:

```html
<!-- mcp-app.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Get Time App</title>
  </head>
  <body>
    <p>
      <strong>Server Time:</strong>
      <code id="server-time">Loading...</code>
    </p>
    <button id="get-time-btn">Get Server Time</button>
    <script type="module" src="/src/mcp-app.ts"></script>
  </body>
</html>
```

And the TypeScript module:

```typescript
// src/mcp-app.ts
import { App } from "@modelcontextprotocol/ext-apps";

const serverTimeEl = document.getElementById("server-time")!;
const getTimeBtn = document.getElementById("get-time-btn")!;

const app = new App({ name: "Get Time App", version: "1.0.0" });

// Establish communication with the host
app.connect();

// Handle the initial tool result pushed by the host
app.ontoolresult = (result) => {
  const time = result.content?.find((c) => c.type === "text")?.text;
  serverTimeEl.textContent = time ?? "[ERROR]";
};

// Proactively call tools when users interact with the UI
getTimeBtn.addEventListener("click", async () => {
  const result = await app.callServerTool({
    name: "get-time",
    arguments: {},
  });
  const time = result.content?.find((c) => c.type === "text")?.text;
  serverTimeEl.textContent = time ?? "[ERROR]";
});
```

The key parts:

- **`app.connect()`**: Establishes communication with the host. Call this once when your app initializes.
- **`app.ontoolresult`**: A callback that fires when the host pushes a tool result to your app (e.g., when the tool is first called and the UI renders).
- **`app.callServerTool()`**: Lets your app proactively call tools on the server. Keep in mind that each call involves a round-trip to the server, so design your UI to handle latency gracefully.

The `App` class provides additional methods for logging, opening URLs, and updating the model’s context with structured data from your app. See the full [API documentation](https://apps.extensions.modelcontextprotocol.io/api/).

## Testing your app

To test your MCP App, build the UI and start your local server:

```shellscript
npm run build && npm run serve
```

```powershell
npm run build; npm run serve
```

In the default configuration, your server will be available at `http://localhost:3001/mcp`. However, to see your app render, you need an MCP host that supports MCP Apps. You have several options.

### Testing with Claude

[Claude](https://claude.ai/) (web) and [Claude Desktop](https://claude.ai/download) support MCP Apps. For local development, you’ll need to expose your server to the internet. You can run an MCP server locally and use tools like `cloudflared` to tunnel traffic through.

In a separate terminal, run:

```shellscript
npx cloudflared tunnel --url http://localhost:3001
```

Copy the generated URL (e.g., `https://random-name.trycloudflare.com`) and add it as a [custom connector](https://support.anthropic.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp) in Claude - click on your profile, go to **Settings**, **Connectors**, and finally **Add custom connector**.

Custom connectors are available on paid Claude plans (Pro, Max, or Team).

![Adding a custom connector in Claude](https://mintcdn.com/mcp/GU_E-622SLWFdCrP/images/quickstart-apps/add-custom-connector.gif?s=c4ec0750413ff7575c7f9492e2713212)

Adding a custom connector in Claude

### Testing with the basic-host

The `ext-apps` repository includes a test host for development. Clone the repo and install dependencies:

```shellscript
git clone https://github.com/modelcontextprotocol/ext-apps.git
cd ext-apps/examples/basic-host
npm install
```

```powershell
git clone https://github.com/modelcontextprotocol/ext-apps.git
cd ext-apps\examples\basic-host
npm install
```

Running `npm start` from `ext-apps/examples/basic-host/` will start the basic-host test interface. To connect it to a specific server (e.g., one you’re developing), pass the `SERVERS` environment variable inline:

```shellscript
SERVERS='["http://localhost:3001/mcp"]' npm start
```

```powershell
$env:SERVERS='["http://localhost:3001/mcp"]'; npm start
```

Navigate to `http://localhost:8080`. You’ll see a simple interface where you can select a tool and call it. When you call your tool, the host fetches the UI resource and renders it in a sandboxed iframe. You can then interact with your app and verify that tool calls work correctly.

![Example of the QR code MCP App running with the basic host](https://mintcdn.com/mcp/GU_E-622SLWFdCrP/images/quickstart-apps/qr-code-server.gif?s=48a3b47239b8394017c0949162d63de9)

Example of the QR code MCP App running with the basic host

## Learn more

## Feedback

MCP Apps is under active development. If you encounter issues or have ideas for improvements, open an issue on the [GitHub repository](https://github.com/modelcontextprotocol/ext-apps/issues). For broader discussions about the extension’s direction, join the conversation in [GitHub Discussions](https://github.com/modelcontextprotocol/ext-apps/discussions).