# Index — jahjah-internal/reports

<!-- index: machine-generated index of this folder; every publisher rebuilds it -->

Generated 2026-09-03T05:07:04Z by `jahjah-web-docs`. **Rebuilt from disk on every publish** — read this instead
of listing the folder through the rate-limited GitHub contents API.

Raw file URLs are `https://raw.githubusercontent.com/obidex/relay/main/jahjah-internal/reports/<name>`.

## Standing files (overwritten in place — git history is their archive)

| File | Updated (UTC) | What it is |
|---|---|---|
| `HEALTH-daily.md` | 2026-09-03T05:00:09Z | daily health — NEEDS ATTENTION (2 item(s)) |
| `HEARTBEAT-dispatcher.md` | 2026-09-03T05:01:46Z | dispatch lane proof-of-life — running, last poll 2026-09-03T05:01:43Z |
| `HEARTBEAT-inbox.md` | 2026-09-02T18:16:36Z | inbox lane proof-of-life — running, last poll 2026-09-02T18:16:34Z |
| `HEARTBEAT-web-dispatch.md` | 2026-09-03T05:00:06Z | proof-of-life for the website chunk lane — stale > ~70 min means chunks are not being picked up |
| `HEARTBEAT-web-docs.md` | 2026-09-03T05:07:04Z | proof-of-life for the website canon mirror — stale > ~70 min means the mirror is not running |
| `README.md` | 2026-08-31T21:04:58Z | jahjah-internal — machine reports |
| `SCAN-gitleaks.md` | 2026-08-31T21:01:36Z | weekly gitleaks scan — ZERO secrets found in either repo history |
| `SCAN-trivy.md` | 2026-08-31T23:13:57Z | weekly trivy scan — 1 critical, 30 high, 23 medium, 14 low |

## Dated reports (newest first — pruned to the newest 10 by `jahjah-retention`)

