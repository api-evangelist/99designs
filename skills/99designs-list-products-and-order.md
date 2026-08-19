---
name: List partner products and order the right one
description: Read the products a 99designs partner has available for sale, pick one, and place an order against that exact product version.
api: openapi/99designs-products-api-openapi.yml
operations: [listProducts, createOrder]
---

# List partner products and order the right one

The Orders API will only accept a `productId` that names a product **version** the calling
partner actually has available. Always read `listProducts` first rather than hard-coding a
product id — product versions change and are partner-specific.

## Auth
Send both `Api-Key-Id` and `Api-Key-Secret` headers on every request. Base URL is
`https://api.99designs.com/resources/v1`. A missing or invalid pair returns `401`
`{"message":"Unauthorized"}`.

## Steps
1. `listProducts` (GET `/products`) — returns `products[]`, each with `id` (a name-and-version
   slug such as `website-revamp-v1`), `title`, `description`, `price` (in cents), and the
   `details` / `additionalDetails` objects that carry the customer-facing caveats
   ("Up to 5 pages", "Excludes hosting"). The docs state each product also carries a JSON
   Schema representation of the brief a client fills in to purchase it.
2. Choose the product whose `id`, `price` and `details` match what the customer agreed to.
   Surface `price` as currency by dividing by 100 — it is cents.
3. `createOrder` (POST `/orders`) — send `productId` set to that exact `id`, plus a nested
   `brief` object (`title`, `description`, `category`, `timeline`, `approval`,
   `businessName`) whose fields must validate against that product version's schema.
4. On `201`, read `orderItems[].briefId` and `orderItems[].returnUrls` and redirect the
   customer to `returnUrls.briefStart` — the order is not complete until the end user
   finishes the brief on 99designs (they must create or log into a 99designs account).

## Rules
- Never invent a `productId`. If `listProducts` does not return it, the order will fail.
- `price` is in cents, as is `budget.value` on the Briefs API.
- No idempotency key exists on this API — do not blindly retry `createOrder`; re-read state
  or check for an existing `briefId` first.
- A `400` on `/products` returns a single `{"message": ...}` string; a `400` on `/orders`
  returns an `errors[]` array with `path` and `message` per validation failure.
- The API enforces an undocumented limit of 100 requests per minute and returns
  `X-RateLimit-Limit` / `X-RateLimit-Remaining` / `X-RateLimit-Reset` on every response.
  Read `X-RateLimit-Remaining` before batching product lookups.
