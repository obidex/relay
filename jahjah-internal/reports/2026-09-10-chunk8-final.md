# Chunk 8 — the lane is fixed and installed; two guards turned out to be watching nothing; GATE 2 is HELD on a red `sql` job that is not this chunk's diff

<!-- index: chunk 8 final — detective control, chunk breaker, quota/open-PR outcomes, DB lane, reports off the mirror, D237+D238; MERGE HELD: the shared dev DB has an orphan row failing catalog_supplier_tests on every branch -->

**In one paragraph for the owner.** Everything this chunk set out to do is written, installed and
running on the work engine: the chunk lane now **records** who approved each chunk instead of
refusing anything, its breaker actually works, a usage cap is treated as a queue problem rather than
a crash, a chunk that leaves a pull request behind says so, and the public documentation site has
stopped serving old session reports. `/relay-report` is installed and was used for this chunk's own
reports. **The two most valuable findings were not on the plan:** the publish-before-apply guard was
*authorising the files it refused*, and it had never once seen the apply command written in our own
runbook. Both are fixed. **The merge is HELD, and not for anything in this chunk's changes:** the
shared development database has a leftover row that makes one SQL test suite fail on **every**
branch, `main` included. It needs one delete, and I cannot reach the database — which is the same
gap your rider's second item exists to close.

## THE TWO THINGS THAT NEED YOU

**1. The database credential** (rider item 2). My session is not permitted to create a credential
file, and I did not route around that.

```
/opt/jahjah/session/write-pgpass.sh
/opt/jahjah/session/write-pgpass.sh --verify
```

`--check` proves the file's shape; only `--verify` proves the credential actually authenticates.

**2. Then the database cleanup that is holding the merge.** Once the credential exists I can
diagnose and fix it myself, with your approval for the delete. The diagnostic is read-only:

```sql
select poi.id, poi.po_id, po.po_number, po.status, po.created_at
from public.purchase_order_items poi
left join public.purchase_orders po on po.id = poi.po_id
where poi.product_id = '00000000-0000-4000-8000-0000000000f1'
order by po.created_at desc nulls last;
```

**Any row this returns is test debris.** No seed creates a `purchase_order_items` row against that
product — seed 21 uses free-text descriptions and never references it — so a legitimate row of this
shape does not exist. The fix is to delete those rows and the empty test purchase orders they belong
to. **That is a destructive change and it waits for your explicit approval** (§11H).

## The blocker, stated precisely

| | |
|---|---|
| **Error** | `23503` foreign-key violation |
| **Where** | `catalog_supplier_tests.sql`, its cascade-delete block. The three suites before it pass |
| **Cause** | a `purchase_order_items` row still references the example product, so the suite's `delete from products` cannot proceed |
| **Deterministic?** | **Yes** — failed twice, including a clean re-run of just that job |
| **In this chunk's diff?** | **No.** The diff contains no SQL, no migration and no test suite — verified by file list |
| **Scope** | the `sql` job runs the same suites against the one shared dev database, so **`main` will fail identically** |

**On cause, I will not claim more than I know.** My first hypothesis — that I cancelled a job
mid-suite by closing a scratch pull request — **was wrong, and I checked rather than asserted it**:
that run completed, `sql` included, at 07:47. Today's `sql` job then succeeded three times (09:10,
09:15, 09:20) and failed at 09:31 and on re-run. Something changed the database between 09:22 and
09:31; the commit I pushed in that window touched no SQL. **I cannot close the gap without database
access.** It is most likely a suite that passed while leaving a row behind, and the diagnostic above
is what settles it.

## What shipped, and is live on the box now

| | |
|---|---|
| **Approval-window check** | **RETIRED**, never installed. Its own panel returned twelve findings; two settled it — one refused issue **wedged the lane**, and clearing a refusal needed a person |
| **The replacement** | A **record, not a refusal**: every dispatch writes who approved it and whether a chunk of *either* lane was running. Refuses nothing, nothing to clear. **Proven live** — the real ERP lane dispatched #110 and named another lane's open windows in its own comment |
| **The chunk breaker** | Fixed. It could never trip: the poll that booked a chunk failure erased it one second later. Its own counter now; at 3 the lane disables its timer |
| **A usage cap** | No longer a failure. Re-queued, not counted, and the lane waits. The lane cannot see the quota in advance, and the slot is still spent — both said plainly |
| **An open pull request** | Ends a chunk `blocked` with the PR number and its CI state, not `failed` |
| **The console log** | A work log: 61 KB in a minute, where a whole 810-second chunk once left 439 bytes. Still mode 600, still never published |
| **The database lane** | Finished per your rider — the password sits in a file the client library reads, never on a command line. **Awaiting the one command above** |
| **The public mirror** | Stops serving `reports/`. Of the four it served, **one carries 22 lines of infrastructure detail** — counted, not quoted |
| **`/relay-report`** | Installed; used for this chunk's own preflight, install and final reports, and by dispatched chunk #110 |
| **CI** | A `shell-lint` job, green. Nothing had ever looked at root-privileged shell on a two-minute timer |

## The two guards that were watching nothing

**The publish-before-apply guard was authorising the files it refused.** Its refusal message ended
with the exact text the check searches for. A report that quotes its refusal — *which is what our own
rule tells a blocked report to do* — published that text, and the next run then allowed the file it
had just refused. **This was not theoretical: it happened by accident inside this chunk's own
database test.** Fixed in both halves; the refusal now prints the bare hash.

**And it had never seen the apply command in our own runbook.** That command was written across
continued lines and the guard read each line separately, so no line carried both the command and the
filename. It allowed it silently. Fixed — then fixed again when the review panel showed my first fix
had opened a *different* hole.

