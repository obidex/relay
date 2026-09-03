# Chunk 4 — the inbox lane retired, chunk 3 salvaged

<!-- index: chunk 4 — jahjah-inbox removed from box and relay; only what is still true was carried from chunk3-pr-b -->

**Decision:** `D230`

## Retired

`jahjah-inbox` is gone from the work engine: timer disabled, `jahjah-inbox.service`/`.timer`
removed, `/opt/jahjah/bin/{inbox.sh,inbox-run.sh}` and `/opt/jahjah/inbox/` deleted, both marker
files cleared, `daemon-reload` run. The chunk-3 hook script at `/opt/jahjah/bin/gate1-hook.sh` went
with it — it is superseded by `/opt/jahjah/gate1/` — and the now-empty `/opt/jahjah/bin` was removed.

```
unit files matching jahjah-inbox*: 0
/opt/jahjah/inbox: gone      /opt/jahjah/bin: gone      INBOX_* markers: none
```

On the relay, `jahjah-internal/inbox/` and `HEARTBEAT-inbox.md` are deleted. The chunk-3 **report**
stays — that is the historical record, not a live artefact.

It never carried a real chunk. The registry now holds one row saying so, with a pointer to the
findings.

## Salvaged from `chunk3-pr-b` — only what is still true

| From chunk 3 | Verdict |
|---|---|
| The H3 input-validation pattern (`tmux -e` flags, digits-validated issue number, model asserted against two literals) | **already the shared behaviour** — the website lane was written that way and both instances inherit it. Verified in `web-dispatch.sh`. |
| Registry rows and pitfalls notes | **rewritten** for this chunk, not carried |
| The canon wording about the GATE-1 hook | **discarded** — a panel found it overstated, and the replacement says "accident guard, not a boundary" |
| `health.sh` reporting | **rewritten** — one `lane_health` function for both lanes, plus a structural GATE-1 check |

The branch is superseded and can be deleted once this chunk's work lands.

```
=== RELAY ===
HEAD: d69ead99 (main) | branch chunk4-pr-d bfd64f9, unmerged
CI: pass — post-merge main green for #87, PR-C #93, #88
DONE: jahjah-inbox retired — units, scripts, state and relay folders removed, registry row added; chunk3-pr-b salvaged to what is still true (the injection fix was already shared behaviour; the overstated canon discarded).
FILES: box teardown only + docs/runbooks/automations.md row
FINDINGS/BLOCKERS: none
NEXT-NEEDED: none
=== END ===
```
