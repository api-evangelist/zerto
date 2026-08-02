---
name: Triage and dismiss alerts
description: List Zerto alerts, filter by level, and dismiss handled ones.
api: Zerto ZVM REST API v1
operations:
  - GET /v1/alerts
  - POST /v1/alerts/{id}/dismiss
---

# Triage and dismiss alerts

Pull open Zerto alerts (e.g. to page on-call or post to a channel) and dismiss the ones you've
handled.

## Steps

1. **Authenticate** — get a bearer token (see `zerto-authenticate`).
2. **List alerts** — `GET /v1/alerts`, optionally filtered by `startDate`, `endDate`, `level`,
   `vpgIdentifier`, or `entities`.
3. **Route** — forward Warning/Error-level alerts to your notification channel.
4. **Dismiss** — once handled, `POST /v1/alerts/{id}/dismiss`. Use
   `POST /v1/alerts/{id}/undismiss` to reverse.

## Rules

- Reference `GET /v1/alerts/levels` and `GET /v1/alerts/entities` for valid filter values.
- Dismissing is idempotent in effect but has no dedicated idempotency key — dismiss by explicit id.
- Prefer filtering server-side by `startDate`/`endDate` for scheduled polling instead of pulling
  the full history each run.
