# Index — jahjah-internal/reports

<!-- index: machine-generated index of this folder; every publisher rebuilds it -->

Generated 2026-09-10T12:07:04Z by `jahjah-web-docs`. **Rebuilt from disk on every publish** — read this instead
of listing the folder through the rate-limited GitHub contents API.

Raw file URLs are `https://raw.githubusercontent.com/obidex/relay/main/jahjah-internal/reports/<name>`.

## Standing files (overwritten in place — git history is their archive)

| File | Updated (UTC) | What it is |
|---|---|---|
| `HEALTH-daily.md` | 2026-09-10T05:00:07Z | daily health — OK, all 12 automations alive |
| `HEARTBEAT-chunk8-scratch.md` | 2026-09-10T07:44:00Z | proof-of-life for the scratch chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-dispatcher.md` | 2026-09-10T11:56:26Z | dispatch lane proof-of-life — running, last poll 2026-09-10T11:56:23Z |
| `HEARTBEAT-erp-dispatch.md` | 2026-09-10T11:27:06Z | proof-of-life for the ERP chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-web-dispatch.md` | 2026-09-10T11:22:06Z | proof-of-life for the website chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-web-docs.md` | 2026-09-10T12:07:04Z | proof-of-life for the website canon mirror — stale > ~70 min means the mirror is not running |
| `README.md` | 2026-08-31T21:04:58Z | jahjah-internal — machine reports |
| `SCAN-gitleaks.md` | 2026-09-07T04:00:09Z | weekly gitleaks scan — ZERO secrets found in either repo history |
| `SCAN-trivy.md` | 2026-09-07T03:00:19Z | weekly trivy scan — 10 critical, 150 high, 181 medium, 132 low |

## Dated reports (newest first — pruned to the newest 10 by `jahjah-retention`)

| File | Updated (UTC) | What it is |
|---|---|---|
| `2026-09-10-chunk8-preflight.md` | 2026-09-10T00:25:45Z | chunk 8 preflight — lane code written, bash -n + shellcheck clean, mirror lane closed with 9 sabotage-prove... |
| `2026-09-10-chunk8-install.md` | 2026-09-10T08:13:28Z | chunk 8 install — the new chunk-lane code is live on both lanes, resolved config differs by one key, both t... |
| `2026-09-10-chunk8-final.md` | 2026-09-10T10:24:54Z | chunk 8 final — MERGED 6718a45; detective control, chunk breaker, quota/open-PR outcomes, working DB lane, ... |
| `2026-09-10-chunk8-dblane.md` | 2026-09-10T08:05:43Z | **Chunk 8 smoke — the dispatched-chunk database lane: GRANTED BUT NOT USABLE HEADLESSLY (ii). The GATE-1 gu... |
| `2026-09-05-chunk7-preflight.md` | 2026-09-05T12:01:42Z | chunk 7 — preflight: the `D234` migration (decision A) |
| `2026-09-05-chunk7-gate1.md` | 2026-09-05T12:13:55Z | chunk 7 — GATE 1: the migration, published before it is applied |
| `2026-09-05-chunk7-final.md` | 2026-09-05T14:04:54Z | chunk 7 — FINAL: the `D234` migration landed, and the panel changed its headline |
| `2026-09-04-job-100.md` | 2026-09-04T05:53:02Z | chunk 6 (#97) postmortem — the session hit the 5-hour usage cap 7 seconds after writing a complete report, ... |
| `2026-09-04-chunk6-progress.md` | 2026-09-04T07:03:19Z | chunk 6 — progress: the migration is in, the panel has been round the loop once, round two is running |
| `2026-09-04-chunk6-preflight.md` | 2026-09-04T06:04:51Z | chunk 6 (resume) — PREFLIGHT + RESCUE |
| `2026-09-04-chunk6-panel2.md` | 2026-09-04T21:53:25Z | chunk 6 — the panel is done. I am NOT merging, and the reason is my own record. |
| `2026-09-04-chunk6-migration.md` | 2026-09-04T06:19:12Z | chunk 6 — the migration is APPLIED, and the result was read back out of the catalog |
| `2026-09-04-chunk6-gate1.md` | 2026-09-04T06:16:35Z | chunk 6 — GATE 1: the migration, published before it is applied |
| `2026-09-04-chunk6-final.md` | 2026-09-05T07:32:54Z | chunk 6 — MERGED. `f82f7cc` on `main`, production READY, the mirror serving the new canon. |
