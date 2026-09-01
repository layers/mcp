# Layers MCP

Layers MCP is the growth endpoint a coding agent calls to run growth for a product: onboarding, the competitive map, what to post, the plan, the persona, the render, delivery, the review, paid campaigns, Layers-operated accounts, and halt on one call.

Twenty-seven tools and fourteen resource families. Every tool carries its price in credits, and credits come out of the organization's balance when the tool runs.

This repository holds the public description of that endpoint and its `server.json` for MCP directories. The server itself is a hosted service at `https://mcp.layers.com/mcp`.

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

Step 2 is the one that fails first in a young repository. Setup reads the product name from the package manifest, the README's own title or the directory, and it reads the one-line description from the README's first prose paragraph, skipping headings, badge rows, lists, tables and code fences. When no paragraph answers, setup exits 1 and says so. Write that sentence and run setup again. Layers receives those two lines and nothing else.

`layers setup` resolves the repository root, authenticates with OAuth 2.1 and PKCE, stores the session in the operating system credential store, selects or creates the project, derives the app brief, writes `.layers/project.json` and the `.mcp.json` entry, installs the skill pointer, and starts the first landscape build at no charge. It is idempotent: running it again repairs Layers-owned entries, leaves every other MCP server entry byte for byte as it was, and creates no second project.

Files it writes, all safe to commit:

| Path | What it holds |
| --- | --- |
| `.layers/project.json` | public organization id, public project id, environment, issuer, setup version |
| `.mcp.json` | `{ "command": "layers", "args": ["mcp"] }` merged under server name `layers` |
| `.agents/skills/layers/SKILL.md` | thin pointer to the server-owned skill index |
| `.claude/skills/layers` | relative symlink to the canonical skill |

## Connect

- **Endpoint:** `https://mcp.layers.com/mcp`
- **Transport:** Streamable HTTP. POST one JSON-RPC message per request. The response is `application/json` or `text/event-stream`. `Mcp-Session-Id` rides both directions.
- **Auth:** `Authorization: Bearer <the session layers login issues>`
- **Health:** `https://mcp.layers.com/health`

The preferred client configuration is `layers mcp` as the stdio server. Your client spawns it and it forwards every JSON-RPC message with the keyring session. It is the credential's only reader.

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

## The envelope

Every tool returns one envelope: the result, the cost with credits spent and balance remaining, the freshness of the platform data behind it, the next tools worth calling with an estimated cost each, and a request id worth quoting to support. An error envelope carries a public code, a message, a recovery action and whether it is retryable.

Every tool takes an optional `idempotencyKey`. Send your own and a retry replays the first call, or omit it and Layers generates one for this call. A read-only arm that mutates nothing, such as a status read or a preview, is a strict object that takes no key at all.

Every charged tool takes `quoteOnly`, which returns the exact maximum credits, the price version and a fifteen minute expiry. It calls no provider and runs no model. Read the price before you spend.

## Tools

### Growth

| Tool | Credits | What it does |
| --- | --- | --- |
| `onboard_product` | 0 | Binds the product, starts the social and measurement connections, and runs the first landscape build free. |
| `manage_growth_landscape` | 0 curate, 6 refresh, 6 full rebuild | Pins, removes and rescopes tracked accounts. |
| `find_growth_opportunities` | 0 | The ranked queue of tests, each with its source evidence. |
| `plan_content_experiment` | 6 new or structural, 0 creative edit | One opportunity, a source link or a manual hypothesis into an editable plan, aimed at a social account this project holds. |
| `generate_influencer` | 0 | One persona from a gender, an age range and an optional one-line look. The `inf_` id comes back immediately at status generating and the portrait is drawn in the background. |
| `render_content` | 200 per 10-second video, 400 per 20-second video, 85 per slideshow, each per usable variant, and 25 per usable output image | Four recipes: `reaction_demo`, `slideshow_builder`, `static_ad`, `adapt_source`. An unusable output is refunded. The finished job names `layers://artifacts/<id>`. |
| `deliver_content_experiment` | 0 prepare, 5 per target | A rights-cleared artifact to a draft inbox or a direct post. TikTok is the built platform. |
| `track_growth_experiment` | 0 | Registers a post the human published. |
| `review_growth_results` | 0 stored, 1 forced provider read | Evidence, verdict, next test. |
| `halt` | 0 | Stops queued and running work and pauses active spend in the scope you name: one running job, one experiment, one social account, one paid campaign, one managed account, or the whole project. On a paid campaign it pauses delivery at the provider. |
| `cancel_trial_auto_renewal` | 0 | Previews the trial end, then confirms and returns the receipt. |
| `send_feedback` | 0 | A message to the founders, who read every one. It works for any member of the workspace, including one whose organization has run out of credits or has billing paused. |
| `add_brand_asset` | 0 | Stores one of your own product assets from a public https URL as kind `app_screenshot`, `app_demo` or `end_card`, and returns the `bas_` id. This is what fills an empty library. |
| `install_growth_measurement` | 0 | Action `spec` returns the exact dependency, `LAYERS_APP_ID`, environment variable and init snippet for this product's platform, so your agent installs the Layers SDK in your own repository. No bot opens a pull request. Action `verify` reads real events back and answers `reporting` or `no_events_yet`. |
| `generate_tracking_link` | 0 | Mints `https://in.layers.com/c/<app id>` with your campaign, source, medium, content and term on it. It records the click, the store tap and the ad network's own click id, and on Android writes them into the Play install referrer. |

