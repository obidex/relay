DONE — chunk 8 merged as `6718a45`; the lane is fixed and live, `reports/` is off the public mirror, and three guards that were refusing nothing are closed.

<!-- index: chunk 8 final — MERGED 6718a45; detective control, chunk breaker, quota/open-PR outcomes, working DB lane, reports off the mirror; D237 + D238; three guards found watching nothing -->

**In one paragraph for the owner.** The chunk lane now **records** who approved each chunk instead of
refusing anything; its breaker actually works; a usage cap is a queue problem rather than a crash;
and a chunk that leaves a pull request behind says so. The public documentation site has stopped
serving old session reports — the one that carried 22 lines of infrastructure detail now returns 404.
A dispatched chunk can reach the database properly for the first time. `/relay-report` is installed
and carried this chunk's own reports. **The most valuable findings were not on the plan:** three
separate guards turned out to be **refusing nothing while blocking real work**, and one of them was
*authorising the very files it refused*.

## Merged and verified

| | |
|---|---|
| Merge commit | **`6718a45`** (squash, PR #111, 10 commits) |
| Pre-merge `ci-ok` | **green — all 10 jobs** |
| Post-merge CI on `main` | **green** |
| Vercel production | **serving `6718a45`** |
| Mirror: the four reports | **404, all four** |
| Mirror: canon | **200** |
| Mirror index | count **8**, `reportCount` **absent**, no report entries, `canon` lane only |
| 404 body | identical to any other rejection — **no oracle** |
| Repo↔box drift | **nothing** |
| Both lane timers | armed, polling clean |

## The three guards that were watching nothing

**1. The publish-before-apply guard was authorising the files it refused.** Its refusal message ended
with the exact text the check searches for. A report that quotes its refusal — *which is what our own
rule tells a blocked report to do* — published that text, and the next run then allowed the file it
had just refused. **This was not theoretical: it happened by accident inside this chunk's own
database test.** Both halves now print the bare hash.

**2. The same guard had never seen the apply command in our own runbook.** It was written across
continued lines and the guard read each line separately, so no line carried both the command and the
filename. Fixed — then fixed again when the review panel showed my first fix had opened a *different*
hole (an even number of trailing backslashes merges two commands into one).

**3. A self-test guard I added refused to run under systemd** — meant to keep it out of a unit. It
never did that, but a CI runner *is* systemd, so it silently blocked the only place that exercises
the usage-cap classifier. CI caught it on the job's first run.

**The pattern is the finding**, and it is now the standing lesson in `D238`: a guard that stops work
without stopping harm is worse than no guard, because it looks like it is working.

## What shipped

| | |
|---|---|
| **Approval-window check** | **RETIRED**, never installed. Twelve panel findings; two settled it — one refused issue **wedged the lane**, and clearing a refusal needed a person |
| **Its replacement** | A record, not a refusal. Every dispatch writes who approved it and whether a chunk of *either* lane was running. **Proven live** on the real lane |
| **Chunk breaker** | Fixed. It could never trip: the poll that booked a failure erased it one second later |
| **Usage cap** | Re-queued, not counted. The classifier is forgery-resistant (a chunk cannot fake it in the mode we run) with five CI fixtures |
| **Open pull request** | Ends a chunk `blocked` with the PR number and CI state, not `failed` |
| **Console log** | A work log: 61 KB in a minute where a whole 810-second chunk once left 439 bytes. Still never published |
| **Database lane** | Working. The password sits in a file the client library reads — never on a command line, never in the environment |
| **Public mirror** | `reports/` gone. `D234`(11) **CLOSED** |
| **`/relay-report`** | Installed; used for this chunk's own reports and by dispatched chunk #110 |
| **CI** | `shell-lint`, plus an E2E-fixture purge that makes a cancelled run unable to red the next one |

## The CI failure, and the habit it cost

A `23503` foreign-key error failed the `sql` job on **every** branch, `main` included, on a diff
containing no SQL. It was **E2E fixture debris**: two purchase orders left behind when
`cancel-in-progress` killed a Playwright run before its teardown. Purged with the **designed** script,
never a hand-written delete; read back **0** and **0**, seeded data intact, and the script proven
idempotent by running it twice.

**The structural fix ships with it:** the `sql` job now purges as its **first** step, so a cancelled
E2E run can never red the next one. It earned a step rather than a runbook note because *the job that
breaks is not the job that caused it, and the branch that goes red is not the branch that did it.*

**The diagnostic habit is the part worth keeping.** My first hypothesis was checked and was wrong. My
second position — "I cannot determine this without database access" — was honest but incomplete: the
answer was one query away once the credential existed. **Do not stop at "not mine"; find the row.**

## What the review panel changed

Four seats, then three rounds on the fixes. Beyond the two blockers above it caught: the cap
classifier **sniffing its own mode from the log it was judging**; the redactor being **blind to the
new secret's shape** (a password-file line has no variable name, so a pasted line would have reached
the public relay intact); a **one-way latch** that would have parked the lane after three capped
issues; **two disclosure leaks I had just introduced**; and four destructive prompts deleted where the
canon named two — three stay gone, **`git clean` is now denied** because it removes the environment
file *and* the guard's registration in one command.

