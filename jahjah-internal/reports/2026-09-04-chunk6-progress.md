# chunk 6 — progress: the migration is in, the panel has been round the loop once, round two is running

**The irreversible part is done and reported** (`2026-09-04-chunk6-migration.md`). This is a
checkpoint for the part that follows: the tests, the collateral fixes, the `D229` audit hardening,
and what the four-agent panel found — because it found real things, including two mistakes of mine
that a green suite would never have surfaced.

## Where it stands

| | |
|---|---|
| Migration | `20260903120000` applied, read back from `pg_catalog`, residual **4/4 → 0/4**, deadlock **reproduced → gone** |
| Local gate | 28/28 SQL suites · 605 unit tests · lint · typecheck · build · **from-scratch container replay green** |
| Panel | round 1 complete (1 high + several mediums, all triaged); **round 2 running on the complete diff** |
| Not yet done | commit, PR-F, merge, post-merge watch |

## What the panel caught that I had wrong

**1. My deadlock probe's safety gate was a converse error — and I had already run it twice on the
strength of it.** It refused unless a seeded demo location existed, and the comment claimed a
database without that row "is not a seeded development database". But `docs/runbooks/backup.md` §3
applies the seeds to the live project, so the row is present there and the refusal would never have
fired anywhere it mattered. There is **one** database in this project and nothing a query can ask
distinguishes it. The probe now refuses unless `PROBE_ACK=locks-real-rows` is set in the environment,
and says exactly what it costs. **Stated plainly rather than buried: both runs in this session went
against the shared database on a false premise.** Both were `begin … rollback`, the script reads back
that zero rows survived, and it did — but it held `KEY SHARE` and then `FOR UPDATE` on a real
`sales_order_items` row for a few seconds, which would have blocked a concurrent dispatch of that
order line.

**2. `GRANT_AUDIT_ADVISORY` was truthy-tested.** `[ -n "$VAR" ]` fires on `GRANT_AUDIT_ADVISORY=0` —
the exact value someone reaches for to turn advisory mode **off**. Worse, the script would then exit
0, so its caller took the success branch, printed no warning, and the run ended on
`replay-check: PASS`. Now `= "1"`, matching the caller. Two gates on one switch must share one truth
condition.

**3. The new `PUBLIC` verdict conflated two different defects.** `has_function_privilege('anon', …)`
is true for a blanket grant to `PUBLIC` *and* for an explicit `GRANT … TO anon`. My comment asserted
"anon holds nothing of its own here" — **false**: three functions in this schema carry an explicit
anon grant, one of them with no PUBLIC grant at all. The label and its printed remedy would have sent
the reader to the wrong line. `PUBLIC` and `ANON` are now separate verdicts.

**4. The relation lane never probed `anon` at all.** I closed the hole on the function side and left
the table side open: an unauthenticated-readable table still scored `OK`. This repo has shipped that
exact defect once already. Fixed and sabotage-proven.

**5. The lock mode had no CI-enforced pin.** The `40P01` deadlock needs two concurrent sessions, so
it cannot live in an always-rollback suite — which left a **one-word** revert to `for update` passing
every automated gate and resurfacing as a live deadlock. TEST 14 now pins both halves structurally
(the new form present, no bare `for update`), each sabotage-checked.

**6. And a trap I documented and then walked straight into, twice.** `scripts/grant-audit.sh` builds
its query in an **unquoted** heredoc, so bash expands backticks inside it — including inside SQL
comments. A backtick in a comment ran as a command and that text was silently **deleted from the
query**; the only symptom was a stray `oid: command not found` on stderr while the audit still
printed `PASS`. The script now reads its own bytes and refuses rather than running a query bash has
edited, and `CLAUDE.md` §13.18 records the class next to its block-comment sibling.

## Three findings that need a migration, so they are recorded rather than fixed (`D234`, OPEN)

The chunk's cap is exactly one migration and it is spent. Each of these is written down with its real
severity instead of being quietly carried:

1. **The guard is a read oracle for the three values it exists to hide.** It is `SECURITY DEFINER`
   precisely because a `manage_inventory`-only actor cannot read those tables — and it interpolates
   the variant, the location and the dispatch-line quantity into its `JHI11` messages, which
   PostgREST hands back to that actor. It is silent, too: the raise sorts before the activity writer.
   **What still gates it** is the `(dispatch_item_id, order_item_id)` pair, which the fourth message
   does *not* leak and which is unreadable without a sales key — so it is disclosure to someone who
   already holds the hard part, not a way in. It does, though, weaken the "they still need a UUID
   whose line matches all three" claim, and that claim has been corrected wherever it appeared.
   The fix is dropping one `%` from three raises.
2. **`sale_return` is the unguarded mirror of the hole just closed** — nothing binds a return's
   variant, location or quantity to its return line. Not an escalation over the `adjustment` write
   the posture already accepts, but **laundering**: it posts as a customer return against a real
   return line, and reconciliation will trust it.
3. **A null `actor_id` now means two things.** The FK is `ON DELETE SET NULL`, so it has always meant
   "that user was deleted"; since `D161` it also means "system context". Deleting a user relabels
   their payment-audit rows as system-originated, by a path that never touches the audit table.

## Also worth the strategist's eye

- **`docs/STATE.md` no longer carries the work-engine SSH host and login.** That file is served
  publicly and unauthenticated from `/internal/docs` — confirmed in the mirror allowlist, not assumed
  — and the relay's own publisher redacts that address on sight, so the mirror was the one lane still
  serving it. `docs/pitfalls/docs-mirror.md` listed the VPS IP as "already present, accepted
  disclosure"; after this change **no mirrored file contains it**, so that line has been corrected —
  otherwise it reads as a licence to put it back.
- **`docs/runbooks/backup.md` gained a correction that matters for disaster recovery.** The guard is
  the one cross-table `ENABLE ALWAYS` trigger, and it now reads **three** parent tables instead of
  one — so a data-only restore must load all three before `stock_movements`. Verified against the
  actual dump's emission order rather than assumed.
- **The seven-writer money sweep in `activity_log_tests` was hand-kept and had stopped covering two
  writers.** It is now derived from the catalog, with one documented exclusion and a floor check so a
  derived-but-empty roster cannot pass.

```
=== RELAY ===
HEAD: 9c0ed84e3a50f9b5ae4bbd7c9245ad26b95cbd55 | tree: dirty (uncommitted; 2 new files, 14 modified)
CI: not run yet — local gate fully green (28/28 SQL suites, 605 unit tests, lint, typecheck, build, container replay)
DONE: migration applied and verified; tests written and sabotage-checked; two collateral breakages fixed; D229 audit hardening landed; panel round 1 triaged and fixed; canon written (D232/D233/D234)
FILES: 16 (2 new: the migration + scripts/probe-sale-issue-deadlock.sh)
FINDINGS/BLOCKERS: no blocker. One panel HIGH needs a second migration and is recorded as D234(1) rather than fixed, because the chunk's cap is one migration. Disclosed: the deadlock probe ran twice against the shared database on a false safety premise — both runs rolled back and were verified to leave zero rows.
NEXT-NEEDED: none — panel round 2 is running; PR-F follows
=== END ===
```
