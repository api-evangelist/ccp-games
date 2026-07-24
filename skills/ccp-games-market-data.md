---
name: Pull EVE Online market data
description: Read public regional market orders and reference prices from ESI and resolve type metadata.
api: openapi/ccp-games-esi-openapi-original.json
operations:
- GetMarketsRegionIdOrders
- GetMarketsPrices
- GetUniverseTypesTypeId
- PostUniverseNames
auth: none (public endpoints)
---

# Pull EVE Online market data

Public, unauthenticated. Base URL `https://esi.evetech.net`. Send a descriptive
`User-Agent`.

## Steps

1. **GetMarketsPrices** (`GET /markets/prices/`) — average and adjusted reference
   prices for every type; a good baseline snapshot.
2. **GetMarketsRegionIdOrders** (`GET /markets/{region_id}/orders/`) — live buy/sell
   orders in a region. Filter with `?type_id=` and `?order_type=buy|sell|all`.
   Paginate with `?page=N`; read `X-Pages` for the total.
3. Resolve item metadata with **GetUniverseTypesTypeId**
   (`GET /universe/types/{type_id}/`) for names, group, and attributes.
4. Bulk-resolve any IDs (types, systems, regions) to names with **PostUniverseNames**
   (`POST /universe/names/`).

## Rules

- Market data is heavily cached — respect `Expires` and use `ETag`/`If-None-Match`.
  Do not poll faster than the cache timer; ESI applies rate limiting to the
  market-order endpoints.
- Watch `X-ESI-Error-Limit-Remain`; a `420` means back off until
  `X-ESI-Error-Limit-Reset`. See `conventions/ccp-games-conventions.yml`.
