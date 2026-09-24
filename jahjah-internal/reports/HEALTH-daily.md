# Daily health — `germany-vpn`

<!-- index: daily health — NEEDS ATTENTION (1 item(s)) -->

**Generated (UTC):** 2026-09-24T05:00:04Z · **Verdict:** **NEEDS ATTENTION — 1 item(s)**

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## Needs attention

- `jahjah-web-dispatch.timer` is retired (D254) but still installed — remove it

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-09-25 02:00 | ok: 3.0M in 4s, 108 tables, 7 kept, 1 rotated out |
| `jahjah-cleanup` | enabled | 13h 28m ago | success | 0 / 2 | 2026-09-27 04:30 | ok: nothing to do — 10.4 GB free on /, at or above 8 GB |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 7 attention item(s), 11 job(s) in the ledger |
| `jahjah-retention` | enabled | 3d 22h ago | success | 0 / 3 | 2026-09-27 06:00 | ok: 2 folder(s), 16 pruned · branches: 0 deleted |
| `jahjah-scan-gitleaks` | enabled | 3d 0h ago | success | 0 / 3 | 2026-09-28 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 2180 commits |
| `jahjah-scan-trivy` | enabled | 3d 1h ago | success | 0 / 3 | 2026-09-28 03:00 | ok: published SCAN-trivy.md — 8 targets, 13 critical, 376 high, 531 medium, 344 low |
| `jahjah-sql-live` | enabled | 1h 44m ago | success | 0 / 3 | 2026-09-25 03:15 | ok: 30/30 suites passed against live in 15s at 5b6467c (migrations in step) |
| `jahjah-web-backup-check` | enabled | 3d 1h ago | success | 0 / 3 | 2026-09-28 03:30 | ok: OK — sanity-production-20260921-023004.tar.gz (0h old): product=22 ok; brand=5 ok; c |
| `jahjah-web-backup` | enabled | 2h 29m ago | success | 0 / 3 | 2026-09-25 02:30 | ok: 62K in 4s, 33 documents, 7 kept, 1 rotated out |
| `jahjah-web-truth` | enabled | 2d 23h ago | success | 0 / 3 | 2026-09-28 05:30 | ok: build **clean**, 68 page(s), 1 live issue(s) |

A job disables its own timer when its consecutive failures reach the cap in its row
(**3** unless the row says otherwise) and publishes `ALERT-<job>-disabled.md` next to this file.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 3.0M |
| Tables in it | 108 |
| Last dump took | 4s |
| Dumps kept | 7 (7 nights) |
| Space used | 21M |

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
| Detail | sanity-production-20260921-023004.tar.gz (0h old): product=22 ok; brand=5 ok; category=6 ok; 3 image reference(s), all present; 3 image file(s) in the archive; 33 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 26G used of 38G (72%), 11G free |
| Memory | 805 MB used of 3819 MB (21%), 3014 MB available |
| Swap | 371 MB used of 4095 MB (9%) |
| Load | 0.00, 0.00, 0.09 (over 2 cores) |
| Uptime | 3 weeks, 4 days, 7 hours, 37 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 53 | 5 | 115 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 6m ago |
| peer 2 | 3d 9h ago |
| peer 3 | 14h 47m ago |
| peer 4 | never |
| peer 5 | never |
| peer 6 | 0m ago |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 3 weeks |

## Database credential

| | |
|---|---|
| Credential precondition | OK |

One daily check that the box can still reach the database as configured (`D237`).
**Deliberately a bare token.** This page is world-readable; exactly what is checked, and
what a change would mean, is operational detail that belongs on the box. The attention list
and `docs/pitfalls/*` carry it. This row only says whether to go and look.
