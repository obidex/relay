# Daily health — `germany-vpn`

<!-- index: daily health — NEEDS ATTENTION (1 item(s)) -->

**Generated (UTC):** 2026-09-02T05:16:43Z · **Verdict:** **NEEDS ATTENTION — 1 item(s)**

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## Needs attention

- there is no website catalogue backup in `/root/backups/web` at all

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 3h 16m ago | success | 0 / 3 | 2026-09-03 02:00 | ok: 1.6M in 2s, 95 tables, 3 kept, 0 rotated out |
| `jahjah-dispatcher` | enabled | 0m ago | success | 0 / 3 | 2026-09-02 05:21 | — |
| `jahjah-health` | enabled | 16m ago | success | 0 / 3 | 2026-09-03 05:00 | ok: published HEALTH-daily.md — 0 attention item(s), 9 job(s) in the ledger |
| `jahjah-retention` | enabled | 1d 8h ago | success | 0 / 3 | 2026-09-06 06:00 | ok: 1 folder(s), 0 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 1d 8h ago | success | 0 / 3 | 2026-09-07 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 141 commits |
| `jahjah-scan-trivy` | enabled | 1d 6h ago | success | 0 / 3 | 2026-09-07 03:00 | ok: published SCAN-trivy.md — 4 targets, 1 critical, 30 high, 23 medium, 14 low |
| `jahjah-web-backup` | enabled | 24m ago | success | 0 / 3 | 2026-09-03 02:30 | ok: 62K in 2s, 33 documents, 1 kept, 0 rotated out |
| `jahjah-web-docs` | enabled | 9m ago | success | 0 / 3 | 2026-09-02 05:37 | ok: mirrored 939653a, 6 file(s), 0 absent |
| `jahjah-web-truth` | enabled | 1d 5h ago | success | 0 / 3 | 2026-09-07 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 3h 16m ago |
| Size | 1.6M |
| Tables in it | 95 |
| Last dump took | 2s |
| Dumps kept | 3 (7 nights) |
| Space used | 6.9M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Website catalogue backup

| | |
|---|---|
| Newest export | **none found** |
| Size | — |
| Documents in it | 33 |
| Exports kept | 0 (7 nights) |

The Sanity catalogue, exported nightly by `jahjah-web-backup` into `/root/backups/web`
(mode 700) and never published. **Older than 30h raises an attention item** — a unit whose
conditions fail is skipped rather than failed, so freshness is the only signal that sees it.

## Host

| | |
|---|---|
| Disk `/` | 16G used of 38G (44%), 21G free |
| Memory | 1090 MB used of 3819 MB (28%), 2729 MB available |
| Swap | 416 MB used of 4095 MB (10%) |
| Load | 0.20, 0.17, 0.17 (over 2 cores) |
| Uptime | 3 days, 7 hours, 54 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 43 | 1 | 38 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 0m ago |
| peer 2 | 6m ago |
| peer 3 | 1m ago |
| peer 4 | 12h 55m ago |
| peer 5 | never |
| peer 6 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 35 hours |

## Dispatch lane

| | |
|---|---|
| Heartbeat state | running |
| Heartbeat age | 2m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.
