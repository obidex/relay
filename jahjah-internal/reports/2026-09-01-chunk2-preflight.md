# Chunk 2 preflight — 2026-09-01

<!-- index: chunk-2 preflight — LANE MISMATCH: no database access in this session, so T1/T2 cannot be applied; T1 also fails GATE 1 on the identifier rule -->

**Session:** owner-approved chunk `2026-09-01-chunk2` (T0 close chunk 1 · T1 `D218` sale double-drain ·
T2 `D215` payment-guard promotion). **Model: Opus 5, xhigh** — the prompt specified Opus 4.8; reported
under the §17 pilot rule, exactly as chunk 1 did. **This report is the preflight only** — at the time
of writing nothing has been changed in either repository and nothing has touched any database.

## 1. Verdict

| | |
|---|---|
| **Box / lane** | **NOT the VPS work engine.** This chunk is executing in a Claude Code cloud container. |
| **Database access** | **NONE — neither lane.** No `.env.local`, no `SUPABASE_DB_PASSWORD`, no `SUPABASE_ACCESS_TOKEN`, no SSH client and no key material. Both the primary (`psql`/`pg_dump`) and the fallback (Management API) lanes are unavailable for lack of credentials, not for lack of network. |
| **Repo** | `main` @ `a745e45`, working tree **clean**, designated branch `claude/chunk-2026-09-01-migrations-pbc83g` exists and is even with `main`. |
| **CI at HEAD** | **GREEN** — run `33502873616`, `success`, 2026-09-01T11:30–11:39Z. |
| **T0** | **ALREADY COMPLETE before this session started** — PR #80 merged at 11:30:52Z, post-merge CI green. Nothing to rebase or fix. |
| **T1 premises** | Identifiers **CONFIRMED**; the row-count premise is **imprecise but not blocking**; **the approved SQL itself has a defect that requires a SEMANTIC change** — see §4. |
| **T2 premises** | **CONFIRMED** — the guard set is exactly two triggers, both at `tgenabled = 'O'`, and the approved one-line promotion applies to each as written. |
| **Decision** | **T0 delivered. T1 and T2 BLOCKED, no SQL written to any database.** Two independent stop rules fire; either alone is sufficient. |

## 2. The lane mismatch, stated plainly

Chunk 1 ran on the VPS work engine, which is the **primary** database lane — its preflight exercised
`psql` and `pg_dump` against the live project and reported the server banner. **This session is not on
that box.** It is a fresh cloud container with the repository cloned at `main`.

What that removes, precisely:

| Step the chunk requires | Status here |
|---|---|
| Mandatory pre-migration `pg_dump` backup | **impossible** — no credentials |
| Apply the migration to the live project | **impossible** — no credentials |
| Read the end state back from `pg_catalog` | **impossible** — no credentials |
| Run the SQL suites (`node scripts/db-query.mjs`) | **impossible** — no `SUPABASE_ACCESS_TOKEN` |
| `npm run reference` (live-truth regeneration) | **impossible** — needs a read-only DB connection |
| `npm run types` / `bash scripts/replay-check.sh` | **impossible here** — both are Docker-backed and the Docker daemon is not running in this container |

The binaries exist (`psql` and `pg_dump` are installed; the Docker *client* is 29.3.1) — what is absent
is credentials and a running daemon. **This is a routing problem, not a tooling problem:** a chunk whose
every task is a migration has to run on the box that owns the database lane.

Note also that CI cannot substitute. The `sql` job runs the suites against the shared **live** dev
database; a migration committed but never applied there makes every new assertion red, so "let CI prove
it" cannot reach green, and GATE 2 (merge only on green CI) can never be crossed. There is no path from
this container to a merged migration.

## 3. Permission preflight

Everything not database-shaped works, and nothing prompted or stalled.

