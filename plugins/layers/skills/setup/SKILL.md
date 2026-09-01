---
name: setup
description: Set up Layers in this repository - install the CLI, get the README sentence right, run `layers setup`, then read the skill index.
disable-model-invocation: true
---

# Set up Layers here

This plugin carries the `layers` MCP entry. The server behind it needs the
Layers CLI on this machine and a project bound to this repository. Walk the
person through both.

## Steps

1. Install the CLI: `npm install -g @layers/cli`. The same binary also comes
   from `brew install layers/tap/layers` and
   `curl -fsSL https://layers.com/install.sh | sh`.

2. Read this repository's README. Setup sends the README's first prose
   paragraph as the product brief, so the README has to open with a plain
   sentence saying what this product is. Setup skips headings, badge rows,
   lists, tables and code fences, and it exits 1 when no paragraph answers.
   Write that sentence, show it to the person, and let them correct it before
   you continue. Layers receives the product name and that one line. It never
   receives source code.

3. Run `layers setup` from the repository root. It opens the browser once for
   sign-in and waits; that browser step is the only one that needs a person.
   Then it selects or creates the project, writes `.layers/project.json`,
   merges the `layers` entry into `.mcp.json`, installs the skill pointer at
   `.agents/skills/layers/SKILL.md`, and starts the first landscape build at no
   charge. Running it again repairs Layers-owned entries, leaves every other
   MCP entry byte for byte as it was, and creates no second project. Every file
   it writes is safe to commit; the session goes to the operating system
   credential store, and the CLI is its only reader.

4. If the Layers tools were unavailable before setup finished, run
   `/reload-plugins` or restart the session so the client reconnects the
   server.

5. Read `layers://skills/v1`, then tell the person what to post first.

## When it stops

Step 2 is the one that fails first in a young repository. A setup that exits 1
says which line it could not find. Write the sentence and run setup again.
