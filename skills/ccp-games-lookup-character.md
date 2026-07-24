---
name: Look up an EVE Online character and their org
description: Resolve character IDs and read public character, corporation, and alliance information from ESI without authentication.
api: openapi/ccp-games-esi-openapi-original.json
operations:
- PostCharactersAffiliation
- GetCharactersCharacterId
- GetCorporationsCorporationId
- GetAlliancesAllianceId
- PostUniverseNames
auth: none (public endpoints)
---

# Look up an EVE Online character and their org

All operations here are public — no EVE SSO token is required. Base URL is
`https://esi.evetech.net`. Always send a descriptive `User-Agent` identifying your
app and a contact (see `conventions/ccp-games-conventions.yml`).

## Steps

1. If you have a name instead of an ID, resolve it with **PostUniverseNames**
   (`POST /universe/names/`) — pass the id list, or use the character search flow.
2. Get affiliations for one or more character IDs with **PostCharactersAffiliation**
   (`POST /characters/affiliation/`) to learn each character's current
   `corporation_id`, `alliance_id`, and `faction_id`.
3. Fetch the character's public profile with **GetCharactersCharacterId**
   (`GET /characters/{character_id}/`).
4. Fetch the corporation with **GetCorporationsCorporationId**
   (`GET /corporations/{corporation_id}/`) and, if the corp is in an alliance, the
   alliance with **GetAlliancesAllianceId** (`GET /alliances/{alliance_id}/`).

## Rules

- Honor caching: respect `Expires` and use `ETag` / `If-None-Match` to get `304`
  responses instead of re-fetching (see conventions).
- Watch the error budget: `X-ESI-Error-Limit-Remain` / `X-ESI-Error-Limit-Reset`.
  A `420` means you exhausted it — stop and back off until reset.
- Errors are a JSON `{"error": "..."}` body — see `errors/ccp-games-problem-types.yml`.
