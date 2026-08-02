---
name: Authenticate to the Zerto API
description: Obtain a Keycloak OAuth2 bearer token and call the Zerto ZVM REST API.
api: Zerto ZVM REST API v1
operations:
  - POST /auth/realms/zerto/protocol/openid-connect/token
---

# Authenticate to the Zerto API

Every Zerto ZVM/ZCA/ZIC appliance embeds a Keycloak identity provider. You must
exchange a username/password for a short-lived bearer JWT, then send it on every `/v1` call.

## Steps

1. **Request a token** — `POST https://<appliance-host>/auth/realms/zerto/protocol/openid-connect/token`
   with `Content-Type: application/x-www-form-urlencoded` and body:
   `grant_type=password`, `scope=openid` (REQUIRED), `client_id=zerto-client`,
   `username=<user>`, `password=<pass>`.
2. **Read `access_token`** from the JSON response. `expires_in` is often 60 seconds.
3. **Call the API** — attach `Authorization: Bearer <access_token>` (and `Accept: application/json`)
   to any `/v1/...` request, e.g. `GET /v1/localsite`.
4. **Renew** — for short scripts just re-authenticate; for long-running tools use the
   `refresh_token` also returned by Keycloak.

## Rules

- `scope=openid` is mandatory — omitting it yields a token that `/v1/*` rejects with 401.
- Lab/PoC installs may use the `master` realm and `admin-cli` client instead of `zerto`/`zerto-client`.
- Appliance certs are frequently self-signed; production clients should trust the appliance CA
  rather than disabling verification.
