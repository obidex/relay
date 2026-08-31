# Session report — VPS P3: the automation fleet

<!-- index: P3 — five scheduled jobs built, run and published; relay conventions; CI secret+SAST gates merged -->

**Session:** VPS platform P3 — infrastructure + light CI
**Box:** `germany-vpn` · **Repo HEAD after this session:** `6e51487` · **Tree:** clean

Five unattended jobs now run on the work engine, each obeying the three owner laws. The relay repo
gained a fixed publishing convention. One code PR added two blocking CI gates and put the automation
registry on the public docs mirror.

-----

## 1. The five jobs — schedule and kill switch

All times UTC, pinned explicitly in each timer (`OnCalendar=… UTC`) so a timezone change on the box
cannot silently move a schedule. All use `Persistent=true`, so one run missed to downtime is caught
up rather than skipped.

| Job | Runs | Timeout | Kill switch | Publishes |
|---|---|---|---|---|
| `jahjah-backup` | daily **02:00** | 1800s | `touch /opt/jahjah/BACKUP_OFF` | nothing (dumps stay on the box) |
| `jahjah-scan-trivy` | **Mon 03:00** | 5400s | `touch /opt/jahjah/SCAN_TRIVY_OFF` | `SCAN-trivy.md` |
| `jahjah-scan-gitleaks` | **Mon 04:00** | 2400s | `touch /opt/jahjah/SCAN_GITLEAKS_OFF` | `SCAN-gitleaks.md` (+ `ALERT-gitleaks-<date>.md` on any hit) |
| `jahjah-health` | daily **05:00** | 900s | `touch /opt/jahjah/HEALTH_OFF` | `HEALTH-daily.md` |
| `jahjah-retention` | **Sun 06:00** | 900s | `touch /opt/jahjah/RETENTION_OFF` | rebuilt `INDEX.md` |

Every unit passed `systemd-analyze verify` clean (a misspelt systemd key is accepted silently and is
visible nowhere else), and `systemctl list-timers 'jahjah-*'` shows a real `NEXT` for all six timers
including the pre-existing dispatcher.

**Failure law, all five:** three consecutive failed runs and the job runs `systemctl disable --now`
on its **own** timer and publishes `ALERT-<job>-disabled.md`. It does not come back on its own or
after a reboot. A clean run resets the counter.

## 2. First-run proof

Each job was started by hand **after** its timer was installed, so both the unit and the publish path
are proven. All five exited 0 with `Result=success`.

| Job | Started | Took | Outcome | Output landed at |
|---|---|---|---|---|
| `jahjah-backup` | 21:00:28Z | 3s | **1,483,988 bytes, 95 tables, verified** | `/root/backups/nightly_20260831-210028.sql` · log `/opt/jahjah/backup.log` |
| `jahjah-scan-trivy` | 21:00:44Z | 36s | 5 targets scanned | `SCAN-trivy.md` · log `/opt/jahjah/scan-trivy.log` |
| `jahjah-scan-gitleaks` | 21:01:33Z | 4s | **0 hits across 141 commits** | `SCAN-gitleaks.md` · log `/opt/jahjah/scan-gitleaks.log` |
| `jahjah-retention` | 21:01:49Z | 2s | 1 folder, 3 dated reports, 0 pruned (under the cap) | log `/opt/jahjah/retention.log` |
| `jahjah-health` | 21:03:13Z | 3s | **verdict OK**, 6 jobs in the ledger | `HEALTH-daily.md` · log `/opt/jahjah/health.log` |

**Two paths the first runs could not exercise were tested deliberately**, because an untested
failure path is not a safety net:

- **Retention's pruning branch** had nothing to prune (3 reports, cap 10). The real `retention.sh`
  was run against a throwaway git repo seeded with 14 dated reports and 7 standing files. Result:
  exactly the 4 oldest pruned, all 10 newest kept, **every standing file and both `ALERT-*` files
  untouched**, `INDEX.md` rebuilt — and a pruned report still readable via `git show`, which is the
  claim that makes pruning safe at all.
- **The 3-strike self-disable** was driven to its cap with a fake job. Counter went 1 → 2 → 3, the
  timer-disable fired, `ALERT-faketest-disabled.md` was written, committed, pushed and indexed, and
  a subsequent good run reset the counter to 0.

## 3. Relay conventions

**Standing files are overwritten in place; git history is their archive.** They never accumulate, so
each question has exactly one current answer: `INDEX.md`, `HEALTH-daily.md`, `SCAN-trivy.md`,
`SCAN-gitleaks.md`, `HEARTBEAT-dispatcher.md`, `ALERT-*`.

