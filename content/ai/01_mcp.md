---
title: "MCP"
weight: 1
---
# Model Context Protocol
Generally, I try to learn how something works before using it.
However, in the case of MCP, I used it and enjoyed the benefits of it before I thought that I should start learning about it.

Specifically, I have used the GitHub MCP and the Atlassian Confluence one, which has been great for reviewing code and updating docs.

This page will document my learnings about MCP.

## Why?
MCP standardizes the way that AI systems connect models with the tools that are required to perform tasks. Something like how LSP servers made it easier for multiple IDEs to get deeper insights into multiple programming languages.

If it's an API based tool e.g., GitHub PR API, why wouldn't you let the AI agent call the API directly?
That wouldn't be efficient, given the lack of context. Maybe the current API themselves weren't made with AI agents in mind
and return responses in an inefficient manner. An MCP server could handle responses better.

## What is it made of?
I'm going to talk about an MCP server first. It's made of : 
1. Tools. Which can be used by clients to perform actions on the server. Think of these as function calls.
2. Resources. Like documents on the server, containing domain specific information, which can be used as context for the client.
3. Prompt template. Gives prompts to the client to effectively use the MCP server. The client can fill in parts of the tempalte or modify if required.

## How does it work?
There is a lifecyle in the communication between an MCP server and client.
1. Initialization
2. Message Exchange (through Streamable HTTP. Which will use normal HTTP REST APIs. Stateless connections. but can upgrade to SSE for push)
3. Termination

The communication could also be done through stdio for local MCP servers, which can be started as subprocesses by the client.

## Build an MCP Server
FastMCP is the most popular choice for python based MCP servers.
It takes care of the protocol part of the process and you can focus on the application itself.
The docs are good <https://github.com/PrefectHQ/fastmcp/blob/main/docs/servers/server.mdx>.

## Read More
1. https://modelcontextprotocol.io/docs/learn/architecture
2. https://modelcontextprotocol.io/docs/develop/build-server
3. https://apps.extensions.modelcontextprotocol.io/api/ 
4. https://modelcontextprotocol.io/docs/develop/build-client
5. https://agentskills.io/home