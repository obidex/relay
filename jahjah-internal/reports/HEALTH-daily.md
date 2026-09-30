# Daily health — `germany-vpn`

<!-- index: daily health — OK, all 11 automations alive -->

**Generated (UTC):** 2026-09-30T05:00:04Z · **Verdict:** **OK** — everything below is within normal bounds.

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-10-01 02:00 | ok: 4.5M in 4s, 127 tables, 7 kept, 1 rotated out |
| `jahjah-cleanup` | enabled | 3d 0h ago | success | 0 / 2 | 2026-10-04 04:30 | ok: nothing to do — 8.1 GB free on /, at or above 8 GB |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 11 job(s) in the ledger |
| `jahjah-retention` | enabled | 2d 22h ago | success | 0 / 3 | 2026-10-04 06:00 | ok: 2 folder(s), 0 pruned · branches: 20 deleted |
| `jahjah-runner-watchdog` | enabled | running now | success | 0 / 2 | (running now) | ok: no runner stalled |
| `jahjah-scan-gitleaks` | enabled | 2d 0h ago | success | 0 / 3 | 2026-10-05 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 2370 commits |
| `jahjah-scan-trivy` | enabled | 2d 1h ago | success | 0 / 3 | 2026-10-05 03:00 | ok: published SCAN-trivy.md — 2 targets, 0 critical, 11 high, 11 medium, 5 low |
| `jahjah-sql-live` | enabled | 1h 44m ago | success | 0 / 3 | 2026-10-01 03:15 | ok: 35/35 suites passed against live in 18s at 347b37f (migrations in step) |
| `jahjah-web-backup-check` | enabled | 2d 1h ago | success | 0 / 3 | 2026-10-05 03:30 | ok: OK — sanity-production-20260928-023004.tar.gz (0h old): product=22 ok; brand=5 ok; c |
| `jahjah-web-backup` | enabled | 2h 29m ago | success | 0 / 3 | 2026-10-01 02:30 | ok: 62K in 3s, 33 documents, 7 kept, 1 rotated out |
| `jahjah-web-truth` | enabled | 1d 23h ago | success | 0 / 3 | 2026-10-05 05:30 | ok: build **clean**, 90 page(s), 1 live issue(s) |

A job disables its own timer when its consecutive failures reach the cap in its row
(**3** unless the row says otherwise) and publishes `ALERT-<job>-disabled.md` next to this file.

**Branch sweep:** 2026-09-27T06:00:04Z deleted 20 merged branch(es) on jahjah-internal.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 4.5M |
| Tables in it | 127 |
| Last dump took | 4s |
| Dumps kept | 7 (7 nights) |
| Space used | 34M |

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
| Last checked | 2d 1h ago |
| Detail | sanity-production-20260928-023004.tar.gz (0h old): product=22 ok; brand=5 ok; category=6 ok; 3 image reference(s), all present; 3 image file(s) in the archive; 33 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 28G used of 38G (78%), 8.1G free |
| Memory | 1072 MB used of 3819 MB (28%), 2747 MB available |
| Swap | 461 MB used of 4095 MB (11%) |
| Load | 0.00, 0.00, 0.00 (over 2 cores) |
| Uptime | 4 weeks, 3 days, 7 hours, 37 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 50 | 1 | 303 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 9h 9m ago |
| peer 2 | 22h 27m ago |
| peer 3 | 5d 7h ago |
| peer 4 | never |
| peer 5 | never |
| peer 6 | 4h 51m ago |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 4 weeks |

## Database credential

| | |
|---|---|
| Credential precondition | OK |

One daily check that the box can still reach the database as configured (`D237`).
**Deliberately a bare token.** This page is world-readable; exactly what is checked, and
what a change would mean, is operational detail that belongs on the box. The attention list
and `docs/pitfalls/*` carry it. This row only says whether to go and look.