**`INDEX.md` — one row per file with its UTC time and a one-line summary — is rebuilt by every
publisher**, so the strategist never needs the rate-limited GitHub contents API. It is regenerated
**from what is on disk**, never patched row by row, so a file pruned by retention or added by hand is
reflected without anyone remembering to. A file supplies its own summary via an `<!-- index: … -->`
comment; failing that the generator falls back to its `#` heading.

**The shared relay lock — the hazard that had to be designed around.** Six writers now share one
clone: the five jobs above and the dispatch lane. Publishing begins with `git reset --hard
origin/main`, which **silently destroys a file another job has written but not yet pushed**, and two
simultaneous pushes race into a non-fast-forward. `flock` on `/opt/jahjah/relay.lock` is now taken
before the sync and held across write → commit → push. **The pre-existing dispatcher was patched to
take the same lock** (and to rebuild `INDEX.md` when it publishes); it has since republished its
heartbeat cleanly under the new locking.

`/opt/jahjah/lib/jahjah-common.sh` is the shared half of every job: logging, kill switch, per-job
lock, relay lock, **secret redaction before anything is published**, the index generator, and the
failure law.

## 4. Versions pinned

| Tool | Version | Where |
|---|---|---|
| trivy | **0.74.0** | `/usr/local/bin/trivy`, official GitHub release, **sha256 verified against the release checksum file** |
| gitleaks | **8.30.1** | `/usr/local/bin/gitleaks`, official GitHub release, **sha256 verified** |
| semgrep | **1.175.0** | CI only — pinned container image. Not installed on the box. |
| gitleaks (CI) | 8.24.3 | whatever `gitleaks/gitleaks-action@v2` pins internally — **note it differs from the box's 8.30.1** |

## 5. The PR

