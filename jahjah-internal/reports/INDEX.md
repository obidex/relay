# Index — jahjah-internal/reports

<!-- index: machine-generated index of this folder; every publisher rebuilds it -->

Generated 2026-09-12T17:44:04Z by `jahjah-web-dispatch`. **Rebuilt from disk on every publish** — read this instead
of listing the folder through the rate-limited GitHub contents API.

Raw file URLs are `https://raw.githubusercontent.com/obidex/relay/main/jahjah-internal/reports/<name>`.

## Standing files (overwritten in place — git history is their archive)

| File | Updated (UTC) | What it is |
|---|---|---|
| `HEALTH-daily.md` | 2026-09-12T05:00:06Z | daily health — OK, all 12 automations alive |
| `HEARTBEAT-erp-dispatch.md` | 2026-09-12T17:01:05Z | proof-of-life for the ERP chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-web-dispatch.md` | 2026-09-12T17:44:04Z | proof-of-life for the website chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-web-docs.md` | 2026-09-12T17:37:05Z | proof-of-life for the website canon mirror — stale > ~70 min means the mirror is not running |
| `README.md` | 2026-08-31T21:04:58Z | jahjah-internal — machine reports |
| `SCAN-gitleaks.md` | 2026-09-07T04:00:09Z | weekly gitleaks scan — ZERO secrets found in either repo history |
| `SCAN-trivy.md` | 2026-09-07T03:00:19Z | weekly trivy scan — 10 critical, 150 high, 181 medium, 132 low |
| `SQL-live.md` | 2026-09-12T03:15:18Z | nightly SQL suites vs LIVE — PASS 28/28 in 12s |

## Dated reports (newest first — pruned to the newest 10 by `jahjah-retention`)

| File | Updated (UTC) | What it is |
|---|---|---|
| `2026-09-12-chunk-158-blocked.md` | 2026-09-12T01:53:54Z | KIND: blocked |
| `2026-09-12-chunk-157-final.md` | 2026-09-12T01:04:22Z | KIND: final |
| `2026-09-11-workflow-cards-final.md` | 2026-09-11T13:07:08Z | KIND: final |
| `2026-09-11-workflow-cards-blocked.md` | 2026-09-11T10:39:13Z | KIND: blocked |
| `2026-09-11-hygiene-and-lean-B-final.md` | 2026-09-11T18:17:56Z | KIND: final |
| `2026-09-11-gate0-lane-final.md` | 2026-09-11T19:32:11Z | KIND: final |
| `2026-09-11-chunk-145-final.md` | 2026-09-11T10:05:32Z | KIND: final |
| `2026-09-11-chunk-135-final.md` | 2026-09-11T09:35:32Z | KIND: final |
| `2026-09-11-chunk-134-final.md` | 2026-09-11T09:32:35Z | KIND: final |
| `2026-09-10-chunk9-rider.md` | 2026-09-10T18:23:37Z | chunk 9 rider — MERGED 8e4715a; today's reds explained; ci-ok fails on any failure-level annotation; D240 (... |
| `2026-09-10-chunk9-preflight.md` | 2026-09-10T17:01:46Z | chunk 9 preflight — suites in the replay container: 2/28 as-is, 26/28 with harness actors + auth.uid(); 2 e... |
| `2026-09-10-chunk9-final.md` | 2026-09-10T17:45:44Z | chunk 9 final — MERGED 75c474c; suites hermetic 26/28 (2 excluded, D240 a), nightly live 28/28, D239 label-... |
| `2026-09-10-chunk8-preflight.md` | 2026-09-10T00:25:45Z | chunk 8 preflight — lane code written, bash -n + shellcheck clean, mirror lane closed with 9 sabotage-prove... |
| `2026-09-10-chunk8-install.md` | 2026-09-10T08:13:28Z | chunk 8 install — the new chunk-lane code is live on both lanes, resolved config differs by one key, both t... |
