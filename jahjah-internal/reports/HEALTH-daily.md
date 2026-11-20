# Daily health — `germany-vpn`

<!-- index: daily health — OK, all 12 automations alive -->

**Generated (UTC):** 2026-11-20T21:46:20Z · **Verdict:** **OK** — everything below is within normal bounds.

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-agents` | enabled | 16h 22m ago | success | 0 / 3 | 2026-11-21 05:20 | ok: published HEALTH-agents.md — 6 repositories, 1 with a "?" |
| `jahjah-backup` | enabled | 2h 30m ago | success | 0 / 3 | 2026-11-21 02:00 | ok: 8.1M in 7s, 162 tables, 7 kept, 1 rotated out |
| `jahjah-cleanup` | enabled | 16m ago | success | 0 / 2 | 2026-11-20 22:30 | ok: nothing to do — 10.5 GB free on /, at or above 10 GB |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 0 attention item(s), 12 job(s) in the ledger |
| `jahjah-retention` | enabled | never | success | 0 / 3 | 2026-11-22 06:00 | ok: 2 folder(s), 0 pruned · branches: 0 deleted |
| `jahjah-runner-watchdog` | enabled | 6m ago | success | 0 / 2 | 2026-11-20 21:50 | ok: no runner stalled |
| `jahjah-scan-gitleaks` | enabled | never | success | 0 / 3 | 2026-11-23 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 2624 commits |
| `jahjah-scan-trivy` | enabled | never | success | 0 / 3 | 2026-11-23 03:00 | ok: published SCAN-trivy.md — 2 targets, 0 critical, 19 high, 31 medium, 6 low |
| `jahjah-sql-live` | enabled | 18h 29m ago | success | 0 / 3 | 2026-11-21 03:15 | ok: 102/102 suites passed against live in 93s at 823cae4 (migrations in step) |
| `jahjah-web-backup-check` | enabled | never | success | 0 / 3 | 2026-11-23 03:30 | ok: OK — sanity-production-20261116-023006.tar.gz (0h old): product=8 ok; brand=4 ok; ca |
| `jahjah-web-backup` | enabled | 19h 16m ago | success | 0 / 3 | 2026-11-21 02:30 | ok: 8.9K in 4s, 24 documents, 7 kept, 1 rotated out |
| `jahjah-web-truth` | enabled | never | success | 0 / 3 | 2026-11-23 05:30 | ok: build **clean**, 56 page(s), 3 live issue(s) |

A job disables its own timer when its consecutive failures reach the cap in its row
(**3** unless the row says otherwise) and publishes `ALERT-<job>-disabled.md` next to this file.

**Disk cleanup:** 2026-11-20 — 5.1 GB → 6.0 GB free on /, 1.0 GB freed; skipped: gha-infra (running a job) gha-internal (running a job) gha-website (running a job).

## Database backup

| | |
|---|---|
| Newest dump | 2h 30m ago |
| Size | 8.1M |
| Tables in it | 162 |
| Last dump took | 7s |
| Dumps kept | 7 (7 nights) |
| Space used | 56M |

Dumps stay on the box in `/var/lib/jj-db/dumps` (mode 700) and are never published.

## Website catalogue backup

| | |
|---|---|
| Newest export | 19h 16m ago |
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
| Last checked | 4d 18h ago |
| Detail | sanity-production-20261116-023006.tar.gz (0h old): product=8 ok; brand=4 ok; category=11 ok; 0 image reference(s), all present; 0 image file(s) in the archive; 24 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 25G used of 38G (69%), 12G free |
| Memory | 1128 MB used of 3819 MB (29%), 2691 MB available |
| Swap | 749 MB used of 4095 MB (18%) |
| Load | 5.62, 2.82, 1.29 (over 2 cores) |
| Uptime | 20 hours, 41 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 49 | 0 | 38 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

8 peer(s) configured, 5 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | never |
| peer 2 | 1m ago |
| peer 3 | never |
| peer 4 | never |
| peer 5 | never |
| peer 6 | 1m ago |
| peer 7 | 13h 34m ago |
| peer 8 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 21 hours |

## Database credential

| | |
|---|---|
| Credential precondition | OK |

One daily check that the box can still reach the database as configured (`D237`).
**Deliberately a bare token.** This page is world-readable; exactly what is checked, and
what a change would mean, is operational detail that belongs on the box. The attention list
and `docs/pitfalls/*` carry it. This row only says whether to go and look.
