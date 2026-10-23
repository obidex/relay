# Daily health — `germany-vpn`

<!-- index: daily health — NEEDS ATTENTION (3 item(s)) -->

**Generated (UTC):** 2026-10-23T05:00:02Z · **Verdict:** **NEEDS ATTENTION — 3 item(s)**

Overwritten in place once a day. Git history is the archive — the previous days are in
this file's commit log, not in extra files. **If the timestamp above is more than ~26 hours
old, the health job itself has stopped and nothing here can be trusted as current.**

## Needs attention

- `jahjah-sql-live.timer` is not armed — it will never fire
- `jahjah-sql-live.timer` is `disabled`, not enabled
- `jahjah-sql-live` has 3 consecutive failure(s)

## The automation fleet

Every `jahjah-*` timer on the box. **Armed** is the one that catches the silent failure:
a timer can be `enabled` and still have no next elapse, in which case it never fires.

| Job | Enabled | Last run | Result | Consecutive failures | Next run (UTC) | Last run said |
|---|---|---|---|---|---|---|
| `jahjah-backup` | enabled | 2h 59m ago | success | 0 / 3 | 2026-10-24 02:00 | ok: 7.8M in 6s, 145 tables, 7 kept, 1 rotated out |
| `jahjah-cleanup` | enabled | 30m ago | success | 0 / 2 | 2026-10-24 04:30 | ok: nothing to do — 8.4 GB free on /, at or above 8 GB |
| `jahjah-health` | enabled | running now | success | 0 / 3 | (running now) | ok: published HEALTH-daily.md — 3 attention item(s), 11 job(s) in the ledger |
| `jahjah-retention` | enabled | 4d 22h ago | success | 0 / 3 | 2026-10-25 06:00 | ok: 2 folder(s), 0 pruned · branches: 0 deleted |
| `jahjah-runner-watchdog` | enabled | running now | success | 0 / 2 | (running now) | ok: no runner stalled |
| `jahjah-scan-gitleaks` | enabled | 4d 0h ago | success | 0 / 3 | 2026-10-26 04:00 | ok: published SCAN-gitleaks.md — 0 hit(s) across 2556 commits |
| `jahjah-scan-trivy` | enabled | 4d 1h ago | success | 0 / 3 | 2026-10-26 03:00 | ok: published SCAN-trivy.md — 2 targets, 0 critical, 17 high, 28 medium, 6 low |
| `jahjah-sql-live` | disabled | never | success | 3 / 3 | **NOT ARMED** | FAILED (cap tripped): 1 of 88 suite(s) did not pass against live: po_schedule_one_transact |
| `jahjah-web-backup-check` | enabled | 4d 1h ago | success | 0 / 3 | 2026-10-26 03:30 | ok: OK — sanity-production-20261019-023002.tar.gz (0h old): product=22 ok; brand=5 ok; c |
| `jahjah-web-backup` | enabled | 2h 29m ago | success | 0 / 3 | 2026-10-24 02:30 | ok: 62K in 3s, 33 documents, 7 kept, 1 rotated out |
| `jahjah-web-truth` | enabled | 3d 23h ago | success | 0 / 3 | 2026-10-26 05:30 | ok: build **clean**, 90 page(s), 1 live issue(s) |

A job disables its own timer when its consecutive failures reach the cap in its row
(**3** unless the row says otherwise) and publishes `ALERT-<job>-disabled.md` next to this file.

**Disk cleanup:** 2026-10-16 — 3.9 GB → 13.3 GB free on /, 9.4 GB freed.

## Database backup

| | |
|---|---|
| Newest dump | 2h 59m ago |
| Size | 7.8M |
| Tables in it | 145 |
| Last dump took | 6s |
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
| Last checked | 4d 1h ago |
| Detail | sanity-production-20261019-023002.tar.gz (0h old): product=22 ok; brand=5 ok; category=6 ok; 3 image reference(s), all present; 3 image file(s) in the archive; 33 documents total |

`jahjah-web-backup-check`, Mondays 03:30 UTC: it unpacks the newest archive and compares its
product, brand and category counts with the LIVE Sanity dataset, then checks that every image
the exported documents point at is actually inside the archive. **Freshness above says an
archive exists; this says it is a copy of the catalogue.** A mismatch is a finding, not a
failed job, so it never appears in the fleet table — only here.

## Host

| | |
|---|---|
| Disk `/` | 28G used of 38G (77%), 8.4G free |
| Memory | 1254 MB used of 3819 MB (32%), 2564 MB available |
| Swap | 307 MB used of 4095 MB (7%) |
| Load | 0.03, 0.01, 0.00 (over 2 cores) |
| Uptime | 1 week, 3 hours, 54 minutes |

## SSH attack blocking (fail2ban — active)

| Jail | Banned in last 24h | Currently banned | Banned ever |
|---|---|---|---|
| `sshd` | 46 | 0 | 120 |

Counts only. Addresses are deliberately not published.

## VPN peers (wg0 — up)

6 peer(s) configured, 5 have never completed a handshake.

| Peer | Last handshake |
|---|---|
| peer 1 | 6d 0h ago |
| peer 2 | never |
| peer 3 | never |
| peer 4 | never |
| peer 5 | never |
| peer 6 | never |

Peers are numbered, not named. Keys and endpoint addresses are deliberately not published.

## Containers (docker — up)

| Container | State | Status |
|---|---|---|
| `portainer` | running | Up 4 hours |

## Database credential

| | |
|---|---|
| Credential precondition | OK |

One daily check that the box can still reach the database as configured (`D237`).
**Deliberately a bare token.** This page is world-readable; exactly what is checked, and
what a change would mean, is operational detail that belongs on the box. The attention list
and `docs/pitfalls/*` carry it. This row only says whether to go and look.