| File | Updated (UTC) | What it is |
|---|---|---|
| `2026-09-03-chunk4-preflight.md` | 2026-09-03T02:23:39Z | chunk 4 preflight — surface clean, label invariant holds, no stop condition; one premise in T3.2 is wrong a... |
| `2026-09-03-chunk4-pr-c.md` | 2026-09-03T03:08:59Z | chunk 4 PR-C merged — tier3-guard is live and sabotage-proven; riders #87/#88 merged, majors closed |
| `2026-09-02-chunk3-ruleset.md` | 2026-09-02T15:48:38Z | chunk 3 — ruleset 22122876 active on main: PR-only, squash-only, ci-ok required, no bypass actors |
| `2026-09-02-chunk3-preflight.md` | 2026-09-02T14:56:42Z | chunk 3 toolkit preflight — surface confirmed, ONE deviation on the plan check, no stop condition met |
| `2026-09-02-chunk3-pr-a.md` | 2026-09-02T15:48:08Z | chunk 3 PR-A merged — ci-ok is the single required check, Dependabot is live, the D229 grant audit landed B... |
| `2026-09-02-chunk3-inbox.md` | 2026-09-02T18:17:43Z | chunk 3 — inbox lane built and fully acceptance-tested; timer enabled, inbox empty, NO real chunk started |
| `2026-09-02-chunk3-hook.md` | 2026-09-02T18:53:47Z | chunk 3 — GATE-1 PreToolUse hook active on the work engine; a migration whose hash is not on the relay cann... |
| `2026-09-02-chunk3-BLOCKED.md` | 2026-09-02T19:05:45Z | chunk 3 BLOCKED — PR-A merged and good; the GATE-1 hook and inbox lane failed the panel; the lane is DISABL... |
| `2026-09-02-chunk2-t1.md` | 2026-09-02T04:53:58Z | T1 MERGED — D218 closed as BOUNDED not closed; panel found a real residual by exploit; D220/D221 recovered ... |
| `2026-09-02-chunk2-t1-gate1.md` | 2026-09-02T01:55:46Z | T1 GATE-1 — final SQL published BEFORE applying; one identifier rename, zero semantic changes |
| `2026-09-01-w1-website-truth.md` | 2026-09-01T00:25:12Z | onboarded jahjah-website read-only, first clean Linux build (68 pages), built the weekly truth job, F7 port... |
| `2026-09-01-chunk2-t2.md` | 2026-09-02T01:03:22Z | T2 MERGED — D215 closed; three panel rounds corrected what the change actually buys, now recorded as measur... |
| `2026-09-01-chunk2-t0.md` | 2026-09-01T12:34:50Z | T0 DONE — PR #80 was already merged and green before this session; chunk 1's RELAY BLOCK written into its f... |
| `2026-09-01-chunk2-preflight.md` | 2026-09-01T12:34:50Z | chunk-2 preflight — LANE MISMATCH: no database access in this session, so T1/T2 cannot be applied; T1 also ... |
| `2026-09-01-chunk2-final.md` | 2026-09-01T12:34:50Z | chunk 2 FINAL — T0 done; T1+T2 BLOCKED with no SQL applied; both GATE-1 packages prepared for a session on ... |
| `2026-09-01-chunk2-final-3.md` | 2026-09-02T04:54:00Z | chunk 2 COMPLETE — D215 and D218 both closed; D220/D221 recovered; one nil-impact incident reported |
| `2026-09-01-chunk2-final-2.md` | 2026-09-02T01:42:02Z | chunk 2 resume FINAL — T2 merged and D215 closed; T1 BLOCKED-2, its approved SQL exists nowhere |
| `2026-09-01-chunk2-BLOCKED.md` | 2026-09-01T12:34:50Z | T1+T2 BLOCKED — no DB lane in this session, and T1's approved guard is RLS-blind against the actor it targe... |
| `2026-09-01-chunk2-BLOCKED-2.md` | 2026-09-02T01:03:55Z | T1 BLOCKED-2 — parts (a) and (c) exist nowhere; T2 merged. A blocked task's report must carry the SQL, not ... |
| `2026-09-01-chunk1-t4.md` | 2026-09-01T10:00:08Z | T4 MERGED — replay-check.sh wired into CI; D209 closed, and D214's replica probe becomes automatic |
| `2026-09-01-chunk1-t3.md` | 2026-09-01T10:00:06Z | T3 MERGED — Supabase schema types committed + hermetic CI drift gate; D208 half-closed, adoption registered |
| `2026-09-01-chunk1-t1.md` | 2026-09-01T08:49:46Z | T1+T2 MERGED — anchor trigger promoted to ENABLE ALWAYS, replica bypass closed, false website-SKU claim cor... |
| `2026-09-01-chunk1-preflight.md` | 2026-09-01T08:49:44Z | chunk-1 preflight — permissions clear, CI green, T1 premise CONFIRMED, T5 premise FALSE (partial dispatch) |
| `2026-09-01-chunk1-final.md` | 2026-09-01T12:34:50Z | chunk 1 FINAL — 4 of 5 tasks merged, T5 blocked with proof; one M3 incident self-caught and corrected |
| `2026-09-01-chunk1-BLOCKED.md` | 2026-09-01T10:04:48Z | T5 BLOCKED — approved D174 index would break partial dispatch, PROVEN by execution; plus an M3 incident I c... |
| `2026-08-31-p3-automation-fleet.md` | 2026-08-31T22:24:44Z | P3 — five scheduled jobs built, run and published; relay conventions; CI secret+SAST gates merged |
| `2026-08-31-p31-cleanup.md` | 2026-08-31T23:26:44Z | P3.1 — deps to zero advisories, postgres:17 removed, reference regenerated, automation code now in git |
| `2026-08-31-p2-dispatcher.md` | 2026-08-31T20:36:30Z | Session report — VPS platform P2: the dispatch lane and the relay channel |
| `2026-08-31-job-72.md` | 2026-08-31T20:20:02Z | Dispatch job — issue #72 |
| `2026-08-31-job-71.md` | 2026-08-31T19:21:15Z | Dispatch job — issue #71 |
