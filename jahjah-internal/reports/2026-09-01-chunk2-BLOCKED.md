# Chunk 2 — BLOCKED: T1 and T2 not applied

<!-- index: T1+T2 BLOCKED — no DB lane in this session, and T1's approved guard is RLS-blind against the actor it targets; corrected SQL enclosed for re-approval -->

**Nothing was applied to any database. Nothing was merged. No migration file was committed.** T0
completed and stays done. This report carries everything the next attempt needs so that GATE 1 is
re-crossed once, on evidence, rather than re-derived.

## 1. Which stop rules fired

Two, independently. **Either alone is sufficient**, which matters: fixing one does not unblock the
chunk.

| # | Rule | Applies to |
|---|---|---|
| **1** | *"anything needing secrets … → BLOCKED report, STOP"* | **T1 and T2.** This session has no database credentials in either lane, so the mandatory `pg_dump` backup, the apply, the read-back, the SQL suites and `npm run reference` are all unreachable. Details in the preflight. |
| **2** | *"Any semantic change — logic, predicates, added/dropped statements, different tables → publish the BLOCKED report and STOP"* | **T1 only.** The approved guard needs a clause it does not have. §3 below. |

Stop rule 1 is a **routing** fact, not a defect: a chunk of three migrations has to run on the box
that owns the database lane. Stop rule 2 would have stopped T1 on that box too.

## 2. Status of each task

| Task | Verdict | Why |
|---|---|---|
| **T0** close chunk 1 | **DONE** | PR #80 was already merged and green; chunk 1's RELAY BLOCK is now written into its final report. |
| **T1** `D218` sale double-drain | **BLOCKED — rules 1 and 2** | Identifiers clean; the SQL itself is defective. Corrected block in §3. |
| **T2** `D215` payment-guard promotion | **BLOCKED — rule 1 only** | Premise fully confirmed, identifiers clean, one-line-per-guard action stands exactly as approved. Ready to apply on the database lane with no further approval work beyond the GATE-1 file. §4. |

## 3. T1 — the defect, and the corrected block

### 3.1 What is wrong

**Part (b)'s guard function carries no `SECURITY` clause, so it is `SECURITY INVOKER`** — and a
function with no clause shows no line at all in `pg_get_functiondef`, which is exactly why this reads
as fine (universal pitfall §13.6).

An INVOKER trigger function runs as the **calling** role, so its reads obey RLS as that role. The
gates on the three tables it reads are asymmetric with the gate on the table it guards:

| | gate |
|---|---|
| `stock_movements` **INSERT** | `manage_inventory` |
| `stock_movements` **SELECT** | the cost keys (`view_shipment_costs` / `manage_shipment_costs`) |
| `sales_dispatch_items` **SELECT** | `view_sales_orders` / `manage_sales_orders` / `dispatch_sales_orders` |
| `sales_order_items` **SELECT** | `view_sales_orders` / `manage_sales_orders` |

`D218` defines the residual gap as *"the raw PostgREST INSERT by a `manage_inventory` holder"* — an
actor who passes the INSERT gate and fails all three SELECT gates. For that actor:

- `v_dispatched` reads no rows → `0`
- `v_issued` reads no rows → `0` (including the row just inserted)
- `0 > 0` is false → **the guard returns NEW and the over-drain is admitted**
- the `for update` on the order line takes no lock, for the same reason

Meanwhile `public.dispatch_sales_order` is `SECURITY DEFINER`, so on the legitimate path the trigger
runs as the table owner, sees everything, and the guard works. **The control is live on the honest
path and blind on the attack path.** A test written through the RPC — the natural way to test it —
passes; the negative test that matters must be a raw INSERT as a `manage_inventory`-only actor, and
that is the assertion that would have caught this.

This is not a new insight in this repository. The dispatch RPC's own step-7 comment says it must read
the ledger *"AS OWNER"* because the on-hand view *"would fail-closed for a dock caller"*, and
`docs/pitfalls/inventory-stock.md` records the same write-key ≠ read-key asymmetry as the reason that
table's write RPC must not use `RETURNING`. The approved SQL walks into a trap the canon already
documents.

