---
name: Submit a brief and place a 99designs order
description: Collect a design brief and place an order against a 99designs product, then hand the customer the returned brief URLs.
api: openapi/99designs-openapi.yml
operations: [createBrief, createOrder]
---

# Submit a brief and place a 99designs order

Use the 99designs API to intake a project and start managed creative work.

## Auth
Send both `Api-Key-Id` and `Api-Key-Secret` headers. Base URL is `https://api.99designs.com/resources/v1`.

## Steps
1. `createBrief` (POST `/briefs`) — submit `category`, `title`, `description`, `industry`, `budget` (`value` in cents + `currency`), `timeframe`, `usage`, and `language`. On `201` capture `id` and `briefUrl`.
2. `createOrder` (POST `/orders`) — supply `productId` (e.g. `logo-essential-v1`) and a nested `brief` object (`title`, `description`, `category`, `timeline`, `approval`, `businessName`) that validates against the product-version schema. On `201` read `orderItems[].briefId` and `orderItems[].returnUrls` (`briefStart`, `briefEnd`, `review`, `postReview`).
3. Redirect the customer to the appropriate `returnUrls` value to start or review their brief.

## Rules
- `budget.value` is in cents.
- No idempotency key is documented — do not blindly retry a POST; check for a created resource first.
- A `400` returns an `errors[]` array with `path`/`message` describing each validation failure.