### Research

Each one is a live read at the moment of the call. One credit is charged on the call whatever the count, and nothing is charged when the read finds nothing. Every one takes `quoteOnly` to price it first, and an optional `idempotencyKey` so a retry replays the first call.

| Tool | Credits | What it does |
| --- | --- | --- |
| `research_tiktok_creator` | 1 | One creator by handle: followers, following, post count, bio, bio link, verification, private or not, avatar, profile URL and the account creation date. The follower and following counts are the unrounded ones. A handle nobody holds answers creator null, free. |
| `research_tiktok_creator_posts` | 1 | One creator's recent posts, newest first, up to 20 a call, with caption, post URL, format, length, views, likes, comments, shares, saves, cover, sound and post id. `nextCursor` pages forward. Every post id is what `render_content`'s `adapt_source` recipe takes as `sourcePost`. |
| `research_tiktok_search` | 1 | Public posts for a topic, up to 20 a call, with the same numbers plus the handle, display name and follower count behind each one. `sortBy` relevance or likes; `postedWithinDays` 1, 7, 30, 90 or 180. A query that matches nothing is free. |
| `research_tiktok_hashtag` | 1 | One tag's post count, view count and average views per post, plus up to 20 recent posts. `momentum` compares the posts in that answer against the tag's own average and reads above 1 when the tag is running hot. A tag nobody has posted under is free. |
| `research_tiktok_trending_sounds` | 1 | The live TikTok sound chart, up to 20 tracks: title, artist, length, how many posts carry each one, whether anybody sings on it, a preview URL and cover art. `renderableTrackId` is the `trk_` id when the track is already in the curated catalog and null while it is not. |

### Managed distribution

Layers-operated TikTok accounts, billed in dollars from the organization's cash wallet.

| Tool | Credits | What it does |
| --- | --- | --- |
| `plan_managed_distribution` | 0 | Quotes 1 to 20 accounts at $150 per account per month against the cash wallet, with every unmet precondition and its fix. Reserves nothing. |
| `provision_approved_managed_accounts` | 0 | Orders the exact fleet one plan quoted and reserves the fee from the cash wallet in one transaction. A wallet spent since the quote is refused with the shortfall and a top-up link, and nothing is reserved. |
| `release_managed_accounts` | 0 | Previews what release cancels, costs and returns, then confirms. Published posts stay published and measurement stays readable. |

### Paid media

On for every bound workspace. TikTok is the built provider.

| Tool | Credits | What it does |
| --- | --- | --- |
| `plan_paid_campaign` | 0 | A complete plan with caps, schedule and closed stop conditions. No mutation. |
| `create_approved_campaign_draft` | 10 per campaign | Provider objects, created paused. |
| `launch_approved_campaign` | 0 | Starts one campaign. Ad spend is billed by TikTok against the human's own ad account and never consumes credits. |
| `apply_approved_campaign_change` | 5 per campaign | Previews, then applies one bounded change: the daily total, or deleting the campaign at the provider. |

## Resources

Fourteen families, all under `layers://`. Resources are free, read stored state, and each reports when its data was observed and which tool refreshes it. A client without resource support gets `read_layers_state` instead.

