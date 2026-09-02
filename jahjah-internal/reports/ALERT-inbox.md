# ALERT — the inbox lane refused a chunk file

<!-- index: ALERT — the inbox lane refused a chunk file; it was NOT started -->

**When (UTC):** 2026-09-02T16:23:13Z
**Box:** `germany-vpn`

One or more files in `jahjah-internal/inbox/` did not pass the checks, so **nothing was started**.
Each refusal also counts a strike; 3 in a row disable the lane.

## Refused this pass

- `CHUNK-2.md` — author is `Someone Else <someone@example.invalid>`, not the owner `obidex <144545793+obidex@users.noreply.github.com>`
- `CHUNK-3.md` — body sha256 is `802e2f2266884f1111bcf9f366aa40b42e6cba981c2f28498185438d40161d8c` but line 1 declares `a6db0a955e706a6ad0a9ae14f2e825363d21006a7f0de44d5b139869061c7854` — the file was altered after it was hashed

## What to do

Fix the file and push it again — a corrected push is picked up on the next poll. The
matching `state/CHUNK-<n>.rejected` file records the reason. Delete that marker if you
want the same chunk number retried after correcting it.

The checks, and why each one exists, are in `docs/runbooks/automations.md`.
