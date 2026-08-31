# Daily health — `germany-vpn`

<!-- index: daily health — NEEDS ATTENTION (1 item(s)) -->

**Generated (UTC):** 2026-08-31T21:02:01Z · **Verdict:** **NEEDS ATTENTION — 1 item(s)**

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## Needs attention

- `jahjah-dispatcher.timer` is not armed — it will never fire

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 1m ago | success | 0 / 3 | 2026-09-01 02:00 | ok: 1.5M in 3s, 95 tables, 1 kept, 0 rotated out |
| `jahjah-dispatcher` | enabled | 0m ago | success | 0 / 3 | **NOT ARMED** | — |
| `jahjah-health` | enabled | running now | success | 0 / 3 | 2026-09-01 05:00 | — |
| `jahjah-retention` | enabled | 0m ago | success | 0 / 3 | 2026-09-06 06:00 | ok: 1 folder(s), 0 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 0m ago | success | 0 / 3 | 2026-09-07 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 141 commits |
| `jahjah-scan-trivy` | enabled | 0m ago | success | 0 / 3 | 2026-09-07 03:00 | ok: published SCAN-trivy.md — 5 targets, 15 critical, 98 high, 161 medium, 152 low |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 1m ago |
| Size | 1.5M |
| Tables in it | 95 |
| Last dump took | 3s |
| Dumps kept | 1 (7 nights) |
| Space used | 2.1M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Host

| | |
|---|---|
| Disk `/` | 13G used of 38G (36%), 24G free |
| Memory | 1400 MB used of 3819 MB (36%), 2419 MB available |
| Swap | 30 MB used of 4095 MB (0%) |
| Load | 0.73, 0.32, 0.17 (over 2 cores) |
| Uptime | 1 day, 23 hours, 39 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 34 | 0 | 94 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 0m ago |
| peer 2 | 0m ago |
| peer 3 | 1d 2h ago |
| peer 4 | 23h 16m ago |
| peer 5 | never |
| peer 6 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 2 hours |

## Dispatch lane

| | |
|---|---|
| Heartbeat state | PAUSED by kill switch |
| Heartbeat age | 40m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.
