---
name: Run a non-disruptive failover test
description: Start a failover test for a VPG, watch it complete, then stop it.
api: Zerto ZVM REST API v1
operations:
  - GET /v1/vpgs
  - POST /v1/vpgs/{vpgId}/FailoverTest
  - GET /v1/tasks/{taskId}
  - POST /v1/vpgs/{vpgId}/FailoverTestStop
---

# Run a non-disruptive failover test

Validate recoverability by booting a VPG's VMs in an isolated test network without disrupting
production, then tear the test down.

## Steps

1. **Authenticate** — get a bearer token (see `zerto-authenticate`).
2. **Find the VPG** — `GET /v1/vpgs` and select the target `vpgId`.
3. **Start the test** — `POST /v1/vpgs/{vpgId}/FailoverTest` with a JSON body (checkpoint
   selection and, optionally, tag). The call returns a task identifier.
4. **Poll the task** — `GET /v1/tasks/{taskId}` until it completes; the test VMs then boot in
   the test network.
5. **Validate**, then **stop the test** — `POST /v1/vpgs/{vpgId}/FailoverTestStop` (optionally
   with a test-result note), which cleans up the test VMs.

## Rules

- This is a safety-critical DR operation — require explicit human confirmation before step 3
  and step 5 when run by an agent.
- Failover test is non-disruptive to production; a live `POST .../Failover` is NOT — never
  substitute one for the other.
- All write operations are asynchronous: always confirm success by polling `/v1/tasks/{taskId}`,
  not by the POST's immediate response.
