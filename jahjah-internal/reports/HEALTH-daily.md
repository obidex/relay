# Daily health — `germany-vpn`

<!-- index: daily health — OK, all 9 automations alive -->

**Generated (UTC):** 2026-09-02T05:00:04Z · **Verdict:** **OK** — everything below is within normal bounds.

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-09-03 02:00 | ok: 1.6M in 2s, 95 tables, 3 kept, 0 rotated out |
| `jahjah-dispatcher` | enabled | 1m ago | success | 0 / 3 | 2026-09-02 05:03 | — |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 7 job(s) in the ledger |
| `jahjah-retention` | enabled | 1d 7h ago | success | 0 / 3 | 2026-09-06 06:00 | ok: 1 folder(s), 0 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 1d 7h ago | success | 0 / 3 | 2026-09-07 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 141 commits |
| `jahjah-scan-trivy` | enabled | 1d 5h ago | success | 0 / 3 | 2026-09-07 03:00 | ok: published SCAN-trivy.md — 4 targets, 1 critical, 30 high, 23 medium, 14 low |
| `jahjah-web-backup` | enabled | 7m ago | success | 0 / 3 | 2026-09-03 02:30 | ok: 62K in 2s, 33 documents, 1 kept, 0 rotated out |
| `jahjah-web-docs` | enabled | never | success | 0 / 3 | 2026-09-02 05:07 | — |
| `jahjah-web-truth` | enabled | 1d 4h ago | success | 0 / 3 | 2026-09-07 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 1.6M |
| Tables in it | 95 |
| Last dump took | 2s |
| Dumps kept | 3 (7 nights) |
| Space used | 7.0M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Host

| | |
|---|---|
| Disk `/` | 16G used of 38G (44%), 21G free |
| Memory | 1288 MB used of 3819 MB (33%), 2531 MB available |
| Swap | 253 MB used of 4095 MB (6%) |
| Load | 0.05, 0.09, 0.14 (over 2 cores) |
| Uptime | 3 days, 7 hours, 37 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 41 | 0 | 36 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 1m ago |
| peer 2 | 2m ago |
| peer 3 | 0m ago |
| peer 4 | 12h 38m ago |
| peer 5 | never |
| peer 6 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 34 hours |

## Dispatch lane

| | |
|---|---|
| Heartbeat state | running |
| Heartbeat age | 49m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.
