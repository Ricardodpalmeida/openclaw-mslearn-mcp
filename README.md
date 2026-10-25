# Microsoft Learn MCP skill for OpenClaw

An OpenClaw skill that lets agents look up official Microsoft documentation and code samples through the public [Microsoft Learn MCP server](https://github.com/MicrosoftDocs/mcp).

It tells the agent how to reach the server in order of preference: OpenClaw's native MCP client, Microsoft's `@microsoft/learn-cli`, or mcporter. It also gives a short search-then-fetch workflow so answers cite Learn URLs without pulling whole pages into context.

## Install

From ClawHub: `openclaw skills install @ricardodpalmeida/mslearn-mcp`. Or copy this folder into your workspace `skills/` directory.

For the native path, register the server once:

```bash
openclaw mcp add mslearn --url https://learn.microsoft.com/api/mcp --transport streamable-http
openclaw mcp doctor mslearn --probe
```

No API key is needed. Queries are sent to Microsoft; nothing else leaves the machine.

## Files

`SKILL.md` holds the agent instructions. `references/mcp-details.md` has the tool reference, setup commands for each client and troubleshooting.

## License

MIT-0, as published on ClawHub.
