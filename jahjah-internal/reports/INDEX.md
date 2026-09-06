# Index — jahjah-internal/reports

<!-- index: machine-generated index of this folder; every publisher rebuilds it -->

Generated 2026-09-06T07:07:04Z by `jahjah-web-docs`. **Rebuilt from disk on every publish** — read this instead
of listing the folder through the rate-limited GitHub contents API.

Raw file URLs are `https://raw.githubusercontent.com/obidex/relay/main/jahjah-internal/reports/<name>`.

## Standing files (overwritten in place — git history is their archive)

| File | Updated (UTC) | What it is |
|---|---|---|
| `HEALTH-daily.md` | 2026-09-06T05:00:09Z | daily health — NEEDS ATTENTION (1 item(s)) |
| `HEARTBEAT-dispatcher.md` | 2026-09-06T06:09:35Z | dispatch lane proof-of-life — running, last poll 2026-09-06T06:09:33Z |
| `HEARTBEAT-erp-dispatch.md` | 2026-09-06T06:57:05Z | proof-of-life for the ERP chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-web-dispatch.md` | 2026-09-06T06:56:05Z | proof-of-life for the website chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-web-docs.md` | 2026-09-06T07:07:04Z | proof-of-life for the website canon mirror — stale > ~70 min means the mirror is not running |
| `README.md` | 2026-08-31T21:04:58Z | jahjah-internal — machine reports |
| `SCAN-gitleaks.md` | 2026-08-31T21:01:36Z | weekly gitleaks scan — ZERO secrets found in either repo history |
| `SCAN-trivy.md` | 2026-08-31T23:13:57Z | weekly trivy scan — 1 critical, 30 high, 23 medium, 14 low |

## Dated reports (newest first — pruned to the newest 10 by `jahjah-retention`)

| File | Updated (UTC) | What it is |
|---|---|---|
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
