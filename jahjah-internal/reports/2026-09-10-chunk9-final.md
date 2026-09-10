DONE — chunk 9 merged as `75c474c`. The SQL suites now run in CI's throwaway database, not the shared live one; their live run is a nightly job that has already passed 28/28; migration chunks can start from the label.

<!-- index: chunk 9 final — MERGED 75c474c; suites hermetic 26/28 (2 excluded, D240 a), nightly live 28/28, D239 label-started migrations, shellcheck warning 0 -->

**For the owner, in one paragraph.** Until today CI tested the database against the one shared live
copy, so anybody's leftover rows could turn every pull request red for no reason. The tests now run
in the throwaway copy CI already builds from the repository: no secrets, no shared state, and they
now run for Dependabot's update PRs too. The live check didn't go away — it now runs every night at
03:15 UTC from the work engine and posts one page (`SQL-live.md`); its first runs passed 28 of 28.
Moving the tests found **two real defects in what the repository builds** (live is fine): a fresh
database gives one administrator role four fewer permissions than live, and its first shipment
number collides with a demo row. Both are pinned so CI names them on every run. Fixing them is a
one-file change each, which this chunk was not authorised to make — **that is the next thing to
approve**, and until it lands two permission test suites run only nightly, not before a merge.

## Verified, with numbers

