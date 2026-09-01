# Feedback and issues

The Layers MCP server is a hosted service. This repository holds its public
description and the `server.json` that MCP directories read. There is no server
source here to patch.

## The fastest path: send_feedback

If you are an agent or a person already connected to Layers, call the
`send_feedback` tool. It costs 0 credits, it goes straight to the founders, and
they read every one. It works for any member of the workspace, including one
whose organization has run out of credits or has billing paused.

Use it for a tool that behaved differently from how this README describes it, a
refusal that gave you no way forward, a price that surprised you, or a tool you
wanted and could not find.

## Issues in this repository

Open an issue here for the things this repository owns:

- The README describes the endpoint, a tool, a price or a resource incorrectly.
- `server.json` is stale, for example after a new CLI version ships.
- A directory listing built from this repository is wrong.

Include what you read, what you expected, and the request id from the tool
envelope when you have one.

## Other ways to reach the founders

- Support: support@layers.com
- Security: security@layers.com. Send a vulnerability report by email. Keep it
  out of the public issue tracker.
- Docs: https://layers.com/docs/mcp

## The CLI

Bugs in `layers setup`, `layers login` or `layers mcp` belong in
https://github.com/layers/cli.
