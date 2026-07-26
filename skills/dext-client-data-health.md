---
name: Pull client data-health and activity stats from Dext
description: >-
  Authenticate to the Dext Data Health & Insights API and retrieve a practice's
  clients, a single client's data-health detail, and its rolling activity
  statistics, respecting the 60/min rate limit.
api: openapi/dext-data-health-openapi.yml
operations: [listClients, getClient, getClientActivityStats]
---

# Pull client data-health and activity stats from Dext

Use the Dext **Data Health & Insights API** (`https://api.precision.dext.com`)
to read a practice's client data-health metrics. This API is read-only.

## Auth
- Create a token in the Dext web app: **Practice settings > Data Health > API tokens**.
  The token value is shown once and cannot be retrieved later — store it securely.
- Send it on every request as `Authorization: Bearer <token>`.

## Steps
1. **List clients** — call `listClients` (`GET /clients`) to get a summarised list
   of every client the token can access. On a `401`, the token is missing/invalid.
2. **Get client detail** — for a `clientId` of interest, call `getClient`
   (`GET /clients/{clientId}`) to read data-health metrics, VAT details, and bank
   reconciliation data. A `404` means the client is unknown or out of the token's scope.
3. **Get activity stats** — call `getClientActivityStats`
   (`GET /clients/{clientId}/activity-stats`) for rolling annual, monthly-average,
   and quarterly-average activity statistics.

## Rules
- **Rate limit:** 60 requests per minute per token. Read `X-RateLimit-Remaining`
  to pace calls; on `429 Too Many Requests`, back off until the window resets.
- Errors follow standard HTTP status semantics (`401` auth, `404` not found,
  `429` rate limit) — see `errors/dext-problem-types.yml`.
- Conventions (auth, rate-limit signalling) are captured in
  `conventions/dext-conventions.yml`.
