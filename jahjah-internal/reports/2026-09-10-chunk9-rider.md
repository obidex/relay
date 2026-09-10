DONE — chunk 9 rider merged as `8e4715a`. Today's reds are all explained, a green job can no longer show red without failing CI, and both repository defects D240 found are fixed: all 28 database test suites now run in CI.

<!-- index: chunk 9 rider — MERGED 8e4715a; today's reds explained; ci-ok fails on any failure-level annotation; D240 (a)(b) CLOSED by seed fixes; 28/28 hermetic -->

**For the owner, in one paragraph.** Every red you could have seen today came from chunk 8's work
this morning, before 10:00 UTC. One was a real test failure that was fixed the same morning. One was
leftover test data in the shared database — the problem chunk 9 exists to remove. Three were runs
GitHub cancelled on purpose because a newer push replaced them. This rider's own first run was also
red twice this evening — a Supabase outage in the path CI uses to reach the database, which cleared
without any change on our side. **No passing job carried a red error today**; the "five errors on a green job" came from a miscount in my own earlier note, and I have
corrected it here. CI now refuses to go green if any job shows a red error, so that situation cannot
arise unnoticed. Separately, the two defects chunk 9 found in how the repository builds a fresh
database are fixed. A fresh database now matches live exactly on every built-in role's permissions,
and **all 28 test suites run before every merge**, including the two that were only running nightly.

## Every red of today, with its cause

12 CI runs today; 33 annotations (25 failure, 7 notice, 1 warning). Every failure-level annotation
sits on a job that failed or was cancelled.

| Run | Time (UTC) | What was red | Cause |
|---|---|---|---|
| `34459039012` · chunk 8 `0ea6762` | 09:09 | `shell-lint` **failed** (5 "quota classifier … gave ''" + exit 1), `e2e` cancelled, `ci-ok` | a self-test guard refused to run under systemd — and a CI runner *is* systemd. Fixed in chunk 8's next push |
| `34460697922` · chunk 8 `f903d0d` | 09:27 | `sql` **failed**, `ci-ok` | E2E fixture debris in the shared database (`23503`). Structurally impossible in CI since `D240` removed the `sql` job |
| `34459493612` · `34459884654` · `34463664406` · chunk 8 | 09:13 · 09:18 · 09:59 | `e2e` **cancelled**, `ci-ok` | superseded by a newer push (`cancel-in-progress`) — **by design, not a failure** |
| `34511403771` · this rider `03bd95b`, attempt 1 | 17:59 | `e2e` **failed** in its first step, `ci-ok`, merge BLOCKED | `HTTP 401 Unauthorized` from the Supabase Management API: the CI secret `SUPABASE_ACCESS_TOKEN` was refused by the fixture purge. Not this diff — the identical step authenticated at 17:29 and 17:37. **The re-run (attempt 2, 18:01) failed the same step with HTTP 504**, a gateway timeout rather than a refusal. Meanwhile our database answered a direct connection in 1 s, and Supabase's status page reported its **API Gateway degraded** with "Unresponsive Projects" open. **Cause: an external Supabase gateway outage, not a dead token and not a repository defect.** Not "fixed" with a retry: the canon forbids the transport retry on this committing step. **Resolved by recovery, not by a change:** once the gateway was back, the next run's purge returned HTTP 201 and the run went green on every job. |

**The premise "5 error annotations on the green shell-lint job" was wrong, and the source was me.**
That figure was my own `grep -c '::error::'` over a job log that echoes the step's script, and the
script contains five `::error::` strings. The annotations API — the ground truth — shows **zero**
failure-level annotations on every green job today. The five real ones the owner most likely saw
belong to chunk 8's *red* 09:09 job. Nothing in the workflow prints `::error` at line start on a
passing path, so there was no line to wrap.

## The gate — a green job may not show red (`D241`)

`ci-ok`'s last step reads every job's annotations through the API and **fails on any failure-level
one**. It has `actions: read, checks: read` on that job only. Its filter is fixture-tested before it
touches the run (green 0 · red 1 · empty 0), and it **fails closed** if it cannot read the run.
**Proven on real data before shipping:** red against run `34459039012` (8 failure annotations,
listed), green against run `34509353856` (8 jobs, 0). **The rider PR's own run:** **0 failure-level annotations on all 9 jobs** — the only annotation in the run is e2e's `69 passed` notice — and `ci-ok`'s gate printed *self-test green 0 · red 1 · empty 0* then *8 jobs, no failure-level annotation*. The
rule is recorded in `docs/runbooks/testing.md` §2: an error annotation on a passing job is a defect.

## D240 (a) and (b) — CLOSED, no migration

