# chunk 7 — preflight: the `D234` migration (decision A)

**Nothing has touched the database yet.** This report exists so the orientation, the branch and the
file list are on the record before the first DB step, per the chunk plan §9.

| | |
|---|---|
| Chunk | **7 — the `D234` migration**, decision A (owner, 2026-09-05) |
| Tier | **3** — schema, RLS-adjacent guards, money/stock math |
| Session shape | **INTERACTIVE, pasted** on the work engine (`D233`) — this session holds the database lane a dispatched chunk does not |
| Issue | **#104**, deliberately **unlabelled** (a `chunk:*` label would make the ERP dispatcher start a duplicate run) |
| Branch | `chunk7-d234-ledger-bindings`, cut from `main` at **`b9fda08`** |
| Model / effort | Opus 5, xhigh |

## Orientation done (read, not skimmed)

`CLAUDE.md` · `docs/STATE.md` · `docs/DECISIONS.md` `D232`–`D235` · `docs/pitfalls/inventory-stock.md` ·
`docs/runbooks/backup.md` §2 and §3 in full · `docs/runbooks/testing.md` ·
`supabase/migrations/20260903120000_sale_issue_guard_bind_and_audit_actor.sql` · the five test suites
in the plan's list · `supabase/seed/32_reliability_examples.sql` · `src/app/(app)/inventory/actions.ts`.

Also read, because the migration's correctness depends on them rather than on a document:
`record_stock_movement`, `record_sales_return`, `post_shipment_receipt_to_stock` and
`shipment_landed_costs` — the live bodies are re-checked against `pg_get_functiondef` in the GATE-1
report, not inferred from the migration that created them.

### Two premises checked at the source, because the design rests on them

- **`record_stock_movement` names `unit_cost` in its INSERT column list even for an adjustment**
  (where the value is NULL). That is what makes the column-level INSERT grant alternative refuse
  every adjustment with `42501` — the rejection the plan records in `D236`, confirmed in the source
  rather than accepted from the brief.
- **`post_shipment_receipt_to_stock` writes `coalesce(unit_landed_minor, 0)`.** The new guard's
  document arm computes the same expression the same way, so the honest receipt path is bound to
  itself and not to a re-derivation of it.

## Branch state

`git status` was clean on `main` at `b9fda08` before branching — which is `origin/main`, i.e. the
chunk 6b canon PR (#103) merged after `docs/STATE.md` was last written; the file's own "`main` HEAD"
line still reads `f82f7cc` and is corrected in this chunk's canon pass.

## Files this chunk will touch

| File | Why |
|---|---|
| `supabase/migrations/20260905120000_d234_ledger_bindings.sql` | **new** — the one migration, GATE 1 |
| `supabase/tests/sales_dispatch_tests.sql` | TEST 15 **inverted** (never deleted) |
| `supabase/tests/inventory_stock_tests.sql` | a cost-key fixture role; TEST 4 re-cast; new TEST 12 |
| `supabase/tests/inventory_receipt_transfer_tests.sql` | new TEST 13 |
| `supabase/tests/security_sweep_hardening_tests.sql` | A.1 count, A.1b expects an empty set |
| `supabase/tests/sales_payments_returns_tests.sql` | run unchanged; hygiene only if a fixture reds |
| `supabase/seed/32_reliability_examples.sql` | the dispatch half retired |
| `src/app/(app)/inventory/actions.ts` | one error-mapping line |
| `docs/DECISIONS.md`, `docs/STATE.md`, `docs/pitfalls/inventory-stock.md`, `docs/runbooks/backup.md`, `docs/ROADMAP.md` | canon |
| `docs/reference/db.md`, `docs/reference/app.md`, `src/lib/supabase/database.types.ts` | regenerated after the apply |

## Scope discipline

`D234` **(1)**, **(2)**, **(5)**, **(7)** and **(8)** close here. **(3)**, **(4)**, **(6)**, **(9)**,
**(10)**, **(11)** and **(12)** are explicitly NOT in scope and are carried with pointers only.
Two SQLSTATEs are allocated by this chunk — **`JHI13`** and **`JHI14`** — and nothing else.

## Order of operations from here

1. Tests, seed and app edits written on the branch.
2. **GATE 1** — `2026-09-05-chunk7-gate1.md` published with the migration's SHA-256 and the
   pre-apply catalog read-back, **pushed before the apply**. The accident guard reads it.
3. Mandatory `pg_dump` backup, verified non-empty and containing `CREATE TABLE`.
4. Apply in ONE transaction with its `schema_migrations` row; seeds; the separately-approved data
   delete; catalog read-back; the before/after measurement.
5. Every suite, `replay-check`, lint, typecheck, Vitest; `npm run types`; `npm run reference`.
6. Canon, the adversarial panel, then the PR and GATE 2.

**Stop conditions are live:** a non-identifier change to the migration text, a GATE-1 refusal, a
suite that would only go green by weakening a pin, a data-delete count that differs, or a panel
blocker. In each case this chunk publishes what it has and stops.

`D235`: this report describes shape and register numbers only.
