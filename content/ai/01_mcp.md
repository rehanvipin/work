---
title: "MCP"
weight: 1
---
# Model Context Protocol
Generally, I try to learn how something works before using it.
However, in the case of MCP, I benefited a lot using it, and felt I should understand it better.

Specifically, I have used the GitHub MCP and the Atlassian Confluence one, which has been great for reviewing code and updating docs.

This page will document my learnings about MCP.

## Why?
MCP standardizes the way that AI systems connect models with the tools that are required to perform tasks. Something like how LSP servers made it easier for multiple IDEs to get deeper insights into multiple programming languages.

If it's an API based tool e.g., GitHub PR API, why wouldn't you let the AI agent call the API directly?
That wouldn't be efficient, given the lack of context. Maybe the current API themselves weren't made with AI agents in mind
and return responses in an inefficient manner. An MCP server could handle responses better.

## What is it made of?
I'm going to talk about an MCP server first. It's has a data layer and a transport layer.
The transport could be through stdio / streamable http.
The data layer defines the lifecycle management (MCP is stateful after all), some primitives, and notifications.

The data layer supports: 
1. Tools. Which can be used by clients to perform actions on the server. Think of these as function calls.
2. Resources. Like documents on the server, containing domain specific information, made available without tool calls.
3. Prompt template. Gives prompts to the client to effectively use the MCP server. 

Will cover these in more detail below.

There's also an MCP client which talks to the server, and an MCP host which can have one or more clients. Users use the host.

## How does it work?
There is a lifecyle in the communication between an MCP server and client.
1. Initialization
2. Message Exchange (through Streamable HTTP. Which will use normal HTTP REST APIs. Stateless connections. but can upgrade to SSE for push)
3. Termination

The communication could also be done through stdio for local MCP servers, which can be started as subprocesses by the client.

Note that the model doesn't directly interact with the MCP servers. That is the responsibility of the MCP client (e.g., VS Code Copilot, Claude Code etc.)
The MCP client presents a list of servers and their tools to the model. The model can choose what tool it needs for a task and ask the client to call it.
The model will also present the arguments that it needs to be passed, and the client takes the responsibility of communicating them to the server.

Models do have an idea of tool call vs normal prompt, so the mcp client needs to see whether the model output is asking for a tool call or whether it is a text output to be displayed to the user.

A resource is a document that the mcp server can share with the mcp client to use pass additional context to the model. E.g., features of the mcp server.
It is possible fetch additional specific resources as well based on parameters.

Prompt templates are specially crafted prompts to interact with the mcp server. This saves the user the burden of prompt engineering. These prompts can also take arguments.

## Build an MCP Server
FastMCP is the most popular choice for python based MCP servers.
It takes care of the protocol part of the process and you can focus on the application itself.
The docs are good <https://github.com/PrefectHQ/fastmcp/blob/main/docs/servers/server.mdx>.

## Read More
MCP Apps are really cool! They enable dynamic UIs on the client. Read the [docs](https://apps.extensions.modelcontextprotocol.io/api/).

Docs for the python SDK are here https://py.sdk.modelcontextprotocol.io/#api-reference

## Experiments to try out
There are client-side primitives as well, which can be enabled by MCP clients.
Like how clients use the primitives from the server, the server can use these to provide better services.
* Sampling - the client enables access to the AI model which it's talking to.
* Elicitation - get more information about the users.

It is also possible for the server to notify clients of changes in the tools & resources.

Make an MCP server which uses sampling for its own shady purposes.

## Skills?
There have been speculations that agent skills reduce the need for MCP servers. Maybe, but there is definitely a use case for MCP servers. Tool calls and prompt templates 

A skill is primarily an MD file plus other docs and scripts which tell the agent how to execute a very specific task. A skill is activated based on its description. These must be placed within a directory in the .agents/skills folder.