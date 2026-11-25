# Daily health — `germany-vpn`

<!-- index: daily health — OK, all 13 automations alive -->

**Generated (UTC):** 2026-11-25T05:00:06Z · **Verdict:** **OK** — everything below is within normal bounds.

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-agents` | enabled | 23h 32m ago | success | 0 / 3 | 2026-11-25 05:20 | ok: published HEALTH-agents.md — 6 repositories, 0 with a "?" |
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-11-26 02:00 | ok: 8.4M in 6s, 179 tables, 7 kept, 1 rotated out |
| `jahjah-cleanup` | enabled | 30m ago | success | 0 / 2 | 2026-11-25 05:30 | ok: nothing to do — 10.0 GB free on /, at or above 10 GB |
| `jahjah-gh-app-token` | enabled | 1m ago | success | 0 / 3 | 2026-11-25 05:18 | — |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 13 job(s) in the ledger |
| `jahjah-retention` | enabled | 2d 22h ago | success | 0 / 3 | 2026-11-29 06:00 | ok: 2 folder(s), 0 pruned · branches: 0 deleted |
| `jahjah-runner-watchdog` | enabled | running now | success | 0 / 2 | (running now) | ok: no runner stalled |
| `jahjah-scan-gitleaks` | enabled | 2d 0h ago | success | 0 / 3 | 2026-11-30 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 2653 commits |
| `jahjah-scan-trivy` | enabled | 2d 1h ago | success | 0 / 3 | 2026-11-30 03:00 | ok: published SCAN-trivy.md — 2 targets, 0 critical, 20 high, 32 medium, 6 low |
| `jahjah-sql-live` | enabled | 1h 43m ago | success | 0 / 3 | 2026-11-26 03:15 | ok: 115/115 suites passed against live in 106s at 0c91e91 (migrations in step) |
| `jahjah-web-backup-check` | enabled | 2d 1h ago | success | 0 / 3 | 2026-11-30 03:30 | ok: OK — sanity-production-20261123-023001.tar.gz (0h old): product=8 ok; brand=4 ok; ca |
| `jahjah-web-backup` | enabled | 2h 30m ago | success | 0 / 3 | 2026-11-26 02:30 | ok: 8.9K in 3s, 24 documents, 7 kept, 1 rotated out |
| `jahjah-web-truth` | enabled | 1d 23h ago | success | 0 / 3 | 2026-11-30 05:30 | ok: build **clean**, verify 0 FAIL · 0 WARN · 55 pages, 56 page(s), 3 live issue(s) |

A job disables its own timer when its consecutive failures reach the cap in its row
(**3** unless the row says otherwise) and publishes `ALERT-<job>-disabled.md` next to this file.

**Disk cleanup:** 2026-11-25 — 9.8 GB → 10.1 GB free on /, 0.2 GB freed.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 8.4M |
| Tables in it | 179 |
| Last dump took | 6s |
| Dumps kept | 7 (7 nights) |
| Space used | 58M |

Dumps stay on the box in `/var/lib/jj-db/dumps` (mode 700) and are never published.

## Website catalogue backup

| | |
|---|---|
| Newest export | 2h 30m ago |
| Size | 8.9K |
| Documents in it | 24 |
| Exports kept | 7 (7 nights) |

The Sanity catalogue, exported nightly by `jahjah-web-backup` into `/root/backups/web`
(mode 700) and never published. **Older than 30h raises an attention item** — a unit whose
conditions fail is skipped rather than failed, so freshness is the only signal that sees it.

## Website backup integrity

| | |
|---|---|
| Verdict | **OK** |
| Last checked | 2d 1h ago |
| Detail | sanity-production-20261123-023001.tar.gz (0h old): product=8 ok; brand=4 ok; category=11 ok; 0 image reference(s), all present; 0 image file(s) in the archive; 24 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 28G used of 38G (78%), 8.0G free |
| Memory | 1099 MB used of 3819 MB (28%), 2719 MB available |
| Swap | 728 MB used of 4095 MB (17%) |
| Load | 0.04, 0.21, 0.18 (over 2 cores) |
| Uptime | 5 days, 3 hours, 54 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 8 | 0 | 162 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

9 peer(s) configured, 4 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | never |
| peer 2 | 10h 0m ago |
| peer 3 | 1d 10h ago |
| peer 4 | never |
| peer 5 | never |
| peer 6 | 4h 41m ago |
| peer 7 | 3m ago |
| peer 8 | never |
| peer 9 | 2m ago |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 5 days |

## Database credential

| | |
|---|---|
| Credential precondition | OK |

One daily check that the box can still reach the database as configured (`D237`).
**Deliberately a bare token.** This page is world-readable; exactly what is checked, and
what a change would mean, is operational detail that belongs on the box. The attention list
and `docs/pitfalls/*` carry it. This row only says whether to go and look.
