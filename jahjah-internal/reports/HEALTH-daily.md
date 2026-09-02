# Daily health — `germany-vpn`

<!-- index: daily health — OK, all 12 automations alive -->

**Generated (UTC):** 2026-09-02T18:28:41Z · **Verdict:** **OK** — everything below is within normal bounds.

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 16h 28m ago | success | 0 / 3 | 2026-09-03 02:00 | ok: 1.6M in 2s, 95 tables, 3 kept, 0 rotated out |
| `jahjah-dispatcher` | enabled | 1m ago | success | 0 / 3 | 2026-09-02 18:32 | — |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 11 job(s) in the ledger |
| `jahjah-inbox` | enabled | 1m ago | success | 0 / 3 | 2026-09-02 18:32 | ok: inbox empty |
| `jahjah-retention` | enabled | 1d 21h ago | success | 0 / 3 | 2026-09-06 06:00 | ok: 1 folder(s), 0 pruned, newest 10 kept per folder |
| `jahjah-scan-gitleaks` | enabled | 1d 21h ago | success | 0 / 3 | 2026-09-07 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 141 commits |
| `jahjah-scan-trivy` | enabled | 1d 19h ago | success | 0 / 3 | 2026-09-07 03:00 | ok: published SCAN-trivy.md — 4 targets, 1 critical, 30 high, 23 medium, 14 low |
| `jahjah-web-backup-check` | enabled | 4m ago | success | 0 / 3 | 2026-09-07 03:30 | ok: OK — sanity-production-20260902-045203.tar.gz (13h old): product=22 ok; brand=5 ok;  |
| `jahjah-web-backup` | enabled | 13h 36m ago | success | 0 / 3 | 2026-09-03 02:30 | ok: 62K in 2s, 33 documents, 1 kept, 0 rotated out |
| `jahjah-web-dispatch` | enabled | 0m ago | success | 0 / 3 | 2026-09-02 18:30 | ok: idle — nothing approved |
| `jahjah-web-docs` | enabled | 21m ago | success | 0 / 3 | 2026-09-02 18:37 | ok: no change (58912bd) |
| `jahjah-web-truth` | enabled | 1d 18h ago | success | 0 / 3 | 2026-09-07 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer after **3 consecutive failures** and publishes
`ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 16h 28m ago |
| Size | 1.6M |
| Tables in it | 95 |
| Last dump took | 2s |
| Dumps kept | 3 (7 nights) |
| Space used | 7.0M |

Dumps stay on the box in `/root/backups` (mode 700) and are never published.

## Website catalogue backup

| | |
|---|---|
| Newest export | 13h 36m ago |
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
| Memory | 1297 MB used of 3819 MB (33%), 2522 MB available |
| Swap | 203 MB used of 4095 MB (4%) |
| Load | 0.49, 0.48, 0.25 (over 2 cores) |
| Uptime | 3 days, 21 hours, 6 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 42 | 0 | 22 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 0m ago |
| peer 2 | 0m ago |
| peer 3 | 12h 3m ago |
| peer 4 | 1d 2h ago |
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
| Heartbeat age | 49m ago |

Full detail in `HEARTBEAT-dispatcher.md` next to this file.

## Inbox lane

| | |
|---|---|
| Heartbeat state | running |
| Heartbeat age | 12m ago |
| Chunk in flight | none |

The lane that starts a chunk from a file the owner pushes to `jahjah-internal/inbox/`.
Full detail in `HEARTBEAT-inbox.md` next to this file.

## GATE-1 hook (publish before apply)

| | |
|---|---|
| State | ACTIVE |
| Last decision | PARSE (of 36 decision(s) logged) |

Not a timer, so it has no row in the fleet table above. It refuses to apply any migration
whose SHA-256 is not published on the relay (`D228`). **PAUSED or MISSING here means that
gate is not in force.** Only the decision KIND is published — the log itself stays on the
box, as `gate1-hook.sh` promises.