### 3.2 The diff against the approved draft

Parts **(a)** and **(c)** are **unchanged** — no identifier differed from live reality, and every
proposed name is free. The whole diff is in (b):

```diff
 create or replace function public.jhx_sale_issue_within_dispatch()
-returns trigger language plpgsql as $$
+returns trigger
+language plpgsql
+security definer
+set search_path = ''
+as $$
 declare
   ...
 end $$;
+
+revoke all on function public.jhx_sale_issue_within_dispatch() from public, anon;
+grant execute on function public.jhx_sale_issue_within_dispatch() to postgres, service_role;
```

Three added things, and nothing else:

1. **`security definer`** — the fix. Without it the guard is a no-op for its own threat model.
2. **`set search_path = ''`** — mandatory for a DEFINER function and the house convention for all 129
   functions here. The body already schema-qualifies every reference, so nothing else moves.
3. **The revoke/grant pin** — a freshly created function starts at `EXECUTE TO PUBLIC`, i.e. `anon`
   (universal pitfall §13.7). Every other trigger function on these tables sits at
   `postgres=X, service_role=X`; this matches them. A trigger fires without the invoker holding
   `EXECUTE`, so this removes a call path and costs nothing.

**The body itself is not touched** — no predicate, no table, no statement inside it changes.

### 3.3 One judgement call for GATE 1

The raise message interpolates `v_issued` and `v_dispatched`. Under `security definer` those totals
are computed with RLS bypassed and then handed to an actor who may hold no sales key at all — a small
quantity disclosure to a non-holder. Quantities (never money) are already disclosed to a price-free
audience by ratified decision, so this is arguably in-posture, but it should be **decided rather than
inherited**. Recommended: keep the numbers out of the message.

```sql
    raise exception 'sale_issue total exceeds dispatched total for order item %',
      new.source_sales_order_item_id using errcode = '23514';
```

Either form is fine; the strategist should pick one at GATE 1 so the next session does not have to.

### 3.4 Two findings that are not blockers

1. **Naming.** `dispatch_item_id` diverges from the table's own `source_*_id` convention for source
   links, and from `D218`'s design (A), which called it `source_sales_dispatch_item_id`. No `jhx_`
   prefix exists on any of the 129 functions either — the house convention names a trigger function
   after its trigger. Both are **new** identifiers with no live reality to correct, so under the
   identifier rule they were deliberately **not** renamed. Worth deciding at re-approval, because
   renaming after apply costs a second migration.
2. **The guard imposes an undeclared write-ordering contract.** It compares issued-so-far against
   dispatched-so-far, so any writer must insert the `sales_dispatch_items` row **before** the matching
   movement. The current RPC does exactly that, and part (c) makes the same demand structurally — but
   nothing states it.

### 3.5 What else T1 needs, beyond the SQL

- **The RPC change must ship in the SAME migration as part (c).** The new `CHECK` rejects any
  `sale_issue` without a `dispatch_item_id`; until `dispatch_sales_order` populates it, **every
  dispatch fails**. The minimal change is to the movement INSERT alone — join the dispatch rows just
  written on `(dispatch_id, order_item_id)`, which is backed by the unique index
  `sales_dispatch_items_dispatch_item_key`, and select `d.id` into the new column. No `RETURNING`, in
  keeping with that table's rule. `create or replace` with the identical signature preserves the
  function's ACL.
- **The FK sweep (§13.8).** Part (c) adds the first FK between `stock_movements` and
  `sales_dispatch_items`, so a bare PostgREST embed of that parent becomes newly *resolvable* rather
  than ambiguous — but the repo-wide grep is still owed in the same change, because `tsc` sees none of
  it.
- **The collateral sweep (§13.14).** A new always-on guard on `stock_movements` is a breakage risk for
  **every** other suite that writes there. That sweep is part of the work, not a follow-up.
- **The tests the prompt already names**, plus the one it does not: a **raw INSERT by a
  `manage_inventory`-only actor** must be rejected. That is the assertion that distinguishes the
  corrected guard from the approved one — under the approved version it passes, and everything else in
  the suite stays green.

