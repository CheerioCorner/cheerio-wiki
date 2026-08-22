---
title: Architecture overview
description:
url: https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture
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
  - architecture
---
# Architecture overview

> Source: [Architecture overview](https://modelcontextprotocol.io/docs/2026-07-28/learn/architecture)
> Clipped: 2026-08-22

This overview of the Model Context Protocol (MCP) discusses its [scope](#scope) and [core concepts](#concepts-of-mcp), and provides an [example](#example) demonstrating each core concept.

Because MCP SDKs abstract away many concerns, most developers will likely find the [data layer protocol](#data-layer-protocol) section to be the most useful. It discusses how MCP servers can provide context to an AI application.

For specific implementation details, please refer to the documentation for your [language-specific SDK](https://modelcontextprotocol.io/docs/2026-07-28/sdk).

## Scope

The Model Context Protocol includes the following projects:

- [MCP Specification](https://modelcontextprotocol.io/specification/latest): A specification of MCP that outlines the implementation requirements for clients and servers.
- [MCP SDKs](https://modelcontextprotocol.io/docs/2026-07-28/sdk): SDKs for different programming languages that implement MCP.
- **MCP Development Tools**: Tools for developing MCP servers and clients, including the [MCP Inspector](https://github.com/modelcontextprotocol/inspector)
- [MCP Reference Server Implementations](https://github.com/modelcontextprotocol/servers): Reference implementations of MCP servers.

MCP focuses solely on the protocol for context exchange—it does not dictate how AI applications use LLMs or manage the provided context.

## Concepts of MCP

### Participants

MCP follows a client-server architecture where an MCP host — an AI application like [Claude Code](https://www.anthropic.com/claude-code) or [Claude Desktop](https://www.claude.ai/download) — establishes connections to one or more MCP servers. The MCP host accomplishes this by creating one MCP client for each MCP server. Each MCP client maintains a dedicated connection with its corresponding MCP server.

Local MCP servers that use the STDIO transport typically serve a single MCP client, whereas remote MCP servers that use the Streamable HTTP transport will typically serve many MCP clients.

The key participants in the MCP architecture are:

- **MCP Host**: The AI application that coordinates and manages one or multiple MCP clients
- **MCP Client**: A component that maintains a connection to an MCP server and obtains context from an MCP server for the MCP host to use
- **MCP Server**: A program that provides context to MCP clients

**For example**: Visual Studio Code acts as an MCP host. When Visual Studio Code establishes a connection to an MCP server, such as the [Sentry MCP server](https://docs.sentry.io/product/sentry-mcp/), the Visual Studio Code runtime instantiates an MCP client object that maintains the connection to the Sentry MCP server. When Visual Studio Code subsequently connects to another MCP server, such as the [local filesystem server](https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem), the Visual Studio Code runtime instantiates an additional MCP client object to maintain this connection.

<svg id="mermaid-_r_2nd_-1787390581115" width="100%" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="max-width: 970.56640625px;" viewBox="0 0 970.56640625 296" role="graphics-document document" aria-roledescription="flowchart-v2"><g><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-pointEnd" viewBox="0 0 10 10" refX="5" refY="5" markerUnits="userSpaceOnUse" markerWidth="8" markerHeight="8" orient="auto"><path d="M 0 0 L 10 5 L 0 10 z" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-pointStart" viewBox="0 0 10 10" refX="4.5" refY="5" markerUnits="userSpaceOnUse" markerWidth="8" markerHeight="8" orient="auto"><path d="M 0 5 L 10 10 L 10 0 z" style="stroke-width: 1; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-pointEnd-margin" viewBox="0 0 11.5 14" refX="11.5" refY="7" markerUnits="userSpaceOnUse" markerWidth="10.5" markerHeight="14" orient="auto"><path d="M 0 0 L 11.5 7 L 0 14 z" style="stroke-width: 0; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-pointStart-margin" viewBox="0 0 11.5 14" refX="1" refY="7" markerUnits="userSpaceOnUse" markerWidth="11.5" markerHeight="14" orient="auto"><polygon points="0,7 11.5,14 11.5,0" style="stroke-width: 0; stroke-dasharray: 1, 0;"></polygon></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-circleEnd" viewBox="0 0 10 10" refX="11" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-circleStart" viewBox="0 0 10 10" refX="-1" refY="5" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 1; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-circleEnd-margin" viewBox="0 0 10 10" refY="5" refX="12.25" markerUnits="userSpaceOnUse" markerWidth="14" markerHeight="14" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 0; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-circleStart-margin" viewBox="0 0 10 10" refX="-2" refY="5" markerUnits="userSpaceOnUse" markerWidth="14" markerHeight="14" orient="auto"><circle cx="5" cy="5" r="5" style="stroke-width: 0; stroke-dasharray: 1, 0;"></circle></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-crossEnd" viewBox="0 0 11 11" refX="12" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-crossStart" viewBox="0 0 11 11" refX="-1" refY="5.2" markerUnits="userSpaceOnUse" markerWidth="11" markerHeight="11" orient="auto"><path d="M 1,1 l 9,9 M 10,1 l -9,9" style="stroke-width: 2; stroke-dasharray: 1, 0;"></path></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-crossEnd-margin" viewBox="0 0 15 15" refX="17.7" refY="7.5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 1,1 L 14,14 M 1,14 L 14,1" style="stroke-width: 2.5;"></path></marker><marker id="mermaid-_r_2nd_-1787390581115_flowchart-v2-crossStart-margin" viewBox="0 0 15 15" refX="-3.5" refY="7.5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto"><path d="M 1,1 L 14,14 M 1,14 L 14,1" style="stroke-width: 2.5; stroke-dasharray: 1, 0;"></path></marker><g><g><g id="mermaid-_r_2nd_-1787390581115-subGraph0" data-look="classic"><rect style="" x="8" y="8" width="954.56640625" height="104"></rect><g transform="translate(386.853515625, 8)"><foreignObject width="196.859375" height="24"><p>MCP Host (AI Application)</p></foreignObject></g></g></g><g><path d="M120.664,87L120.664,91.167C120.664,95.333,120.664,103.667,120.664,116C120.664,128.333,120.664,144.667,120.664,161C120.664,177.333,120.664,193.667,120.664,201.833L120.664,210" id="mermaid-_r_2nd_-1787390581115-L_Client1_ServerA_0" style=";" data-edge="true" data-et="edge" data-id="L_Client1_ServerA_0" data-points="W3sieCI6MTIwLjY2NDA2MjUsInkiOjg3fSx7IngiOjEyMC42NjQwNjI1LCJ5IjoxMTJ9LHsieCI6MTIwLjY2NDA2MjUsInkiOjE2MX0seyJ4IjoxMjAuNjY0MDYyNSwieSI6MjEwfV0=" data-look="classic" fill="none" stroke="currentColor"></path><path d="M393.836,87L393.836,91.167C393.836,95.333,393.836,103.667,393.836,116C393.836,128.333,393.836,144.667,393.836,161C393.836,177.333,393.836,193.667,393.836,201.833L393.836,210" id="mermaid-_r_2nd_-1787390581115-L_Client2_ServerB_0" style=";" data-edge="true" data-et="edge" data-id="L_Client2_ServerB_0" data-points="W3sieCI6MzkzLjgzNTkzNzUsInkiOjg3fSx7IngiOjM5My44MzU5Mzc1LCJ5IjoxMTJ9LHsieCI6MzkzLjgzNTkzNzUsInkiOjE2MX0seyJ4IjozOTMuODM1OTM3NSwieSI6MjEwfV0=" data-look="classic" fill="none" stroke="currentColor"></path><path d="M639.059,87L639.059,91.167C639.059,95.333,639.059,103.667,639.059,116C639.059,128.333,639.059,144.667,648.753,161C658.448,177.333,677.837,193.667,687.532,201.833L697.227,210" id="mermaid-_r_2nd_-1787390581115-L_Client3_ServerC_0" style=";" data-edge="true" data-et="edge" data-id="L_Client3_ServerC_0" data-points="W3sieCI6NjM5LjA1ODU5Mzc1LCJ5Ijo4N30seyJ4Ijo2MzkuMDU4NTkzNzUsInkiOjExMn0seyJ4Ijo2MzkuMDU4NTkzNzUsInkiOjE2MX0seyJ4Ijo2OTcuMjI2NTE4MTEwNzk1NSwieSI6MjEwfV0=" data-look="classic" fill="none" stroke="currentColor"></path><path d="M847.988,87L847.988,91.167C847.988,95.333,847.988,103.667,847.988,116C847.988,128.333,847.988,144.667,838.294,161C828.599,177.333,809.21,193.667,799.515,201.833L789.82,210" id="mermaid-_r_2nd_-1787390581115-L_Client4_ServerC_0" style=";" data-edge="true" data-et="edge" data-id="L_Client4_ServerC_0" data-points="W3sieCI6ODQ3Ljk4ODI4MTI1LCJ5Ijo4N30seyJ4Ijo4NDcuOTg4MjgxMjUsInkiOjExMn0seyJ4Ijo4NDcuOTg4MjgxMjUsInkiOjE2MX0seyJ4Ijo3ODkuODIwMzU2ODg5MjA0NSwieSI6MjEwfV0=" data-look="classic" fill="none" stroke="currentColor"></path></g><g><g transform="translate(120.6640625, 161)"><g data-id="L_Client1_ServerA_0" transform="translate(-42.1328125, -24)"><foreignObject width="84.265625" height="48"><p>Dedicated<br>connection</p></foreignObject></g></g><g transform="translate(393.8359375, 161)"><g data-id="L_Client2_ServerB_0" transform="translate(-42.1328125, -24)"><foreignObject width="84.265625" height="48"><p>Dedicated<br>connection</p></foreignObject></g></g><g transform="translate(639.05859375, 161)"><g data-id="L_Client3_ServerC_0" transform="translate(-42.1328125, -24)"><foreignObject width="84.265625" height="48"><p>Dedicated<br>connection</p></foreignObject></g></g><g transform="translate(847.98828125, 161)"><g data-id="L_Client4_ServerC_0" transform="translate(-42.1328125, -24)"><foreignObject width="84.265625" height="48"><p>Dedicated<br>connection</p></foreignObject></g></g></g><g><g id="mermaid-_r_2nd_-1787390581115-flowchart-Client1-0" data-look="classic" transform="translate(120.6640625, 60)"><rect style="" x="-77.6640625" y="-27" width="155.328125" height="54" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-47.6640625, -12)"><rect></rect><foreignObject width="95.328125" height="24"><p>MCP Client 1</p></foreignObject></g></g><g id="mermaid-_r_2nd_-1787390581115-flowchart-Client2-1" data-look="classic" transform="translate(393.8359375, 60)"><rect style="" x="-79.2890625" y="-27" width="158.578125" height="54" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-49.2890625, -12)"><rect></rect><foreignObject width="98.578125" height="24"><p>MCP Client 2</p></foreignObject></g></g><g id="mermaid-_r_2nd_-1787390581115-flowchart-Client3-2" data-look="classic" transform="translate(639.05859375, 60)"><rect style="" x="-79.3515625" y="-27" width="158.703125" height="54" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-49.3515625, -12)"><rect></rect><foreignObject width="98.703125" height="24"><p>MCP Client 3</p></foreignObject></g></g><g id="mermaid-_r_2nd_-1787390581115-flowchart-Client4-3" data-look="classic" transform="translate(847.98828125, 60)"><rect style="" x="-79.578125" y="-27" width="159.15625" height="54" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-49.578125, -12)"><rect></rect><foreignObject width="99.15625" height="24"><p>MCP Client 4</p></foreignObject></g></g><g id="mermaid-_r_2nd_-1787390581115-flowchart-ServerA-4" data-look="classic" transform="translate(120.6640625, 249)"><rect style="" x="-111.7265625" y="-39" width="223.453125" height="78" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-81.7265625, -24)"><rect></rect><foreignObject width="163.453125" height="48"><p>MCP Server A - Local<br>(e.g. Filesystem)</p></foreignObject></g></g><g id="mermaid-_r_2nd_-1787390581115-flowchart-ServerB-5" data-look="classic" transform="translate(393.8359375, 249)"><rect style="" x="-111.4453125" y="-39" width="222.890625" height="78" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-81.4453125, -24)"><rect></rect><foreignObject width="162.890625" height="48"><p>MCP Server B - Local<br>(e.g. Database)</p></foreignObject></g></g><g id="mermaid-_r_2nd_-1787390581115-flowchart-ServerC-6" data-look="classic" transform="translate(743.5234375, 249)"><rect style="" x="-120.359375" y="-39" width="240.71875" height="78" fill="none" stroke="currentColor"></rect><g style="" transform="translate(-90.359375, -24)"><rect></rect><foreignObject width="180.71875" height="48"><p>MCP Server C - Remote<br>(e.g. Sentry)</p></foreignObject></g></g></g></g></g><defs></defs><defs></defs><linearGradient id="mermaid-_r_2nd_-1787390581115-gradient" gradientUnits="objectBoundingBox" x1="0%" y1="0%" x2="100%" y2="0%"><stop offset="0%" stop-color="#cccccc" stop-opacity="1"></stop><stop offset="100%" stop-color="hsl(180, 0%, 18.3529411765%)" stop-opacity="1"></stop></linearGradient></svg>

Note that **MCP server** refers to the program that serves context data, regardless of where it runs. MCP servers can execute locally or remotely. For example, when Claude Desktop launches the [filesystem server](https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem), the server runs locally on the same machine because it uses the STDIO transport. This is commonly referred to as a “local” MCP server. The official [Sentry MCP server](https://docs.sentry.io/product/sentry-mcp/) runs on the Sentry platform, and uses the Streamable HTTP transport. This is commonly referred to as a “remote” MCP server.

### Layers

MCP consists of two layers:

- **Data layer**: Defines the JSON-RPC based protocol for client-server communication, including capability and version discovery, and core primitives, such as tools, resources, prompts and notifications.
- **Transport layer**: Defines the communication mechanisms and channels that enable data exchange between clients and servers, including transport-specific connection establishment, message framing, and authorization.

Conceptually the data layer is the inner layer, while the transport layer is the outer layer.

#### Data layer

The data layer implements a [JSON-RPC 2.0](https://www.jsonrpc.org/) based exchange protocol that defines the message structure and semantics. This layer includes:

- **Discovery**: Lets clients query a server’s supported protocol versions, capabilities, and identity through the `server/discover` request
- **Server features**: Enables servers to provide core functionality including tools for AI actions, resources for context data, and prompts for interaction templates from and to the client
- **Client features**: Enables servers to elicit input from the user. Sampling is [deprecated](https://modelcontextprotocol.io/specification/2026-07-28/deprecated) as of protocol version `2026-07-28`.
- **Utility features**: Supports additional capabilities like notifications for real-time updates and progress tracking for long-running operations

#### Transport layer

The transport layer manages communication channels and authentication between clients and servers. It handles connection establishment, message framing, and secure communication between MCP participants.

MCP supports two transport mechanisms:

- **Stdio transport**: Uses standard input/output streams for direct process communication between local processes on the same machine, providing optimal performance with no network overhead.
- **Streamable HTTP transport**: Uses HTTP POST for client-to-server messages with optional Server-Sent Events for streaming capabilities. This transport enables remote server communication and supports standard HTTP authentication methods including bearer tokens, API keys, and custom headers. MCP recommends using OAuth to obtain authentication tokens.

The transport layer abstracts communication details from the protocol layer, enabling the same JSON-RPC 2.0 message format across all transport mechanisms.

### Data Layer Protocol

A core part of MCP is defining the schema and semantics between MCP clients and MCP servers. Developers will likely find the data layer — in particular, the set of [primitives](#primitives) — to be the most interesting part of MCP. It is the part of MCP that defines the ways developers can share context from MCP servers to MCP clients.

MCP uses [JSON-RPC 2.0](https://www.jsonrpc.org/) as its underlying RPC protocol. Client and servers send requests to each other and respond accordingly. Notifications can be used when no response is required.

#### Statelessness and discovery

MCP is a stateless protocol. Every request carries the protocol version and the capabilities relevant to that request in its `_meta` field, so the server can process each request on its own. Clients should also identify themselves in the same field unless configured not to. Servers advertise their supported versions and capabilities through the mandatory [`server/discover`](https://modelcontextprotocol.io/specification/2026-07-28/server/discover) request, which clients may send before any other request. Detailed information can be found in the [specification](https://modelcontextprotocol.io/specification/2026-07-28/basic/index#statelessness), and the [example](#example) showcases the per-request metadata and the discovery sequence.

#### Primitives

MCP primitives are the most important concept within MCP. They define what clients and servers can offer each other. These primitives specify the types of contextual information that can be shared with AI applications and the range of actions that can be performed.

MCP defines three core primitives that *servers* can expose:

- **Tools**: Executable functions that AI applications can invoke to perform actions (e.g., file operations, API calls, database queries)
- **Resources**: Data sources that provide contextual information to AI applications (e.g., file contents, database records, API responses)
- **Prompts**: Reusable templates that help structure interactions with language models (e.g., system prompts, few-shot examples)

Each primitive type has associated methods for discovery (`*/list`), retrieval (`*/get`), and in some cases, execution (`tools/call`). MCP clients will use the `*/list` methods to discover available primitives. For example, a client can first list all available tools (`tools/list`) and then execute them. This design allows listings to be dynamic.

As a concrete example, consider an MCP server that provides context about a database. It can expose tools for querying the database, a resource that contains the schema of the database, and a prompt that includes few-shot examples for interacting with the tools.

For more details about server primitives see [server concepts](https://modelcontextprotocol.io/docs/2026-07-28/learn/server-concepts).

MCP also defines primitives that *clients* can expose. These primitives allow MCP server authors to build richer interactions.

- **Elicitation**: Allows servers to request additional information from users. This is useful when server authors want to get more information from the user, or ask for confirmation of an action. Servers request user input with the `elicitation/create` method.

Elicitation requests are delivered through the [Multi Round-Trip Requests](https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/mrtr) pattern, explained in the [elicitation overview](https://modelcontextprotocol.io/docs/2026-07-28/learn/client-concepts#elicitation).

**Deprecated**: The following client primitives are deprecated as of protocol version `2026-07-28`.

- **Sampling**: Allows servers to request language model completions from the client’s AI application. This is useful when server authors want access to a language model, but want to stay model-independent and not include a language model SDK in their MCP server. Servers request completions with the `sampling/createMessage` method, also delivered through the Multi Round-Trip Requests pattern. New implementations should integrate directly with LLM provider APIs.
- **Logging**: Enables servers to send log messages to clients for debugging and monitoring purposes. New implementations should log to `stderr` (stdio transport) or use OpenTelemetry.

For more details about client primitives see [client concepts](https://modelcontextprotocol.io/docs/2026-07-28/learn/client-concepts).

Besides server and client primitives, the protocol supports optional [extensions](https://modelcontextprotocol.io/extensions/overview) that build on the core protocol. For example, the [Tasks extension](https://modelcontextprotocol.io/extensions/tasks/overview) lets servers return a durable handle for long-running requests, so clients can poll for status and retrieve the result later.

#### Notifications

The protocol supports real-time notifications to enable dynamic updates between servers and clients. For example, when a server’s available tools change (such as when new functionality becomes available or existing tools are modified), the server can send tool update notifications to inform connected clients about these changes. Notifications are sent as JSON-RPC 2.0 notification messages (without expecting a response). Change notifications are opt-in: the client opens a long-lived [`subscriptions/listen`](https://modelcontextprotocol.io/specification/2026-07-28/basic/patterns/subscriptions) stream naming the notification types it wants to receive, and the server delivers matching notifications on that stream.

## Example

### Data Layer

This section provides a step-by-step walkthrough of an MCP client-server interaction, focusing on the data layer protocol. We’ll demonstrate discovery, tool operations, and notifications using JSON-RPC 2.0 messages.