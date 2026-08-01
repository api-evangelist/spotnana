---
name: Authenticate and obtain a Spotnana API token
description: Provision an API user and exchange its credentials for a Bearer token used by every Spotnana API.
api: openapi/spotnana-auth-openapi-original.json
operations: [createApiUser, fetchOauth2Token, rotateClientSecret, deleteApiUser]
---

# Authenticate with the Spotnana API

Every Spotnana API (Air, Hotel, Trip, Company, Users, Events, Payments, Policy, Document, Template) is protected by an HTTP `Authorization: Bearer <token>` credential. Tokens are minted from an API user's `client_id` / `client_secret`.

## Steps

1. **Provision an API user** — `createApiUser` (`POST /v2/api-users`). Returns a `client_id` and `client_secret`. Store the secret securely; it is shown once.
2. **Exchange credentials for a token** — `fetchOauth2Token` (`POST /v2/auth/oauth2-token`), `Content-Type: application/x-www-form-urlencoded`:
   - `grant_type=client_credentials`
   - `client_id`, `client_secret`
   - optional `audience=https://apis.spotnana.com/v2`
   - To act on behalf of a subject, use `grant_type=urn:ietf:params:oauth:grant-type:token-exchange` with `subject_token` / `subject_token_type`.
   - Response: `access_token`, `expires_in`, `token_type`, `refresh_token`, `scope`.
3. **Call any API** — send `Authorization: Bearer <access_token>`. Refresh before `expires_in` elapses.
4. **Rotate on schedule / compromise** — `rotateClientSecret` (`POST /v2/api-users/rotate`).
5. **Revoke when retiring an integration** — `deleteApiUser` (`POST /v2/api-users/revoke`).

## Rules
- Sandbox host: `https://api-ext-sboxmeta.partners.spotnana.com`; production audience: `https://apis.spotnana.com/v2`.
- `401` = missing/invalid token; `403` = valid token but insufficient RBAC permission.
- Errors return the `ErrorResponse` envelope (`debugIdentifier` + `errorMessages[].errorCode`). No `Idempotency-Key` is supported — do not assume safe retries on writes.
