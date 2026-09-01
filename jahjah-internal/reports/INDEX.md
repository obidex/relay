# Index — jahjah-internal/reports

<!-- index: machine-generated index of this folder; every publisher rebuilds it -->

Generated 2026-09-01T12:34:25Z by `chunk-2 session`. **Rebuilt from disk on every publish** — read this instead
of listing the folder through the rate-limited GitHub contents API.

Raw file URLs are `https://raw.githubusercontent.com/obidex/relay/main/jahjah-internal/reports/<name>`.

## Standing files (overwritten in place — git history is their archive)

| File | Updated (UTC) | What it is |
|---|---|---|
| `HEALTH-daily.md` | 2026-09-01T05:00:02Z | daily health — OK, all 7 automations alive |
| `HEARTBEAT-dispatcher.md` | 2026-09-01T11:44:14Z | dispatch lane proof-of-life — running, last poll 2026-09-01T11:44:13Z |
| `README.md` | 2026-08-31T21:04:58Z | jahjah-internal — machine reports |
| `SCAN-gitleaks.md` | 2026-08-31T21:01:36Z | weekly gitleaks scan — ZERO secrets found in either repo history |
| `SCAN-trivy.md` | 2026-08-31T23:13:57Z | weekly trivy scan — 1 critical, 30 high, 23 medium, 14 low |

## Dated reports (newest first — pruned to the newest 10 by `jahjah-retention`)

| File | Updated (UTC) | What it is |
|---|---|---|
| `2026-09-01-chunk2-t0.md` | 2026-09-01T12:34:25Z | T0 DONE — PR #80 was already merged and green before this session; chunk 1's RELAY BLOCK written into its final report |
| `2026-09-01-chunk2-preflight.md` | 2026-09-01T12:34:25Z | chunk-2 preflight — LANE MISMATCH: no database access in this session, so T1/T2 cannot be applied; T1 also fails GATE... |
| `2026-09-01-chunk2-final.md` | 2026-09-01T12:34:25Z | chunk 2 FINAL — T0 done; T1+T2 BLOCKED with no SQL applied; both GATE-1 packages prepared for a session on the databa... |
| `2026-09-01-chunk2-BLOCKED.md` | 2026-09-01T12:34:25Z | T1+T2 BLOCKED — no DB lane in this session, and T1's approved guard is RLS-blind against the actor it targets; correc... |
| `2026-09-01-chunk1-final.md` | 2026-09-01T12:34:25Z | chunk 1 FINAL — 4 of 5 tasks merged, T5 blocked with proof; one M3 incident self-caught and corrected |
| `2026-09-01-chunk1-BLOCKED.md` | 2026-09-01T10:04:48Z | T5 BLOCKED — approved D174 index would break partial dispatch, PROVEN by execution; plus an M3 incident I caused and ... |
| `2026-09-01-chunk1-t4.md` | 2026-09-01T10:00:08Z | T4 MERGED — replay-check.sh wired into CI; D209 closed, and D214's replica probe becomes automatic |
| `2026-09-01-chunk1-t3.md` | 2026-09-01T10:00:06Z | T3 MERGED — Supabase schema types committed + hermetic CI drift gate; D208 half-closed, adoption registered |
| `2026-09-01-chunk1-t1.md` | 2026-09-01T08:49:46Z | T1+T2 MERGED — anchor trigger promoted to ENABLE ALWAYS, replica bypass closed, false website-SKU claim corrected |
| `2026-09-01-chunk1-preflight.md` | 2026-09-01T08:49:44Z | chunk-1 preflight — permissions clear, CI green, T1 premise CONFIRMED, T5 premise FALSE (partial dispatch) |
| `2026-09-01-w1-website-truth.md` | 2026-09-01T00:25:12Z | onboarded jahjah-website read-only, first clean Linux build (68 pages), built the weekly truth job, F7 portainer no-op |
| `2026-08-31-p31-cleanup.md` | 2026-08-31T23:26:44Z | P3.1 — deps to zero advisories, postgres:17 removed, reference regenerated, automation code now in git |
| `2026-08-31-p3-automation-fleet.md` | 2026-08-31T22:24:44Z | P3 — five scheduled jobs built, run and published; relay conventions; CI secret+SAST gates merged |
| `2026-08-31-p2-dispatcher.md` | 2026-08-31T20:36:30Z | Session report — VPS platform P2: the dispatch lane and the relay channel |
| `2026-08-31-job-72.md` | 2026-08-31T20:20:02Z | Dispatch job — issue #72 |
| `2026-08-31-job-71.md` | 2026-08-31T19:21:15Z | Dispatch job — issue #71 |
