---
name: Find and evaluate a 99designs designer
description: Search the 99designs designer network, inspect a designer's reviews and portfolio, and confirm they fit a project before engaging.
api: openapi/99designs-openapi.yml
operations: [searchDesigners, getDesigner, getDesignerReviews, getDesignerDesigns]
---

# Find and evaluate a 99designs designer

Use the 99designs API to shortlist a designer matched to a brief.

## Auth
Send both `Api-Key-Id` and `Api-Key-Secret` headers on every request. Base URL is `https://api.99designs.com/resources/v1`.

## Steps
1. `searchDesigners` (POST `/designers`) — post criteria such as `category`, `industry`, `language`, `designerLevel`, `country` (ISO alpha-2), and `keywordQuery`. Use `page`/`pageSize` to page through results; read `totalPages`/`totalResults`.
2. `getDesigner` (GET `/designers/{designerId}`) — pull the full profile (expertise, responsivenessScore, isAvailable, certifications) for a promising candidate.
3. `getDesignerReviews` (GET `/designers/{designerId}/reviews`) — read `rating`/`message` entries, paging with `page`/`pageSize`.
4. `getDesignerDesigns` (GET `/designers/{designerId}/designs`) — inspect portfolio pieces, optionally filtered by `category`.

## Rules
- Pagination is offset-style: `page` + `pageSize` in, `totalPages` + `totalResults` back.
- On 404 the designer does not exist; on 400 the search body failed validation (see the `errors[]` envelope).
