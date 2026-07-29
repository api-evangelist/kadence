---
name: Create and cancel a desk booking
description: Find a bookable space, create a Kadence booking for a user, then cancel it if plans change.
api: openapi/kadence-public-api-openapi.yml
operations: [api_v1publicbookable-spaces_get_collection, api_v1publicbookings_post, api_v1publicbookings_id_get, api_v1publicbookings_idcancel_post]
---

# Create and cancel a desk booking

## Auth
OAuth 2.0 `client_credentials` token from `https://login.onkadence.co/oauth2/token` (`scope=public`); send `Authorization: Bearer <token>`. Base URL `https://api.onkadence.co`.

## Steps
1. **Find a bookable space** — `api_v1publicbookable-spaces_get_collection` (`GET /v1/public/bookable-spaces`) to get a space ULID available for the desired window.
2. **Create the booking** — `api_v1publicbookings_post` (`POST /v1/public/bookings`) with the user, space, and start/end date-times. On success you get `201` and the booking ULID.
3. **Confirm** — `api_v1publicbookings_id_get` (`GET /v1/public/bookings/{id}`).
4. **Cancel** (if needed) — `api_v1publicbookings_idcancel_post` (`POST /v1/public/bookings/{id}/cancel`).

## Rules
- Date-time filters use API Platform operators: `startDateTime[after]`, `endDateTime[before]`, `order[startDateTime]`.
- `400` = malformed request; `422` = validation failure with a Hydra `ConstraintViolationList` (e.g. space already booked). See `errors/kadence-problem-types.yml`.
- No idempotency-key header is documented; guard create/cancel with a prior state read rather than automatic retries.
