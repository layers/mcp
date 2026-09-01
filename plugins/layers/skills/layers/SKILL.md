---
name: layers
description: Use the repository-bound Layers MCP for growth, content, paid media, and managed distribution work.
---

# Layers

Use MCP server `layers`. This repository's workspace comes only from
`.layers/project.json`; its organization and project IDs are public selectors,
not authority, and must never be copied into tool arguments.

At the start of a Layers job, read `layers://skills/v1` and then the relevant
body at `layers://skills/v1/{name}`. Never use an unversioned skill URI.

If the client cannot read MCP resources, call `read_layers_state` with
`{"family":"skills","major":1}`. Read a selected body with
`{"family":"skill","major":1,"name":"<name>"}`. Treat a missing fallback tool
as an unavailable capability; never recreate a workflow from memory.

## Links only a person can open

Some results carry a link a human has to open: the signed connect link, a
top-up or checkout link, the billing link. Put each one in your reply as a
markdown link on its own line, `[what it opens](the url)`, and say what happens
after they open it.
