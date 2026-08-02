---
name: Report every VPG and its RPO
description: Pull all Virtual Protection Groups and their actual RPO for a nightly report.
api: Zerto ZVM REST API v1
operations:
  - GET /v1/vpgs
  - GET /v1/vpgs/{vpgId}
---

# Report every VPG and its RPO

Produce a report of every Virtual Protection Group (VPG) and its current recovery point
objective (RPO), e.g. for a nightly CSV or a status dashboard.

## Steps

1. **Authenticate** — get a bearer token (see `zerto-authenticate`).
2. **List VPGs** — `GET /v1/vpgs`. The response array includes each VPG's identifier, name,
   status, and `ActualRPO` (seconds).
3. **(Optional) Enrich** — for extra detail per VPG, `GET /v1/vpgs/{vpgId}`.
4. **Emit** — flatten name + status + ActualRPO into your report format.

## Rules

- This is a read-only flow — safe to run on a schedule.
- Filter/sort client-side; the list endpoint returns a JSON array without a pagination envelope.
- A high or growing `ActualRPO` relative to the configured target is the signal to alert on.
