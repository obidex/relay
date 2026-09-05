# chunk 7 — FINAL: the `D234` migration landed, and the panel changed its headline

**Headline, stated the way it actually is rather than the way the chunk plan expected.** The
migration `20260905120000` is applied, verified and green. It closes `D234` **(1)**, **(2)**,
**(5)** and **(8)**. **It does NOT close (7)** — it shuts that door's actor-side arm, measurably, but
its own adversarial panel found the remaining arm does not bind everything that determines the
number, and the first draft of the canon said "closed" in four files. **That is corrected, pinned by
a tripwire, and handed to the next inventory migration with a named first item.** No blocker was
raised at any point, and the migration text was never changed after GATE 1.

| | |
|---|---|
| Issue | **#104** (unlabelled, deliberately) |
| PR | **#105**, `chunk7-d234-ledger-bindings` → `main` |
| Migration | `supabase/migrations/20260905120000_d234_ledger_bindings.sql` |
| **SHA256** | `d87eeffb4d8e29faddb4bbde08391f345eb9819236c999c14387e7d8ab89a83b` |
| GATE 1 | `2026-09-05-chunk7-gate1.md`, pushed BEFORE the apply; the accident guard logged **ALLOW** on those exact bytes |
| Deviations from the approved text | **none** — every identifier premise re-checked against the live catalog |
| Backup | `pre_20260905120000_d234_ledger_bindings_20260905.sql` — **634756 bytes**, 63 `CREATE TABLE`, 63 `COPY` blocks |
| Applied via | `psql -1` (one transaction with its `schema_migrations` row) — `--migration` is unusable here, there is no `SUPABASE_ACCESS_TOKEN` in `.env.local` |
| Register | **`D236`** |

## The measurement, before and after, on live in rolled-back transactions

Seven misuse arms as a **`manage_inventory`-only** actor — four on the return side (`D234`(2)),
three on the valuation side (`D234`(7)):

| | Before | After |
|---|---|---|
| **ACCEPTED** | **7 of 7** | **0 of 7** |

Each now refused by its own SQLSTATE — `JHI13` ×4, `JHI14` ×3.

## The owner-approved data delete (separate approval; not in the migration)

| Check | Expected | Actual |
|---|---|---|
| Ledger rows citing those lines | 0 | **0** |
| `sales_dispatch_items` deleted | 8 | **8** |
| `sales_dispatches` deleted | 8 | **8** |
| Unissued dispatch lines remaining anywhere | 0 | **0** |

39 units of dispatch budget that no ledger row ever consumed. The four `CUST-DEMO-1` orders stay
`confirmed` — they always were — so the one-tap reorder E2E is unaffected. `seed/32`'s dispatch
half is retired so a from-scratch replay does not recreate them.

## The panel — four rounds, no blocker, and it changed the headline

Every high was against what the chunk **wrote**, not against the migration, which was never altered
after GATE 1. Rounds 2, 3 and 4 each found a fresh error in the correction the previous round had
produced — which is itself the most useful thing to record.

| Round | Finding | Disposition |
|---|---|---|
| 1 | **`D234`(7) claimed CLOSED.** The guard's document arm does not bind everything that determines the number | Confirmed by live measurement. **NARROWED, not closed** — corrected in four files, tripwire added |
| 1 | The `transfer_in` acceptance rationale this chunk wrote was false | Confirmed · corrected |
| 2 | **That correction was also false.** The cited figure belonged to a different, unrecorded door; the probe's fold order was a random tie-break | Re-measured with the order pinned · both recorded |
| 2 | **`D236` published mechanism for an OPEN defect on the public mirror** | Register cut to shape-only; mechanism moved off-mirror (`D235`) |
| 2 | A tripwire arm was vacuous — the fixture variant shared the line's product | Split into distinct arms |
| 3 | Disclosure residue; a **fourth** error in the same paragraph; arms still conflated | All corrected |
| 4 | A bare direction assertion; an over-claim; **a hole in a fix nobody has written yet** | All corrected |

**One premise was REJECTED after checking.** A seat read the moving-average fold from a superseded
migration and concluded only one movement type is average-valued. The live body folds **three**. The
lesson is §5's: read the live definition, not the migration you think defines it.

