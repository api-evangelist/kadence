---
name: Read workplace occupancy and space inventory
description: Walk the Kadence building/floor/space hierarchy and pull today's desk bookings to report occupancy.
api: openapi/kadence-public-api-openapi.yml
operations: [api_v1publicbuildings_get_collection, api_v1publicfloors_get_collection, api_v1publicspaces_get_collection, api_v1publicbookings_get_collection]
---

# Read workplace occupancy and space inventory

## Auth
OAuth 2.0 `client_credentials` token from `https://login.onkadence.co/oauth2/token` (`scope=public`); send `Authorization: Bearer <token>`. Base URL `https://api.onkadence.co`.

## Steps
1. **List buildings** — `api_v1publicbuildings_get_collection` (`GET /v1/public/buildings`).
2. **List floors** — `api_v1publicfloors_get_collection` (`GET /v1/public/floors`) to map floors to buildings.
3. **List spaces** — `api_v1publicspaces_get_collection` (`GET /v1/public/spaces`) for the desk/room inventory.
4. **List today's bookings** — `api_v1publicbookings_get_collection` (`GET /v1/public/bookings?itemsPerPage=200&order[startDateTime]=asc&type=desk&startDateTime[after]=<today>T00:00:00Z`). Join booking → building/space to compute utilization.

## Rules
- Paginate with `page` + `itemsPerPage`; totals are in `hydra:totalItems`. For bulk export, request `Accept: text/csv`.
- Ids are ULIDs; a Booking references its `building` and space. See `data-model/kadence-data-model.yml`.
- Read-only flow; safe to run on a schedule.