| Category | Probe | Result |
|---|---|---|
| `git` (status/log/branch/commit/push) | exercised | ok |
| GitHub API (PR read, CI runs, merge state) | exercised via the GitHub tool surface | ok |
| relay repository (clone, write, push) | cloned and verified | ok |
| `node` / `npm` | `--version` | ok — Node **22.22.2** (pinned major, correct), npm 10.9.7 |
| file edits | exercised | ok |
| `psql` / `pg_dump` binaries | `--version` | present, **unusable — no credentials** |
| `docker` | `docker ps` | client present, **daemon not running** |
| `ssh` | — | **absent**, and no key material |
| local PostgreSQL server binaries | present, but **v16** against a 17.6 project | not a substitute for the replay container |

`node_modules` is absent in this container; it was not installed, because nothing downstream of the two
blockers can be reached by installing it.

## 4. Premise check, task by task

### T0 — close chunk 1 — **ALREADY DONE**

PR #80 (`chunk1/end-canon`) was merged into `main` at 2026-09-01T11:30:52Z as `a745e45`, and the
post-merge run on `main` is green. Nothing needed rebasing. The one residual piece of T0 —
chunk 1's RELAY BLOCK, which was never written into `2026-09-01-chunk1-final.md` — is delivered by
this chunk (see the T0 report).

### T1 — `D218`, the sale double-drain — **IDENTIFIERS CONFIRMED, SQL DEFECTIVE**

**Identifiers all check out against the generated reference**, so the identifier rule is not what
stops (a) or (c):

- `sales_dispatch_items(id, dispatch_id, order_item_id, quantity)` — exact, including types.
- `stock_movements.source_sales_order_item_id` and `.source_sales_return_item_id` both exist;
  `movement_type` is `text` (not an enum), `quantity_delta` is `integer`.
- `public.sales_order_items(id …)` exists and is the parent the lock statement names.
- The constraint and index names proposed are all free; the table already carries an analogous pair
  (`stock_movements_receipt_line_variant_uniq`, `stock_movements_sale_issue_source_check`).

**The row-count premise is imprecise.** The prompt states *"`stock_movements` currently has ZERO
rows"*. Chunk 1 measured the ledger as **5 rows** — 3 `opening_balance`, 1 `stock_in`, 1 `adjustment`
— with **zero `sale_issue` and zero `sale_return`** rows. The operative claim survives: both partial
indexes cover an empty set, the new `CHECK` is satisfied by all five existing rows, and **no backfill
is required.** Recorded because "zero rows" would also have implied the table could be rewritten
freely, which is not the same statement.

**The defect — part (b) is RLS-blind against exactly the actor it exists to stop.** The approved
function carries no `SECURITY` clause, and a function with no clause is `SECURITY INVOKER`
(universal pitfall §13.6 — `pg_get_functiondef` shows no line at all, which is how this survives a
reading). An INVOKER trigger function runs as the calling role, so its three reads are subject to RLS
as that role. The gates are asymmetric by design:

| Table the guard reads | SELECT gated by |
|---|---|
| `stock_movements` | `view_shipment_costs` **or** `manage_shipment_costs` (the cost keys) |
| `sales_dispatch_items` | `view_sales_orders` / `manage_sales_orders` / `dispatch_sales_orders` |
| `sales_order_items` | `view_sales_orders` / `manage_sales_orders` |

while `stock_movements` **INSERT** is gated by `manage_inventory` alone. The threat model `D218`
names is precisely *"the raw PostgREST INSERT by a `manage_inventory` holder"* — an actor who passes
the INSERT policy and fails all three SELECT policies. For that actor every read inside the guard
returns **no rows**, `coalesce(sum(...), 0)` yields `0` on both sides, `0 > 0` is false, and the
guard **admits the over-drain**. The `for update` on the order line takes no lock either, for the
same reason.

Meanwhile the legitimate path — `public.dispatch_sales_order`, which is `SECURITY DEFINER` — runs the
same trigger as the table owner, where RLS does not apply, so the guard **does** bite there. The
control is therefore live on the honest path and blind on the attack path: exactly inverted.