## Three doors this chunk did not cause and nobody was looking for

All measured, all recorded off-mirror, none in scope to fix here:

1. **A negative stock `adjustment` has no on-hand floor.** One statement, through a sanctioned RPC,
   with the single key the inventory module already grants, takes on-hand and company-wide value
   through zero. Cheaper than anything the chunk was aimed at.
2. **`transfer_out` inflates.** Two statements took a variant's company-wide value from 10,000 to
   **10,010,000** — 1001× — with `manage_inventory` and nothing else.
3. **The structural fix already BANKED for that door is evadable exactly as specified**, for a reason
   that only surfaces when someone sits down to write it. The one-line addition that closes it is
   recorded with the fix. **Read it before writing that migration, not after.**

## Gates

| Gate | Result |
|---|---|
| SQL suites | **28 / 28** |
| `scripts/replay-check.sh` | PASS — including the grant audit (124 references) |
| `npm run lint` · `typecheck` | clean · clean |
| `npm test` | **605 passed** (49 files) |
| `npm run types` · `npm run reference` | re-run; diffs committed with the migration |

## Catalog read-back after the apply

FIVE `ENABLE ALWAYS` triggers on three tables (the currency anchor, the two payment guards, and both
`stock_movements` binding guards); the valuation guard at **`'O'`**, origin-only by design and
asserted by name so a "promote everything" sweep reds in CI. All four pinned functions read
`{postgres, service_role}`. `record_sales_return` and `record_stock_movement` still executable by
`authenticated`. The trigger-function RPC surface is **empty**. 78 migrations applied.

## Owed cleanups (§8)

- The stray empty `.probe-b` directory: found at the repository root, confirmed empty, removed.
- `docs/STATE.md`'s ledger cited `2026-09-05-chunk6b-final`, which was never published — re-pointed
  at **issue #102's closing comment**, and the row now says why.
- Stale remote branches: deleted after the merge; `chunk5-t1-lane-fixes` and
  `security/sweep-section-1-instruments` KEPT.

## What is open, and what the next inventory migration should take first

`D234` **(7)** narrowed, not closed · five smaller residuals qualifying the same guard · the two
re-priced ledger doors and the hole in their banked fix. **All shape-only here and in the register;
mechanisms are in `docs/pitfalls/inventory-stock.md`, off the mirror** (`D235`).
`D234` (3), (4), (6), (10) go with the next payment-writer migration; (9) with the restore drill;
(11) with the reports-lane sweep; (12) with the next sales chunk.

**Product consequence, carried to the UI rebuild:** a `manage_inventory`-only user can no longer
enter a receipt or opening-balance cost. The form still offers those movement types and the Server
Action maps the refusal to "forbidden" — hiding them is rebuild work.

## PR, CI and the merge

| | |
|---|---|
| PR | **#105** → `main` |
| `tier3-guard` | **pass** — after one self-inflicted red: the authorization line was wrapped in markdown bold and the guard's regex is anchored at line start, so `**Tier-3:` failed it. Corrected to the bare form; the guard re-ran on the `edited` trigger, exactly as chunk 4 sabotage-proved it does. **The guard was right and the PR was wrong** — worth recording, because a red `tier3-guard` reads at a glance like a scope problem and this one was punctuation. |
| `ci-ok` | **green** on run `33969840589` — every job: `checks`, `secret-scan`, `sast`, `types`, `replay` (+ grant audit), `sql`, `e2e`, `tier3-guard` |
| Merge | squash, **`f43d8d1`**, branch deleted |
| Post-merge `main` | **green** at `f43d8d1` — a green PR is not a green `main` on shared mutable state, so it was watched |

**The two E2E risks this chunk carried, both resolved green.** Deleting the demo dispatches puts
those four orders back in the dispatch dock with remaining quantity, which changes which order the
dock spec picks first; and the reorder spec depends on `seed/32`. Both specs pass — the dock spec
uses `.first()` with no count assertions, and reorder needs only a confirmed order with a resolvable
price and never reads a dispatch.
