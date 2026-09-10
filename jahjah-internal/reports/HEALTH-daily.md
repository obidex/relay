# Daily health — `germany-vpn`

<!-- index: daily health — OK, all 12 automations alive -->

**Generated (UTC):** 2026-09-10T05:00:04Z · **Verdict:** **OK** — everything below is within normal bounds.

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-09-11 02:00 | ok: 1.8M in 3s, 95 tables, 7 kept, 1 rotated out |
| `jahjah-dispatcher` | enabled | 0m ago | success | 0 / 3 | 2026-09-10 05:04 | — |
| `jahjah-erp-dispatch` | enabled | 1m ago | success | 0 / 3 | 2026-09-10 05:01 | ok: idle — nothing approved |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 12 job(s) in the ledger |
| `jahjah-retention` | enabled | 3d 22h ago | success | 0 / 3 | 2026-09-13 06:00 | ok: 2 folder(s), 48 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 3d 0h ago | success | 0 / 3 | 2026-09-14 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 813 commits |
| `jahjah-scan-trivy` | enabled | 3d 1h ago | success | 0 / 3 | 2026-09-14 03:00 | ok: published SCAN-trivy.md — 6 targets, 10 critical, 150 high, 181 medium, 132 low |
| `jahjah-web-backup-check` | enabled | 3d 1h ago | success | 0 / 3 | 2026-09-14 03:30 | ok: OK — sanity-production-20260907-023004.tar.gz (0h old): product=22 ok; brand=5 ok; c |
| `jahjah-web-backup` | enabled | 2h 29m ago | success | 0 / 3 | 2026-09-11 02:30 | ok: 62K in 3s, 33 documents, 7 kept, 1 rotated out |
| `jahjah-web-dispatch` | enabled | running now | success | 0 / 3 | (running now) | ok: idle — nothing approved |
| `jahjah-web-docs` | enabled | 22m ago | success | 0 / 3 | 2026-09-10 05:07 | ok: no change (13e7ec9) |
| `jahjah-web-truth` | enabled | 2d 23h ago | success | 0 / 3 | 2026-09-14 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 1.8M |
| Tables in it | 95 |
| Last dump took | 3s |
| Dumps kept | 7 (7 nights) |
| Space used | 17M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Website catalogue backup

| | |
|---|---|
| Newest export | 2h 29m ago |
| Size | 62K |
| Documents in it | 33 |
| Exports kept | 7 (7 nights) |

The Sanity catalogue, exported nightly by `jahjah-web-backup` into `/root/backups/web`
(mode 700) and never published. **Older than 30h raises an attention item** — a unit whose
conditions fail is skipped rather than failed, so freshness is the only signal that sees it.

## Website backup integrity

| | |
|---|---|
| Verdict | **OK** |
| Last checked | 3d 1h ago |
| Detail | sanity-production-20260907-023004.tar.gz (0h old): product=22 ok; brand=5 ok; category=6 ok; 3 image reference(s), all present; 3 image file(s) in the archive; 33 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 17G used of 38G (47%), 19G free |
| Memory | 1069 MB used of 3819 MB (27%), 2750 MB available |
| Swap | 505 MB used of 4095 MB (12%) |
| Load | 0.06, 0.04, 0.00 (over 2 cores) |
| Uptime | 1 week, 4 days, 7 hours, 37 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 35 | 0 | 258 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 4h 4m ago |
| peer 2 | 1m ago |
| peer 3 | 21h 17m ago |
| peer 4 | 16h 29m ago |
| peer 5 | never |
| peer 6 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 9 days |

## Dispatch lane

| | |
|---|---|
| Heartbeat state | running |
| Heartbeat age | 16m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.

## Chunk lanes

| Lane | Repository | Heartbeat state | Heartbeat age | In flight | For | Chunk fails | Usage cap |
|---|---|---|---|---|---|---|---|
| `jahjah-web-dispatch` | `obidex/jahjah-website` | OK — idle, no `chunk:approved` issue open | 24m ago | none | — | 0/3 | — |
| `jahjah-erp-dispatch` | `obidex/jahjah-internal` | OK — idle, no `chunk:approved` issue open | 19m ago | none | — | 0/3 | — |

Both lanes are the SAME script with a different parameter file (`D230`). Each picks up
`chunk:approved` issues on its own repository every 2 minutes and runs them on this box.
**An idle lane and a dead one look identical in the fleet table** — the heartbeat age above
is what separates them. Full detail in `HEARTBEAT-<lane>.md` next to this file.

**Chunk fails** is the CHUNK breaker (`D237`), which is separate from the poll's
three-strike law: at 3 the lane disables its own timer and publishes
`ALERT-<lane>-chunks-disabled.md`. A **usage cap** death does not count toward it — hitting
the account's five-hour cap is a queue problem, not a fault, so the chunk is re-queued and
the lane waits until the time shown.

**Chunk windows open across both tmux sessions: 0.** The lanes cap concurrency
independently, so two chunks — one per lane — can run at once. Two is allowed; it is listed
as an attention item so it is never a surprise.

### Dispatches in the last 24 hours

| Dispatched (UTC) | Lane | Issue | `chunk:approved` applied by | Chunk windows open at that instant |
|---|---|---|---|---|
| _none_ | | | | |

This is the **detective control** that replaced the retired approval-window check
(`D237`). A dispatched chunk runs with this box's own GitHub credential — the same account
whose Triage right IS the `chunk:approved` gate — so the label gate does not bind the thing
it gates (`D231`). Rather than refuse, the lane RECORDS who approved each chunk and whether
any chunk of either lane was running at that instant, and shows it here.

**A flagged row is not a finding.** Approving the next chunk from a phone while the current
one is still running produces exactly the same row, and it is the ordinary case. What the
record buys is that the act is visible at all; what it does NOT buy is a boundary — a root
chunk can rewrite these files, and an approval deferred past every window is
indistinguishable from the owner's. The real fix stays parked with its trigger (`D231`).

## GATE-1 accident guard

| | |
|---|---|
| State | ACTIVE |
| Last decision | ALLOW (of 39 logged) |

Refuses to apply a migration whose SHA-256 is not published on the relay (`D228`). Two
halves: the check inside `scripts/db-query.mjs`, and a project-local PreToolUse hook for the
raw forms. **It is an accident guard, not a boundary** — nothing on this box binds a root
session holding the database password. **DEGRADED here means even the
accident guard is degraded. **Which half is degraded is deliberately NOT published** — this
page is world-readable, and advertising exactly which guard is off is an invitation. The
detail is in the attention list on the box. Only the decision KIND is published; the log
itself never leaves the machine.
