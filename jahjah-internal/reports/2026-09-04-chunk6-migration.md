# chunk 6 — the migration is APPLIED, and the result was read back out of the catalog

**`20260903120000_sale_issue_guard_bind_and_audit_actor` is live.** It went in as one transaction
together with its `schema_migrations` row, after its SHA-256 was published to the relay and the
GATE-1 hook had checked it. The bytes applied are byte-identical to the SQL the owner approved — the
diff is empty, and no identifier correction turned out to be needed.

The headline is the measurement, not the DDL. **Before the migration, all four ways of misusing a
dispatch line were accepted by the live database. After it, all four are refused, each by its own
named check.** The deadlock that D225(d) predicted reproduced with the guard named in the stack, and
is gone.

## The apply

| | |
|---|---|
| Backup first | `/root/backups/pre_sale_issue_guard_bind_and_audit_actor_20260904.sql` — 631,240 bytes, 63 `CREATE TABLE`, 63 `COPY` blocks |
| GATE-1 report | `2026-09-04-chunk6-gate1.md`, pushed **before** the apply; hash confirmed present on the relay's `origin/main` |
| SHA256 | `07c44665ac520417c0d0391192cc6251aaafaf156b044024f48621468fd90121` |
| Lane | `psql -1 -f <file> -c "<schema_migrations insert>"` — one transaction, the file named by literal path (`node scripts/db-query.mjs --migration` is unrunnable here: no `SUPABASE_ACCESS_TOKEN`) |
| Hook | allowed it (content hash matched the published one). It was not bypassed, and there was nothing to bypass |
| Statements | `CREATE FUNCTION` ×3, `REVOKE` ×2, `GRANT`, `ALTER TABLE` ×2, `INSERT 0 1` — exit 0 |
| Published SQL integrity | the SQL block in the published GATE-1 report is byte-identical to the local migration file (checked after the relay's redaction pass) |

## The read-back — from `pg_catalog`, never inferred from the DDL that was sent

### 1. Function security and ACLs

| Function | `prosecdef` | `proacl` | `authenticated` EXECUTE | `anon` EXECUTE |
|---|---|---|---|---|
| `sale_issue_within_dispatch` | `t` | `{postgres, service_role}` | **f** | **f** |
| `rls_auto_enable` | `t` | `{postgres, service_role}` | **f** | **f** |
| `customer_payments_log_activity` | `t` | `{postgres, service_role}` | f | f |
| `dispatch_sales_order` | `t` | `{postgres, authenticated, service_role}` | t | f |

Two things are being proved here at once. The guard and `rls_auto_enable` are **off the RPC
surface** — the `authenticated` row that the instance's default privileges had granted is gone, which
is the half a plain `revoke … from public, anon` never removed. And `dispatch_sales_order` **kept**
its `authenticated` grant, which is the §13.7 property `create or replace` is relied on for: a
`DROP`+`CREATE` would have reset it to `EXECUTE TO PUBLIC` and quietly published it to `anon`.

Also read back: **exactly one `pg_proc` row per name**, so no mistyped signature left an old body
alive as a live overload.

### 2. The four `ENABLE ALWAYS` triggers — all still `A`

```
 currencies        | currencies_anchor_immutable    | A
 customer_payments | customer_payments_before_write | A
 customer_payments | customer_payments_log_activity | A
 stock_movements   | sale_issue_within_dispatch     | A
```

`tgenabled` lives on `pg_trigger`, not `pg_proc`, so `create or replace` cannot disturb it — but that
is a claim, and this is the reading that settles it. Both replaced functions kept their promotion.

### 3. The FK

```
 stock_movements_dispatch_item_id_fkey | r | FOREIGN KEY (dispatch_item_id)
                                            REFERENCES sales_dispatch_items(id) ON DELETE RESTRICT
```

Same name, now `confdeltype = 'r'`, matching its four operational siblings. It was dropped and
re-added inside the one transaction, so no window existed in which the link was absent.

### 4. The guard body

Live `pg_get_functiondef` confirms `for no key update` is present and **no bare `for update`
remains**, with six `JHI11` raises — one per binding check plus the kept per-order-line invariant.

## MEASURED BEFORE → AFTER

### The D225 residual: 4 of 4 → 0 of 4

Each arm was given its **own** dispatch line, so the one-issue-per-dispatch-line unique index could
never be the control that answered. Same probe, same fixture shape, run either side of the apply.

| Arm | Before | After |
|---|---|---|
| (i) unrelated **variant** | ACCEPTED | **REFUSED JHI11** — `variant … does not match the dispatched line's variant …` |
| (ii) unrelated **location** | ACCEPTED | **REFUSED JHI11** — `location … does not match the dispatch location …` |
| (iii) a dispatch line of **another order line** | ACCEPTED | **REFUSED JHI11** — `dispatch line … does not belong to order line …` |
| (iv) **5 units against a 2-unit dispatch line** | ACCEPTED | **REFUSED JHI11** — `quantity 5 is not within its dispatch line quantity 2` |

Every arm is refused by the check that names it, not by a neighbour — which is what makes the four
new tests in `sales_dispatch_tests.sql` mean something rather than merely pass.

**The harm, in the ledger.** Before the migration the probe left on-hand at **−1** for a
`(variant, location)` pair that has never held that variant, and −1 for the stocked variant at a
branch it was never dispatched to. After the migration both read **0**. The standing exposure on live
data was **8 dispatch lines carrying 39 units** of unspent budget, every one of which was spendable
against anything, anywhere. It is now spendable only against the line it belongs to.

### D225(d), the deadlock: reproduced → gone

**Before** — two sessions, both `begin … rollback`, the second doing the raw insert this guard exists
to police:

```
  session B blocked on a lock : yes
  deadlock (40P01) observed   : yes
  ERROR:  deadlock detected
  CONTEXT:  while locking tuple (0,10) in relation "sales_order_items"
  SQL statement "SELECT 1 from public.sales_order_items
      where id = new.source_sales_order_item_id for update"
  PL/pgSQL function public.sale_issue_within_dispatch() line 6 at PERFORM
```

**After** — the same probe, unchanged:

```
  session B blocked on a lock : no
  deadlock (40P01) observed   : no
```

`FOR NO KEY UPDATE` does not conflict with the KEY SHARE the FK check already holds, so the race
serialises; it still conflicts with itself, so two raw issues against one order line stay mutually
exclusive. The probe writes nothing — it finds an existing dispatch line rather than creating one,
both sessions roll back, and it reads back that zero rows survived rather than asserting it.

## What is NOT done yet

Tests, the two collateral fixes the first attempt found, the D229 grant-audit hardening, the panel,
`npm run reference` and PR-F. **In particular `scripts/replay-check.sh`'s second arm is red as of
right now, by design** — it pinned the D161 defect as an open residual and D161 has just been closed.
It gets flipped to a positive pin (one audit row, `actor_id` null), not deleted.

```
=== RELAY ===
HEAD: 9c0ed84e3a50f9b5ae4bbd7c9245ad26b95cbd55 | tree: dirty (migration + probe script, uncommitted)
CI: not run yet — replay-check.sh's second arm is knowingly red until T3.3 flips it
DONE: backup verified; GATE-1 published and hash-checked; migration applied in ONE transaction with its schema_migrations row; full catalog read-back clean; residual 4/4 -> 0/4; deadlock reproduced -> gone
FILES: 2 new (supabase/migrations/20260903120000_sale_issue_guard_bind_and_audit_actor.sql, scripts/probe-sale-issue-deadlock.sh)
FINDINGS/BLOCKERS: none
NEXT-NEEDED: none — continuing unattended to T3
=== END ===
```