**A third instance, for completeness:** a self-test guard I added refused to run under systemd,
meant to keep it out of a unit. It never did that — but a CI runner *is* systemd, so it silently
blocked the only place that exercises the usage-cap classifier. CI caught it on the job's first run.

**The pattern is the finding.** Three times in one chunk, a guard refused nothing while blocking
something real.

## The review panel

Four seats, then three more rounds on the fixes. **Two blockers, both mine, both above.** The rounds
that followed caught, among others: my continuation fix creating a **new** evasion (backslash parity
merges two independent commands); the cap classifier **sniffing its own mode from the log it was
judging**, defeatable by one oversized record; the redactor being **blind to the new secret's shape**
(a password-file line has no variable name in it, so a pasted line would have reached the public
relay intact); a **one-way latch** that would have parked the lane after three capped issues; and two
**disclosure leaks I had just introduced** — the health page publishing its own precondition and the
approver's handle to a world-readable file.

It also caught that I had deleted four destructive-command prompts while `D238` mentioned only two.
Three stay gone (they are ordinary work); **`git clean` is now denied** — in the lane's worktree it
removes the environment file holding the database password *and* the settings file carrying the
guard's registration and the database grant, in one command. Verified by dry run. It bound
immediately: it blocked my own command minutes later.

**One correction I could not make:** auto mode pins the *mode*, not the settings the mode consults.
Those live in the machine's user-level file and, measured, they describe the **website** project —
this project appears in them **zero** times. The canon now says so instead of the opposite. **That
file is shared with the website project and is not this chunk's to edit.**

## Verification

- `bash -n` + `shellcheck --severity=error` clean over all 21 shell scripts; lint, types and
  **608/608** unit tests clean; **nine of ten** CI jobs green.
- **The `psql -1` apply command was measured, not asserted**, against a throwaway Postgres 17.6:
  `-1` spans both the file and the version row, exit 3 on failure, `-f` before `-c` — and **a file
  that wraps itself in `begin`/`commit` defeats `-1` entirely**, which is why "bare DDL" is not a
  style rule. The preflight greps were then corrected twice: the first was case-sensitive, the
  second fired on 27 of 78 migrations.
- Both lanes' resolved configuration differs from baseline by exactly the intended keys, printed
  from the installed copies before and after.
- Repo↔box drift: **nothing**. Three files that were invisible to that check are now in it.

## Residuals

- **User separation for chunks** (`D231`) — unchanged, still the real fix, trigger unchanged.
- **Auto mode's settings are website-scoped** — yours to decide.
- **`docs/STRATEGIST.md` still says a dispatched session has no database lane** — strategist-owned;
  reported, not edited.
- **`npm run` is a shell no hook can see** — recorded off-mirror beside the existing `node -e` note.
- The `chunk5-t1-lane-fixes` branch is superseded and can be deleted.

```
=== RELAY ===
HEAD: f903d0d (branch chunk8-infra-lane, 9 commits) | tree: clean
CI: 9 of 10 GREEN — checks, secret-scan, sast, shell-lint, types, replay, tier3-guard all pass; e2e skipped behind sql; **sql FAILS** on a shared-dev-DB orphan row, deterministic across two runs, and NOT in this diff (no SQL touched). ci-ok therefore red. MERGE HELD.
DONE: Approval-window check RETIRED and replaced by a detective record, proven live on the real lane (#110 named another lane's open windows). Chunk breaker fixed (it could never trip). Usage cap re-queued not counted, with a forgery-resistant classifier and CI fixtures. Open-PR outcome. stream-json work log. Reports lane off the public mirror (D234(11) CLOSED), nine assertions each red against the unfixed source. /relay-report installed and used. shell-lint CI job. D237 + D238 written. Owner rider items 1 and 3 done; item 2's code done, its credential file pending.
FILES: 21 changed across 9 commits; 1 new script (write-pgpass.sh), 1 new skill (relay-report), 1 new CI job.
FINDINGS/BLOCKERS: (1) MERGE HELD by a red `sql` job — an orphan purchase_order_items row referencing the example product makes catalog_supplier_tests fail on EVERY branch, main included. Deterministic. Not in this diff. Diagnostic SQL in the report; the delete needs owner approval (§11H) and database access I do not have. (2) The publish-before-apply guard was AUTHORISING the files it refused — its refusal message contained the exact string its check greps for, and a report quoting a refusal (which D223 requires) published it. Happened by accident inside this chunk's own smoke. Fixed both halves. (3) The same guard had never seen the apply command written in our own runbook (backslash continuations read line by line). Fixed; my first fix opened a parity evasion, caught by the panel, fixed by classifying both views. (4) jj_redact was blind to the new secret's shape — a password-file line carries no variable name, so it would have reached the public relay intact. One rule, fixture-proven. (5) Auto mode pins the mode, not its settings; those are user-level and describe the WEBSITE project — this project appears zero times. Canon corrected; the file is not this chunk's to edit. (6) Four destructive prompts were deleted where D238 named two; three stay gone, git clean is now denied (it removes .env.local AND the guard's registration in one command). (7) THREE guards this chunk found refusing nothing while blocking real work — the two above plus a self-test guard that blocked CI and protected nothing. (8) bypassPermissions is refused for root, measured before anything was changed.
NEXT-NEEDED: TWO commands from the owner. (a) `/opt/jahjah/session/write-pgpass.sh` then `--verify` — the credential file my session may not create. (b) Then approval to delete the orphan purchase_order_items row(s) the diagnostic returns, after which ci-ok goes green and GATE 2 proceeds under its standing pre-authorization.
=== END ===
```
