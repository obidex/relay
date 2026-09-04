# chunk 6 — BLOCKED before GATE 2. The work is done and green; the confirming panel round is not.

**Nothing was merged, nothing was pushed, and that is deliberate.** The migration has been applied and
verified since 07:0xZ; everything since has been tests, tooling and canon. The whole tree is now a
single LOCAL commit on a LOCAL branch, and it stops there because the panel round that must clear the
last set of fixes died on the session usage cap (resets 18:20 UTC). `CLAUDE.md` §10 says a Tier-3
panel re-runs after fixes, and §17 names two of those seats as never-downgraded. A cap that prevents
the panel is not a reason to skip it, so this is a stop, not a shortcut.

## First, correct the brief

The chunk brief was written on the belief that the previous session "reached panel round 2 running and
then died silently". **It did not die.** It ran panel rounds 2, 3, 4, 5 and 6 to completion. That
matters, because those rounds found real defects — including two in the last round alone — and the
brief's T1 ("run the interrupted round 2") is therefore round **7**, a confirming pass over round 6's
fixes. That is the round the cap killed.

The brief also expects 16 dirty files; there are **17**. The extra is `docs/pitfalls/docs-mirror.md`,
edited in a later round after a reviewer pointed out that removing the work-engine address from
`STATE.md` had left that file still describing the address as "accepted disclosure" — a licence to put
it back.

## T0 — recovered and verified (read-only; zero database writes)

| Check | Result |
|---|---|
| HEAD, `origin/main` | `9c0ed84` both |
| `chunk6-*` on origin | none — the 0.1 stop condition (which names origin) was not met |
| Applied migration | `20260903120000` |
| Migration file SHA-256 | `07c44665ac520417c0d0391192cc6251aaafaf156b044024f48621468fd90121` — matches the brief exactly |
| `D232` / `D233` / `D234` | all present |
| `CLAUDE.md` §13.18 | the heredoc-expansion pitfall is recorded |
| `STATE.md` SSH host/login | gone |
| `docs-mirror.md` | says the address is "NO LONGER among them" |
| Local gate | lint · typecheck · **605** unit tests · build · **28/28** SQL suites · container replay — all green |

## What round 6 found, because it changes the plan

Round 6 returned three highs. Two of them were **my records being wrong in ways that would have
misdirected the next migration** — not code defects, which makes them exactly the class the owner
cannot catch.

**1. The ledger's biggest open money door is not the one this chunk has been discussing.** A paragraph
I had written called the `opening_balance` cost write "the ledger's only unbounded money-write by a
non-cost-key holder" and counted the class at two siblings. Measured from `pg_proc`:
`record_stock_movement` is `SECURITY INVOKER`, `EXECUTE` granted to `authenticated`, and refuses only
`transfer_in`, `transfer_out`, `sale_issue` and `sale_return`. **`stock_in` and `opening_balance` go
through the front door.** So a `manage_inventory`-only holder — who cannot even SELECT that ledger,
because reading it needs a cost key — can set company-wide inventory valuation to any value through a
sanctioned RPC, with **no control at all**: no guard, no tripwire, nothing. That is a larger door than
the one six rounds were spent on, and my own pitfalls file had contradicted itself about it two
paragraphs earlier. Corrected, off-mirror.

**2. Three of the four `ENABLE ALWAYS` triggers read beyond their own row, not two.**
`customer_payments_before_write` has read `currencies` and `payment_applications` since August. What is
genuinely new in this chunk is the first cross-table *writer*. The consequence that was missing from
the runbook matters more than the count: **on a data-only restore that trigger recomputes
`amount_operating` from the target database's currencies**, so a selective restore silently rewrites a
booked figure — a worse failure than `D234`(6)'s appended phantom row. Both are now in
`docs/runbooks/backup.md` §2.

The third high was a third instance of one habit of mine: I had stripped a worked reproduction from one
paragraph of the mirrored register and left the same detail four lines up in another. Every fix in this
round was therefore applied as a sweep of the class rather than to the flagged line.

## State on disk

- Branch **`chunk6-hardening`**, commit **`9e05464`** — the whole 17-file tree as one commit.
- **LOCAL ONLY.** `git ls-remote origin 'chunk6*'` is empty. Nothing is on GitHub, nothing deployed.
- Working tree clean. `main` is still `9c0ed84`.
- The commit message itself carries a `NOT YET REVIEWED` line naming the unreviewed delta, so the next
  session cannot merge it by accident without reading that.

**The unreviewed delta is round 6's fixes:** mostly prose in the canon and pitfalls, plus two pieces of
real code — a new derived gate `A.1b-ii` in `security_sweep_hardening_tests.sql` (non-trigger
anon-executable functions, which nothing previously enumerated; sabotage-proven) and constraint-name
discrimination in `replay-check.sh`'s fifth arm.

## To resume, after 18:20 UTC

1. `git checkout chunk6-hardening` — it is already committed; do not rebuild it.
2. Run the four-agent panel on `git diff main`. That is round 7.
3. Triage in writing; anything needing a second migration goes under `D234`, which already has six
   items. Do not fix it in this chunk.
4. Then T2 as the brief describes: push, PR-F with the brief's title and the `Tier-3: authorized by
   chunk 6 — hardening` line, `ci-ok` green, squash-merge, watch the post-merge run, verify Vercel
   READY and `/internal/docs/docs/STATE.md`, delete the branch.

**One caution for whoever writes the PR body:** `D234` has **six** residuals now, not the three that
existed when the 07:03Z progress report was published, and `D234`(6) is a regression this change
introduced rather than a pre-existing one. Say so plainly.

## Disclosure carried forward

The deadlock probe ran twice against the shared database earlier in this chunk on a safety premise
that was a converse error (a seeded fixture does not prove a development database, because the seeds
are applied to the live project). Both runs were `begin … rollback` and the script reads back that zero
rows survived — verified — but it did hold row locks on a real order line for a few seconds. The gate
is now an explicit acknowledgement plus a readback in both sessions, and the probe is not wired into
CI.

```
=== RELAY ===
HEAD: 9c0ed84e3a50f9b5ae4bbd7c9245ad26b95cbd55 | tree: clean (work is committed on LOCAL branch chunk6-hardening, 9e05464, unpushed)
CI: not run — nothing pushed. Local gate fully green: lint, typecheck, 605 unit tests, build, 28/28 SQL suites, container replay
DONE: T0 recovered and verified (migration applied, SHA256 matches, canon present, mirror clean); local gate re-run green; whole tree committed to a local branch
FILES: 17 in one commit (2 new: the migration + scripts/probe-sale-issue-deadlock.sh)
FINDINGS/BLOCKERS: BLOCKED at T1. All four panel seats died on the session usage cap (resets 18:20 UTC), so round 6's fixes have had no panel pass and §10 forbids merging them. Round 6 itself found that the ledger's largest unbounded money write is record_stock_movement's front door for stock_in/opening_balance — a manage_inventory-only holder can set company-wide inventory value through a granted RPC with no control at all. That is D234's biggest item and it is bigger than the sale_return hole this chunk has been discussing.
NEXT-NEEDED: after 18:20 UTC, run the four-agent panel on `git diff main` from branch chunk6-hardening, then land PR-F. Nothing else is outstanding.
=== END ===
```
