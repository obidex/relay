# Daily health — `germany-vpn`

<!-- index: daily health — NEEDS ATTENTION (3 item(s)) -->

**Generated (UTC):** 2026-11-09T05:00:02Z · **Verdict:** **NEEDS ATTENTION — 3 item(s)**

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## Needs attention

- `jahjah-runner-watchdog.timer` is not armed — it will never fire
- `jahjah-runner-watchdog.timer` is `disabled`, not enabled
- `jahjah-runner-watchdog` has 2 consecutive failure(s)

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-agents` | enabled | 23h 38m ago | success | 0 / 3 | 2026-11-09 05:20 | ok: published HEALTH-agents.md — 6 repositories, 0 with a "?" |
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-11-10 02:00 | ok: 7.8M in 7s, 145 tables, 7 kept, 1 rotated out |
| `jahjah-cleanup` | enabled | 30m ago | success | 0 / 2 | 2026-11-09 05:30 | ok: nothing to do — 11.3 GB free on /, at or above 10 GB |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 3 attention item(s), 12 job(s) in the ledger |
| `jahjah-retention` | enabled | 22h 59m ago | success | 0 / 3 | 2026-11-15 06:00 | ok: 2 folder(s), 0 pruned · branches: 0 deleted |
| `jahjah-runner-watchdog` | disabled | never | success | 2 / 2 | **NOT ARMED** | FAILED (cap tripped): gha-runner-gha-quick still takes no jobs after its restart (job 1260 |
| `jahjah-scan-gitleaks` | enabled | 59m ago | success | 0 / 3 | 2026-11-16 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 2599 commits |
| `jahjah-scan-trivy` | enabled | 1h 59m ago | success | 0 / 3 | 2026-11-16 03:00 | ok: published SCAN-trivy.md — 2 targets, 0 critical, 19 high, 29 medium, 6 low |
| `jahjah-sql-live` | enabled | 1h 43m ago | success | 0 / 3 | 2026-11-10 03:15 | ok: 92/92 suites passed against live in 67s at 2e60230 (migrations in step) |
| `jahjah-web-backup-check` | enabled | 1h 30m ago | success | 0 / 3 | 2026-11-16 03:30 | ok: OK — sanity-production-20261109-023001.tar.gz (0h old): product=22 ok; brand=5 ok; c |
| `jahjah-web-backup` | enabled | 2h 29m ago | success | 0 / 3 | 2026-11-10 02:30 | ok: 62K in 6s, 33 documents, 7 kept, 1 rotated out |
| `jahjah-web-truth` | enabled | 6d 23h ago | success | 0 / 3 | 2026-11-09 05:30 | ok: build **clean**, 90 page(s), 1 live issue(s) |

A job disables its own timer when its consecutive failures reach the cap in its row
(**3** unless the row says otherwise) and publishes `ALERT-<job>-disabled.md` next to this file.

**Disk cleanup:** 2026-11-09 — 4.4 GB → 11.5 GB free on /, 7.2 GB freed.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 7.8M |
| Tables in it | 145 |
| Last dump took | 7s |
| Dumps kept | 7 (7 nights) |
| Space used | 59M |

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
| Last checked | 1h 30m ago |
| Detail | sanity-production-20261109-023001.tar.gz (0h old): product=22 ok; brand=5 ok; category=6 ok; 3 image reference(s), all present; 3 image file(s) in the archive; 33 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 25G used of 38G (69%), 12G free |
| Memory | 1080 MB used of 3819 MB (28%), 2739 MB available |
| Swap | 286 MB used of 4095 MB (6%) |
| Load | 0.29, 0.67, 0.72 (over 2 cores) |
| Uptime | 3 weeks, 3 days, 3 hours, 54 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 56 | 1 | 144 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

8 peer(s) configured, 2 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 18h 7m ago |
| peer 2 | 9h 19m ago |
| peer 3 | 1d 22h ago |
| peer 4 | never |
| peer 5 | never |
| peer 6 | 5h 41m ago |
| peer 7 | 6h 9m ago |
| peer 8 | 8h 5m ago |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 3 days |

## Database credential

| | |
|---|---|
| Credential precondition | OK |

One daily check that the box can still reach the database as configured (`D237`).
**Deliberately a bare token.** This page is world-readable; exactly what is checked, and
what a change would mean, is operational detail that belongs on the box. The attention list
and `docs/pitfalls/*` carry it. This row only says whether to go and look.
