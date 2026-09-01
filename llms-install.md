# Installing Layers MCP

Instructions for an AI agent installing the Layers growth endpoint for a person.
Two commands do the work. One step, the browser sign-in, needs the person.

## What you are installing

Layers is the endpoint a coding agent calls to run growth for the product it is
building: research, personas, video renders, TikTok delivery, paid campaigns,
and measurement. Twenty-seven tools, each priced in credits before it runs.
Layers never receives source code. Site: https://growth.layers.com

## Steps

1. Install the CLI.

   ```
   npm install -g @layers/cli
   ```

   The same binary also comes from `brew install layers/tap/layers` or
   `curl -fsSL https://layers.com/install.sh | sh`.

2. Make sure this repository's README opens with one plain sentence saying what
   the product is. Setup sends that first prose paragraph as the product brief
   and exits 1 when it cannot find one. It skips headings, badges, lists,
   tables and code fences. If the sentence is missing, write it, show it to
   the person, and let them correct it before continuing.

3. Run setup from the repository root.

   ```
   layers setup
   ```

   It opens the browser once for sign-in and waits. Ask the person to complete
   the sign-in, then continue. Setup writes `.layers/project.json`, merges a
   `layers` entry into `.mcp.json`, installs a skill pointer at
   `.agents/skills/layers/SKILL.md`, and starts the first landscape build at
   no charge. The session goes to the operating system credential store;
   nothing secret lands in the repository. Every file it writes is safe to
   commit.

4. Reconnect the MCP client if the Layers tools were not visible before setup
   finished (reload MCP servers or restart the session).

5. Read `layers://skills/v1`, then tell the person what to post first.

## Configuration

The stdio entry setup writes:

```json
{ "mcpServers": { "layers": { "command": "layers", "args": ["mcp"] } } }
```

Remote alternative, if the client cannot spawn a process:

```json
{
  "mcpServers": {
    "layers": {
      "url": "https://mcp.layers.com/mcp",
      "headers": { "Authorization": "Bearer <session from `layers login`>" }
    }
  }
}
```

## Rules for the agent

- Every charged tool takes `quoteOnly`. Read the price before you spend.
- `halt` stops everything in scope on one call. Reach for it first when the
  person says stop.
- A trial starts with 100 credits. On `OUT_OF_CREDITS`, give the person the
  top-up link from the message as a markdown link.
- `send_feedback` is free and goes to the founders. Use it when something is
  wrong.

## Verify

Call `read_layers_state` with `{"family":"account"}` (or read `layers://account`).
A balance and a plan name mean the install worked.
