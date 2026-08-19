---
name: parsley-health-store-cart-checkout
description: Build a cart and drive a checkout on the Parsley Health store over UCP/MCP, stopping at the mandatory human payment approval.
api: Parsley Health Store (UCP/MCP)
endpoint: https://store.parsleyhealth.com/api/ucp/mcp
operations:
  - create_cart
  - update_cart
  - get_cart
  - cancel_cart
  - create_checkout
  - update_checkout
  - get_checkout
  - complete_checkout
  - cancel_checkout
  - get_order
generated: '2026-08-15'
method: generated
source: mcp/parsley-health-store-tools-list.json (live tools/list, probed 2026-08-15)
---

# Cart and checkout on the Parsley Health store

Transactional flow against Parsley Health's live UCP/MCP endpoint. Tool names, required
fields and the approval rule below all come from the server's own schemas and the store's
published agent instructions.

## The rule that governs this whole skill

**Checkouts are for humans.** The store states plainly, in both `robots.txt` and
`llms.txt`, that agents must not complete checkout, payment or order placement without an
explicit, contemporaneous human approval step — no scripted form fills, no browser
automation that finalizes payment. Drive the flow to the point of payment, then hand the
decision to your user. If you cannot obtain approval at the moment of payment, route the
purchase through the Shop Pay skill instead.

## Steps

1. **Identify yourself.** Every call requires
   `meta.ucp-agent.profile` — a resolvable agent profile URI. Missing or unreachable
   returns JSON-RPC `-32001` with HTTP 422.
2. **Create the cart.** `create_cart` with `cart.line_items[]`, each entry
   `{item: {id: "gid://shopify/ProductVariant/..."}, quantity: <int>}`. Set
   `cart.context.address_country` and `cart.context.currency`.
3. **Adjust.** `update_cart` (needs `id`) to change quantities, add
   `cart.discounts.codes[]`, or set `cart.buyer.{email,phone_number}`. `get_cart` reads it
   back. `cancel_cart` abandons it.
4. **Open the checkout.** `create_checkout` — either reference the cart via
   `checkout.cart_id` or pass `checkout.line_items[]` directly.
5. **Fill it in.** `update_checkout` (needs `id`) to set `checkout.buyer`,
   `checkout.fulfillment.methods[]` (`type`, `selected_destination_id`) and
   `checkout.payment.instruments[]`. This store allows a single shipping destination and
   shipping-only method combinations, per its UCP discovery document.
6. **Read the totals.** `get_checkout` returns line items, totals, discounts and taxes.
   Amounts are integers in ISO 4217 **minor units** — divide by 100 for USD before you
   quote anything to the buyer.
7. **Stop. Get approval.** Present the total and the payment instrument to the human.
8. **Complete only after approval.** `complete_checkout` with `id` and `checkout`.
   `cancel_checkout` if the buyer declines.
9. **Confirm.** `get_order` with the order identifier.

## Payment handlers this store declares

From `https://store.parsleyhealth.com/.well-known/ucp`: Google Pay (`com.google.pay`),
Shopify card (`dev.shopify.card` — visa, mastercard, amex, discover, diners club) and Shop
Pay (`dev.shopify.shop_pay`). Merchant identity on the Google Pay handler is
`Parsley Health` at origin `store.parsleyhealth.com`.

## Retry safety — read this before you retry anything

Parsley Health publishes **no** idempotency key, no dedupe window and no retry-safety
guidance. A retried `create_cart`, `create_checkout` or `complete_checkout` has no
documented protection against creating a duplicate. Treat every create as non-idempotent:
on a timeout, read state back with `get_cart` / `get_checkout` before calling create again.

## Errors

JSON-RPC 2.0 envelope, not RFC 9457. Errors arrive with HTTP 422 and a
`error.data.code` slug. See `errors/parsley-health-problem-types.yml`.