## Residuals

- **User separation for chunks** (`D231`) — unchanged, still the real fix, trigger unchanged.
- **Auto mode's settings are user-level and describe the *website* project** — this project appears in
  them zero times. Owner's call; that file is shared and not this chunk's to edit.
- **`docs/STRATEGIST.md` still says a dispatched session has no database lane** — strategist-owned;
  reported, not edited.
- **`npm run` is a shell no hook can see** — recorded off-mirror beside the existing `node -e` note.
- `chunk5-t1-lane-fixes` is superseded and can be deleted.

```
=== RELAY ===
HEAD: 6718a45 (main) | tree: clean
CI: pre-merge ci-ok GREEN (all 10 jobs) · post-merge main GREEN · Vercel production serving 6718a45
DONE: Approval-window check RETIRED, replaced by a detective record proven live on the real lane. Chunk breaker fixed (it could never trip). Usage cap re-queued not counted, classifier forgery-resistant with 5 CI fixtures. Open-PR outcome. stream-json work log. Dispatched-chunk DATABASE LANE working (owner decision A) — password in a mode-600 file libpq reads, non-secret PG* exported by the poll, verified end to end. reports/ OFF the public mirror: all four 404, canon 200, index 8/canonCount 8/reportCount absent, 404 body identical (no oracle) — D234(11) CLOSED. /relay-report installed and used. shell-lint CI job + an E2E-fixture purge step. D237 + D238 written. Owner rider items 1, 2 and 3 all complete.
FILES: 24 changed across 10 commits; new: infra/vps/session/write-pgpass.sh, .claude/skills/relay-report/SKILL.md, 2 CI jobs/steps.
FINDINGS/BLOCKERS: (1) THREE GUARDS WERE REFUSING NOTHING WHILE BLOCKING REAL WORK — the publish-before-apply guard AUTHORISED the files it refused (its refusal message carried the exact string its check greps for, and a report quoting a refusal — which D223 requires — published it; it happened by accident inside this chunk's own smoke); the same guard had never seen the apply command written in our own runbook (backslash continuations read line by line); and a self-test guard blocked CI while protecting nothing. All three closed. (2) My first fix for the second one OPENED a parity evasion — caught by the panel, fixed by classifying both views. (3) jj_redact was blind to the new secret's shape; one rule, fixture-proven. (4) The sql job was red on EVERY branch from E2E fixture debris a cancelled run left in the shared DB — purged with the designed script, and the job now purges as its first step. (5) Auto mode pins the mode, not its settings; those are user-level and describe the WEBSITE project. Canon corrected; owner's call. (6) bypassPermissions is refused for root — measured before anything was changed. (7) git clean promoted to deny: it removes .env.local AND the guard's registration in one command.
NEXT-NEEDED: none. Next planned step is the UI/UX rebuild spec session (docs/STATE.md §5).
=== END ===
```
