# Layers Marketing MCP

Give your agent marketing superpowers. Layers Marketing MCP is the endpoint your coding agent calls to run growth for your product: onboarding, the competitive map, what to post, the plan, the persona, the render, delivery, the review, paid campaigns, Layers-operated accounts, and halt on one call. Every tool carries its price in credits, and credits come out of your organization's balance when the tool runs.

This repository holds the public description of that endpoint and the `server.json` that MCP directories read. The server is a hosted service.

## Connect

- **Endpoint:** `https://mcp.layers.com/mcp`
- **Transport:** Streamable HTTP. POST one JSON-RPC message per request. The response is `application/json` or `text/event-stream`, and `Mcp-Session-Id` rides both directions.
- **Auth:** `Authorization: Bearer <the session layers login issues>`
- **Health:** `https://mcp.layers.com/health`

The configuration to prefer is `layers mcp` as a stdio server. Your client spawns it, it forwards every JSON-RPC message with the session from your OS keyring, and it is the only thing that reads that credential.

```json
{
  "mcpServers": {
    "layers": {
      "command": "layers",
      "args": ["mcp"]
    }
  }
}
```

Install the CLI with `npm install -g @layers/cli`, `brew install layers/tap/layers`, or `curl -fsSL https://layers.com/install.sh | sh`. Source: [layers/cli](https://github.com/layers/cli).

## Set up

Paste this into Claude Code, Codex, Cursor, or any coding agent working in your repository. The agent runs both commands. The browser sign-in is the only step that needs a person.

```text
Set up Layers Growth for me so you can plan, render and deliver content for this product from here.
1. Install the CLI: run `npm install -g @layers/cli`.
2. Check that the README opens with a plain sentence saying what this product is, and write one if it does not. Setup sends that single line as the product brief, and it stops with exit code 1 when it cannot find one.
3. Set it up: run `layers setup` from this repository. It opens my browser once for sign-in and waits for me; it writes the `.mcp.json` entry and the Layers skill into the repo and stores the session in my OS credential store, so nothing secret lands in the repo.
4. Read the skill at `.agents/skills/layers/SKILL.md`, then tell me what to post first.
Let me know when it is ready.
```

## Install as a Claude Code plugin

```text
/plugin marketplace add layers/mcp
/plugin install layers@layers
```

The plugin carries the `layers` MCP entry, the Layers skill, and three commands. `/layers:setup` walks the paste above. `/layers:post` takes one opportunity through plan, render and delivery as a draft, quoting the cost before every charged call. `/layers:status` reads `layers://account`, `layers://workspace` and `layers://jobs` and summarizes them.

The plugin configures the client. The CLI holds the session and `.layers/project.json` binds the project, so install `@layers/cli` and run `layers setup` in the repository as well. In a repository that has both, Claude Code lists the plugin's entry as `plugin:layers:layers` and the one `layers setup` wrote as `layers`. They reach the same server, and one of them is enough. Codex and Cursor read the same `.mcp.json` entry and the same skill that `layers setup` writes, so there is no separate plugin for them.

## How it costs

One credit balance per organization, and the same operation costs the same on every plan. Credits are bought in packs from one of two shelves: pay as you go is $0 a month and prices credits at two cents each, and a paid plan buys the same pack sizes cheaper. Packs add to the same balance and do not expire. Every charged tool takes `quoteOnly`, which returns the exact maximum credits before anything runs, so your agent can read the price before it spends. Plenty of the catalog charges nothing at all: reading any `layers://` resource, reading the standing opportunity queue, reading stored experiment results, planning a paid campaign, quoting a managed fleet, storing one of your own brand assets, creating a persona, minting a tracking link, installing measurement, and halt. Ad spend is billed by the ad platform against your own ad account, and a Layers-operated account is $150 per account per month from your cash wallet; neither one consumes credits.

## What your agent can do

Bind a product from the repository it is already in and start its first landscape build; keep the map of accounts it is measured against; read TikTok live for creators, their posts, a topic, a hashtag or the sound chart; turn one opportunity into an editable plan; create a persona and render a reaction reel, a slideshow, a still ad or a remix of a public post; deliver an artifact to a platform draft inbox or post it directly; register a post you published yourself and score it against that account's own normal; install the Layers SDK in your own repository and mint the tracking link that resolves an install back to a click; plan, draft, launch and adjust paid campaigns under exact caps and stop conditions; order and release Layers-operated TikTok accounts; and stop everything in scope on one call.

The live tool list is `tools/list` on the endpoint. The full generated inventory with prices is <https://growth.layers.com/llms-full.txt>.

## Feedback

`send_feedback` sends a message to the founders, who read every one. A bug, a price you think is wrong, a capability you looked for and did not find, or something that worked. It is free, and it works for any member of the workspace, including one whose organization has run out of credits.

## Links

- Site: <https://growth.layers.com>
- Machine-readable: <https://growth.layers.com/llms.txt> and <https://growth.layers.com/llms-full.txt>
- Docs: <https://layers.com/docs/mcp>
- App: <https://app.layers.com>
- CLI: <https://github.com/layers/cli>
- Support: support@layers.com
- Security: security@layers.com

## License

The contents of this repository are MIT licensed. See [LICENSE](LICENSE). The Layers MCP server is a hosted service, and its source is not in this repository.