**[#75](https://github.com/obidex/jahjah-internal/pull/75) — `ci: secret + static-analysis gates; serve automations registry`** · squash-merged as `6e51487`.

**Two blocking CI gates**, both running in parallel with `checks` (neither needs a database or any
secret of its own; only `sql` and `e2e` stay serialized on the shared DB):

- `secret-scan` — `gitleaks/gitleaks-action@v2`, `fetch-depth: 0`. A secret deleted in a later commit
  is still in the repository and still has to be rotated, so the scan needs real history.
- `sast` — `semgrep/semgrep:1.175.0`, ruleset `p/ci` (17 curated rules, not the full registry),
  `--severity ERROR --error`. `semgrep scan` exits 0 even with findings; `--error` is what makes a
  finding fail the job.

**Both were proven before being made blocking**, so neither could land red on arrival: gitleaks found
0 across both repos' full history, semgrep found 0 ERROR over 700 files, and the semgrep gate was
confirmed to actually **fail** (exit 1) on a deliberate `eval(req.query.code)`.

**Docs mirror — closes P2 finding F7.** `docs/runbooks/automations.md` joined the canon allowlist,
with its `outputFileTracingIncludes` entry and the manifest script's mirror list **in the same
change** — a runtime `fs.readFile` is invisible to Next's dependency trace, so an allowlisted but
untraced file 404s in production while working perfectly in `next dev`.

Tests assert **both halves**: the registry serves, *and* `backup.md`, `testing.md` and the pitfalls
files still 404 — serving one runbook must not turn the directory into a prefix lane. Both were
**sabotage-checked**: dropping the file from the allowlist reddens 4 assertions; allowlisting it
without the tracing entry reddens the tracing mirror.

**CI:** post-merge `main` run
[33445041331](https://github.com/obidex/jahjah-internal/actions/runs/33445041331) — **all 5 jobs
green** (checks, secret-scan, sast, sql, e2e).

**Production verified after deploy** (`6e51487`, no auth, from the public domain):
`/internal/docs/docs/runbooks/automations.md` → **200, 22,214 bytes**, all five jobs present;
`canonCount` now **8**. And the negative half: `docs/runbooks/backup.md`, `docs/runbooks/testing.md`,
`docs/pitfalls/infra-vps.md`, `docs/pitfalls/docs-mirror.md` and `.env.local` all → **404**.

**Disclosure check before publishing that file:** no IP literals, no credentials, no VPN peer or
person names, gitleaks clean.

## 6. Registry state

`docs/runbooks/automations.md` §2 now carries all six live jobs — the dispatcher plus the five built
here — each with what/why/schedule/where/how-to-see/heartbeat/kill-switch and its cap table. §3 is
new: the relay publishing conventions including the shared lock. The four "planned P3 candidates" are
marked built; **the one genuinely unbuilt candidate is a CI-red watcher**. One new trap was added
(§5.5, found the hard way — see findings).

-----

## 7. Findings

**F1 — `HEALTH-daily.md` reported a healthy job as dead on its first run (fixed in session).**
`systemctl show -p NextElapseUSecRealtime` is **empty for a monotonic timer** — one built on
`OnBootSec`/`OnActiveSec`/`OnUnitInactiveSec`, which is exactly how the dispatch lane is built —
because its next elapse lives in `NextElapseUSecMonotonic`. Reading only the realtime property
labelled the perfectly healthy dispatcher **NOT ARMED**. Now reads
`systemctl list-timers --output=json`, whose `next` is a plain microsecond epoch correct for both
kinds, and checks "running right now" first (a monotonic timer legitimately has no next elapse
mid-run). Recorded as trap §5.5. **A monitor that cries wolf daily is worse than no monitor.**

**F2 — the gitleaks CI job failed on a permissions error, not a scan error (fixed in session).**
First run failed in 8s with `403 Resource not accessible by integration`. Cause: the action asks the
API for the PR's commit list to decide its scan range, and the job's `permissions:` block granted only
`contents: read`. Fixed by adding `pull-requests: read`. Worth recording because the failure **looks
like a scan failure**. The log also settles the licensing question: *"[obidex] is an individual user.
No license key is required."*

**F3 — the repo's own npm dependencies carry 9 HIGH advisories. Needs a decision.**
0 critical. Named: `next` **16.2.9** → fixed in **16.2.11**; `nanoid` 3.3.12 → 3.3.18. A dependency
bump needs approval (CLAUDE.md §11D) and was **not** taken here. `next` is the framework, so it is a
real upgrade rather than a lockfile nudge.

**F4 — most of the box's critical CVE count belongs to what looks like unused images.**
Of 15 criticals, **14 are in `postgres:17`** and 1 in `portainer-ce:lts`. Neither `postgres:17` nor
`hello-world:latest` is running — only `portainer` is. If those two images are leftovers, deleting
them removes 14 of the 15 criticals without changing anything that runs. **Not deleted: destructive
and not authorised in this prompt.**

**F5 — `docs/reference/app.md` is stale on `main`, and predates this session.**
Regenerating it produces a diff in i18n key counts only (`attributes` 83→79, `categories` 67→66,
`procurement` 458→448, `products` 131→129). It was committed already-stale in `def4db0` (the S4
cuts) — that commit changed those i18n files and `app.md` in one go, with counts that disagree.
**Not bundled** into this PR (CLAUDE.md §11E, one thing at a time); it wants its own trivial task.
Note this file **is** publicly served, so it is publishing four wrong numbers today.

**F6 — the automation code itself is not under version control.**
`/opt/jahjah/lib/` and the five job directories exist only on the box. If it is lost, so are they —
`/root/backups` holds database dumps only. The registry documents them but is not a copy. Worth a
follow-up on where these scripts should live.

**Pilot rule (CLAUDE.md §17):** no reversion. No output failed the review/CI bar for its class.

**Tier note:** treated as **Tier 2** — the code change touches no schema, auth, RLS, secret, or money
path; it is an additive entry to an existing, well-tested allowlist plus two CI jobs. Reviewed
thoroughly single-pass rather than with the four-agent panel. Flagging the classification so it can
be overruled.

-----

```
=== RELAY ===
HEAD: 6e51487 | tree: clean
CI: pass — post-merge main run 33445041331, all 5 jobs green (checks, secret-scan, sast, sql, e2e)
    https://github.com/obidex/jahjah-internal/actions/runs/33445041331
DONE: 5 jahjah-* jobs built (backup/health/scan-trivy/scan-gitleaks/retention) — units verified, timers armed, each run once and publishing
DONE: relay conventions — standing files overwritten in place + INDEX.md rebuilt by every publisher; shared relay lock retrofitted to the dispatcher
DONE: PR #75 merged — gitleaks + semgrep blocking CI gates, automations registry on the public docs mirror (P2 F7), production verified 200 + negatives 404
FILES: 7 in-repo (ci.yml, automations.md, next.config.ts, generate-docs-manifest.mjs, docs registry + 2 test files); ~17 on the box under /opt/jahjah (lib/ + 5 job dirs + units in /etc/systemd/system)
FINDINGS/BLOCKERS: F1 monotonic-timer false "NOT ARMED" (fixed) · F2 gitleaks CI needed pull-requests:read (fixed) · F3 9 HIGH npm advisories incl. next 16.2.9->16.2.11, needs approval · F4 14 of 15 criticals are in the apparently-unused postgres:17 image, deletion not authorised · F5 docs/reference/app.md stale on main since def4db0, publicly served, not bundled · F6 automation scripts are not in version control
NEXT-NEEDED: decide F3 (bump next/nanoid?) and F4 (delete unused postgres:17 + hello-world images?) — both need explicit authorisation
=== END ===
```
