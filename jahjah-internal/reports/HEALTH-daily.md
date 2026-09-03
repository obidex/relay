# Daily health — `germany-vpn`

<!-- index: daily health — NEEDS ATTENTION (2 item(s)) -->

**Generated (UTC):** 2026-09-03T05:00:04Z · **Verdict:** **NEEDS ATTENTION — 2 item(s)**

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## Needs attention

- `jahjah-inbox.timer` is not armed — it will never fire
- `jahjah-inbox.timer` is `disabled`, not enabled

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-09-04 02:00 | ok: 1.7M in 3s, 95 tables, 4 kept, 0 rotated out |
| `jahjah-dispatcher` | enabled | 3m ago | success | 0 / 3 | 2026-09-03 05:01 | — |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 2 attention item(s), 12 job(s) in the ledger |
| `jahjah-inbox` | disabled | never | success | 0 / 3 | **NOT ARMED** | ok: inbox empty |
| `jahjah-retention` | enabled | 2d 7h ago | success | 0 / 3 | 2026-09-06 06:00 | ok: 1 folder(s), 0 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 2d 7h ago | success | 0 / 3 | 2026-09-07 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 141 commits |
| `jahjah-scan-trivy` | enabled | 2d 5h ago | success | 0 / 3 | 2026-09-07 03:00 | ok: published SCAN-trivy.md — 4 targets, 1 critical, 30 high, 23 medium, 14 low |
| `jahjah-web-backup-check` | enabled | 9h 23m ago | success | 0 / 3 | 2026-09-07 03:30 | ok: OK — sanity-production-20260902-045203.tar.gz (14h old): product=22 ok; brand=5 ok;  |
| `jahjah-web-backup` | enabled | 2h 29m ago | success | 0 / 3 | 2026-09-04 02:30 | ok: 62K in 4s, 33 documents, 2 kept, 0 rotated out |
| `jahjah-web-dispatch` | enabled | running now | success | 0 / 3 | (running now) | ok: idle — nothing approved |
| `jahjah-web-docs` | enabled | 23m ago | success | 0 / 3 | 2026-09-03 05:07 | ok: no change (6e28bc3) |
| `jahjah-web-truth` | enabled | 2d 4h ago | success | 0 / 3 | 2026-09-07 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 1.7M |
| Tables in it | 95 |
| Last dump took | 3s |
| Dumps kept | 4 (7 nights) |
| Space used | 8.7M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Website catalogue backup

| | |
|---|---|
| Newest export | 2h 29m ago |
| Size | 62K |
| Documents in it | 33 |
| Exports kept | 2 (7 nights) |

The Sanity catalogue, exported nightly by `jahjah-web-backup` into `/root/backups/web`
(mode 700) and never published. **Older than 30h raises an attention item** — a unit whose
conditions fail is skipped rather than failed, so freshness is the only signal that sees it.

## Website backup integrity

| | |
|---|---|
| Verdict | **OK** |
| Last checked | 9h 23m ago |
| Detail | sanity-production-20260902-045203.tar.gz (14h old): product=22 ok; brand=5 ok; category=6 ok; 3 image reference(s), all present; 3 image file(s) in the archive; 33 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 16G used of 38G (44%), 21G free |
| Memory | 1026 MB used of 3819 MB (26%), 2793 MB available |
| Swap | 451 MB used of 4095 MB (11%) |
| Load | 0.08, 0.02, 0.01 (over 2 cores) |
| Uptime | 4 days, 7 hours, 37 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 43 | 0 | 38 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 2h 33m ago |
| peer 2 | 0m ago |
| peer 3 | 59m ago |
| peer 4 | 1d 12h ago |
| peer 5 | never |
| peer 6 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 2 days |

## Dispatch lane

| | |
|---|---|
| Heartbeat state | running |
| Heartbeat age | 1h 0m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.

## Website chunk lane

| | |
|---|---|
| Heartbeat state | OK — idle, no `chunk:approved` issue open |
| Heartbeat age | 0m ago |
| Chunk in flight | none |
| In flight for | nothing running |

Picks up `chunk:approved` issues on `obidex/jahjah-website` every 2 minutes and runs them
on this box. Full detail in `HEARTBEAT-web-dispatch.md` next to this file. **An idle lane and
a dead one look identical in the fleet table** — the heartbeat age above is what separates them.
