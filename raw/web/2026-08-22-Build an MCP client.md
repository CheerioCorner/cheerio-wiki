---
title: Build an MCP client
description: Get started building your own client that can integrate with all MCP servers.
url: https://modelcontextprotocol.io/docs/2026-07-28/develop/build-client
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
  - mcp-client
---
# Build an MCP client

> Source: [Build an MCP client](https://modelcontextprotocol.io/docs/2026-07-28/develop/build-client)
> Clipped: 2026-08-22

In this tutorial, you’ll learn how to build an LLM-powered chatbot client that connects to MCP servers.

Before you begin, it helps to have gone through our [Build an MCP Server](https://modelcontextprotocol.io/docs/2026-07-28/develop/build-server) tutorial so you can understand how clients and servers communicate.

[You can find the complete code for this tutorial here.](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/mcp-client-python)

## System Requirements

Before starting, ensure your system meets these requirements:

- Mac or Windows computer
- Latest Python version installed
- Latest version of `uv` installed
- You must use the Python MCP SDK 2.0.0 or higher

## Setting Up Your Environment

First, create a new Python project with `uv`:

```shellscript
# Create project directory
uv init mcp-client
cd mcp-client

# Create virtual environment
uv venv

# Activate virtual environment
source .venv/bin/activate

# Install required packages
uv add mcp anthropic python-dotenv

# Remove boilerplate files
rm main.py

# Create our main file
touch client.py
```

```powershell
# Create project directory
uv init mcp-client
cd mcp-client

# Create virtual environment
uv venv

# Activate virtual environment
.venv\Scripts\activate

# Install required packages
uv add mcp anthropic python-dotenv

# Remove boilerplate files
del main.py

# Create our main file
new-item client.py
```

## Setting Up Your API Key

You’ll need an Anthropic API key from the [Anthropic Console](https://console.anthropic.com/settings/keys).

Create a `.env` file to store it:

```shellscript
echo "ANTHROPIC_API_KEY=your-api-key-goes-here" > .env
```

Add `.env` to your `.gitignore`:

```shellscript
echo ".env" >> .gitignore
```

Make sure you keep your `ANTHROPIC_API_KEY` secure!

## Creating the Client

### Imports and Setup

First, let’s set up our imports and the pieces the rest of the file shares:

```python
import asyncio
import sys

from mcp import Client, StdioServerParameters
from mcp.client.stdio import stdio_client
from mcp_types import TextContent

from anthropic import Anthropic
from dotenv import load_dotenv

load_dotenv()  # load environment variables from .env

MODEL = "claude-opus-5"
anthropic = Anthropic()
```

`Client` is the single object your program talks to the server through. Listing the tools, calling one, reading a resource: each of those is a method on it.

### Server Connection Management

Next, we’ll work out which process to launch for a given server script:

```python
def server_params(server_script_path: str) -> StdioServerParameters:
    """Describe the subprocess that runs an MCP server

    Args:
        server_script_path: Path to the server script (.py or .js)
    """
    if server_script_path.endswith(".py"):
        command = "python"
    elif server_script_path.endswith(".js"):
        command = "node"
    else:
        raise ValueError("Server script must be a .py or .js file")

    return StdioServerParameters(command=command, args=[server_script_path])
```

`StdioServerParameters` is configuration, not a connection. `stdio_client()` turns it into a stdio transport, and `Client` opens that transport when you enter its `async with` block. We’ll do both in `main()`.

### Query Processing Logic

Now let’s add the core functionality for processing queries and handling tool calls:

```python
async def process_query(client: Client, query: str) -> str:
    """Process a query using Claude and available tools"""
    messages = [
        {
            "role": "user",
            "content": query
        }
    ]

    tool_list = await client.list_tools()
    available_tools = [{
        "name": tool.name,
        "description": tool.description,
        "input_schema": tool.input_schema
    } for tool in tool_list.tools]

    # Initial Claude API call
    response = anthropic.messages.create(
        model=MODEL,
        max_tokens=1000,
        messages=messages,
        tools=available_tools
    )

    # Process response and handle tool calls
    final_text = []
    tool_results = []

    for content in response.content:
        if content.type == 'text':
            final_text.append(content.text)
        elif content.type == 'tool_use':
            tool_name = content.name
            tool_args = content.input

            # Execute tool call
            result = await client.call_tool(tool_name, tool_args)
            final_text.append(f"[Calling tool {tool_name} with args {tool_args}]")

            tool_results.append({
                "type": "tool_result",
                "tool_use_id": content.id,
                "content": "\n".join(
                    block.text
                    for block in result.content
                    if isinstance(block, TextContent)
                ),
                "is_error": result.is_error
            })

    if tool_results:
        messages.append({"role": "assistant", "content": response.content})
        messages.append({"role": "user", "content": tool_results})

        # Get next response from Claude
        response = anthropic.messages.create(
            model=MODEL,
            max_tokens=1000,
            messages=messages,
            tools=available_tools
        )

        for content in response.content:
            if content.type == 'text':
                final_text.append(content.text)

    return "\n".join(final_text)
```

`call_tool` returns a `CallToolResult`. Its `content` is a list of blocks, which is why we narrow to `TextContent` before reading `.text`. A tool that raises does not raise here: it answers with `is_error` set, and passing that flag on lets Claude read the message and try something else.

### Interactive Chat Interface

Now we’ll add the chat loop:

```python
async def chat_loop(client: Client) -> None:
    """Run an interactive chat loop"""
    print("\nMCP Client Started!")
    print("Type your queries or 'quit' to exit.")

    while True:
        try:
            query = (await asyncio.to_thread(input, "\nQuery: ")).strip()
        except EOFError:
            break

        if query.lower() == 'quit':
            break

        try:
            response = await process_query(client, query)
            print("\n" + response)
        except Exception as e:
            print(f"\nError: {e}")
```

`input()` blocks, so it runs on a worker thread. That keeps the event loop free to service the connection while you type.

### Main Entry Point

Finally, we’ll add the main execution logic:

```python
async def main() -> None:
    if len(sys.argv) < 2:
        print("Usage: python client.py <path_to_server_script>")
        sys.exit(1)

    async with Client(stdio_client(server_params(sys.argv[1]))) as client:
        tool_list = await client.list_tools()
        tool_names = [tool.name for tool in tool_list.tools]
        print("\nConnected to server with tools:", tool_names)

        await chat_loop(client)

if __name__ == "__main__":
    asyncio.run(main())
```

That `async with` is the entire connection lifecycle. Entering it launches the server and agrees a protocol version with it; leaving it disconnects and shuts the subprocess down. There is nothing to close by hand.

You can find the complete `client.py` file [here](https://github.com/modelcontextprotocol/quickstart-resources/blob/main/mcp-client-python/client.py).

## Key Components Explained

### 1\. Client Initialization

- A single `Client` carries the connection, and `async with` is its whole lifecycle
- There is no connect/close pair to call and nothing to clean up afterwards
- Configures the Anthropic client for Claude interactions

### 2\. Server Connection

- Supports both Python and Node.js servers
- Validates server script type
- Launches the server as a subprocess and speaks stdio to it
- Lists the available tools once the connection is open

### 3\. Query Processing

- Maintains conversation context
- Handles Claude’s responses and tool calls
- Manages the message flow between Claude and tools
- Combines results into a coherent response

### 4\. Interactive Interface

- Provides a simple command-line interface
- Handles user input and displays responses
- Includes basic error handling
- Allows graceful exit

### 5\. Resource Management

- Leaving the `async with` block disconnects and shuts the server subprocess down
- A failing query is reported without ending the session
- Typing `quit`, or closing standard input, exits cleanly

## Common Customization Points

1. **Tool Handling**
	- Modify `process_query()` to handle specific tool types
		- Add custom error handling for tool calls
		- Implement tool-specific response formatting
2. **Response Processing**
	- Customize how tool results are formatted
		- Add response filtering or transformation
		- Implement custom logging
3. **User Interface**
	- Add a GUI or web interface
		- Implement rich console output
		- Add command history or auto-completion

## Running the Client

To run your client with any MCP server:

```shellscript
uv run client.py path/to/server.py # python server
uv run client.py path/to/build/index.js # node server
```

If you’re continuing [the weather tutorial from the server quickstart](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/weather-server-python), your command might look something like this: `python client.py .../quickstart-resources/weather-server-python/weather.py`

The client will:

1. Connect to the specified server
2. List available tools
3. Start an interactive chat session where you can:
	- Enter queries
		- See tool executions
		- Get responses from Claude

Here’s an example of what it should look like if connected to the weather server from the server quickstart:

![](https://mintcdn.com/mcp/4ZXF1PrDkEaJvXpn/images/client-claude-cli-python.png?w=2500&fit=max&auto=format&n=4ZXF1PrDkEaJvXpn&q=85&s=da01c2527db68cb0c99d29d20751a868)

## How It Works

When you submit a query:

1. The client gets the list of available tools from the server
2. Your query is sent to Claude along with tool descriptions
3. Claude decides which tools (if any) to use
4. The client executes any requested tool calls through the server
5. Results are sent back to Claude
6. Claude provides a natural language response
7. The response is displayed to you

## Best practices

1. **Error Handling**
	- Check `result.is_error` rather than expecting a failing tool to raise
		- Provide meaningful error messages
		- Gracefully handle connection issues
2. **Resource Management**
	- Let the `async with` block own the connection
		- Keep it open for as long as you need the server
		- Handle server disconnections
3. **Security**
	- Store API keys securely in `.env`
		- Validate server responses
		- Be cautious with tool permissions
4. **Tool Names**
	- Tool names can be validated according to the format specified [here](https://modelcontextprotocol.io/specification/2026-07-28/server/tools#tool-names)
		- If a tool name conforms to the specified format, it should not fail validation by an MCP client

## Troubleshooting

### Server Path Issues

- Double-check the path to your server script is correct
- Use the absolute path if the relative path isn’t working
- For Windows users, make sure to use forward slashes (/) or escaped backslashes (\\) in the path
- Verify the server file has the correct extension (.py for Python or.js for Node.js)

Example of correct path usage:

```shellscript
# Relative path
uv run client.py ./server/weather.py

# Absolute path
uv run client.py /Users/username/projects/mcp-server/weather.py

# Windows path (either format works)
uv run client.py C:/projects/mcp-server/weather.py
uv run client.py C:\\projects\\mcp-server\\weather.py
```

### Response Timing

- The first response might take up to 30 seconds to return
- This is normal and happens while:
	- The server initializes
		- Claude processes the query
		- Tools are being executed
- Subsequent responses are typically faster
- Don’t interrupt the process during this initial waiting period

### Common Error Messages

If you see:

- `FileNotFoundError`: Check your server path
- `Connection refused`: Ensure the server is running and the path is correct
- `Tool execution failed`: Verify the tool’s required environment variables are set
- `Timeout error`: Consider raising `read_timeout_seconds` on the `Client`

[You can find the complete code for this tutorial here.](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/mcp-client-typescript)

## System Requirements

Before starting, ensure your system meets these requirements:

- Mac or Windows computer
- Node.js 20 or higher installed
- Latest version of `npm` installed
- Anthropic API key (Claude)

## Setting Up Your Environment

First, let’s create and set up our project:

```shellscript
# Create project directory
mkdir mcp-client-typescript
cd mcp-client-typescript

# Initialize npm project
npm init -y

# Install dependencies
npm install @anthropic-ai/sdk @modelcontextprotocol/client dotenv

# Install dev dependencies
npm install -D @types/node typescript

# Create source file
touch index.ts
```

```powershell
# Create project directory
md mcp-client-typescript
cd mcp-client-typescript

# Initialize npm project
npm init -y

# Install dependencies
npm install @anthropic-ai/sdk @modelcontextprotocol/client dotenv

# Install dev dependencies
npm install -D @types/node typescript

# Create source file
new-item index.ts
```

Update your `package.json` to set `type: "module"` and a build script:

package.json

```json
{
  "type": "module",
  "scripts": {
    "build": "tsc && chmod 755 build/index.js"
  }
}
```

Create a `tsconfig.json` in the root of your project:

tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "Node16",
    "moduleResolution": "Node16",
    "types": ["node"],
    "outDir": "./build",
    "rootDir": "./",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["index.ts"],
  "exclude": ["node_modules"]
}
```

## Setting Up Your API Key

You’ll need an Anthropic API key from the [Anthropic Console](https://console.anthropic.com/settings/keys).

Create a `.env` file to store it:

```shellscript
echo "ANTHROPIC_API_KEY=<your key here>" > .env
```

Add `.env` to your `.gitignore`:

```shellscript
echo ".env" >> .gitignore
```

Make sure you keep your `ANTHROPIC_API_KEY` secure!

## Creating the Client

### Basic Client Structure

First, let’s set up our imports and create the basic client class in `index.ts`:

```typescript
import { Anthropic } from "@anthropic-ai/sdk";
import {
  MessageParam,
  Tool,
} from "@anthropic-ai/sdk/resources/messages/messages.mjs";
import { Client } from "@modelcontextprotocol/client";
import { StdioClientTransport } from "@modelcontextprotocol/client/stdio";
import readline from "readline/promises";
import dotenv from "dotenv";

dotenv.config();

const ANTHROPIC_API_KEY = process.env.ANTHROPIC_API_KEY;
if (!ANTHROPIC_API_KEY) {
  throw new Error("ANTHROPIC_API_KEY is not set");
}

class MCPClient {
  private mcp: Client;
  private anthropic: Anthropic;
  private transport: StdioClientTransport | null = null;
  private tools: Tool[] = [];

  constructor() {
    this.anthropic = new Anthropic({
      apiKey: ANTHROPIC_API_KEY,
    });
    this.mcp = new Client({ name: "mcp-client-cli", version: "1.0.0" });
  }
  // methods will go here
}
```

### Server Connection Management

Next, we’ll implement the method to connect to an MCP server:

```typescript
async connectToServer(serverScriptPath: string) {
  try {
    const isJs = serverScriptPath.endsWith(".js");
    const isPy = serverScriptPath.endsWith(".py");
    if (!isJs && !isPy) {
      throw new Error("Server script must be a .js or .py file");
    }
    const command = isPy
      ? process.platform === "win32"
        ? "python"
        : "python3"
      : process.execPath;

    this.transport = new StdioClientTransport({
      command,
      args: [serverScriptPath],
    });
    await this.mcp.connect(this.transport);

    const toolsResult = await this.mcp.listTools();
    this.tools = toolsResult.tools.map((tool) => {
      return {
        name: tool.name,
        description: tool.description,
        input_schema: tool.inputSchema,
      };
    });
    console.log(
      "Connected to server with tools:",
      this.tools.map(({ name }) => name)
    );
  } catch (e) {
    console.log("Failed to connect to MCP server: ", e);
    throw e;
  }
}
```

### Query Processing Logic

Now let’s add the core functionality for processing queries and handling tool calls:

```typescript
async processQuery(query: string) {
  const messages: MessageParam[] = [
    {
      role: "user",
      content: query,
    },
  ];

  const response = await this.anthropic.messages.create({
    model: "claude-opus-5",
    max_tokens: 1000,
    messages,
    tools: this.tools,
  });

  const finalText = [];

  for (const content of response.content) {
    if (content.type === "text") {
      finalText.push(content.text);
    } else if (content.type === "tool_use") {
      const toolName = content.name;
      const toolArgs = content.input as { [x: string]: unknown } | undefined;

      const result = await this.mcp.callTool({
        name: toolName,
        arguments: toolArgs,
      });
      finalText.push(
        \`[Calling tool ${toolName} with args ${JSON.stringify(toolArgs)}]\`
      );

      messages.push({
        role: "user",
        content: result.content
          .filter((block) => block.type === "text")
          .map((block) => block.text)
          .join("\n"),
      });

      const response = await this.anthropic.messages.create({
        model: "claude-opus-5",
        max_tokens: 1000,
        messages,
      });

      finalText.push(
        response.content[0].type === "text" ? response.content[0].text : ""
      );
    }
  }

  return finalText.join("\n");
}
```

### Interactive Chat Interface

Now we’ll add the chat loop and cleanup functionality:

```typescript
async chatLoop() {
  const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout,
  });

  try {
    console.log("\nMCP Client Started!");
    console.log("Type your queries or 'quit' to exit.");

    while (true) {
      const message = await rl.question("\nQuery: ");
      if (message.toLowerCase() === "quit") {
        break;
      }
      const response = await this.processQuery(message);
      console.log("\n" + response);
    }
  } finally {
    rl.close();
  }
}

async cleanup() {
  await this.mcp.close();
}
```

### Main Entry Point

Finally, we’ll add the main execution logic:

```typescript
async function main() {
  if (process.argv.length < 3) {
    console.log("Usage: node index.ts <path_to_server_script>");
    return;
  }
  const mcpClient = new MCPClient();
  try {
    await mcpClient.connectToServer(process.argv[2]);
    await mcpClient.chatLoop();
  } catch (e) {
    console.error("Error:", e);
    await mcpClient.cleanup();
    process.exit(1);
  } finally {
    await mcpClient.cleanup();
    process.exit(0);
  }
}

main();
```

## Running the Client

To run your client with any MCP server:

```shellscript
# Build TypeScript
npm run build

# Run the client
node build/index.js path/to/server.py # python server
node build/index.js path/to/build/index.js # node server
```

If you’re continuing [the weather tutorial from the server quickstart](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/weather-server-typescript), your command might look something like this: `node build/index.js .../quickstart-resources/weather-server-typescript/build/index.js`

**The client will:**

1. Connect to the specified server
2. List available tools
3. Start an interactive chat session where you can:
	- Enter queries
		- See tool executions
		- Get responses from Claude

## How It Works

When you submit a query:

1. The client gets the list of available tools from the server
2. Your query is sent to Claude along with tool descriptions
3. Claude decides which tools (if any) to use
4. The client executes any requested tool calls through the server
5. Results are sent back to Claude
6. Claude provides a natural language response
7. The response is displayed to you

## Best practices

1. **Error Handling**
	- Use TypeScript’s type system for better error detection
		- Wrap tool calls in try-catch blocks
		- Provide meaningful error messages
		- Gracefully handle connection issues
2. **Security**
	- Store API keys securely in `.env`
		- Validate server responses
		- Be cautious with tool permissions

## Troubleshooting

### Server Path Issues

- Double-check the path to your server script is correct
- Use the absolute path if the relative path isn’t working
- For Windows users, make sure to use forward slashes (/) or escaped backslashes (\\) in the path
- Verify the server file has the correct extension (.js for Node.js or.py for Python)

Example of correct path usage:

```shellscript
# Relative path
node build/index.js ./server/build/index.js

# Absolute path
node build/index.js /Users/username/projects/mcp-server/build/index.js

# Windows path (either format works)
node build/index.js C:/projects/mcp-server/build/index.js
node build/index.js C:\\projects\\mcp-server\\build\\index.js
```

### Response Timing

- The first response might take up to 30 seconds to return
- This is normal and happens while:
	- The server initializes
		- Claude processes the query
		- Tools are being executed
- Subsequent responses are typically faster
- Don’t interrupt the process during this initial waiting period

### Common Error Messages

If you see:

- `Error: Cannot find module`: Check your build folder and ensure TypeScript compilation succeeded
- `Connection refused`: Ensure the server is running and the path is correct
- `Tool execution failed`: Verify the tool’s required environment variables are set
- `ANTHROPIC_API_KEY is not set`: Check your.env file and environment variables
- `TypeError`: Ensure you’re using the correct types for tool arguments
- `BadRequestError`: Ensure you have enough credits to access the Anthropic API

This is a quickstart demo based on Spring AI MCP auto-configuration and boot starters. To learn how to create sync and async MCP Clients manually, consult the [Java SDK Client](https://java.sdk.modelcontextprotocol.io/) documentation.

This example demonstrates how to build an interactive chatbot that combines Spring AI’s Model Context Protocol (MCP) with the [Brave Search MCP Server](https://github.com/modelcontextprotocol/servers-archived/tree/main/src/brave-search). The application creates a conversational interface powered by Anthropic’s Claude AI model that can perform internet searches through Brave Search, enabling natural language interactions with real-time web data. [You can find the complete code for this tutorial here.](https://github.com/spring-projects/spring-ai-examples/tree/main/model-context-protocol/web-search/brave-chatbot)

## System Requirements

Before starting, ensure your system meets these requirements:

- Java 17 or higher
- Maven 3.6+
- npx package manager
- Anthropic API key (Claude)
- Brave Search API key

## Setting Up Your Environment

1. Install npx (Node Package eXecute): First, make sure to install [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) and then run:
	```shellscript
	npm install -g npx
	```
2. Clone the repository:
	```shellscript
	git clone https://github.com/spring-projects/spring-ai-examples.git
	cd model-context-protocol/web-search/brave-chatbot
	```
3. Set up your API keys:
	```shellscript
	export ANTHROPIC_API_KEY='your-anthropic-api-key-here'
	export BRAVE_API_KEY='your-brave-api-key-here'
	```
4. Build the application:
	```shellscript
	./mvnw clean install
	```
5. Run the application using Maven:
	```shellscript
	./mvnw spring-boot:run
	```

Make sure you keep your `ANTHROPIC_API_KEY` and `BRAVE_API_KEY` keys secure!

## How it Works

The application integrates Spring AI with the Brave Search MCP server through several components:

### MCP Client Configuration

1. Required dependencies in pom.xml:

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-mcp-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-anthropic</artifactId>
</dependency>
```

2. Application properties (application.yml):

```yaml
spring:
  ai:
    mcp:
      client:
        enabled: true
        name: brave-search-client
        version: 1.0.0
        type: SYNC
        request-timeout: 20s
        stdio:
          root-change-notification: true
          servers-configuration: classpath:/mcp-servers-config.json
        toolcallback:
          enabled: true
    anthropic:
      api-key: ${ANTHROPIC_API_KEY}
```

This activates the `spring-ai-starter-mcp-client` to create one or more `McpClient` s based on the provided server configuration. The `spring.ai.mcp.client.toolcallback.enabled=true` property enables the tool callback mechanism, that automatically registers all MCP tool as spring ai tools. It is disabled by default.

3. MCP Server Configuration (`mcp-servers-config.json`):

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "<PUT YOUR BRAVE API KEY>"
      }
    }
  }
}
```

### Chat Implementation

The chatbot is implemented using Spring AI’s ChatClient with MCP tool integration:

```java
var chatClient = chatClientBuilder
    .defaultSystem("You are useful assistant, expert in AI and Java.")
    .defaultToolCallbacks((Object[]) mcpToolAdapter.toolCallbacks())
    .defaultAdvisors(new MessageChatMemoryAdvisor(new InMemoryChatMemory()))
    .build();
```

Key features:

- Uses Claude AI model for natural language understanding
- Integrates Brave Search through MCP for real-time web search capabilities
- Maintains conversation memory using InMemoryChatMemory
- Runs as an interactive command-line application

### Build and run

```shellscript
./mvnw clean install
java -jar ./target/ai-mcp-brave-chatbot-0.0.1-SNAPSHOT.jar
```

or

```shellscript
./mvnw spring-boot:run
```

The application will start an interactive chat session where you can ask questions. The chatbot will use Brave Search when it needs to find information from the internet to answer your queries.

The chatbot can:

- Answer questions using its built-in knowledge
- Perform web searches when needed using Brave Search
- Remember context from previous messages in the conversation
- Combine information from multiple sources to provide comprehensive answers

### Advanced Configuration

The MCP client supports additional configuration options:

- Client customization through `McpClientCustomizer<McpClient.SyncSpec>` or `McpClientCustomizer<McpClient.AsyncSpec>` beans
- Multiple clients with multiple transport types: `STDIO` and Streamable HTTP
- Integration with Spring AI’s tool execution framework
- Automatic client initialization and lifecycle management

To connect to a remote MCP server over Streamable HTTP, configure a connection URL:

```ini
spring.ai.mcp.client.streamable-http.connections.server1.url=http://localhost:8080
```

For WebFlux-based applications, you can use the WebFlux starter instead:

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-mcp-client-webflux</artifactId>
</dependency>
```

This provides similar functionality but uses a WebFlux-based Streamable HTTP transport implementation, recommended for production deployments.

[You can find the complete code for this tutorial here.](https://github.com/modelcontextprotocol/kotlin-sdk/tree/main/samples/kotlin-mcp-client)

## System Requirements

Before starting, ensure your system meets these requirements:

- JDK 11 or higher
- Anthropic API key (Claude)

## Setting up your environment

First, let’s install `java` and `gradle` if you haven’t already. You can download `java` from [official Oracle JDK website](https://www.oracle.com/java/technologies/downloads/). Verify your `java` installation:

```shellscript
java --version
```

Now, let’s create and set up your project:

```shellscript
# Create a new directory for our project
mkdir kotlin-mcp-client
cd kotlin-mcp-client

# Initialize a new kotlin project
gradle init
```

```powershell
# Create a new directory for our project
md kotlin-mcp-client
cd kotlin-mcp-client
# Initialize a new kotlin project
gradle init
```

After running `gradle init`, select **Application** as the project type, **Kotlin** as the programming language.

Alternatively, you can create a Kotlin application using the [IntelliJ IDEA project wizard](https://kotlinlang.org/docs/jvm-get-started.html).

After creating the project, replace the contents of your `build.gradle.kts` with:

build.gradle.kts

```kotlin
// Check latest versions at https://github.com/modelcontextprotocol/kotlin-sdk/releases
val mcpVersion = "0.9.0"
val ktorVersion = "3.2.3"
val anthropicVersion = "2.15.0"
val slf4jVersion = "2.0.17"

plugins {
    kotlin("jvm") version "2.3.20"
    id("com.gradleup.shadow") version "8.3.9"
    application
}

application {
    mainClass.set("MainKt")
}

dependencies {
    implementation("io.modelcontextprotocol:kotlin-sdk:$mcpVersion")
    implementation("io.ktor:ktor-client-cio:$ktorVersion")
    implementation("com.anthropic:anthropic-java:$anthropicVersion")
    implementation("org.slf4j:slf4j-simple:$slf4jVersion")
}
```

Verify that everything is set up correctly:

```shellscript
./gradlew build
```

## Setting up your API key

You’ll need an Anthropic API key from the [Anthropic Console](https://console.anthropic.com/settings/keys).

Set up your API key:

```shellscript
export ANTHROPIC_API_KEY='your-anthropic-api-key-here'
```

Make sure you keep your `ANTHROPIC_API_KEY` secure!

## Creating the Client

### Basic Client Structure

First, let’s create the basic client class:

```kotlin
class MCPClient(apiKey: String) : AutoCloseable {
    private val anthropic = AnthropicOkHttpClient.builder()
        .apiKey(apiKey)
        .build()

  private val mcp: Client = Client(
        clientInfo = Implementation(name = "mcp-client-cli", version = "1.0.0")
  )
    private var serverProcess: Process? = null
    private lateinit var tools: List<ToolUnion>

    // methods will go here

    override fun close() {
        runBlocking {
            mcp.close()
        }
        serverProcess?.destroy()
        anthropic.close()
    }
}
```

### Server connection management

Next, we’ll implement the method to connect to an MCP server:

```kotlin
suspend fun connectToServer(serverScriptPath: String) {
    val command = buildList {
        when (serverScriptPath.substringAfterLast(".")) {
            "js" -> add("node")
            "py" -> add(if (System.getProperty("os.name").lowercase().contains("win")) "python" else "python3")
            "jar" -> addAll(listOf("java", "-jar"))
            else -> throw IllegalArgumentException("Server script must be a .js, .py or .jar file")
        }
        add(serverScriptPath)
    }

    val process = ProcessBuilder(command).start()
    serverProcess = process

    val transport = StdioClientTransport(
        input = process.inputStream.asSource().buffered(),
        output = process.outputStream.asSink().buffered(),
    )

    mcp.connect(transport)

    val toolsResult = mcp.listTools()
    tools = toolsResult.tools.map { tool ->
        ToolUnion.ofTool(
            Tool.builder()
                .name(tool.name)
                .description(tool.description ?: "")
                .inputSchema(
                    Tool.InputSchema.builder()
                        .type(JsonValue.from(tool.inputSchema.type))
                        .properties(tool.inputSchema.properties?.toJsonValue() ?: EmptyJsonObject.toJsonValue())
                        .putAdditionalProperty("required", JsonValue.from(tool.inputSchema.required))
                        .build(),
                )
                .build(),
        )
    }
    println("Connected to server with tools: ${tools.joinToString(", ") { it.tool().get().name() }}")
}
```

JsonObject.toJsonValue() helper

This helper converts a kotlinx.serialization `JsonObject` to an Anthropic SDK `JsonValue` using Jackson:

```kotlin
private fun JsonObject.toJsonValue(): JsonValue {
    val mapper = ObjectMapper()
    val node = mapper.readTree(this.toString())
    return JsonValue.fromJsonNode(node)
}
```

### Query processing logic

Now let’s add the core functionality for processing queries and handling tool calls:

```kotlin
suspend fun processQuery(query: String): String {
    val messages = mutableListOf(
        MessageParam.builder()
            .role(MessageParam.Role.USER)
            .content(query)
            .build(),
    )

    val response = anthropic.messages().create(
        MessageCreateParams.builder()
            .model("claude-opus-5")
            .maxTokens(1024)
            .messages(messages)
            .tools(tools)
            .build(),
    )

    val finalText = mutableListOf<String>()
    response.content().forEach { content ->
        when {
            content.isText() -> finalText.add(content.text().get().text())

            content.isToolUse() -> {
                val toolName = content.toolUse().get().name()
                val toolArgs =
                    content.toolUse().get()._input().convert(object : TypeReference<Map<String, JsonValue>>() {})

                val result = mcp.callTool(
                    name = toolName,
                    arguments = toolArgs ?: emptyMap(),
                )
                finalText.add("[Calling tool $toolName with args $toolArgs]")

                messages.add(
                    MessageParam.builder()
                        .role(MessageParam.Role.USER)
                        .content(
                            result.content
                                .filterIsInstance<TextContent>()
                                .joinToString("\n") { it.text }
                        )
                        .build(),
                )

                val aiResponse = anthropic.messages().create(
                    MessageCreateParams.builder()
                        .model("claude-opus-5")
                        .maxTokens(1024)
                        .messages(messages)
                        .build(),
                )

                finalText.add(aiResponse.content().first().text().get().text())
            }
        }
    }

    return finalText.joinToString("\n")
}
```

### Interactive chat

We’ll add the chat loop:

```kotlin
suspend fun chatLoop() {
    println("\nMCP Client Started!")
    println("Type your queries or 'quit' to exit.")

    while (true) {
        print("\nQuery: ")
        val message = readlnOrNull() ?: break
        if (message.trim().lowercase() == "quit") break

        try {
            val response = processQuery(message)
            println("\n$response")
        } catch (e: Exception) {
            println("\nError: ${e.message}")
        }
    }
}
```

### Main entry point

Finally, we’ll add the main execution function:

```kotlin
fun main(args: Array<String>) = runBlocking {
    require(args.isNotEmpty()) { "Usage: java -jar <path> <path_to_server_script>" }

    val apiKey = System.getenv("ANTHROPIC_API_KEY")
    require(!apiKey.isNullOrBlank()) { "ANTHROPIC_API_KEY environment variable is not set" }

    val client = MCPClient(apiKey)
    client.use {
        client.connectToServer(args.first())
        client.chatLoop()
    }
}
```

## Running the client

To run your client with any MCP server:

```shellscript
./gradlew build

# Run the client
java -jar build/libs/kotlin-mcp-client-0.1.0-all.jar path/to/server.jar # JVM server
java -jar build/libs/kotlin-mcp-client-0.1.0-all.jar path/to/server.py  # Python server
java -jar build/libs/kotlin-mcp-client-0.1.0-all.jar path/to/build/index.js # Node server
```

Alternatively, you can run directly with Gradle:

```shellscript
./gradlew run --args="path/to/server.jar"
```

If you’re continuing the weather tutorial from the server quickstart, your command might look something like this: `java -jar build/libs/kotlin-mcp-client-0.1.0-all.jar .../samples/weather-stdio-server/build/libs/weather-stdio-server-0.1.0-all.jar`

**The client will:**

1. Connect to the specified server
2. List available tools
3. Start an interactive chat session where you can:
	- Enter queries
		- See tool executions
		- Get responses from Claude

## How it works

Here’s a high-level workflow schema:

<svg id="mermaid-_r_824_-1787390737348" width="100%" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" style="max-width: 1205px;" viewBox="-50 -10 1205 721" role="graphics-document document" aria-roledescription="sequence"><g><rect x="955" y="635" fill="#eaeaea" stroke="#666" width="150" height="65" name="Tools" rx="3" ry="3"></rect><text x="1030" y="667.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="1030" dy="0">Tools</tspan></text></g> <g><rect x="724" y="635" fill="#eaeaea" stroke="#666" width="150" height="65" name="MCP_Server" rx="3" ry="3"></rect><text x="799" y="667.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="799" dy="0">MCP Server</tspan></text></g> <g><rect x="524" y="635" fill="#eaeaea" stroke="#666" width="150" height="65" name="Claude" rx="3" ry="3"></rect><text x="599" y="667.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="599" dy="0">Claude</tspan></text></g> <g><rect x="200" y="635" fill="#eaeaea" stroke="#666" width="150" height="65" name="Client" rx="3" ry="3"></rect><text x="275" y="667.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="275" dy="0">Client</tspan></text></g> <g></g><g><line id="actor37" x1="1030" y1="65" x2="1030" y2="635" stroke-width="0.5px" stroke="#999" name="Tools" data-et="life-line" data-id="Tools"></line><g id="root-37" data-et="participant" data-type="participant" data-id="Tools"><rect x="955" y="0" fill="#eaeaea" stroke="#666" width="150" height="65" name="Tools" rx="3" ry="3"></rect><text x="1030" y="32.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="1030" dy="0">Tools</tspan></text></g></g> <g><line id="actor36" x1="799" y1="65" x2="799" y2="635" stroke-width="0.5px" stroke="#999" name="MCP_Server" data-et="life-line" data-id="MCP_Server"></line><g id="root-36" data-et="participant" data-type="participant" data-id="MCP_Server"><rect x="724" y="0" fill="#eaeaea" stroke="#666" width="150" height="65" name="MCP_Server" rx="3" ry="3"></rect><text x="799" y="32.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="799" dy="0">MCP Server</tspan></text></g></g> <g><line id="actor35" x1="599" y1="65" x2="599" y2="635" stroke-width="0.5px" stroke="#999" name="Claude" data-et="life-line" data-id="Claude"></line><g id="root-35" data-et="participant" data-type="participant" data-id="Claude"><rect x="524" y="0" fill="#eaeaea" stroke="#666" width="150" height="65" name="Claude" rx="3" ry="3"></rect><text x="599" y="32.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="599" dy="0">Claude</tspan></text></g></g> <g><line id="actor34" x1="275" y1="65" x2="275" y2="635" stroke-width="0.5px" stroke="#999" name="Client" data-et="life-line" data-id="Client"></line><g id="root-34" data-et="participant" data-type="participant" data-id="Client"><rect x="200" y="0" fill="#eaeaea" stroke="#666" width="150" height="65" name="Client" rx="3" ry="3"></rect><text x="275" y="32.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="275" dy="0">Client</tspan></text></g></g> <g><line id="actor33" x1="75" y1="80" x2="75" y2="635" stroke-width="0.5px" stroke="#999" name="User" data-et="life-line" data-id="User"></line></g><g></g><defs><symbol id="mermaid-_r_824_-1787390737348-computer" width="24" height="24"><path transform="scale(.5)" d="M2 2v13h20v-13h-20zm18 11h-16v-9h16v9zm-10.228 6l.466-1h3.524l.467 1h-4.457zm14.228 3h-24l2-6h2.104l-1.33 4h18.45l-1.297-4h2.073l2 6zm-5-10h-14v-7h14v7z"></path></symbol></defs><defs><symbol id="mermaid-_r_824_-1787390737348-database" fill-rule="evenodd" clip-rule="evenodd"><path transform="scale(.5)" d="M12.258.001l.256.004.255.005.253.008.251.01.249.012.247.015.246.016.242.019.241.02.239.023.236.024.233.027.231.028.229.031.225.032.223.034.22.036.217.038.214.04.211.041.208.043.205.045.201.046.198.048.194.05.191.051.187.053.183.054.18.056.175.057.172.059.168.06.163.061.16.063.155.064.15.066.074.033.073.033.071.034.07.034.069.035.068.035.067.035.066.035.064.036.064.036.062.036.06.036.06.037.058.037.058.037.055.038.055.038.053.038.052.038.051.039.05.039.048.039.047.039.045.04.044.04.043.04.041.04.04.041.039.041.037.041.036.041.034.041.033.042.032.042.03.042.029.042.027.042.026.043.024.043.023.043.021.043.02.043.018.044.017.043.015.044.013.044.012.044.011.045.009.044.007.045.006.045.004.045.002.045.001.045v17l-.001.045-.002.045-.004.045-.006.045-.007.045-.009.044-.011.045-.012.044-.013.044-.015.044-.017.043-.018.044-.02.043-.021.043-.023.043-.024.043-.026.043-.027.042-.029.042-.03.042-.032.042-.033.042-.034.041-.036.041-.037.041-.039.041-.04.041-.041.04-.043.04-.044.04-.045.04-.047.039-.048.039-.05.039-.051.039-.052.038-.053.038-.055.038-.055.038-.058.037-.058.037-.06.037-.06.036-.062.036-.064.036-.064.036-.066.035-.067.035-.068.035-.069.035-.07.034-.071.034-.073.033-.074.033-.15.066-.155.064-.16.063-.163.061-.168.06-.172.059-.175.057-.18.056-.183.054-.187.053-.191.051-.194.05-.198.048-.201.046-.205.045-.208.043-.211.041-.214.04-.217.038-.22.036-.223.034-.225.032-.229.031-.231.028-.233.027-.236.024-.239.023-.241.02-.242.019-.246.016-.247.015-.249.012-.251.01-.253.008-.255.005-.256.004-.258.001-.258-.001-.256-.004-.255-.005-.253-.008-.251-.01-.249-.012-.247-.015-.245-.016-.243-.019-.241-.02-.238-.023-.236-.024-.234-.027-.231-.028-.228-.031-.226-.032-.223-.034-.22-.036-.217-.038-.214-.04-.211-.041-.208-.043-.204-.045-.201-.046-.198-.048-.195-.05-.19-.051-.187-.053-.184-.054-.179-.056-.176-.057-.172-.059-.167-.06-.164-.061-.159-.063-.155-.064-.151-.066-.074-.033-.072-.033-.072-.034-.07-.034-.069-.035-.068-.035-.067-.035-.066-.035-.064-.036-.063-.036-.062-.036-.061-.036-.06-.037-.058-.037-.057-.037-.056-.038-.055-.038-.053-.038-.052-.038-.051-.039-.049-.039-.049-.039-.046-.039-.046-.04-.044-.04-.043-.04-.041-.04-.04-.041-.039-.041-.037-.041-.036-.041-.034-.041-.033-.042-.032-.042-.03-.042-.029-.042-.027-.042-.026-.043-.024-.043-.023-.043-.021-.043-.02-.043-.018-.044-.017-.043-.015-.044-.013-.044-.012-.044-.011-.045-.009-.044-.007-.045-.006-.045-.004-.045-.002-.045-.001-.045v-17l.001-.045.002-.045.004-.045.006-.045.007-.045.009-.044.011-.045.012-.044.013-.044.015-.044.017-.043.018-.044.02-.043.021-.043.023-.043.024-.043.026-.043.027-.042.029-.042.03-.042.032-.042.033-.042.034-.041.036-.041.037-.041.039-.041.04-.041.041-.04.043-.04.044-.04.046-.04.046-.039.049-.039.049-.039.051-.039.052-.038.053-.038.055-.038.056-.038.057-.037.058-.037.06-.037.061-.036.062-.036.063-.036.064-.036.066-.035.067-.035.068-.035.069-.035.07-.034.072-.034.072-.033.074-.033.151-.066.155-.064.159-.063.164-.061.167-.06.172-.059.176-.057.179-.056.184-.054.187-.053.19-.051.195-.05.198-.048.201-.046.204-.045.208-.043.211-.041.214-.04.217-.038.22-.036.223-.034.226-.032.228-.031.231-.028.234-.027.236-.024.238-.023.241-.02.243-.019.245-.016.247-.015.249-.012.251-.01.253-.008.255-.005.256-.004.258-.001.258.001zm-9.258 20.499v.01l.001.021.003.021.004.022.005.021.006.022.007.022.009.023.01.022.011.023.012.023.013.023.015.023.016.024.017.023.018.024.019.024.021.024.022.025.023.024.024.025.052.049.056.05.061.051.066.051.07.051.075.051.079.052.084.052.088.052.092.052.097.052.102.051.105.052.11.052.114.051.119.051.123.051.127.05.131.05.135.05.139.048.144.049.147.047.152.047.155.047.16.045.163.045.167.043.171.043.176.041.178.041.183.039.187.039.19.037.194.035.197.035.202.033.204.031.209.03.212.029.216.027.219.025.222.024.226.021.23.02.233.018.236.016.24.015.243.012.246.01.249.008.253.005.256.004.259.001.26-.001.257-.004.254-.005.25-.008.247-.011.244-.012.241-.014.237-.016.233-.018.231-.021.226-.021.224-.024.22-.026.216-.027.212-.028.21-.031.205-.031.202-.034.198-.034.194-.036.191-.037.187-.039.183-.04.179-.04.175-.042.172-.043.168-.044.163-.045.16-.046.155-.046.152-.047.148-.048.143-.049.139-.049.136-.05.131-.05.126-.05.123-.051.118-.052.114-.051.11-.052.106-.052.101-.052.096-.052.092-.052.088-.053.083-.051.079-.052.074-.052.07-.051.065-.051.06-.051.056-.05.051-.05.023-.024.023-.025.021-.024.02-.024.019-.024.018-.024.017-.024.015-.023.014-.024.013-.023.012-.023.01-.023.01-.022.008-.022.006-.022.006-.022.004-.022.004-.021.001-.021.001-.021v-4.127l-.077.055-.08.053-.083.054-.085.053-.087.052-.09.052-.093.051-.095.05-.097.05-.1.049-.102.049-.105.048-.106.047-.109.047-.111.046-.114.045-.115.045-.118.044-.12.043-.122.042-.124.042-.126.041-.128.04-.13.04-.132.038-.134.038-.135.037-.138.037-.139.035-.142.035-.143.034-.144.033-.147.032-.148.031-.15.03-.151.03-.153.029-.154.027-.156.027-.158.026-.159.025-.161.024-.162.023-.163.022-.165.021-.166.02-.167.019-.169.018-.169.017-.171.016-.173.015-.173.014-.175.013-.175.012-.177.011-.178.01-.179.008-.179.008-.181.006-.182.005-.182.004-.184.003-.184.002h-.37l-.184-.002-.184-.003-.182-.004-.182-.005-.181-.006-.179-.008-.179-.008-.178-.01-.176-.011-.176-.012-.175-.013-.173-.014-.172-.015-.171-.016-.17-.017-.169-.018-.167-.019-.166-.02-.165-.021-.163-.022-.162-.023-.161-.024-.159-.025-.157-.026-.156-.027-.155-.027-.153-.029-.151-.03-.15-.03-.148-.031-.146-.032-.145-.033-.143-.034-.141-.035-.14-.035-.137-.037-.136-.037-.134-.038-.132-.038-.13-.04-.128-.04-.126-.041-.124-.042-.122-.042-.12-.044-.117-.043-.116-.045-.113-.045-.112-.046-.109-.047-.106-.047-.105-.048-.102-.049-.1-.049-.097-.05-.095-.05-.093-.052-.09-.051-.087-.052-.085-.053-.083-.054-.08-.054-.077-.054v4.127zm0-5.654v.011l.001.021.003.021.004.021.005.022.006.022.007.022.009.022.01.022.011.023.012.023.013.023.015.024.016.023.017.024.018.024.019.024.021.024.022.024.023.025.024.024.052.05.056.05.061.05.066.051.07.051.075.052.079.051.084.052.088.052.092.052.097.052.102.052.105.052.11.051.114.051.119.052.123.05.127.051.131.05.135.049.139.049.144.048.147.048.152.047.155.046.16.045.163.045.167.044.171.042.176.042.178.04.183.04.187.038.19.037.194.036.197.034.202.033.204.032.209.03.212.028.216.027.219.025.222.024.226.022.23.02.233.018.236.016.24.014.243.012.246.01.249.008.253.006.256.003.259.001.26-.001.257-.003.254-.006.25-.008.247-.01.244-.012.241-.015.237-.016.233-.018.231-.02.226-.022.224-.024.22-.025.216-.027.212-.029.21-.03.205-.032.202-.033.198-.035.194-.036.191-.037.187-.039.183-.039.179-.041.175-.042.172-.043.168-.044.163-.045.16-.045.155-.047.152-.047.148-.048.143-.048.139-.05.136-.049.131-.05.126-.051.123-.051.118-.051.114-.052.11-.052.106-.052.101-.052.096-.052.092-.052.088-.052.083-.052.079-.052.074-.051.07-.052.065-.051.06-.05.056-.051.051-.049.023-.025.023-.024.021-.025.02-.024.019-.024.018-.024.017-.024.015-.023.014-.023.013-.024.012-.022.01-.023.01-.023.008-.022.006-.022.006-.022.004-.021.004-.022.001-.021.001-.021v-4.139l-.077.054-.08.054-.083.054-.085.052-.087.053-.09.051-.093.051-.095.051-.097.05-.1.049-.102.049-.105.048-.106.047-.109.047-.111.046-.114.045-.115.044-.118.044-.12.044-.122.042-.124.042-.126.041-.128.04-.13.039-.132.039-.134.038-.135.037-.138.036-.139.036-.142.035-.143.033-.144.033-.147.033-.148.031-.15.03-.151.03-.153.028-.154.028-.156.027-.158.026-.159.025-.161.024-.162.023-.163.022-.165.021-.166.02-.167.019-.169.018-.169.017-.171.016-.173.015-.173.014-.175.013-.175.012-.177.011-.178.009-.179.009-.179.007-.181.007-.182.005-.182.004-.184.003-.184.002h-.37l-.184-.002-.184-.003-.182-.004-.182-.005-.181-.007-.179-.007-.179-.009-.178-.009-.176-.011-.176-.012-.175-.013-.173-.014-.172-.015-.171-.016-.17-.017-.169-.018-.167-.019-.166-.02-.165-.021-.163-.022-.162-.023-.161-.024-.159-.025-.157-.026-.156-.027-.155-.028-.153-.028-.151-.03-.15-.03-.148-.031-.146-.033-.145-.033-.143-.033-.141-.035-.14-.036-.137-.036-.136-.037-.134-.038-.132-.039-.13-.039-.128-.04-.126-.041-.124-.042-.122-.043-.12-.043-.117-.044-.116-.044-.113-.046-.112-.046-.109-.046-.106-.047-.105-.048-.102-.049-.1-.049-.097-.05-.095-.051-.093-.051-.09-.051-.087-.053-.085-.052-.083-.054-.08-.054-.077-.054v4.139zm0-5.666v.011l.001.02.003.022.004.021.005.022.006.021.007.022.009.023.01.022.011.023.012.023.013.023.015.023.016.024.017.024.018.023.019.024.021.025.022.024.023.024.024.025.052.05.056.05.061.05.066.051.07.051.075.052.079.051.084.052.088.052.092.052.097.052.102.052.105.051.11.052.114.051.119.051.123.051.127.05.131.05.135.05.139.049.144.048.147.048.152.047.155.046.16.045.163.045.167.043.171.043.176.042.178.04.183.04.187.038.19.037.194.036.197.034.202.033.204.032.209.03.212.028.216.027.219.025.222.024.226.021.23.02.233.018.236.017.24.014.243.012.246.01.249.008.253.006.256.003.259.001.26-.001.257-.003.254-.006.25-.008.247-.01.244-.013.241-.014.237-.016.233-.018.231-.02.226-.022.224-.024.22-.025.216-.027.212-.029.21-.03.205-.032.202-.033.198-.035.194-.036.191-.037.187-.039.183-.039.179-.041.175-.042.172-.043.168-.044.163-.045.16-.045.155-.047.152-.047.148-.048.143-.049.139-.049.136-.049.131-.051.126-.05.123-.051.118-.052.114-.051.11-.052.106-.052.101-.052.096-.052.092-.052.088-.052.083-.052.079-.052.074-.052.07-.051.065-.051.06-.051.056-.05.051-.049.023-.025.023-.025.021-.024.02-.024.019-.024.018-.024.017-.024.015-.023.014-.024.013-.023.012-.023.01-.022.01-.023.008-.022.006-.022.006-.022.004-.022.004-.021.001-.021.001-.021v-4.153l-.077.054-.08.054-.083.053-.085.053-.087.053-.09.051-.093.051-.095.051-.097.05-.1.049-.102.048-.105.048-.106.048-.109.046-.111.046-.114.046-.115.044-.118.044-.12.043-.122.043-.124.042-.126.041-.128.04-.13.039-.132.039-.134.038-.135.037-.138.036-.139.036-.142.034-.143.034-.144.033-.147.032-.148.032-.15.03-.151.03-.153.028-.154.028-.156.027-.158.026-.159.024-.161.024-.162.023-.163.023-.165.021-.166.02-.167.019-.169.018-.169.017-.171.016-.173.015-.173.014-.175.013-.175.012-.177.01-.178.01-.179.009-.179.007-.181.006-.182.006-.182.004-.184.003-.184.001-.185.001-.185-.001-.184-.001-.184-.003-.182-.004-.182-.006-.181-.006-.179-.007-.179-.009-.178-.01-.176-.01-.176-.012-.175-.013-.173-.014-.172-.015-.171-.016-.17-.017-.169-.018-.167-.019-.166-.02-.165-.021-.163-.023-.162-.023-.161-.024-.159-.024-.157-.026-.156-.027-.155-.028-.153-.028-.151-.03-.15-.03-.148-.032-.146-.032-.145-.033-.143-.034-.141-.034-.14-.036-.137-.036-.136-.037-.134-.038-.132-.039-.13-.039-.128-.041-.126-.041-.124-.041-.122-.043-.12-.043-.117-.044-.116-.044-.113-.046-.112-.046-.109-.046-.106-.048-.105-.048-.102-.048-.1-.05-.097-.049-.095-.051-.093-.051-.09-.052-.087-.052-.085-.053-.083-.053-.08-.054-.077-.054v4.153zm8.74-8.179l-.257.004-.254.005-.25.008-.247.011-.244.012-.241.014-.237.016-.233.018-.231.021-.226.022-.224.023-.22.026-.216.027-.212.028-.21.031-.205.032-.202.033-.198.034-.194.036-.191.038-.187.038-.183.04-.179.041-.175.042-.172.043-.168.043-.163.045-.16.046-.155.046-.152.048-.148.048-.143.048-.139.049-.136.05-.131.05-.126.051-.123.051-.118.051-.114.052-.11.052-.106.052-.101.052-.096.052-.092.052-.088.052-.083.052-.079.052-.074.051-.07.052-.065.051-.06.05-.056.05-.051.05-.023.025-.023.024-.021.024-.02.025-.019.024-.018.024-.017.023-.015.024-.014.023-.013.023-.012.023-.01.023-.01.022-.008.022-.006.023-.006.021-.004.022-.004.021-.001.021-.001.021.001.021.001.021.004.021.004.022.006.021.006.023.008.022.01.022.01.023.012.023.013.023.014.023.015.024.017.023.018.024.019.024.02.025.021.024.023.024.023.025.051.05.056.05.06.05.065.051.07.052.074.051.079.052.083.052.088.052.092.052.096.052.101.052.106.052.11.052.114.052.118.051.123.051.126.051.131.05.136.05.139.049.143.048.148.048.152.048.155.046.16.046.163.045.168.043.172.043.175.042.179.041.183.04.187.038.191.038.194.036.198.034.202.033.205.032.21.031.212.028.216.027.22.026.224.023.226.022.231.021.233.018.237.016.241.014.244.012.247.011.25.008.254.005.257.004.26.001.26-.001.257-.004.254-.005.25-.008.247-.011.244-.012.241-.014.237-.016.233-.018.231-.021.226-.022.224-.023.22-.026.216-.027.212-.028.21-.031.205-.032.202-.033.198-.034.194-.036.191-.038.187-.038.183-.04.179-.041.175-.042.172-.043.168-.043.163-.045.16-.046.155-.046.152-.048.148-.048.143-.048.139-.049.136-.05.131-.05.126-.051.123-.051.118-.051.114-.052.11-.052.106-.052.101-.052.096-.052.092-.052.088-.052.083-.052.079-.052.074-.051.07-.052.065-.051.06-.05.056-.05.051-.05.023-.025.023-.024.021-.024.02-.025.019-.024.018-.024.017-.023.015-.024.014-.023.013-.023.012-.023.01-.023.01-.022.008-.022.006-.023.006-.021.004-.022.004-.021.001-.021.001-.021-.001-.021-.001-.021-.004-.021-.004-.022-.006-.021-.006-.023-.008-.022-.01-.022-.01-.023-.012-.023-.013-.023-.014-.023-.015-.024-.017-.023-.018-.024-.019-.024-.02-.025-.021-.024-.023-.024-.023-.025-.051-.05-.056-.05-.06-.05-.065-.051-.07-.052-.074-.051-.079-.052-.083-.052-.088-.052-.092-.052-.096-.052-.101-.052-.106-.052-.11-.052-.114-.052-.118-.051-.123-.051-.126-.051-.131-.05-.136-.05-.139-.049-.143-.048-.148-.048-.152-.048-.155-.046-.16-.046-.163-.045-.168-.043-.172-.043-.175-.042-.179-.041-.183-.04-.187-.038-.191-.038-.194-.036-.198-.034-.202-.033-.205-.032-.21-.031-.212-.028-.216-.027-.22-.026-.224-.023-.226-.022-.231-.021-.233-.018-.237-.016-.241-.014-.244-.012-.247-.011-.25-.008-.254-.005-.257-.004-.26-.001-.26.001z"></path></symbol></defs><defs><symbol id="mermaid-_r_824_-1787390737348-clock" width="24" height="24"><path transform="scale(.5)" d="M12 2c5.514 0 10 4.486 10 10s-4.486 10-10 10-10-4.486-10-10 4.486-10 10-10zm0-2c-6.627 0-12 5.373-12 12s5.373 12 12 12 12-5.373 12-12-5.373-12-12-12zm5.848 12.459c.202.038.202.333.001.372-1.907.361-6.045 1.111-6.547 1.111-.719 0-1.301-.582-1.301-1.301 0-.512.77-5.447 1.125-7.445.034-.192.312-.181.343.014l.985 6.238 5.394 1.011z"></path></symbol></defs><defs><marker id="mermaid-_r_824_-1787390737348-arrowhead" refX="7.9" refY="5" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto-start-reverse"><path d="M -1 0 L 10 5 L 0 10 z"></path></marker></defs><defs><marker id="mermaid-_r_824_-1787390737348-crosshead" markerWidth="15" markerHeight="8" orient="auto" refX="4" refY="4.5"><path fill="none" stroke="#000000" stroke-width="1pt" d="M 1,2 L 6,7 M 6,2 L 1,7" style="stroke-dasharray: 0, 0;"></path></marker></defs><defs><marker id="mermaid-_r_824_-1787390737348-filled-head" refX="15.5" refY="7" markerWidth="20" markerHeight="28" orient="auto"><path d="M 18,7 L9,13 L14,7 L9,1 Z"></path></marker></defs><defs><marker id="mermaid-_r_824_-1787390737348-sequencenumber" refX="15" refY="15" markerWidth="60" markerHeight="40" orient="auto"><circle cx="15" cy="15" r="6"></circle></marker></defs><defs><marker id="mermaid-_r_824_-1787390737348-solidTopArrowHead" refX="7.9" refY="7.25" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto-start-reverse"><path d="M 0 0 L 10 8 L 0 8 z"></path></marker></defs><defs><marker id="mermaid-_r_824_-1787390737348-solidBottomArrowHead" refX="7.9" refY="0.75" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto-start-reverse"><path d="M 0 0 L 10 0 L 0 8 z"></path></marker></defs><defs><marker id="mermaid-_r_824_-1787390737348-stickTopArrowHead" refX="7.5" refY="7" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto-start-reverse"><path d="M 0 0 L 7 7" stroke="black" stroke-width="1.5" fill="none"></path></marker></defs><defs><marker id="mermaid-_r_824_-1787390737348-stickBottomArrowHead" refX="7.5" refY="0" markerUnits="userSpaceOnUse" markerWidth="12" markerHeight="12" orient="auto-start-reverse"><path d="M 0 7 L 7 0" stroke="black" stroke-width="1.5" fill="none"></path></marker></defs><g name="User" data-et="participant" data-type="actor" data-id="User" style="stroke: rgb(212, 212, 212);"><line id="actor-man-torso33" x1="75" y1="25" x2="75" y2="45"></line><line id="actor-man-arms33" x1="57" y1="33" x2="93" y2="33"></line><line x1="57" y1="60" x2="75" y2="45"></line><line x1="75" y1="45" x2="91" y2="60"></line><circle cx="75" cy="10" r="15" width="150" height="65"></circle><text x="75" y="67.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="75" dy="0">User</tspan></text></g> <text x="174" y="80" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Send query</text> <line x1="76" y1="115" x2="271" y2="115" data-et="message" data-id="i0" data-from="User" data-to="Client" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="fill: none;"></line><text x="537" y="130" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Get available tools</text> <line x1="279" y1="165" x2="795" y2="165" data-et="message" data-id="i1" data-from="Client" data-to="MCP_Server" stroke-width="2" stroke="none" marker-start="url(#mermaid-_r_824_-1787390737348-arrowhead)" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="fill: none;"></line><text x="436" y="180" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Send query with tool descriptions</text> <line x1="276" y1="215" x2="595" y2="215" data-et="message" data-id="i2" data-from="Client" data-to="Claude" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="fill: none;"></line><text x="439" y="230" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Decide tool execution</text> <line x1="598" y1="265" x2="279" y2="265" data-et="message" data-id="i3" data-from="Claude" data-to="Client" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="stroke-dasharray: 3, 3; fill: none;"></line><text x="536" y="280" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Request tool execution</text> <line x1="276" y1="315" x2="795" y2="315" data-et="message" data-id="i4" data-from="Client" data-to="MCP_Server" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="fill: none;"></line><text x="913" y="330" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Execute chosen tools</text> <line x1="800" y1="365" x2="1026" y2="365" data-et="message" data-id="i5" data-from="MCP_Server" data-to="Tools" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="fill: none;"></line><text x="916" y="380" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Return results</text> <line x1="1029" y1="415" x2="803" y2="415" data-et="message" data-id="i6" data-from="Tools" data-to="MCP_Server" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="stroke-dasharray: 3, 3; fill: none;"></line><text x="539" y="430" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Send results</text> <line x1="798" y1="465" x2="279" y2="465" data-et="message" data-id="i7" data-from="MCP_Server" data-to="Client" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="stroke-dasharray: 3, 3; fill: none;"></line><text x="436" y="480" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Send tool results</text> <line x1="276" y1="515" x2="595" y2="515" data-et="message" data-id="i8" data-from="Client" data-to="Claude" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="fill: none;"></line><text x="439" y="530" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Provide final response</text> <line x1="598" y1="565" x2="279" y2="565" data-et="message" data-id="i9" data-from="Claude" data-to="Client" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="stroke-dasharray: 3, 3; fill: none;"></line><text x="177" y="580" text-anchor="middle" dominant-baseline="middle" alignment-baseline="middle" dy="1em" style="font-family: inherit; font-size: 16px; font-weight: 400;">Display response</text> <line x1="274" y1="615" x2="79" y2="615" data-et="message" data-id="i10" data-from="Client" data-to="User" stroke-width="2" stroke="none" marker-end="url(#mermaid-_r_824_-1787390737348-arrowhead)" style="stroke-dasharray: 3, 3; fill: none;"></line><g name="User" style="stroke: rgb(212, 212, 212);"><line id="actor-man-torso37" x1="75" y1="660" x2="75" y2="680"></line><line id="actor-man-arms37" x1="57" y1="668" x2="93" y2="668"></line><line x1="57" y1="695" x2="75" y2="680"></line><line x1="75" y1="680" x2="91" y2="695"></line><circle cx="75" cy="645" r="15" width="150" height="65"></circle><text x="75" y="702.5" dominant-baseline="central" alignment-baseline="central" style="text-anchor: middle; font-size: 16px; font-weight: 400; font-family: inherit;"><tspan x="75" dy="0">User</tspan></text></g></svg>

When you submit a query:

1. The client gets the list of available tools from the server
2. Your query is sent to Claude along with tool descriptions
3. Claude decides which tools (if any) to use
4. The client executes any requested tool calls through the server
5. Results are sent back to Claude
6. Claude provides a natural language response
7. The response is displayed to you

## Best practices

1. **Error Handling**
	- Leverage Kotlin’s type system to model errors explicitly
		- Wrap external tool and API calls in `try-catch` blocks when exceptions are possible
		- Provide clear and meaningful error messages
		- Handle network timeouts and connection issues gracefully
2. **Security**
	- Store API keys and secrets securely in `local.properties`, environment variables, or secret managers
		- Validate all external responses to avoid unexpected or unsafe data usage
		- Be cautious with permissions and trust boundaries when using tools
3. **Environment**
	- Set `ANTHROPIC_API_KEY` through environment variables rather than hardcoding
		- Use `.env` files with appropriate `.gitignore` rules for local development

## Troubleshooting

### Server Path Issues

- Double-check the path to your server script is correct
- Use the absolute path if the relative path isn’t working
- For Windows users, make sure to use forward slashes (/) or escaped backslashes (\\) in the path
- Make sure that the required runtime is installed (java for Java, npm for Node.js, or uv for Python)
- Verify the server file has the correct extension (.jar for Java,.js for Node.js or.py for Python)

Example of correct path usage:

```shellscript
# Relative path
java -jar build/libs/client.jar ./server/build/libs/server.jar

# Absolute path
java -jar build/libs/client.jar /Users/username/projects/mcp-server/build/libs/server.jar

# Windows path (either format works)
java -jar build/libs/client.jar C:/projects/mcp-server/build/libs/server.jar
java -jar build/libs/client.jar C:\\projects\\mcp-server\\build\\libs\\server.jar
```

### Build Issues

- Use `./gradlew build` or `./gradlew shadowJar` (not `./gradlew jar`) to create the shadow JAR with all dependencies
- If you get JDK version errors, ensure your installed JDK version matches or exceeds the `jvmToolchain` setting in `build.gradle.kts`

### Response Timing

- The first response might take up to 30 seconds to return
- This is normal and happens while:
	- The server initializes
		- Claude processes the query
		- Tools are being executed
- Subsequent responses are typically faster
- Don’t interrupt the process during this initial waiting period

### Common Error Messages

If you see:

- `Connection refused`: Ensure the server is running and the path is correct
- `Tool execution failed`: Verify the tool’s required environment variables are set
- `ANTHROPIC_API_KEY is not set`: Check your environment variables

[You can find the complete code for this tutorial here.](https://github.com/modelcontextprotocol/csharp-sdk/tree/main/samples/QuickstartClient)

## System Requirements

Before starting, ensure your system meets these requirements:

- .NET 8.0 or higher
- Anthropic API key (Claude)
- Windows, Linux, or macOS

## Setting up your environment

First, create a new.NET project:

```shellscript
dotnet new console -n QuickstartClient
cd QuickstartClient
```

Then, add the required dependencies to your project:

```shellscript
dotnet add package ModelContextProtocol --prerelease
dotnet add package Anthropic.SDK
dotnet add package Microsoft.Extensions.Hosting
dotnet add package Microsoft.Extensions.AI
```

## Setting up your API key

You’ll need an Anthropic API key from the [Anthropic Console](https://console.anthropic.com/settings/keys).

```shellscript
dotnet user-secrets init
dotnet user-secrets set "ANTHROPIC_API_KEY" "<your key here>"
```

## Creating the Client

### Basic Client Structure

First, let’s setup the basic client class in the file `Program.cs`:

```csharp
using Anthropic.SDK;
using Microsoft.Extensions.AI;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using ModelContextProtocol.Client;
using ModelContextProtocol.Protocol.Transport;

var builder = Host.CreateApplicationBuilder(args);

builder.Configuration
    .AddEnvironmentVariables()
    .AddUserSecrets<Program>();
```

This creates the beginnings of a.NET console application that can read the API key from user secrets.

Next, we’ll setup the MCP Client:

```csharp
var (command, arguments) = GetCommandAndArguments(args);

var clientTransport = new StdioClientTransport(new()
{
    Name = "Demo Server",
    Command = command,
    Arguments = arguments,
});

await using var mcpClient = await McpClient.CreateAsync(clientTransport);

var tools = await mcpClient.ListToolsAsync();
foreach (var tool in tools)
{
    Console.WriteLine($"Connected to server with tools: {tool.Name}");
}
```

Add this function at the end of the `Program.cs` file:

```csharp
static (string command, string[] arguments) GetCommandAndArguments(string[] args)
{
    return args switch
    {
        [var script] when script.EndsWith(".py") => ("python", args),
        [var script] when script.EndsWith(".js") => ("node", args),
        [var script] when Directory.Exists(script) || (File.Exists(script) && script.EndsWith(".csproj")) => ("dotnet", ["run", "--project", script, "--no-build"]),
        _ => throw new NotSupportedException("An unsupported server script was provided. Supported scripts are .py, .js, or .csproj")
    };
}
```

This creates an MCP client that will connect to a server that is provided as a command line argument. It then lists the available tools from the connected server.

### Query processing logic

Now let’s add the core functionality for processing queries and handling tool calls:

```csharp
using var anthropicClient = new AnthropicClient(new APIAuthentication(builder.Configuration["ANTHROPIC_API_KEY"]))
    .Messages
    .AsBuilder()
    .UseFunctionInvocation()
    .Build();

var options = new ChatOptions
{
    MaxOutputTokens = 1000,
    ModelId = "claude-opus-5",
    Tools = [.. tools]
};

Console.ForegroundColor = ConsoleColor.Green;
Console.WriteLine("MCP Client Started!");
Console.ResetColor();

PromptForInput();
while(Console.ReadLine() is string query && !"exit".Equals(query, StringComparison.OrdinalIgnoreCase))
{
    if (string.IsNullOrWhiteSpace(query))
    {
        PromptForInput();
        continue;
    }

    await foreach (var message in anthropicClient.GetStreamingResponseAsync(query, options))
    {
        Console.Write(message);
    }
    Console.WriteLine();

    PromptForInput();
}

static void PromptForInput()
{
    Console.WriteLine("Enter a command (or 'exit' to quit):");
    Console.ForegroundColor = ConsoleColor.Cyan;
    Console.Write("> ");
    Console.ResetColor();
}
```

## Key Components Explained

### 1\. Client Initialization

- The client is initialized using `McpClient.CreateAsync()`, which sets up the transport type and command to run the server.

### 2\. Server Connection

- Supports Python, Node.js, and.NET servers.
- The server is started using the command specified in the arguments.
- Configures to use stdio for communication with the server.
- Initializes the session and available tools.

### 3\. Query Processing

- Leverages [Microsoft.Extensions.AI](https://learn.microsoft.com/dotnet/ai/ai-extensions) for the chat client.
- Configures the `IChatClient` to use automatic tool (function) invocation.
- The client reads user input and sends it to the server.
- The server processes the query and returns a response.
- The response is displayed to the user.

## Running the Client

To run your client with any MCP server:

```shellscript
dotnet run -- path/to/server.csproj # dotnet server
dotnet run -- path/to/server.py # python server
dotnet run -- path/to/server.js # node server
```

If you’re continuing the weather tutorial from the server quickstart, your command might look something like this: `dotnet run -- path/to/QuickstartWeatherServer`.

The client will:

1. Connect to the specified server
2. List available tools
3. Start an interactive chat session where you can:
	- Enter queries
		- See tool executions
		- Get responses from Claude
4. Exit the session when done

Here’s an example of what it should look like if connected to the weather server quickstart:

![](https://mintcdn.com/mcp/4ZXF1PrDkEaJvXpn/images/quickstart-dotnet-client.png?w=2500&fit=max&auto=format&n=4ZXF1PrDkEaJvXpn&q=85&s=ac9271a3dd0d7b424bb390ad0c31e14e)

[You can find the complete code for this tutorial here.](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/mcp-client-ruby)

## System Requirements

Before starting, ensure your system meets these requirements:

- Mac or Windows computer
- Ruby 3.2.0 or higher installed (required by the [Anthropic SDK](https://github.com/anthropics/anthropic-sdk-ruby))
- Anthropic API key (Claude)

## Setting Up Your Environment

First, create a new Ruby project:

```shellscript
# Create project directory
mkdir mcp-client
cd mcp-client

# Create a Gemfile
bundle init

# Add required dependencies
bundle add anthropic base64 dotenv mcp

# Create our main file
touch client.rb
```

```powershell
# Create project directory
mkdir mcp-client
cd mcp-client

# Create a Gemfile
bundle init

# Add required dependencies
bundle add anthropic base64 dotenv mcp

# Create our main file
new-item client.rb
```

## Setting Up Your API Key

You’ll need an Anthropic API key from the [Anthropic Console](https://console.anthropic.com/settings/keys).

Create a `.env` file to store it:

```shellscript
echo "ANTHROPIC_API_KEY=your-api-key-goes-here" > .env
```

Add `.env` to your `.gitignore`:

```shellscript
echo ".env" >> .gitignore
```

Make sure you keep your `ANTHROPIC_API_KEY` secure!

## Creating the Client

### Basic Client Structure

First, let’s set up our requires and create the basic client class:

```ruby
require "anthropic"
require "dotenv/load"
require "json"
require "mcp"

class MCPClient
  ANTHROPIC_MODEL = "claude-opus-5"

  def initialize
    @mcp_client = nil
    @transport = nil
    @anthropic_client = nil
  end

  # methods will go here
end
```

### Server Connection Management

Next, we’ll implement the method to connect to an MCP server:

```ruby
def connect_to_server(server_script_path)
  command = case File.extname(server_script_path)
  when ".rb"
    "ruby"
  when ".py"
    "python3"
  when ".js"
    "node"
  else
    raise ArgumentError, "Server script must be a .rb, .py, or .js file."
  end

  @transport = MCP::Client::Stdio.new(command: command, args: [server_script_path])
  @mcp_client = MCP::Client.new(transport: @transport)
  @mcp_client.connect

  tool_names = @mcp_client.tools.map(&:name)
  puts "\nConnected to server with tools: #{tool_names}"
end
```

### Query Processing Logic

Now let’s add the core functionality for processing queries and handling tool calls:

```ruby
private

def process_query(query)
  messages = [{ role: "user", content: query }]

  available_tools = @mcp_client.tools.map do |tool|
    { name: tool.name, description: tool.description, input_schema: tool.input_schema }
  end

  # Initial Claude API call.
  response = chat(messages, tools: available_tools)

  # Process response and handle tool calls.
  if response.content.any?(Anthropic::Models::ToolUseBlock)
    assistant_content = response.content.filter_map do |content_block|
      case content_block
      when Anthropic::Models::TextBlock
        { type: "text", text: content_block.text }
      when Anthropic::Models::ToolUseBlock
        { type: "tool_use", id: content_block.id, name: content_block.name, input: content_block.input }
      end
    end
    messages << { role: "assistant", content: assistant_content }
  end

  response.content.each_with_object([]) do |content, response_parts|
    case content
    when Anthropic::Models::TextBlock
      response_parts << content.text
    when Anthropic::Models::ToolUseBlock
      # Execute tool call via MCP.
      result = @mcp_client.call_tool(name: content.name, arguments: content.input)
      response_parts << "[Calling tool #{content.name} with args #{content.input.to_json}]"

      tool_result_content = result.dig("result", "content")
      result_text = if tool_result_content.is_a?(Array)
        tool_result_content.filter_map { |content_item| content_item["text"] }.join("\n")
      else
        tool_result_content.to_s
      end

      messages << {
        role: "user",
        content: [{
          type: "tool_result",
          tool_use_id: content.id,
          content: result_text
        }]
      }

      # Get next response from Claude.
      response = chat(messages)

      response.content.each do |content_block|
        response_parts << content_block.text if content_block.is_a?(Anthropic::Models::TextBlock)
      end
    end
  end.join("\n")
end

def chat(messages, tools: nil)
  params = { model: ANTHROPIC_MODEL, max_tokens: 1000, messages: messages }
  params[:tools] = tools if tools

  anthropic_client.messages.create(**params)
end

def anthropic_client
  @anthropic_client ||= Anthropic::Client.new(api_key: ENV["ANTHROPIC_API_KEY"])
end
```

### Interactive Chat Interface

Now we’ll add the chat loop and cleanup functionality:

```ruby
def chat_loop
  puts <<~MESSAGE
    MCP Client Started!
    Type your queries or 'quit' to exit.
  MESSAGE

  loop do
    print "\nQuery: "
    line = $stdin.gets
    break if line.nil?

    query = line.chomp.strip
    break if query.downcase == "quit"
    next if query.empty?

    begin
      response = process_query(query)
      puts "\n#{response}"
    rescue => e
      puts "\nError: #{e.message}"
    end
  end
end

def cleanup
  @transport&.close
end
```

### Main Entry Point

Finally, we’ll add the main execution logic:

```ruby
if ARGV.empty?
  puts "Usage: ruby client.rb <path_to_server_script>"
  exit 1
end

client = MCPClient.new

begin
  client.connect_to_server(ARGV[0])

  api_key = ENV["ANTHROPIC_API_KEY"]
  if api_key.nil? || api_key.empty?
    puts <<~MESSAGE
      No ANTHROPIC_API_KEY found. To query these tools with Claude, set your API key:
        export ANTHROPIC_API_KEY=your-api-key-here
    MESSAGE
    exit
  end

  client.chat_loop
rescue => e
  puts "Error: #{e.message}"
  exit 1
ensure
  client.cleanup
end
```

You can find the complete `client.rb` file [here](https://github.com/modelcontextprotocol/quickstart-resources/blob/main/mcp-client-ruby/client.rb).

## Key Components Explained

### 1\. Client Initialization

- The `MCPClient` class initializes with nil references for lazy setup
- The Anthropic client is lazily initialized via the `anthropic_client` method
- Uses `dotenv` to load environment variables from `.env`

### 2\. Server Connection

- Supports Ruby, Python, and Node.js servers
- Uses `File.extname` to determine the server script type
- Uses `MCP::Client::Stdio` for stdio transport
- Initializes the MCP client and lists available tools

### 3\. Query Processing

- Maps MCP tools to Anthropic tool format (`name`, `description`, `input_schema`)
- Uses `Anthropic::Models::TextBlock` and `Anthropic::Models::ToolUseBlock` for pattern matching
- Builds assistant content once before iterating tool calls
- Executes tool calls via `@mcp_client.call_tool`
- Uses `chat` helper method to wrap Anthropic API calls
- Extracts tool result content with `result.dig("result", "content")`
- Passes tool results back to Claude for a final response

### 4\. Interactive Interface

- Provides a simple command-line interface
- Handles user input and displays responses
- Skips empty queries
- Includes basic error handling

### 5\. Resource Management

- Proper cleanup of the transport via `begin` … `ensure`
- Top-level `rescue` for error handling
- API key validation after server connection

## Running the Client

To run your client with any MCP server:

```shellscript
bundle exec ruby client.rb path/to/server.rb # ruby server
bundle exec ruby client.rb path/to/server.py # python server
bundle exec ruby client.rb path/to/build/index.js # node server
```

If you’re continuing [the weather tutorial from the server quickstart](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/weather-server-ruby), your command might look something like this: `bundle exec ruby client.rb /path/to/weather-server-ruby/weather.rb`

The client will:

1. Connect to the specified server
2. List available tools
3. Start an interactive chat session where you can:
	- Enter queries
		- See tool executions
		- Get responses from Claude

## How It Works

When you submit a query:

1. The client gets the list of available tools from the server
2. Your query is sent to Claude along with tool descriptions
3. Claude decides which tools (if any) to use
4. The client executes any requested tool calls through the server
5. Results are sent back to Claude
6. Claude provides a natural language response
7. The response is displayed to you

## Best practices

1. **Error Handling**
	- Wrap tool calls in `begin` … `rescue` blocks
		- Provide meaningful error messages
		- Gracefully handle connection issues
2. **Resource Management**
	- Always close the transport when done
		- Use `begin` … `ensure` for proper cleanup
		- Handle server disconnections
3. **Security**
	- Store API keys securely in `.env`
		- Validate server responses
		- Be cautious with tool permissions
4. **Tool Names**
	- Tool names can be validated according to the format specified [here](https://modelcontextprotocol.io/specification/2026-07-28/server/tools#tool-names)
		- If a tool name conforms to the specified format, it should not fail validation by an MCP client

## Troubleshooting

### Server Path Issues

- Double-check the path to your server script is correct
- Use the absolute path if the relative path isn’t working
- For Windows users, make sure to use forward slashes (/) or escaped backslashes (\\) in the path
- Verify the server file has the correct extension (.py for Python,.js for Node.js, or.rb for Ruby)

Example of correct path usage:

```shellscript
# Relative path
bundle exec ruby client.rb ./server/weather.rb

# Absolute path
bundle exec ruby client.rb /Users/username/projects/mcp-server/weather.rb

# Windows path (either format works)
bundle exec ruby client.rb C:/projects/mcp-server/weather.rb
bundle exec ruby client.rb C:\\projects\\mcp-server\\weather.rb
```

### Response Timing

- The first response might take up to 30 seconds to return
- This is normal and happens while:
	- The server initializes
		- Claude processes the query
		- Tools are being executed
- Subsequent responses are typically faster
- Don’t interrupt the process during this initial waiting period

### Common Error Messages

If you see:

- `Errno::ENOENT`: Check your server path and ensure the command (`ruby`, `python3`, `node`) is available
- `Connection refused`: Ensure the server is running and the path is correct
- `Tool execution failed`: Verify the tool’s required environment variables are set
- `Anthropic::Errors::AuthenticationError`: Check your `.env` file has a valid `ANTHROPIC_API_KEY`

[You can find the complete code for this tutorial here.](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/mcp-client-rust)

## System Requirements

Before starting, ensure your Linux system meets these requirements:

- Latest stable version of [Rust and Cargo](https://www.rust-lang.org/tools/install)
- Anthropic API key (Claude)
- A Python, Node.js, or executable MCP server to connect to

## Setting Up Your Environment

First, create a new Rust project:

```shellscript
cargo new mcp-client-rust
cd mcp-client-rust
```

Replace the contents of `Cargo.toml` with the following:

Cargo.toml

```toml
[package]
name = "mcp-client-rust"
version = "0.1.0"
edition = "2024"

[dependencies]
anyhow = "1.0.100"
genai = "0.4.2"
rmcp = { version = "0.8.0", features = ["server", "client", "transport-io", "transport-child-process"] }
tokio = { version = "1.47.1", features = ["full"] }
tracing = "0.1.41"
tracing-subscriber = { version = "0.3", features = ["env-filter"] }
serde_json = "1.0.128"
dotenvy = "0.15.7"
reqwest = "0.12.23"
```

The [`rmcp`](https://github.com/modelcontextprotocol/rust-sdk) crate provides the Rust MCP SDK and child-process transport. This example uses the [`genai`](https://github.com/jeremychone/rust-genai) crate to send requests to Claude and represent tools in the model request.

## Setting Up Your API Key

You’ll need an Anthropic API key from the [Anthropic Console](https://console.anthropic.com/settings/keys).

Create a `.env` file to store it:

```shellscript
echo "ANTHROPIC_API_KEY=your-api-key-goes-here" > .env
```

Add `.env` to your `.gitignore`:

```shellscript
echo ".env" >> .gitignore
```

Make sure you keep your `ANTHROPIC_API_KEY` secure!

## Creating the Client

Open `src/main.rs` and replace its contents as you work through the following sections.

### Imports and Client Structure

First, add the imports, model constant, and basic client structure:

```rust
use anyhow::{Context, Result, bail};
use genai::Client;
use genai::chat::{
    ChatMessage, ChatRequest, ChatResponse, ContentPart, Tool as GenaiTool, ToolResponse,
};
use rmcp::model::{CallToolRequestParam, Tool as McpTool};
use rmcp::service::{RoleClient, RunningService, ServiceExt};
use rmcp::transport::TokioChildProcess;
use serde_json::Value;
use tokio::io::{self, AsyncBufReadExt, BufReader};
use tokio::process::Command;

const MODEL_ANTHROPIC: &str = "claude-opus-5";

struct MCPClient {
    anthropic: Client,
    session: Option<RunningService<RoleClient, ()>>,
    tools: Vec<GenaiTool>,
}
```

The client keeps the model API client, the active MCP session, and the tools advertised by the connected server.

### Client Initialization

Next, initialize the model client and start without an MCP session or tools:

```rust
impl MCPClient {
    fn new() -> Result<Self> {
        Ok(MCPClient {
            anthropic: Client::default(),
            session: None,
            tools: Vec::new(),
        })
    }

    // Additional methods will go here.
}
```

`genai::Client::default()` reads the `ANTHROPIC_API_KEY` environment variable when it sends a request.

### Server Connection Management

Add this method inside the `impl MCPClient` block:

```rust
async fn connect_to_server(&mut self, server_args: &[String]) -> Result<()> {
    if self.session.is_some() {
        bail!("Client is already connected to a server");
    }

    let mut command = Command::new(&server_args[0]);
    command.args(&server_args[1..]);

    let process = TokioChildProcess::new(command)
        .with_context(|| format!("Failed to spawn server process for {:?}", server_args))?;

    let session = ().serve(process).await?;

    let rmcp_tools = session
        .list_all_tools()
        .await
        .context("Unable to list tools from server")?;

    let tool_names: Vec<String> = rmcp_tools
        .iter()
        .map(|tool| tool.name.to_string())
        .collect();

    println!("Connected to server with tools: {tool_names:?}");

    self.tools = convert_tools(&rmcp_tools);
    self.session = Some(session);
    Ok(())
}
```

This method:

1. Starts the server as a child process using the command and arguments supplied on the command line
2. Establishes an MCP session over stdio
3. Lists all tools advertised by the server
4. Converts those tools into the format used in model requests

### Converting MCP Tools

Add this function outside the `impl MCPClient` block:

```rust
fn convert_tools(tools: &[McpTool]) -> Vec<GenaiTool> {
    tools
        .iter()
        .map(|tool| GenaiTool {
            name: tool.name.to_string(),
            description: tool.description.as_deref().map(str::to_string),
            schema: Some(Value::Object(tool.input_schema.as_ref().clone())),
            config: None,
        })
        .collect()
}
```

MCP and model APIs describe tools with similar information but different Rust types. `convert_tools` maps each MCP tool’s name, description, and input schema into a `genai` tool definition.

### Sending Model Requests

Add this helper method inside `impl MCPClient`:

```rust
async fn request_model(&self, chat_req: &ChatRequest) -> Result<ChatResponse> {
    let response = self
        .anthropic
        .exec_chat(MODEL_ANTHROPIC, chat_req.clone(), None)
        .await
        .context("Anthropic chat request failed")?;

    Ok(response)
}
```

This keeps model request handling in one place and adds useful context if the API request fails.

### Query Processing Logic

Now add the core query-processing method inside `impl MCPClient`:

```rust
async fn process_query(&mut self, query: &str) -> Result<String> {
    let session = self
        .session
        .as_ref()
        .context("Client is not connected to any server")?;

    let mut messages = vec![ChatMessage::user(query)];
    let mut final_text = Vec::new();

    // Initial Claude API call with tools
    let mut chat_req = ChatRequest::new(messages.clone()).with_tools(self.tools.clone());
    let mut chat_rsp = self.request_model(&chat_req).await?;

    // Process response content - collect text and handle tool calls
    for text in chat_rsp.texts() {
        final_text.push(text.to_string());
    }

    let tool_calls = chat_rsp.tool_calls();
    if !tool_calls.is_empty() {
        // Append assistant's response to message history
        messages.push(ChatMessage::assistant(chat_rsp.content.clone()));

        // Execute each tool call and collect responses
        let mut tool_results = Vec::new();
        for tool_call in tool_calls {
            // Add information about the tool call to final text
            let tool_args_str = serde_json::to_string(&tool_call.fn_arguments)
                .unwrap_or_else(|_| "{}".to_string());

            final_text.push(format!(
                "[Calling tool {} with args {}]",
                tool_call.fn_name, tool_args_str
            ));

            // Query the MCP server
            let tool_result = session
                .call_tool(CallToolRequestParam {
                    name: tool_call.fn_name.clone().into(),
                    arguments: tool_call.fn_arguments.as_object().cloned(),
                })
                .await
                .with_context(|| format!("Tool call {} failed", tool_call.fn_name))?;

            let payload = serde_json::to_string(&tool_result)
                .context("Failed to serialize tool result")?;

            tool_results.push(ContentPart::ToolResponse(ToolResponse::new(
                tool_call.call_id.clone(),
                payload,
            )));
        }

        // Append tool responses to message history
        messages.push(ChatMessage::user(tool_results));

        // Build the next request and query model
        chat_req = ChatRequest::new(messages.clone());
        chat_rsp = self.request_model(&chat_req).await?;

        // Collect text from response
        for text in chat_rsp.texts() {
            final_text.push(text.to_string());
        }
    }

    Ok(final_text.join("\n"))
}
```

The method first sends the user’s query and available tools to Claude. When Claude requests tools, the client executes each request through the MCP session, sends the results back to Claude, and collects the final text response.

### Interactive Chat Interface

Add the interactive terminal loop inside `impl MCPClient`:

```rust
async fn chat_loop(&mut self) -> Result<()> {
    println!("\nMCP Client Started!");
    println!("Type your queries or 'quit' to exit.");

    let mut stdin = BufReader::new(io::stdin());
    let mut input = String::new();

    loop {
        print!("\nQuery: ");
        std::io::Write::flush(&mut std::io::stdout())?;

        input.clear();
        if stdin.read_line(&mut input).await? == 0 {
            break; // EOF
        }

        let query = input.trim();
        if query.eq_ignore_ascii_case("quit") {
            break;
        }
        if query.is_empty() {
            continue;
        }

        match self.process_query(query).await {
            Ok(response) => println!("\n{}", response),
            Err(err) => println!("\nError: {}", err),
        }
    }

    Ok(())
}
```

The loop accepts queries until the user types `quit` or closes standard input. Query errors are printed without terminating the client.

### Cleanup

Add this method inside `impl MCPClient` to stop the MCP session and child process:

```rust
async fn cleanup(&mut self) -> Result<()> {
    if let Some(session) = self.session.take() {
        let _ = session.cancel().await;
    }
    Ok(())
}
```

### Main Entry Point

Finally, add the asynchronous entry point outside the `impl MCPClient` block:

```rust
#[tokio::main]
async fn main() -> Result<()> {
    dotenvy::dotenv().context("Failed to load env file")?;

    let mut args = std::env::args();
    let _ = args.next();
    let server_args: Vec<String> = args.collect();

    if server_args.is_empty() {
        eprintln!("Usage: cargo run -- <server_script_or_binary> [args...]");
        std::process::exit(1);
    }

    let mut client = MCPClient::new()?;

    let result = async {
        client.connect_to_server(&server_args).await?;
        client.chat_loop().await
    }
    .await;

    let cleanup_result = client.cleanup().await;

    result?;
    cleanup_result?;

    Ok(())
}
```

The entry point loads `.env`, treats all remaining command-line arguments as the server command, connects the client, starts the chat loop, and ensures cleanup runs before exiting.

### Verify the Complete File

Before running the client, confirm the items in `src/main.rs` are placed at the correct scope:

- `new`, `connect_to_server`, `process_query`, `request_model`, `chat_loop`, and `cleanup` are methods inside the single `impl MCPClient` block.
- `main` and `convert_tools` are functions outside the `impl MCPClient` block.

Rust does not require these items to appear in a particular order, but methods and free functions must be placed in the correct scope. Compare your file with the [complete `src/main.rs` example](https://github.com/modelcontextprotocol/quickstart-resources/blob/main/mcp-client-rust/src/main.rs), then check that it compiles:

```shellscript
cargo fmt --check
cargo check
```

## Running the Client

Use `cargo run --` followed by the command you would normally use to start the MCP server:

```shellscript
# Python server
cargo run -- python path/to/server.py

# Node.js server
cargo run -- node path/to/build/index.js

# Executable server
cargo run -- path/to/server-binary
```

Running bare `cargo run` without a server command prints the usage message and exits.

If you’re continuing [the weather tutorial from the server quickstart](https://github.com/modelcontextprotocol/quickstart-resources/tree/main/weather-server-rust), build the server first and then run a command similar to: `cargo run -- ../weather-server-rust/target/debug/weather`

The client will:

1. Start and connect to the specified MCP server
2. List the tools available from that server
3. Start an interactive chat session where you can:
	- Enter queries
		- See tool executions
		- Get responses from Claude

## How It Works

When you submit a query:

1. The client sends your query and the server’s available tools to Claude
2. Claude decides which tools, if any, to use
3. The client executes requested tools through the MCP session
4. Tool results are sent back to Claude
5. Claude provides a natural language response
6. The response is displayed in the terminal

## Best Practices

1. **Error Handling**
	- Add context to errors at process, MCP, model API, and serialization boundaries
		- Report individual query errors without terminating the interactive session
		- Validate server commands before running them
2. **Resource Management**
	- Always cancel the MCP session during cleanup
		- Ensure cleanup runs even when connection or chat-loop operations fail
		- Avoid starting a second server while a session is active
3. **Security**
	- Store API keys securely in `.env`
		- Review the tools exposed by a server before allowing model-driven calls
		- Connect only to servers and executable commands you trust

## Troubleshooting

### Server Command Issues

The arguments after `cargo run --` must form a complete command. Interpreted server scripts need their runtime:

```shellscript
# Correct
cargo run -- python ./server/weather.py
cargo run -- node ./server/build/index.js

# Incorrect: a Python script is not necessarily executable by itself
cargo run -- ./server/weather.py
```

If a command cannot be found, use its absolute path or verify it is available in your `PATH`.

### Environment File Issues

If you see `Failed to load env file`, ensure `.env` exists in the directory where you run the client.

If the model request reports a missing API key, confirm that `.env` contains:

```text
ANTHROPIC_API_KEY=your-api-key-goes-here
```

### Tool and Response Errors

- `Unable to list tools from server`: Verify the server starts successfully and communicates over stdio
- `Tool call ... failed`: Verify the server tool’s required arguments and environment variables
- `Failed to serialize tool result`: Inspect the server’s response for unsupported or malformed content

## Next steps

## [Example servers](https://modelcontextprotocol.io/examples)

Check out our gallery of official MCP servers and implementations