| Resource | What it holds |
| --- | --- |
| `layers://account` | Identity, plan or trial, credit balances, reserved credits, the packs on sale to this organization, active offers, the human billing link. It also answers `layers://account/credit-events`, every balance change, paged. |
| `layers://workspace` | Product brief, growth goal, connected accounts, measurement readiness, paid readiness, managed readiness, posting policy. |
| `layers://landscape` | Ranked tracked accounts, pins, exclusions, scope, freshness, source evidence. |
| `layers://opportunities` | The standing ranked queue with full source evidence. |
| `layers://experiments` | Summaries, the current projection, and the append-only ledger. |
| `layers://artifacts/<cnt_ id>` | One finished render: its state, whether it is video, slideshow or image, the CDN addresses, the measured bytes with their dimensions and duration, the content hash a delivery binds, and the job and experiment behind it. Detail only. |
| `layers://influencers` | The project's personas, each with the look it was generated from, its portrait, whether it is still being drawn, and `slateUrl`. |
| `layers://music` | The curated tracks a reaction render can be scored with, newest first. The catalog is curated by hand, so a track that is not listed is not selectable. |
| `layers://brand-assets` | The project's own product media: app screenshots, demo videos and end cards, each with a stable `bas_` id, what it is called, its type and a URL. |
| `layers://jobs` | Active and recent jobs with state, progress, receipts, errors, recovery, and what a finished one produced. |
| `layers://campaigns` | Paid campaigns by `pcamp_` id. |
| `layers://managed-accounts` | Layers-operated accounts: what was ordered, how far each build has travelled, whether it is posting, whether next month is funded. |
| `layers://measurement` | Measurement state for the product. |
| `layers://skills/v1` | The skill index and each skill body. |

## Skills the server owns

Read `layers://skills/v1` for the index and each body. The skills carry the judgment about when to use which tool.

| Skill | What it covers |
| --- | --- |
| `niche-map` | Build and curate the standing map of direct, adjacent and inspirational accounts. |
| `what-to-post` | One opportunity through plan, render and delivery. |
| `growth-review` | Read early or final evidence, state the uncertainty, pick the next test. |
| `outcome-log` | Register work the human posted themselves and keep the decision with it. |
| `paid-growth` | Promote a confirmed winner under exact spend caps. |
| `managed-distribution` | Plan, monitor and release a disclosed managed fleet. |
| `refusals` | Read a refused call correctly: the code, where it travels, and what it asks for. |

## What works on a fresh install

All twenty-seven tools and all fourteen resource families are admitted the moment `layers setup` binds a project. There is no second install, no per-feature switch, and nothing anybody at Layers turns on for you. `tools/list` in your own session is the authority, because it is the thing that answers. Before a project is bound you are in bootstrap mode, where `onboard_product` is the one admitted tool and no resource is readable.

What differs is what each tool has to work with on day one.

**Runs immediately, with nothing connected:** `onboard_product`, `manage_growth_landscape`, `generate_influencer`, `render_content`, `add_brand_asset`, `send_feedback`, `install_growth_measurement`, `generate_tracking_link`, `plan_paid_campaign`, `plan_managed_distribution`, and `halt` on the whole project. Every resource is readable.

A `slideshow_builder` render works from a brief with nothing else in the project: the persona and the screens each have a default, and the deck lays no audio, so music has no default on it and a stated one is refused. A `reaction_demo` needs one asset of yours for the product half; `render_content` refuses the call before any charge, on the quote as well as the render, when the project holds no demo video and no end card, and the refusal says to call `add_brand_asset` with a public URL and kind `app_demo` for a demo video or `end_card` for a still, or to send `appDemo {"kind":"none"}` to render the reaction alone. An `adapt_source` remix of a public TikTok post needs nothing of yours.

**Needs a connected social account:** `plan_content_experiment`, `deliver_content_experiment` and `track_growth_experiment` all name one as the target. `find_growth_opportunities` returns an empty queue with `connect_social_account` as the next step until one exists, and `layers://landscape` is empty until `manage_growth_landscape` sets a scope.

**Needs a connected ad account:** `create_approved_campaign_draft` builds only from a ready plan. `launch_approved_campaign` and `apply_approved_campaign_change` need a campaign that draft created.

**Needs the cash wallet funded:** `provision_approved_managed_accounts`.

**Needs prior work:** `review_growth_results` needs an experiment. `halt` on a narrower scope needs that job, experiment, account or campaign. `cancel_trial_auto_renewal` needs an auto-renewing trial and an organization administrator to call it.

