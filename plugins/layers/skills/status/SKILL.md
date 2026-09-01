---
name: status
description: Read the Layers account, workspace and jobs resources and summarize the balance, what is connected, and what is running.
disable-model-invocation: true
---

# Layers status

Three free reads. Nothing here charges.

1. `layers://account` - plan or trial, credit balance, reserved credits, the
   packs on sale to this organization, active offers, the human billing link.
2. `layers://workspace` - the product brief, the growth goal, connected social
   accounts, measurement readiness, paid readiness, managed readiness, the
   posting policy.
3. `layers://jobs` - active and recent jobs with state, progress, receipts,
   errors, recovery, and what a finished one produced.

If the client cannot read MCP resources, call `read_layers_state` with
`{"family":"account"}`, `{"family":"workspace"}` and `{"family":"jobs"}`.

## What to say

In this order: the balance and the reserved credits; what is connected and what
is still missing; anything running or recently finished, with its state.

Each resource reports when its data was observed and which tool refreshes it.
Say when a number is old and name the tool that refreshes it.

Give the billing link, and any connect link the workspace carries, as markdown
links.
