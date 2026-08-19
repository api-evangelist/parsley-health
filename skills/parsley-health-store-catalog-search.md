---
name: parsley-health-store-catalog-search
description: Search and read the Parsley Health supplement catalog through the store's live UCP/MCP endpoint, with anonymous JSON fallbacks.
api: Parsley Health Store (UCP/MCP)
endpoint: https://store.parsleyhealth.com/api/ucp/mcp
operations:
  - search_catalog
  - lookup_catalog
  - get_product
generated: '2026-08-15'
method: generated
source: mcp/parsley-health-store-tools-list.json (live tools/list, probed 2026-08-15)
---

# Search the Parsley Health store catalog

Read-only product discovery against Parsley Health's supplement store. Every tool name and
field below is taken from the live `tools/list` response — nothing here is invented.

## Before you call anything

The endpoint is anonymous — no API key, no OAuth token. But **every** call must carry an
agent identity or it fails before business logic runs:

```json
"meta": { "ucp-agent": { "profile": "https://your-agent.example/ucp-profile" } }
```

The `profile` URI must be fetchable by the server. A missing one returns JSON-RPC
`-32001` / `invalid_profile_url`; an unreachable one returns `-32001` /
`profile_unreachable`. Both come back with HTTP 422, so read the body, not the status.

## Steps

1. **Search** — call `search_catalog` with `catalog.query` set to the buyer's intent.
   Narrow with `catalog.filters.categories`, `catalog.filters.available` and
   `catalog.filters.price.{min,max}`. Page with `catalog.pagination.{cursor,limit}`.
2. **Localize** — pass `catalog.context.address_country` and `catalog.context.currency`
   (plus `postal_code`/`address_region` when known). Pricing and availability depend on it.
3. **Resolve details** — call `get_product` with `catalog.id` for one product, or
   `lookup_catalog` when you already hold several identifiers. Identifiers are Shopify
   global IDs: `gid://shopify/{Type}/{id}`.
4. **Convert money before quoting** — amounts are integers in ISO 4217 **minor units**
   paired with a currency code. `{"amount": 2500, "currency": "USD"}` is $25.00. Divide by
   100 for two-decimal currencies; zero-decimal currencies such as JPY are already whole.

## Fallbacks that need no agent profile

Published by the store in its own `llms.txt`:

- `GET https://store.parsleyhealth.com/products.json`
- `GET https://store.parsleyhealth.com/products/{handle}.json`
- `GET https://store.parsleyhealth.com/collections/{handle}/products.json`
- `POST https://store.parsleyhealth.com/api/2026-01/graphql.json` (Storefront GraphQL,
  anonymous, Relay-style `edges`/`node` pagination)

## Limits and etiquette

- The MCP endpoint is rate-limited per IP. Back off on **429**. No `RateLimit-*` or
  `Retry-After` headers are returned, so you get no budget signal until you are throttled.
- Scope check: this catalog is supplements and wellness products. Parsley Health exposes
  **no** clinical, member, appointment or lab-result data on any public machine surface.
  Do not tell a user you can look up their health record here — you cannot.