This is not a novel reading. The repository already knows it in two places: the dispatch RPC's own
step 7 comment says it must read the ledger *"AS OWNER"* because the on-hand view *"would fail-closed
for a dock caller"*, and `docs/pitfalls/inventory-stock.md` records the same write-key ≠ read-key
asymmetry as the reason that table's write RPC must not use `RETURNING`.

**The fix is one clause — `security definer set search_path = ''` (plus the revoke/grant pin every
other trigger function here carries) — and that is a SEMANTIC change, not an identifier change.**
Under the chunk's own identifier rule that is a deviation-stop, so no part of T1 is applied. Applying
(a) and (c) alone would be substitute SQL, which the stop rules also forbid.

Two smaller findings for the re-approval, neither blocking on its own:

1. **Naming.** `dispatch_item_id` diverges from the table's `source_*_id` convention for source links
   (`source_shipment_item_id`, `source_sales_order_item_id`, `source_sales_return_item_id`), and
   `D218`'s own design (A) called it `source_sales_dispatch_item_id`. Likewise no `jhx_`-prefixed
   function exists anywhere in the 129 — the house convention names a trigger function after its
   trigger. Both are *new* identifiers with no live reality to correct, so they were **not** renamed.
2. **The guard imposes a write-ordering contract.** It compares issued-so-far against dispatched-so-far,
   so any writer must insert the `sales_dispatch_items` row **before** the matching movement. The
   current RPC does exactly that, and part (c)'s FK-plus-unique-index makes the same demand
   structurally — but nothing states it, and a future writer that reorders the two inserts would be
   rejected by its own correct behaviour.

Also carried into the re-approval, from the canon rather than from the SQL: the new FK in (c) must be
paired with a repo-wide sweep for PostgREST embeds of `sales_dispatch_items` (§13.8), and a new
always-on guard on `stock_movements` is a collateral-breakage risk for **every** other suite that
writes to that table (§13.14).

### T2 — `D215`, promote the payment-path money guards — **PREMISE CONFIRMED**

The guard set is exactly the two `D215` names, both on `public.customer_payments`, both currently in
the weaker enabled state:

| Trigger | Table | Timing | `tgenabled` |
|---|---|---|---|
| `customer_payments_before_write` | `public.customer_payments` | `BEFORE INSERT OR UPDATE OR DELETE … FOR EACH ROW` | `'O'` |
| `customer_payments_log_activity` | `public.customer_payments` | `AFTER INSERT OR UPDATE OR DELETE … FOR EACH ROW` | `'O'` |

The other two triggers on that table (`customer_payments_set_created_by`,
`customer_payments_set_updated_at`) are provenance/housekeeping, not money guards, and are correctly
out of scope; so are the three on `payment_applications` and the two on `purchase_order_payments`.
`customer_payment_audit` carries no trigger of its own — it is written **only** by
`customer_payments_log_activity`, which is why that one is in the set. The currency anchor remains the
only `'A'` trigger in `public`.

The approved action applies to each as written, identifier for identifier. The matched-pair alignment
`D215` names is real and still present: `supabase/tests/fx_cut_tests.sql` and
`supabase/tests/sales_payments_returns_tests.sql` each re-enable the entry-freeze trigger with a plain
`enable trigger`, and both must move in the same change or every later assertion in those files proves
nothing. **T2 is blocked by the lane, and by nothing else.**

## 5. What happens next

1. **T0 is delivered** — report published, and chunk 1's RELAY BLOCK written into its final report.
2. **T1 and T2 are BLOCKED.** No migration is written to any database; nothing half-applied is left
   behind; nothing is merged.
3. The blocked report carries the corrected T1 SQL for re-approval and the complete T2 guard list, so
   the next attempt on the database lane starts from a cleared GATE 1 rather than from scratch.

Nothing in this chunk touched WireGuard, Docker port bindings, the kill switches, the automation
timers, or the website repository. No secret was read, printed or committed.