| | |
|---|---|
| (a) seeded Director short of 4 default keys | **fixed in `seed/03_roles.sql`** — the migration's eleven keys granted after the catalog exists, **only in the fresh-build state** (the Director holds none of the four seed-created keys), so a later trim on live is never silently undone |
| (b) first minted shipment reference collides with a demo row | **fixed in `seed/21`** — the sequence advanced past SHP-0004, as seed 18 already did for its rows; conditional, so it never runs on live |
| Pins and exclusions | both pins **deleted** as their messages instructed; both exclusions **dropped** |
| Fresh build | **28 run, 28 passed, 0 excluded** (~28 s) |
| Fresh build vs live, every seeded role's grants | **63 = 63, diff empty** — Director 11 keys in both |
| Live, read-only (no write) | Director holds exactly the eleven keys · shipment counter **5488**, past the demo row |
| Register | **`D241`** records D240 (a) and (b) CLOSED |

## Verified

| | |
|---|---|
| Merge | **`8e4715a`** (squash, PR #114) |
| PR CI | **green on every job** on the final commit `beb3d89` (run `34512096823`): `ci-ok` ✓ · `replay` 51 s (*28 run, 28 passed, 0 failed, 0 excluded*) · `checks` 1 m 19 s · `types` 1 m 16 s · `e2e` 7 m 41 s · `shell-lint` · gitleaks · semgrep · `tier3-guard`. The first commit's run was red twice on the Supabase gateway outage (see the table above) |
| Post-merge `main` CI | GREEN (run 34513107507, ci-ok success, e2e included, 0 failure-level annotations; Vercel production deployed 8e4715a) |
| Panel (one seat, pg-semantics) | `issues_found`, one medium, **fixed**. The live apply re-runs every seed with no signed-in user, so an unconditional Director grant would silently hand back any key the Owner trimmed — past the apex-only check and with no audit row. The seed now grants only in the fresh-build state. **Sabotage-proven:** a one-key trim survives a re-seed (Director stays 10); removing all four returns it to 11. The seat's ship-level note is taken too: seed 21's `setval` fires only below 4, so it never runs on live (counter unchanged at 24 on re-seed). The residual — removing all four keys at once would be undone — is recorded in `D241` |
| Repo ↔ box drift | **0** — the nightly reinstalled from `main`; 22 files and 26 units compared; its guard's self-test accepts a committed suite |

```
=== RELAY ===
HEAD: 8e4715a (main) | tree: clean
CI: PR #114 ci-ok GREEN on every job (final commit beb3d89, run 34512096823) · post-merge main GREEN (run 34513107507, ci-ok success, e2e included, 0 failure-level annotations; Vercel production deployed 8e4715a)
DONE: Today's 5 non-success runs explained (1 shell-lint failure fixed in chunk 8, 1 sql debris failure now structurally gone, 3 cancelled-by-supersession by design), plus this rider's own 2 red attempts (HTTP 401 then 504 from the Supabase Management API during Supabase's API Gateway outage — recovered, run green with no change); 0 failure-level annotations on any green job today — the "5 on green shell-lint" premise was my own log-grep miscount, corrected. ci-ok gate added: fails on any failure-level annotation, fixture-tested, fail-closed, proven red/green on real runs; rider run 0 failure-level annotations on all 9 jobs, gate ran and passed. D240 (a)+(b) CLOSED via seed/03 + seed/21 (no migration); pins deleted, exclusions dropped; fresh build 28/28 hermetic; fresh build = live on all 63 seeded-role grants; live confirmed read-only. D241 recorded.
FILES: 10 changed (+213 −150) across 2 commits: supabase/seed/03_roles.sql, supabase/seed/21_po_settlement_examples.sql, scripts/replay-check.sh, .github/workflows/ci.yml, docs (DECISIONS D241, STATE, testing.md, automations.md), infra/vps/sql-live/{sql-live.sh,README.md}
FINDINGS/BLOCKERS: (1) The rider's step-2 premise was false — no error annotation on any green job today; the number came from my own grep of an echoed script. No wrap was added because there was no line to wrap; the gate makes any future one fail. (2) Residual, recorded in D241: the live apply re-runs every seed with no signed-in user (no apex check, no audit row); the Director grant is now guarded to the fresh-build state, so only removing ALL FOUR seed-created keys at once would be undone by the next apply — and every other seeded role's grants in seed/03 already re-assert that way. (4) External: Supabase's API Gateway outage (18:0x UTC) failed the rider's first run twice in the e2e fixture purge (401, then 504); not retried by design (the purge commits); cleared on recovery. (3) Still open and flagged, outside this rider: .claude/skills/new-table-with-rls/SKILL.md and a docs/ROADMAP.md register line name the deleted `sql` job; the first D239 migration chunk must prove a dispatched pg_dump into /root/backups.
NEXT-NEEDED: none
=== END ===
```