The balance gates charged calls on its own, whatever is connected. A trial starts with 100 credits, which covers a slideshow at 85 and falls short of a video at 200. Pay as you go starts at zero, so the first charged call comes after the first pack. Read `layers://account` for the balance and send `quoteOnly` to price a call before it runs.

## The connect link

Start on an unconnected workspace with the two free planners.

`plan_content_experiment` does not wait for a social account. Leave `targetSocialAccountId` out and it plans and renders with no account at all, and its `deliveryReadiness` carries the connect link. Name an account this project does not hold and it refuses with `NOT_FOUND`, "This project holds no such social account to post to."

`deliver_content_experiment` names an account on every call. It answers `NOT_FOUND` for a target that does not exist, and it reports a disconnected or revoked one as an `account_not_connected` blocker rather than an error. An experiment planned before the connect is delivered by naming the account connected since.

`plan_paid_campaign` answers `needs_action` with `connect_ad_account` named. `plan_managed_distribution` answers `blocked` with the wallet shortfall and a signed top-up link. Neither is an error and neither charges.

## Credits and plans

One balance per organization. The same operation costs the same on every plan. A charged call debits that balance as it runs, and auto-reload tops the balance up so a run does not stop part-way. The balance, the trigger level and the pack auto-reload buys are managed by the human at `https://app.layers.com`.

| Plan | Price | Included credits |
| --- | --- | --- |
| Pay as you go | $0 per month, card required | none; the entry rung |
| Trial | 3 days, $0 today, card required | 100 credits, first landscape build included. Converts to Pro monthly at $49 unless cancelled first. |
| Pro monthly | $49 per month | 5,000 credits per month |
| Pro annual | $468 per year, $39 per month | 5,000 credits each month |
| Ultra monthly | $199 per month | 25,000 credits per month |
| Ultra annual | $1,908 per year, $159 per month | 25,000 credits each month |

Credit packs add to the same balance and do not expire. An organization buys from the shelf its plan puts it on, and it names a size rather than a shelf.

| Pack | Pay as you go | On a paid plan |
| --- | --- | --- |
| 500 credits | $10 | $5 |
| 1,000 credits | $20 | $9 |
| 2,000 credits | $40 | $17 |
| 4,000 credits | $80 | $29 |

Pay as you go prices credits at two cents each. Ad spend and the $150 managed-account fee are separate money and never consume credits.

When a charge cannot be covered, the refusal is `OUT_OF_CREDITS` and its message states how many credits short the balance is, links the checkout for the pack sized to cover that shortfall, and points at `layers://account` when a larger pack exists. Read the message rather than retrying the charged call.

## Rules an agent follows

1. Every charged tool takes `quoteOnly`, which returns the exact maximum credits, calls no provider and runs no model. Read the price before you spend.
2. Credits leave the organization's balance when a tool runs. Auto-reload is managed at `https://app.layers.com`.
3. `halt` stops everything in scope on one call. Reach for it first when the human says stop.
4. On `OUT_OF_CREDITS`, read the message, then `layers://account` for the rest of the shelf.
5. On `ENTITLEMENT_REQUIRED`, read `layers://account` and give the human the billing link from it.
6. Read `layers://skills/v1` for the skills and the judgment they carry.
7. `tools/list` is the authority on what you can call.
8. A request carries brief fields and a digest. Layers never receives source code.
9. Never claim a post is public until the platform post id and URL come back.
10. Tell the founders when something is wrong. `send_feedback` is free and it is read.

## Retired

The npm package `@layers/mcp-server`, including its keyless onboarding mode, and the MCP protocol that used to be served at `growth.layers.com` were both retired on 2026-08-30. There is no bridge and no compatibility shim. `growth.layers.com` now serves the landing site; the MCP endpoint is `https://mcp.layers.com/mcp`.

## Links

- Site: https://growth.layers.com
- Machine-readable: https://growth.layers.com/llms.txt and https://growth.layers.com/llms-full.txt
- Docs: https://layers.com/docs/mcp
- App: https://app.layers.com
- CLI: https://github.com/layers/cli and https://www.npmjs.com/package/@layers/cli
- Status: https://mcp.layers.com/health
- Support: support@layers.com
- Security: security@layers.com

## License

The contents of this repository are MIT licensed. See [LICENSE](LICENSE). The Layers MCP server is a hosted service, and its source is not in this repository.