| | |
|---|---|
| Merge | **`75c474c`** (squash, PR #113) |
| PR CI | **green on every job, on all three commits** — final `f6eac5b`: `ci-ok` ✓ · `replay` 53 s · `checks` 1 m 10 s · `types` 1 m 16 s · `e2e` 6 m 22 s · `shell-lint` 13 s (runner: *13 refusal fixtures, 2 controls, 28 committed suites checked*) · gitleaks · semgrep · `tier3-guard` |
| Vercel production | **deployed `75c474c`** (commit status `success`) |
| Post-merge `main` CI | **GREEN (run 34509353856, `ci-ok` success, e2e included)** |
| `replay` job | **44 s → 53 s**, now carrying the suites: *26 run, 26 passed, 0 failed, 2 excluded (of 28)* |
| Whole CI run, wall clock | **10 m 44 s → 8 m 14 s** — `e2e` starts after `checks` (81 s) instead of after `sql` |
| Hermetic suites | **26 / 28**. Excluded, by name, reason printed every run: `activity_log_tests`, `user_management_tests` — **D240 finding (a)** |
| As-is before the harness | **2 / 28** (23 lacked a live member, 2 read the old claims form, 1 hit finding (b) first) |
| `jahjah-sql-live` first hand run | **28 / 28 PASS against live in 12 s** · report on the relay (`SQL-live.md`) |
| `jahjah-sql-live` fired by its own timer | **yes** — 17:04:00 UTC via a temporary drop-in, 28/28 in 11 s; drop-in removed, `NEXT` back to 03:15 UTC |
| Hardened nightly (after both panel rounds) | **28 / 28 PASS**, migrations **in step** — all 78 on `main` applied on live, none on live that `main` lacks |
| `HEALTH-daily.md` | OK, **0 attention items**, 13 jobs, lists `jahjah-sql-live` by name |
| Scratch heartbeat on the relay | **gone** (relay `976c29f`); no scratch timer, unit, state or parameter file on the box |
| `chunk5-t1-lane-fixes` | **deleted** (4 commits not on `main`; tip `b0e0f42` recorded) |
| `docker image ls` after | `supabase/postgres:17.6.1.167` (replay) · `postgres-meta:v0.96.1` (types) · `portainer-ce:lts` (running). `hello-world` / `alpine:3` were **already absent**, and nothing references them |
| `shellcheck --severity=warning` | **0 findings** over 22 scripts (30 before; triaged — fixed or a line-level disable with a reason) |
| Repo ↔ box drift | **0** after deploying every changed script and unit |
| Collaborators | `obidex` only · 0 pending invitations |

## Part B — migration chunks from the label (`D239`)

`docs/STRATEGIST.md` "Two starts, one plan" now carries the owner's text verbatim. **The GATE-1 hook
fires inside the dispatched lane** — chunk 8's database smoke (#110) was dispatched and refused
there — and re-checked today it prints the **bare** hash, never the text its check searches for, so
no scratch-chunk smoke was needed. **Not yet measured:** whether a dispatched chunk can write its
pre-migration dump into `/root/backups` — the first `D239` migration chunk proves it in preflight.

**Every corrected "no database lane / migrations are pasted" line:**
`docs/STRATEGIST.md` (Two starts; the PASTES list; GATE 2's "504 on the `sql` job") ·
`docs/STATE.md` (Chunk lanes row; Dispatched-chunk limits row; the chunk-6 ledger row, annotated) ·
`docs/runbooks/backup.md` §3 · `docs/runbooks/automations.md` (the lane's DB-lane paragraph) ·
`docs/pitfalls/infra-vps.md` · `infra/vps/web-dispatch/README.md`. `D233`'s own heading is
append-only and is superseded by `D239`, not edited.

## What the review panel changed

Four seats, two rounds. Round one: all four `issues_found`, none a blocker. Round two on the fixes:
compliance **clean**, the other three `issues_found` at medium — all fixed in the same PR. No finding
in either round breaks money math or discloses money, so under the bar the panel closed after round
two.
- **Append-only coverage restored before merge** — the replay postlude now reads `activity_log`'s
  access list exactly (column grants included) and refuses any policy but SELECT, so a migration that
  let a signed-in user rewrite, delete or forge log rows goes red. Sabotage-proven with the reviewer's
  own forge repro.
- **The nightly's safety check read lines, and a top-level `end;` (which commits) walked past it.**
  It now reads SQL (escape strings and mid-line psql commands included), and CI checks it on every
  PR: 13 refusal fixtures, 2 controls, all 28 suites accepted.
- **The server now enforces the nightly's time limits**, so a stuck test can't keep holding locks on
  live after the job gives up.
- **"Live ahead of `main`"** — the window between applying a migration and merging it — is reported
  and not counted, so a correct system can't trip the breaker.
- **My own triage had a bug that raising the bar caught:** a `local` at the top level of the website
  job, which passes `bash -n` and fails at run time.

## Open, and recommended next

- **The seed-only rider (`D240` (a) and (b))** — two one-file seed fixes; restores 28/28 and puts
  the rank rules and the append-only suite back in front of every merge. Needs a plan that names
  `supabase/seed/`.
- **Flagged, not edited (outside this chunk's paths):** `.claude/skills/new-table-with-rls/SKILL.md`
  still tells a session to add a step to the deleted `sql` job · `docs/ROADMAP.md`'s transport-retry
  register line names the same job · `docs/STATE.md`'s header and ledger row are the strategist's.
- User separation for chunks (`D231`) — unchanged, trigger unchanged.

```
=== RELAY ===
HEAD: 75c474c (main) | tree: clean
CI: PR #113 ci-ok GREEN on every job · post-merge main GREEN (run 34509353856, `ci-ok` success, e2e included)
DONE: A — suites hermetic in replay (26/28; 2 excluded for D240(a); D240(a)(b) pinned OPEN), `sql` job deleted, e2e needs checks only + purges first, jahjah-sql-live nightly installed, hand-run 28/28 vs live and timer-fired 28/28; B — D239 recorded, STRATEGIST text replaced verbatim, every stale no-DB-lane/pasted-migration line corrected, GATE-1 hook confirmed in the dispatched lane with the bare hash; C — relay scratch heartbeat removed, chunk5-t1-lane-fixes deleted, docker already clean, shellcheck --severity=warning at 0 (30 triaged), STATE line on user-level settings. Panel: two rounds.
FILES: 33 changed (+1440 −280) across 3 commits; new: infra/vps/sql-live/{sql-live.sh,README.md}, infra/vps/systemd/jahjah-sql-live.{service,timer}; D239 + D240
FINDINGS/BLOCKERS: (1) D240(a) — a from-scratch build under-grants the seeded Director by four default permissions; activity_log_tests + user_management_tests excluded from CI and gate no merge until the seed rider (stated in canon), still run nightly vs live. (2) D240(b) — a fresh build's first shipment reference collides with a seeded demo row; pinned. (3) The panel found the nightly's first shape guard passed a top-level `end;` — replaced by an SQL-aware scanner. (4) Raising shellcheck caught a run-time-only bug in this chunk's own triage. (5) Stale mentions of the deleted `sql` job remain in a committed skill and the ROADMAP register — flagged, outside this chunk's paths. (6) Shellcheck count was 30, not 29. (7) D240 was WRITTEN BY THE EXECUTOR, not supplied by the plan — the plan named D239 only, and the hermetic-suite decision plus its two findings needed a register home; the strategist may fold or supersede it. STATE.md edits were limited to the C5 line and the corrections the plan ordered — its header and ledger row are left to the strategist.
NEXT-NEEDED: approve the seed-only rider for D240 (a)+(b) — recommended next; it restores 28/28 and pre-merge gating of the rank rules and the append-only log.
=== END ===
```
