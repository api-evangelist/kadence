---
name: Check a user into their desk booking
description: Find a Kadence user by email, locate their booking for today, and check them in (and later out).
api: openapi/kadence-public-api-openapi.yml
operations: [api_v1publicusers_get_collection, api_v1publicusers_idbookings_get_collection, api_v1publicbookings_idcheck-in_post, api_v1publicbookings_idcheck-out_post]
---

# Check a user into their desk booking

Automate check-in from a badge reader, WiFi event, or computer login.

## Auth
Get a token from `https://login.onkadence.co/oauth2/token` (OAuth 2.0 `client_credentials`, `scope=public`) and send `Authorization: Bearer <token>` on every call. Base URL: `https://api.onkadence.co` (or `https://api.us.onkadence.co`).

## Steps
1. **Find the user** — `api_v1publicusers_get_collection` (`GET /v1/public/users?email=<email>`). Read the ULID `id` from the first `hydra:member`.
2. **Find today's booking** — `api_v1publicusers_idbookings_get_collection` (`GET /v1/public/users/{id}/bookings?order[startDateTime]=asc&startDateTime[local_after]=<today>T00:00:00Z`). Pick the desk booking to check into.
3. **Check in** — `api_v1publicbookings_idcheck-in_post` (`POST /v1/public/bookings/{id}/check-in`).
4. **Check out** (optional, later) — `api_v1publicbookings_idcheck-out_post` (`POST /v1/public/bookings/{id}/check-out`).

## Rules
- Responses are `application/ld+json`; collections paginate with `page` + `itemsPerPage` and expose `hydra:member` / `hydra:totalItems`.
- 404 = user/booking not found; 400/422 (Hydra `ConstraintViolationList`) = the booking is not in a check-in-able state. See `errors/kadence-problem-types.yml`.
- No idempotency key is offered — do not blindly retry a check-in POST; re-fetch the booking to confirm state first.
