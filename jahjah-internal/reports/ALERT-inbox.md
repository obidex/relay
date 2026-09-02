# ALERT — the inbox lane refused a chunk file

<!-- index: ALERT — the inbox lane refused a chunk file; it was NOT started -->

**When (UTC):** 2026-09-02T16:01:03Z
**Box:** `germany-vpn`

One or more files in `jahjah-internal/inbox/` did not pass the checks, so **nothing was started**.
Each refusal also counts a strike; 3 in a row disable the lane.

## Refused this pass

- `CHUNK-0.md` — no `SESSION: jahjah` line (a chunk is never started in the `web` session)

## What to do

Fix the file and push it again — a corrected push is picked up on the next poll. The
matching `state/CHUNK-<n>.rejected` file records the reason. Delete that marker if you
want the same chunk number retried after correcting it.

The checks, and why each one exists, are in `docs/runbooks/automations.md`.