## 4. T2 — ready to run, blocked only by the lane

Confirmed from the generated reference. **The guard set is exactly two**, both on
`public.customer_payments`, both in the weaker enabled state (`tgenabled = 'O'`); the currency anchor
remains the only `'A'` trigger in `public`.

| Trigger | Table | Timing | `tgenabled` | Why it is in the set |
|---|---|---|---|---|
| `customer_payments_before_write` | `public.customer_payments` | `BEFORE INSERT OR UPDATE OR DELETE`, row | `'O'` | the entry freeze and the rate rules |
| `customer_payments_log_activity` | `public.customer_payments` | `AFTER INSERT OR UPDATE OR DELETE`, row | `'O'` | the **sole** writer of the append-only payment audit |

Deliberately **out of scope**, and each checked rather than assumed: `customer_payments_set_created_by`
and `customer_payments_set_updated_at` (provenance/housekeeping, not money guards); the three triggers
on `payment_applications` and the two on `purchase_order_payments` (same reason).
`customer_payment_audit` carries **no trigger of its own** — which is precisely why the second guard
above is load-bearing.

**Approved action, verbatim, one line per guard, nothing more:**

```sql
alter table public.customer_payments enable always trigger customer_payments_before_write;
alter table public.customer_payments enable always trigger customer_payments_log_activity;
```

**Neither guard needs more than that one-line promotion**, so nothing is left behind under the
prompt's "flag it and continue" clause.

**The alignment that must ship in the same change** — verified present, at the lines `D215` names:

- `supabase/tests/fx_cut_tests.sql:351` — plain `enable trigger customer_payments_before_write`
- `supabase/tests/sales_payments_returns_tests.sql:230` — the same

Both disable the guard mid-suite and re-enable it with the plain form. Harmless today; the moment the
promotion lands, each leaves the guard **downgraded for the rest of its run**, so every later
assertion in those two files proves nothing. Both must move to `enable always trigger`.

**No seed touches these triggers** — checked across `supabase/seed/` and `supabase/bootstrap/`. So the
from-scratch alignment is not a seed edit: it is the replay postlude, which must gain a `'A'` pin for
both guards **and** a behavioural probe. A structural pin alone would prove nothing, for the reason
already recorded when the anchor was promoted — every assertion that reads a catalog letter would
still pass if the change were a no-op.

**Also owed by this migration**, and cheap to carry here: the superseding comment for the applied
anchor migration's residual, which the register has been carrying half-delivered since chunk 1 and
expected this promotion to carry.

The mechanism, the actor set and what the promotion does and does not buy are deliberately **not**
restated here — that class of detail belongs in the pitfalls file that is kept off the public mirror,
and this report is world-readable.

## 5. What the next session needs

1. **Run on the database lane.** Nothing else changes; both tasks are otherwise ready.
2. **T2 needs no new approval beyond its GATE-1 file** — the two statements above are the approved
   action, unmodified.
3. **T1 needs GATE 1 re-crossed** on the corrected block in §3.2, plus a one-word answer on §3.3 and,
   if the strategist wants it, §3.4's naming.
4. **Order them T2 → T1.** T2 is a two-statement promotion with a known alignment; T1 is a schema
   change, an RPC rewrite, two sweeps and a new negative test. Landing the small one first keeps the
   larger one from carrying it.

## 6. The transferable lesson

**A guard that reads the table it guards inherits that table's read gate, and a table whose write key
differs from its read key will invert the control.** The approved SQL was sound as logic and wrong as
privilege: every predicate correct, every identifier correct, and blind to precisely the actor it was
written for. The register already held both halves — that this ledger's write key is not its read key,
and that a function with no `SECURITY` clause is INVOKER — and the defect is what happens when the two
are not put together.

The generalisation for review, one level up: **for any new guard, name the actor it exists to stop and
ask what that actor can SEE, not just what they can do.** A guard tested only through the privileged
path is tested in the one universe where it cannot fail.
