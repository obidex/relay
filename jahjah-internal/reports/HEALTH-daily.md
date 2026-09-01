# Daily health — `germany-vpn`

<!-- index: daily health — OK, all 7 automations alive -->

**Generated (UTC):** 2026-09-01T05:00:00Z · **Verdict:** **OK** — everything below is within normal bounds.

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-09-02 02:00 | ok: 1.5M in 3s, 95 tables, 2 kept, 0 rotated out |
| `jahjah-dispatcher` | enabled | 1m ago | success | 0 / 3 | 2026-09-01 05:03 | — |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 6 job(s) in the ledger |
| `jahjah-retention` | enabled | 7h 58m ago | success | 0 / 3 | 2026-09-06 06:00 | ok: 1 folder(s), 0 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 7h 58m ago | success | 0 / 3 | 2026-09-07 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 141 commits |
| `jahjah-scan-trivy` | enabled | 5h 46m ago | success | 0 / 3 | 2026-09-07 03:00 | ok: published SCAN-trivy.md — 4 targets, 1 critical, 30 high, 23 medium, 14 low |
| `jahjah-web-truth` | enabled | 4h 47m ago | success | 0 / 3 | 2026-09-07 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 1.5M |
| Tables in it | 95 |
| Last dump took | 3s |
| Dumps kept | 2 (7 nights) |
| Space used | 3.5M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Host

| | |
|---|---|
| Disk `/` | 15G used of 38G (40%), 22G free |
| Memory | 1101 MB used of 3819 MB (28%), 2718 MB available |
| Swap | 243 MB used of 4095 MB (5%) |
| Load | 0.00, 0.00, 0.00 (over 2 cores) |
| Uptime | 2 days, 7 hours, 37 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 34 | 1 | 107 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 3h 13m ago |
| peer 2 | 0m ago |
| peer 3 | 0m ago |
| peer 4 | 1d 7h ago |
| peer 5 | never |
| peer 6 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 10 hours |

## Dispatch lane

| | |
|---|---|
| Heartbeat state | running |
| Heartbeat age | 26m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.
