# Daily health — `germany-vpn`

<!-- index: daily health — NEEDS ATTENTION (2 item(s)) -->

**Generated (UTC):** 2026-09-02T19:25:52Z · **Verdict:** **NEEDS ATTENTION — 2 item(s)**

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
| `jahjah-backup` | enabled | 17h 25m ago | success | 0 / 3 | 2026-09-03 02:00 | ok: 1.6M in 2s, 95 tables, 3 kept, 0 rotated out |
| `jahjah-dispatcher` | enabled | 2m ago | success | 0 / 3 | 2026-09-02 19:28 | — |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 12 job(s) in the ledger |
| `jahjah-inbox` | disabled | never | success | 0 / 3 | **NOT ARMED** | ok: inbox empty |
| `jahjah-retention` | enabled | 1d 22h ago | success | 0 / 3 | 2026-09-06 06:00 | ok: 1 folder(s), 0 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 1d 22h ago | success | 0 / 3 | 2026-09-07 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 141 commits |
| `jahjah-scan-trivy` | enabled | 1d 20h ago | success | 0 / 3 | 2026-09-07 03:00 | ok: published SCAN-trivy.md — 4 targets, 1 critical, 30 high, 23 medium, 14 low |
| `jahjah-web-backup-check` | enabled | 1h 1m ago | success | 0 / 3 | 2026-09-07 03:30 | ok: OK — sanity-production-20260902-045203.tar.gz (13h old): product=22 ok; brand=5 ok;  |
| `jahjah-web-backup` | enabled | 14h 33m ago | success | 0 / 3 | 2026-09-03 02:30 | ok: 62K in 2s, 33 documents, 1 kept, 0 rotated out |
| `jahjah-web-dispatch` | enabled | 1m ago | success | 0 / 3 | 2026-09-02 19:26 | ok: idle — nothing approved |
| `jahjah-web-docs` | enabled | 18m ago | success | 0 / 3 | 2026-09-02 19:37 | ok: mirrored 44bef69, 6 file(s), 0 absent |
| `jahjah-web-truth` | enabled | 1d 19h ago | success | 0 / 3 | 2026-09-07 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 17h 25m ago |
| Size | 1.6M |
| Tables in it | 95 |
| Last dump took | 2s |
| Dumps kept | 3 (7 nights) |
| Space used | 7.0M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Website catalogue backup

| | |
|---|---|
| Newest export | 14h 33m ago |
| Size | 62K |
| Documents in it | 33 |
| Exports kept | 1 (7 nights) |

The Sanity catalogue, exported nightly by `jahjah-web-backup` into `/root/backups/web`
(mode 700) and never published. **Older than 30h raises an attention item** — a unit whose
conditions fail is skipped rather than failed, so freshness is the only signal that sees it.

## Host

| | |
|---|---|
| Disk `/` | 16G used of 38G (44%), 21G free |
| Memory | 1258 MB used of 3819 MB (32%), 2561 MB available |
| Swap | 240 MB used of 4095 MB (5%) |
| Load | 0.47, 0.35, 0.31 (over 2 cores) |
| Uptime | 3 days, 22 hours, 3 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 45 | 0 | 25 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 1m ago |
| peer 2 | 4m ago |
| peer 3 | 13h 0m ago |
| peer 4 | 1d 3h ago |
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
| Heartbeat age | 43m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.

## Website chunk lane

| | |
|---|---|
| Heartbeat state | running — chunk #12 dispatched |
| Heartbeat age | 15m ago |
| Chunk in flight | 12 |
| In flight for | nothing running |

Picks up `chunk:approved` issues on `obidex/jahjah-website` every 2 minutes and runs them
on this box. Full detail in `HEARTBEAT-web-dispatch.md` next to this file. **An idle lane and
a dead one look identical in the fleet table** — the heartbeat age above is what separates them.
