---
name: Read a character's wallet, assets, and skills
description: Authorize with EVE SSO and read a character's private wallet balance, journal, assets, and trained skills from ESI.
api: openapi/ccp-games-esi-openapi-original.json
operations:
- GetCharactersCharacterIdWallet
- GetCharactersCharacterIdWalletJournal
- GetCharactersCharacterIdAssets
- GetCharactersCharacterIdSkills
- GetCharactersCharacterIdLocation
auth: EVE SSO OAuth 2.0 (authorization code + PKCE)
scopes:
- esi-wallet.read_character_wallet.v1
- esi-assets.read_assets.v1
- esi-skills.read_skills.v1
- esi-location.read_location.v1
---

# Read a character's wallet, assets, and skills

These are authenticated endpoints. You need an EVE SSO access token that carries the
scopes listed above, for the character being queried.

## Authorize (EVE SSO)

1. Send the user to `https://login.eveonline.com/v2/oauth/authorize` with
   `response_type=code`, your `client_id`, `redirect_uri`, a `state`, PKCE
   `code_challenge` (`S256`), and a space-separated `scope` list.
2. Exchange the returned `code` at `https://login.eveonline.com/v2/oauth/token`
   for an `access_token` (JWT). The token's `sub`/name identifies the character_id.
   See `authentication/ccp-games-authentication.yml` and `scopes/ccp-games-scopes.yml`.
3. Call ESI with `Authorization: Bearer <access_token>`.

## Steps

1. **GetCharactersCharacterIdWallet** (`GET /characters/{character_id}/wallet/`) —
   current ISK balance (scope `esi-wallet.read_character_wallet.v1`).
2. **GetCharactersCharacterIdWalletJournal**
   (`GET /characters/{character_id}/wallet/journal/`) — paginated transaction log.
3. **GetCharactersCharacterIdAssets** (`GET /characters/{character_id}/assets/`) —
   paginated asset list (scope `esi-assets.read_assets.v1`).
4. **GetCharactersCharacterIdSkills** (`GET /characters/{character_id}/skills/`) —
   trained skills and total SP (scope `esi-skills.read_skills.v1`).
5. **GetCharactersCharacterIdLocation** (`GET /characters/{character_id}/location/`) —
   current solar system (scope `esi-location.read_location.v1`).

## Rules

- Paginate with `?page=N` and read `X-Pages` on list endpoints (assets, journal).
- Use conditional requests (`ETag` / `If-None-Match`) and respect `Expires`.
- A `403` usually means the token is missing the required scope; a `401` means the
  token is expired/invalid — refresh it. See `errors/ccp-games-problem-types.yml`.
- Respect the error budget (`X-ESI-Error-Limit-Remain`; `420` on exhaustion).
