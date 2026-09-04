<!-- index: chunk 6 BLOCKED at preflight — the ERP chunk lane has no database lane of its own; nothing was applied, nothing was committed, the approved SQL is carried verbatim -->

# Chunk 6 — BLOCKED at T0/T1. No migration was applied and nothing was changed.

**In one paragraph.** Chunk 6 is a Tier-3 migration chunk, and it was routed here because this box
owns the database lane. The box does — but **the dispatched session does not**, and that is a
different thing. A chunk started by the ERP lane runs headless with one added permission
(`publish-report.sh`) on top of the repository's own allow list, and that allow list contains no
`psql` and no `pg_dump`: both were refused, with nobody present to approve them. The second lane is
gone too — `scripts/db-query.mjs` needs `SUPABASE_ACCESS_TOKEN`, and that variable is in neither the
executor's `.env.local` (which holds exactly three names) nor the session environment. So the
mandatory pre-migration dump, the live-catalog premise checks, the apply, the catalog read-back,
every SQL suite and `npm run reference` were all unreachable. **Nothing was applied, nothing was
committed, no branch exists and the working tree is clean at `9c0ed84`.** The approved SQL is
reproduced verbatim below (`D223`) so the next session can go straight to GATE 1. The read-only work
that *was* reachable got done, and it found two collateral breakages the chunk plan does not name —
one of them in a file CI runs, which would have made GATE 2 unreachable.

-----

## 1. THE STOP — two independent failures, both measured

**This is `D221` one level up.** `D221` says a chunk whose tasks are migrations must be routed to the
box that owns the database lane. This chunk was. The refinement it did not anticipate: **the lane
belongs to a SESSION, not to a box.** The preflight question has to be *"can this session take a real
dump and reach the database"*, executed — not *"is this the right box"*, asserted.

### Failure 1 — the PRIMARY lane (direct `psql` / `pg_dump`) is not executable by a dispatched session

| | |
|---|---|
| Measured | `psql --version` → refused, needs approval. `pg_dump --version` → refused, needs approval. `command -v psql` → refused. |
| Why | The lane runs `claude -p … --permission-mode acceptEdits --allowedTools "$CHUNK_ALLOW"`, and `CHUNK_ALLOW` is exactly one rule — `Bash(/opt/jahjah/session/publish-report.sh:*)` (`infra/vps/web-dispatch/run-chunk.sh:96`). Everything else comes from the repository's own `.claude/settings.json` + `settings.local.json`, and **neither grants `psql` or `pg_dump`.** In a headless run there is nobody to answer the permission request, so the refusal is final. |
| NOT the cause | The binaries are installed (`/usr/bin/pg_dump` resolves through `pg_wrapper`), and the box's own nightly `jahjah-backup` job uses them successfully against the live database — last run **2026-09-03T02:00:04Z, 1,764,997 bytes, 95 `CREATE TABLE`, 3s, 4 dumps kept**. Credentials are present. This is a session-permission limit, not a box, network or credential limit. |

### Failure 2 — the FALLBACK lane (Management API) has no token on this box

| | |
|---|---|
| Measured | The executor's `.env.local` carries exactly three variable names: `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_DB_PASSWORD`. **No `SUPABASE_ACCESS_TOKEN`.** Not in the session environment either (checked by name-presence only; no value was read or printed). |
| Consequence | `node --env-file=.env.local scripts/db-query.mjs …` exits 1 with `Missing SUPABASE_ACCESS_TOKEN or NEXT_PUBLIC_SUPABASE_URL in env.` — measured. That helper is simultaneously the documented apply command (`--migration`), the read lane, the SQL-suite runner and the DB half of `npm run reference`. All four are down. |
| **Canon drift worth fixing** | `docs/runbooks/backup.md` §3 says of `--migration`: *"it is effective on the VPS work engine."* **On this box, today, that sentence is false** — the token it needs is not here. `CLAUDE.md` §8 lists `SUPABASE_ACCESS_TOKEN` as one of the five locked variable names, so its absence is a gap, not a design choice. |

### What was deliberately NOT done

- **No apply by any other route.** `D228` is explicit: *a refusal is a stop and a report, never a
  workaround.* No hand-assembled wrapper, no `npx supabase db push` (which would also have needed the
  password on a command line — a secret into a log), no second clone.
- **No self-granted permission.** Writing `psql` into `.claude/settings.local.json` is exactly the
  "a chunk edits the file that grants its own permissions" move `D231` names as the reason the label
  gate is a convention. `.claude/**` is path-guarded anyway.
- **No backup taken.** A dump with no apply behind it protects nothing; taking one would have been
  theatre. (The mechanism is available and is part of the recommendation below — see §4.)
- **No migration file written, no branch, no commit.** `D221`: a migration branch pushed without an
  apply *"is not a partial delivery but a red branch plus an unapplied migration file, which is worse
  than nothing."* The SQL travels in §5 of this report instead, which is what `D223` exists for.

-----

## 2. WHAT WAS REACHABLE AND WAS DONE — T1.1, against the repo rather than the live catalog

Every premise the chunk asks to confirm was checked against `docs/reference/db.md` (generated from
`pg_catalog` and committed with `20260902120000`) and against the migration sources. **Every one
holds.** This is strong evidence, not live evidence — the next session must still re-read the live
catalog before GATE 1, because §5's `IDENTIFIER RULE` turns on it.

| Premise (T1.1) | Found | Source |
|---|---|---|
| `sale_issue_within_dispatch()` is `SECURITY DEFINER` | **DEFINER**, `search_path=""` | `db.md:1612` |
| …its trigger is `ENABLE ALWAYS` | **`always`** — `AFTER INSERT ON public.stock_movements FOR EACH ROW WHEN (new.movement_type = 'sale_issue')` | `db.md:1966` |
| …`EXECUTE` reaches `authenticated` via default privileges | ACL = `postgres=X/postgres` **`authenticated=X/postgres`** `service_role=X/postgres` — no `anon`, no bare PUBLIC | `db.md:1612`, `db.md:2149` |
| The FK on `stock_movements.dispatch_item_id` carries **no** explicit `on delete` | `FOREIGN KEY (dispatch_item_id) REFERENCES sales_dispatch_items(id)` — **NO ACTION** | `db.md:1285` |
| …its constraint name | **`stock_movements_dispatch_item_id_fkey`** — the name §5's SQL drops and re-adds | `db.md:1285` |
| …the siblings' posture | `location_id`, `source_sales_order_item_id`, `source_sales_return_item_id`, `variant_id` → **RESTRICT**. `source_shipment_item_id`, `created_by` → SET NULL. So "matching its siblings' RESTRICT" is true of the four operational FKs; the two SET NULL siblings are provenance columns. | `db.md:1284-1290` |
| `customer_payments_log_activity()` early-returns before the audit insert | **Yes** — `if v_actor is null then return null; end if;` is line 202, the `customer_payment_audit` insert is line 224 | `20260825120600:202` |
| …and its ACL | `postgres=X/postgres service_role=X/postgres` — exactly the `{postgres, service_role}` §5's header claims `create or replace` preserves | `db.md:1551` |
| `rls_auto_enable()` is executable by `anon`/`authenticated` | **DEFINER**, ACL `=X/postgres` (PUBLIC) `postgres=X/…` **`anon=X/…`** **`authenticated=X/…`** `service_role=X/…` — the advisor's finding reproduces from the reference | `db.md:1603`, `db.md:2140` |
| JHI codes in use | `JHI01`–`JHI10`. **`JHI11` and `JHI12` are free** — the identifier rule's fallback is not needed | grep of `supabase/migrations/` |

**One drafting note for the next session, not a semantic objection.** The live body computes `v_ccy`
*after* the null-actor early return, alongside `v_actor_name` / `v_code` / `v_name`. §5's rewrite
correctly hoists the `v_ccy` lookup above the audit insert, because the audit row needs
`currency_code`. That is required and is present; it is called out only so a reviewer does not read
it as an unexplained move.

-----

## 3. T1.2 — THE §13.14 COLLATERAL SWEEP (repo-only, so it is complete and will not change)

This is the part of the chunk that survives the stop intact. **Two of these are not on the chunk's
task list, and one of them runs in CI.**

### 3a. Two collateral breakages the plan does not name

**(i) `supabase/tests/activity_log_tests.sql` block 5a-ii (lines 183–210) — HARD RED.**
The block strips line comments from `pg_get_functiondef`, splits the result at the **first**
occurrence of the literal `customer_payment_audit`, and asserts on the **left** half: it must contain
`'payment_id'` and `'currency_code'`, and must not contain `'amount'` / `'amount_operating'` /
`'fx_rate'`. Its own comment states the assumption out loud — *"The body writes the activity_log
insert FIRST and the customer_payment_audit insert SECOND."*
**§5's D161 rewrite inverts exactly that order.** After the change the left half is only the
declaration block and the audit-insert prelude, so it carries neither `'payment_id'` nor
`'currency_code'`, and the suite raises at line 207: *"the cost-free detail payload lost
payment_id/currency_code"*. It fails loudly rather than passing vacuously, which is the good
direction — but it must be re-anchored in the same change (split on the `activity_log` insert, or on
the **last** occurrence, not the first). The invariant it protects is still worth protecting: no money
key may reach `activity_log.detail`.

**(ii) `scripts/replay-check.sh` SECOND ARM (lines 356–375) — RED, AND IT RUNS IN CI.**
`ci.yml:164` runs `bash scripts/replay-check.sh`. Its second arm asserts that a no-actor
`customer_payments` INSERT writes **zero** audit rows. That is the `D161` residual, pinned
deliberately by `D222`, and §5 closes it — so the arm goes red the moment the migration lands and
**GATE 2 cannot be reached until it is updated.** Its failure message says *"delete this arm"*;
**do not.** The chunk's own T1.2 and `CLAUDE.md` §11O both prescribe the other move: flip it to a
POSITIVE pin — one row, `actor_id is null`, the correct figure — because a deleted case proves
nothing (§13.12). The message text should be rewritten at the same time so it stops pointing at a
closed defect.

### 3b. Two assertions the plan would wrongly flip

T3.3 says to convert *"wherever the old 'no row' was asserted"* into a positive pin in `fx_cut_tests`
/ `sales_payments_returns_tests` / `activity_log_tests`. **Read literally that breaks two correct
tests.** The only no-**actor** "no audit row" pin in the repository is `replay-check.sh`'s second arm
above. What those suites actually assert is the **`edited` event** producing no audit row, with an
actor present throughout:

- `fx_cut_tests.sql` M4, lines 1093–1106 — *"an edit produces NO audit row"*, then `count = 2` for the
  recorded/deleted pair.
- `sales_payments_returns_tests.sql` TEST 19, lines 1028–1030 — the same negative half.

§5 keeps `if tg_op in ('INSERT','DELETE')` unchanged, so **neither is affected and neither may be
flipped.** `activity_log_tests.sql` block 3 ("null-actor skip") writes `public.roles`, not payments —
also unaffected.

### 3c. Sites that must be re-run after the apply (behaviour unchanged, coverage load-bearing)

| File | Why it is in the blast radius |
|---|---|
| `sales_dispatch_tests.sql` TEST 12(b) line 795, TEST 12(c) line 808 | Pin `23514` **and** the message text `'sale_issue exceeds dispatched total'`, precisely because a CHECK shares that SQLSTATE. Both become `JHI11` pins — this is T3.2(vi), and its premise is confirmed. |
| `sales_dispatch_tests.sql` TEST 11, lines 676–724 | Both arms pin the CHECK **constraint name** and reason in comments that *"the D218 CHECK/guard share its SQLSTATE"*. Once the guard raises `JHI11` that reasoning is obsolete; the arms get sharper, but their comments become false if left (§13.12 — change both sides in one commit). |
| `sales_dispatch_tests.sql` TEST 12(d) lines 820–832 | Partial-then-completing dispatch. The case the order-grain index broke (`D218`); the new binding must not break it either. |
| `sales_payments_returns_tests.sql` TEST 10, lines 581–610 | Writes a `sale_issue` through `dispatch_sales_order` for the moving-average neutrality proof. Satisfied by construction under the new binding — but it is a real ledger write and must be re-run. |
| `reference_data_tests.sql` block 10g, line 500 | The live-side pin on `tgenabled` for the four `ENABLE ALWAYS` triggers, `sale_issue_within_dispatch` among them. `create or replace` preserves `tgenabled` (it lives on `pg_trigger`, not `pg_proc`) — this block is what proves it, so it is the read-back, not a formality. |
| `security_sweep_hardening_tests.sql` line 34/37 | `revoked text[]`, commented *"the exact 37 signatures the migration revokes"*. T3.1's **37 → 39** is correct. |
| `supabase/seed/32_reliability_examples.sql` line 27 | *"the dispatch headers here post NO `sale_issue` — posting is RPC-only"*. Confirms no seed writes a `sale_issue`; this seed is also the source of `D225`'s 8 lines / 39 units of unspent budget. |

No seed writes a `sale_issue`. `D225`'s embed sweep (§13.8) does not need re-running: §5 drops and
re-adds the same FK on the same pair inside one transaction, so the relationship set is unchanged.

-----

## 4. THE DECISION NEEDED

**A — hand-drive it.** The owner opens an interactive session on the work engine and pastes chunk 6
there, approving `pg_dump` and `psql` at the prompt.
*Costs:* it needs a person at the keyboard for a Tier-3 migration, and it reaches the database by the
**non-atomic** path — `psql -f` is autocommit statement-at-a-time (`D212(a)`), which is the exact
mechanism that produced `D219`'s two unapproved indexes on the live database. It would have to be
`psql -1 -f`, and the `schema_migrations` row inserted by hand. The GATE-1 accident guard still holds
on that path (the `PreToolUse` hook covers the raw `psql` forms), so this is workable — it is just the
worse of two working options.

**B — give the lane what the canon already says it has, then re-dispatch chunk 6 unattended.**
Two small changes, neither of which widens the trust boundary `D231` already accepted:
1. **Put `SUPABASE_ACCESS_TOKEN` in the executor's `.env.local`.** It is already one of the five
   locked names in `CLAUDE.md` §8 and already exists as a CI secret. The box holds
   `SUPABASE_DB_PASSWORD` today, which is strictly more powerful, so this adds no new capability to
   anyone — it only makes `docs/runbooks/backup.md` §3 true again. This alone restores the read lane,
   the suites, `npm run reference`, **and the documented apply command**, because `Bash(node:*)` is
   already allow-listed.
2. **Give the chunk a real pre-migration dump.** Either add `Bash(pg_dump:*)` to the project-local
   `.claude/settings.local.json`, or have the chunk trigger the registered `jahjah-backup` job through
   the already-granted `systemctl start` — it is a real `pg_dump` of `public`+`auth`+`storage`, it
   verifies non-empty / ≥ 200 000 bytes / contains `CREATE TABLE` before it rotates anything, and it
   leaves size, table count and file name in `state/` where the chunk can read them back. Rotation
   keeps 7 and currently holds 4, so an extra run prunes nothing. The one gap is naming: it writes
   `nightly_<stamp>.sql`, not `pre_<migration>_<date>.sql`, and `automations.md` line 107 already
   notes that rotation only matches the `nightly_*` prefix.

**Recommendation: B.** It is the smaller change; it keeps the migration on the documented apply
command and therefore inside **both** halves of the `D228` guard; it keeps the atomic
`begin` / `commit` wrapper that `psql -f` does not give; and it fixes the class rather than this
instance, so the next migration chunk is dispatchable instead of being another hand-driven exception.
Option A is the fallback if the token cannot be placed today.

**The null option, named so it is a choice and not a drift:** accept that migration chunks are not
dispatchable and route them all through A. That contradicts `D231`, which records applying migrations
as something the box was *already* trusted to do, and it makes the ERP lane a lane for everything
except the work it was hardest to build.

**Whichever is chosen, three things should land with it** (all outside this chunk's file list, none
applied here): the `replay-check.sh` and `activity_log_tests.sql` fixes from §3a folded into the
migration PR; the T3.3 instruction corrected per §3b; and a register entry for the
session-versus-box refinement of `D221` in §1.

-----

## 5. THE APPROVED SQL, VERBATIM (`D223`)

Reproduced byte-for-byte from the chunk prompt. **Nothing here was applied, edited or reordered.**
The next session can take this straight to GATE 1.

**Target file:** `supabase/migrations/20260903120000_sale_issue_guard_bind_and_audit_actor.sql`

**IDENTIFIER RULE, carried with the SQL because it is part of the approval.** Identifier names only
may be corrected — constraint names, and the two SQLSTATE literals `JHI11` / `JHI12` if taken (take
the next unused; **§2 confirms both are free**). The final SQL, its diff against this text, and
`SHA256: <sha256sum of the final file>` must be published in the GATE-1 report and pushed **before**
the apply. **Any semantic change is a BLOCKED stop.**

**NAMING, decided by the owner — `D225`(g):** the column **keeps** the name `dispatch_item_id`.
Consistency with the `source_*_id` siblings is not worth a migration, a type regeneration and app
edits.

```sql
-- D225 (a)-(f) + D161 + the rls_auto_enable ACL, in ONE migration behind ONE fresh GATE 1 (chunk 6).
--
-- (a) THE GUARD NOW BINDS WHAT IT GATES. 20260902120000's guard compared quantities per ORDER line
-- and bound nothing else, so a dispatch line with unspent budget was spendable against an unrelated
-- variant at an unrelated location (measured: 8 lines, 39 units, a 5-unit issue ACCEPTED — D225).
-- The guard now requires the dispatch line to belong to the order line, the variant to match, the
-- location to match, and the issue to stay inside its own line's quantity; the per-order-line total
-- invariant is kept. Still SECURITY DEFINER, still ENABLE ALWAYS, for the D220 reason: the actor it
-- exists to stop cannot read the three tables it consults.
-- (b) EXECUTE is revoked from authenticated too. `revoke ... from public, anon` does not remove this
-- instance's default-privilege grant to authenticated (20260825120800 hit the same thing), which is
-- why the advisor still reported the guard callable via /rest/v1/rpc. A `returns trigger` function
-- refuses direct invocation (0A000) so it was not exploitable; it is now a complete §13.7 pin.
-- (c) A private SQLSTATE. Three CHECK constraints on this table also raise 23514, so a test could
-- not discriminate on the code; the guard now raises JHI11 and the writer's row-count guard JHI12.
-- (d) `for no key update`, not `for update`. The FK check takes KEY SHARE on the order line; FOR
-- UPDATE conflicts with it, so a raw insert racing a legitimate dispatch was a mutual-upgrade
-- deadlock (40P01) on the one path this guard polices — the raw insert takes no advisory lock.
-- NO KEY UPDATE does not conflict with KEY SHARE: the race now serialises.
-- (e) The FK carries an explicit ON DELETE RESTRICT, matching its siblings: a dispatch line that a
-- ledger row cites cannot be deleted from under it.
-- (f) The writer checks its own INSERT's row count: a join that matched nothing used to record a
-- dispatch that drained nothing, silently.
-- (g) NAMING, decided by the owner: the column keeps the approved name dispatch_item_id. Consistency
-- with the source_*_id siblings is not worth a migration, a type regeneration and app edits.
--
-- D161: the payment audit writer now writes the audit row in EVERY context, with a null actor when
-- there is none. customer_payment_audit.actor_id was nullable for exactly this. Only the cost-free
-- activity_log row keeps the system-context skip. Three sessions across two chunks independently
-- rediscovered this gap; this closes it.
--
-- rls_auto_enable(): a SECURITY DEFINER event-trigger function executable by anon and authenticated
-- through PostgREST (Supabase security advisor, 2026-09-02). Event-trigger execution needs no grant
-- to the DDL runner here (migrations run as the owner), so the revoke costs nothing and closes the
-- advisory.
--
-- ROWS: no data changes. `create or replace` with identical signatures preserves each function's
-- ACL and trigger binding (§13.7); the FK is dropped and re-added in the same transaction with the
-- same name, so no window exists in which the link is missing.

-- ---------------------------------------------------------------------------------------------
-- 1. THE GUARD — (a) binding, (c) private SQLSTATE, (d) no-key-update
-- ---------------------------------------------------------------------------------------------
create or replace function public.sale_issue_within_dispatch()
returns trigger
language plpgsql
security definer
set search_path = ''
as $$
declare
  v_dispatched      integer;
  v_issued          integer;
  v_line_qty        integer;
  v_line_order_item uuid;
  v_line_location   uuid;
  v_line_variant    uuid;
begin
  -- (d) KEY SHARE is what the FK check already holds on this row; NO KEY UPDATE does not conflict
  -- with it, so a raw insert racing a legitimate dispatch serialises instead of deadlocking.
  perform 1 from public.sales_order_items
    where id = new.source_sales_order_item_id for no key update;

  -- (a) the dispatch line must exist, belong to this order line, carry this variant, sit at this
  -- location, and the issue must stay inside that line's own quantity.
  select d.quantity, d.order_item_id, sd.location_id, i.variant_id
    into v_line_qty, v_line_order_item, v_line_location, v_line_variant
    from public.sales_dispatch_items d
    join public.sales_dispatches sd on sd.id = d.dispatch_id
    join public.sales_order_items i on i.id = d.order_item_id
    where d.id = new.dispatch_item_id;

  if v_line_qty is null then
    raise exception 'sale_issue names no dispatch line (%)', new.dispatch_item_id
      using errcode = 'JHI11';
  end if;
  if v_line_order_item <> new.source_sales_order_item_id then
    raise exception 'sale_issue dispatch line % does not belong to order line %',
      new.dispatch_item_id, new.source_sales_order_item_id using errcode = 'JHI11';
  end if;
  if v_line_variant <> new.variant_id then
    raise exception 'sale_issue variant % does not match the dispatched line''s variant %',
      new.variant_id, v_line_variant using errcode = 'JHI11';
  end if;
  if v_line_location <> new.location_id then
    raise exception 'sale_issue location % does not match the dispatch location %',
      new.location_id, v_line_location using errcode = 'JHI11';
  end if;
  if new.quantity_delta >= 0 or -new.quantity_delta > v_line_qty then
    raise exception 'sale_issue quantity % is not within its dispatch line quantity %',
      -new.quantity_delta, v_line_qty using errcode = 'JHI11';
  end if;

  -- the per-order-line invariant from 20260902120000, kept: never drain more than dispatched.
  select coalesce(sum(quantity), 0) into v_dispatched
    from public.sales_dispatch_items
    where order_item_id = new.source_sales_order_item_id;

  select coalesce(sum(-quantity_delta), 0) into v_issued
    from public.stock_movements
    where movement_type = 'sale_issue'
      and source_sales_order_item_id = new.source_sales_order_item_id;

  if v_issued > v_dispatched then
    raise exception 'sale_issue exceeds dispatched total for order item %',
      new.source_sales_order_item_id using errcode = 'JHI11';
  end if;
  return new;
end $$;

-- (b) the complete §13.7 pin: authenticated named explicitly, because the default-privilege grant
-- survives `revoke ... from public, anon`.
revoke all on function public.sale_issue_within_dispatch() from public, anon, authenticated;
grant execute on function public.sale_issue_within_dispatch() to postgres, service_role;

-- ---------------------------------------------------------------------------------------------
-- 2. (e) THE FK POSTURE — explicit RESTRICT, same name, same transaction
-- ---------------------------------------------------------------------------------------------
alter table public.stock_movements
  drop constraint stock_movements_dispatch_item_id_fkey;
alter table public.stock_movements
  add constraint stock_movements_dispatch_item_id_fkey
  foreign key (dispatch_item_id) references public.sales_dispatch_items(id) on delete restrict;

-- ---------------------------------------------------------------------------------------------
-- 3. (f) THE WRITER — row-count check on the ledger INSERT
-- `create or replace` with the identical signature PRESERVES the ACL (§13.7). The body is
-- 20260902120000's live definition with two declarations and one check block added after the
-- stock_movements insert; nothing else moves.
-- ---------------------------------------------------------------------------------------------
CREATE OR REPLACE FUNCTION public.dispatch_sales_order(p_order_id uuid, p_location_id uuid, p_lines jsonb, p_occurred_at timestamp with time zone DEFAULT NULL::timestamp with time zone)
 RETURNS jsonb
 LANGUAGE plpgsql
 SECURITY DEFINER
 SET search_path TO ''
AS $function$
declare
  v_actor        uuid := (select auth.uid());
  v_status       public.sales_order_status;
  v_customer     uuid;
  v_terms        uuid;
  v_branch       uuid;
  v_loc_branch   uuid;
  v_active       boolean;
  v_when         timestamptz := coalesce(p_occurred_at, now());
  v_total        bigint;
  v_rel_at       timestamptz;
  v_rel_val      bigint;
  v_reason       text;
  v_dispatch     uuid := gen_random_uuid();
  rec            record;
  v_lines        integer := 0;
  v_units        bigint  := 0;
  v_order_status text;
  v_order_no     text;
  v_actor_name   text;
  v_written      integer := 0;
  v_expected     integer := 0;
begin
  -- 1. Capability gate.
  if not (public.user_has_permission('dispatch_sales_orders')
          or public.user_has_permission('manage_sales_orders')) then
    raise exception 'insufficient privilege to dispatch a sales order' using errcode = '42501';
  end if;

  -- 2. LOCK (i): serialize per-order (guards the status flip + the credit re-check).
  perform pg_advisory_xact_lock(hashtext('sales_dispatch:' || p_order_id::text)::bigint);

  -- 3. Order must exist + be 'confirmed'. Read the FROZEN customer/terms/branch + release
  --    stamp AS OWNER (the dock caller cannot read sales_orders).
  select so.status, so.customer_id, so.payment_terms_id, so.branch_id,
         so.credit_released_at, so.credit_released_value_minor
    into v_status, v_customer, v_terms, v_branch, v_rel_at, v_rel_val
  from public.sales_orders so
  where so.id = p_order_id;
  if v_status is null then
    raise exception 'sales order not found' using errcode = 'JHS03';
  end if;
  if v_status <> 'confirmed' then
    raise exception 'only a confirmed sales order can be dispatched' using errcode = 'JHS03';
  end if;

  -- 4. Branch integrity: the dispatch location must belong to the ORDER's branch.
  select dl.branch_id, dl.is_active into v_loc_branch, v_active
  from public.delivery_locations dl where dl.id = p_location_id;
  if v_loc_branch is null or not v_active then
    raise exception 'dispatch location not found or inactive' using errcode = 'JHS04';
  end if;
  if v_loc_branch <> v_branch then
    raise exception 'the dispatch location is not in the order''s branch' using errcode = 'JHS04';
  end if;

  -- 5. Validate the request. First at the ELEMENT level (a malformed payload): at least one
  --    line; every element carries a line id and a positive integer quantity (so the sum is
  --    well-defined and non-null). Then at the AGGREGATED level (JHS05 on any bad line): the
  --    line belongs to THIS order; NOT short-closed; requested <= the line's REMAINING (ordered
  --    − Σ prior dispatched, from the sales-gated sales_dispatch_items).
  if not exists (
    select 1 from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
                    as l(order_item_id uuid, quantity integer)
  ) then
    raise exception 'a dispatch must include at least one line' using errcode = 'JHS05';
  end if;
  if exists (
    select 1 from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
                    as l(order_item_id uuid, quantity integer)
    where l.order_item_id is null or l.quantity is null or l.quantity <= 0
  ) then
    raise exception 'invalid dispatch line (unknown, short-closed, non-positive, or over the remaining quantity)'
      using errcode = 'JHS05';
  end if;
  if exists (
    with req as (
      select l.order_item_id as oiid, sum(l.quantity)::bigint as qty
      from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
             as l(order_item_id uuid, quantity integer)
      group by l.order_item_id
    )
    select 1 from req
    left join public.sales_order_items i
      on i.id = req.oiid and i.order_id = p_order_id
    where req.qty <= 0
       or i.id is null
       or i.short_closed_at is not null
       or req.qty > (i.quantity - coalesce((
              select sum(di.quantity) from public.sales_dispatch_items di
              where di.order_item_id = i.id), 0))
  ) then
    raise exception 'invalid dispatch line (unknown, short-closed, non-positive, or over the remaining quantity)'
      using errcode = 'JHS05';
  end if;

  -- 6. LOCK (ii): lock each DISTINCT (variant, location) pair in SORTED order (deadlock-free)
  --    BEFORE any availability read — the per-order lock alone does not serialize two orders
  --    draining the same (variant, location).
  for rec in
    select distinct i.variant_id as vid
    from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
           as l(order_item_id uuid, quantity integer)
    join public.sales_order_items i on i.id = l.order_item_id
    order by i.variant_id
  loop
    perform pg_advisory_xact_lock(
      hashtext('sales_dispatch_stock:' || rec.vid::text || ':' || p_location_id::text)::bigint);
  end loop;

  -- 7. Availability under the pair locks: Σ requested per variant at the location must not
  --    exceed on-hand there. Read the ledger AS OWNER (stock_on_hand would fail-closed for a
  --    dock caller). A NEVER-MOVED (variant, location) has NO row -> coalesce to 0 (the
  --    reorder-alerts lesson) so an unstocked variant fails with the neutral shortfall error.
  if exists (
    with req as (
      select i.variant_id as vid, sum(l.quantity)::bigint as qty
      from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
             as l(order_item_id uuid, quantity integer)
      join public.sales_order_items i on i.id = l.order_item_id
      group by i.variant_id
    )
    select 1 from req
    where req.qty > coalesce((
      select sum(sm.quantity_delta) from public.stock_movements sm
      where sm.variant_id = req.vid and sm.location_id = p_location_id), 0)
  ) then
    raise exception 'insufficient stock at the dispatch location' using errcode = 'JHS06';
  end if;

  -- 8. CREDIT RE-CHECK (block-at-confirm AND re-check-at-dispatch). Exclude THIS order from
  --    exposure (already counted as 'confirmed' -> no double-count; identity-preserving with
  --    Confirm). A valid release stamp covering the total passes; a CASH order passes even
  --    under a hold. On a block: RETURN the typed reason, write NOTHING.
  v_total := public.sales_order_total(p_order_id);
  v_reason := public.sales_order_credit_check(
                v_customer, v_terms, v_total, v_rel_at, v_rel_val, p_order_id);
  if v_reason is not null then
    return jsonb_build_object('result', 'credit_blocked', 'reason', v_reason);
  end if;

  -- 9. Writes (all AS OWNER; ids pre-generated; NO RETURNING — the §13 trap). The header, one
  --    dispatch item per line, and one sale_issue per line (quantity_delta = −qty, unit_cost
  --    NULL, location = p_location, occurred_at = v_when, source = the line). created_by is
  --    trigger-forced on the header; the sale_issue's created_by is trigger-forced too.
  insert into public.sales_dispatches (id, order_id, location_id, dispatched_at, notes)
    values (v_dispatch, p_order_id, p_location_id, v_when, null);

  insert into public.sales_dispatch_items (id, dispatch_id, order_item_id, quantity)
  select gen_random_uuid(), v_dispatch, req.oiid, req.qty
  from (
    select l.order_item_id as oiid, sum(l.quantity)::integer as qty
    from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
           as l(order_item_id uuid, quantity integer)
    group by l.order_item_id
  ) req;

  -- D218 part (c): every sale_issue now carries the DISPATCH LINE it belongs to, which is the true
  -- grain of an issue (an ORDER line legitimately receives one movement per partial dispatch -- the
  -- reason the originally-proposed order-line index could not be applied). The dispatch rows were
  -- inserted immediately above, so the join is on the pair backed by the unique index
  -- sales_dispatch_items_dispatch_item_key (dispatch_id, order_item_id) and resolves to exactly one
  -- row per line. No RETURNING: this table's write key is not its read key (docs/pitfalls/
  -- inventory-stock.md), so a RETURNING here would be denied for a writer without a cost key.
  insert into public.stock_movements
    (id, variant_id, location_id, movement_type, quantity_delta, unit_cost,
     reason, occurred_at, source_sales_order_item_id, dispatch_item_id)
  select gen_random_uuid(), i.variant_id, p_location_id, 'sale_issue', -req.qty, null,
         null, v_when, req.oiid, d.id
  from (
    select l.order_item_id as oiid, sum(l.quantity)::integer as qty
    from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
           as l(order_item_id uuid, quantity integer)
    group by l.order_item_id
  ) req
  join public.sales_order_items i on i.id = req.oiid
  join public.sales_dispatch_items d
    on d.dispatch_id = v_dispatch and d.order_item_id = req.oiid;

  -- D225 (f): a join that matched nothing must fail loudly, never record a dispatch that drained
  -- nothing. Exactly one ledger row per distinct order line requested.
  get diagnostics v_written = row_count;
  select count(*)::integer into v_expected
  from (
    select l.order_item_id
    from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
           as l(order_item_id uuid, quantity integer)
    group by l.order_item_id
  ) req;
  if v_written <> v_expected then
    raise exception 'dispatch wrote % stock movements for % order lines',
      v_written, v_expected using errcode = 'JHI12';
  end if;

  select count(*)::integer, coalesce(sum(req.qty), 0)::bigint into v_lines, v_units
  from (
    select l.order_item_id as oiid, sum(l.quantity)::integer as qty
    from jsonb_to_recordset(coalesce(p_lines, '[]'::jsonb))
           as l(order_item_id uuid, quantity integer)
    group by l.order_item_id
  ) req;

  -- 10. If EVERY line is now fully covered (dispatched >= ordered OR short-closed), flip the
  --     order to 'dispatched' (the trigger re-verifies coverage). Else it stays 'confirmed'
  --     (a backorder remains).
  v_order_status := 'confirmed';
  if not exists (
    select 1 from public.sales_order_items i
    where i.order_id = p_order_id
      and i.short_closed_at is null
      and i.quantity > coalesce((
        select sum(di.quantity) from public.sales_dispatch_items di
        where di.order_item_id = i.id), 0)
  ) then
    update public.sales_orders set status = 'dispatched' where id = p_order_id;
    v_order_status := 'dispatched';
  end if;

  -- 11. One COST-FREE oversight row (units / lines only; entity 'sales_order').
  select order_no into v_order_no from public.sales_orders where id = p_order_id;
  select full_name into v_actor_name from public.profiles where id = v_actor;
  insert into public.activity_log (actor_id, action, entity_type, entity_id, detail)
  values (v_actor, 'dispatched', 'sales_order', p_order_id,
    jsonb_build_object('actor_name', v_actor_name, 'doc', v_order_no,
                       'lines', v_lines, 'units', v_units, 'order_status', v_order_status));

  return jsonb_build_object(
    'result', 'posted',
    'order_status', v_order_status,
    'lines_dispatched', v_lines,
    'units_dispatched', v_units);
end;
$function$;

-- ---------------------------------------------------------------------------------------------
-- 4. D161 — THE AUDIT ROW IN EVERY CONTEXT
-- `create or replace` with the identical signature PRESERVES the {postgres, service_role} ACL and
-- the AFTER-trigger binding (§13.7). The body is 20260825120600's with the system-context skip
-- moved BELOW the audit insert, so the skip covers only the cost-free activity_log row.
-- ---------------------------------------------------------------------------------------------
create or replace function public.customer_payments_log_activity()
returns trigger
language plpgsql
security definer
set search_path to ''
as $function$
declare
  v_actor      uuid := (select auth.uid());
  v_actor_name text;
  v_code       text;
  v_name       text;
  v_customer   uuid := coalesce(new.customer_id, old.customer_id);
  v_reference  text := coalesce(new.reference, old.reference);
  v_payment    uuid := coalesce(new.id, old.id);
  v_currency   uuid := coalesce(new.currency_id, old.currency_id);
  v_ccy        text;
  v_row        record;
  v_action     text := case tg_op when 'INSERT' then 'recorded'
                                  when 'UPDATE' then 'edited'
                                  else 'deleted' end;
begin
  select code into v_ccy from public.currencies where id = v_currency;

  -- THE FIGURES, behind the payment key, in EVERY context (D161). actor_id is nullable for exactly
  -- the system-context case: a service-role or migration write of a booked figure is now audited
  -- with a null actor instead of leaving no trace where the freeze does not hold.
  if tg_op in ('INSERT', 'DELETE') then
    -- Explicit branch, NOT `case when ... then new else old end`: NEW/OLD are plpgsql
    -- records and a CASE expression over them is not reliably resolvable.
    if tg_op = 'INSERT' then v_row := new; else v_row := old; end if;
    insert into public.customer_payment_audit
      (payment_id, event, amount, fx_rate, amount_operating, currency_code, actor_id)
    values (v_row.id, v_action, v_row.amount, v_row.fx_rate, v_row.amount_operating,
            v_ccy, v_actor);
  end if;

  -- The cost-free oversight row keeps the system-context skip (activity_log's own posture).
  if v_actor is null then return null; end if;

  select full_name into v_actor_name from public.profiles   where id = v_actor;
  select code, name into v_code, v_name from public.customers where id = v_customer;

  insert into public.activity_log (actor_id, action, entity_type, entity_id, detail)
  values (v_actor, v_action, 'customer_payment', v_customer,
    jsonb_build_object('actor_name', v_actor_name, 'customer_code', v_code,
                       'customer_name', v_name, 'reference', v_reference,
                       'payment_id', v_payment, 'currency_code', v_ccy));

  return null;
end;
$function$;

-- ---------------------------------------------------------------------------------------------
-- 5. rls_auto_enable() — off the RPC surface
-- ---------------------------------------------------------------------------------------------
revoke all on function public.rls_auto_enable() from public, anon, authenticated;

-- END OF MIGRATION
```

-----

## 6. WHAT THE NEXT SESSION INHERITS

- The SQL above, verbatim, with its identifier rule and the `D225`(g) naming decision — GATE 1 is
  re-crossable from this file alone.
- T1.1 confirmed against `docs/reference/db.md`; only the live re-read remains.
- T1.2 complete and stable (§3) — including the two breakages the plan does not name and the two
  assertions it would have wrongly flipped.
- T1.3 **not** measured (the D225 residual count and the D225(d) deadlock probe both need the live
  database). T3.4's probe script was not written; it would have had nothing to run against.

```
=== RELAY ===
HEAD: 9c0ed84 (main, = origin/main, PR-E #99) | tree: clean
CI: not run — no database lane, so the sql/replay jobs could not be exercised locally; no branch and no PR were created, so nothing was submitted to CI
DONE: nothing applied and nothing committed — chunk 6 STOPPED at preflight. T0 passed on every other check (HEAD = origin/main with chunk 5's PR-E as HEAD itself; tree clean; collaborators = obidex only, 0 invitations). T1.1 confirmed all ten premises against docs/reference/db.md and the migration sources — every one holds and JHI11/JHI12 are free. T1.2 (the §13.14 collateral sweep) completed in full. The approved SQL is carried verbatim in the report (D223).
FILES: none created, none modified, none committed. Working tree clean.
FINDINGS/BLOCKERS: (1) THE STOP — no database lane for a DISPATCHED session. psql and pg_dump are refused (the lane adds only Bash(/opt/jahjah/session/publish-report.sh:*) to the repo allow list, which grants neither, and a headless run has nobody to approve them); and SUPABASE_ACCESS_TOKEN is absent from the executor's .env.local (3 names only) and from the environment, so scripts/db-query.mjs — the documented apply command, the read lane, the suite runner and the DB half of npm run reference — exits 1. Measured both. Nothing was routed around: no alternative apply path, no self-granted permission, no backup taken for an apply that could not happen. This refines D221 — the lane belongs to a SESSION, not a box, and the preflight must EXECUTE the check rather than assert the routing. (2) CANON DRIFT: docs/runbooks/backup.md §3 says --migration "is effective on the VPS work engine"; that is false on this box today, because the token it needs is not here. (3) TWO COLLATERAL BREAKAGES THE PLAN DOES NOT NAME — scripts/replay-check.sh's second arm (lines 356-375) asserts a no-actor payment writes ZERO audit rows, which D161 closes; it runs in CI (ci.yml:164), so GATE 2 is unreachable until it is flipped to a positive pin (flip, do not delete — its own message says "delete", §11O and D161 say otherwise). And activity_log_tests.sql block 5a-ii splits pg_get_functiondef at the FIRST "customer_payment_audit" and asserts on the left half; the approved body inverts that order, so the block goes red and must be re-anchored in the same change. (4) T3.3 IS WRONG AS WRITTEN: the "no audit row" assertions in fx_cut_tests M4 and sales_payments_returns_tests TEST 19 are about the EDITED event with an actor present, not system context; D161 does not touch them and they must not be flipped. The only no-actor pin in the repository is replay-check.sh's. (5) No pilot-rule reversion — the model met the bar for the work that was reachable.
NEXT-NEEDED: choose A or B. A = hand-drive chunk 6 in an interactive session on the work engine (a person approves pg_dump/psql; note psql -f is autocommit statement-at-a-time, the D219 mechanism, so it must be psql -1 -f with the schema_migrations row inserted by hand). B = put SUPABASE_ACCESS_TOKEN in the executor's .env.local (already a locked name in CLAUDE.md §8, already a CI secret, and strictly less powerful than the SUPABASE_DB_PASSWORD the box already holds) and give the chunk a real dump — either Bash(pg_dump:*) in the project-local settings, or the registered jahjah-backup job via the already-granted systemctl start (real pg_dump, self-verifying, keeps 7 and holds 4 so an extra run prunes nothing) — then re-dispatch chunk 6 unattended. RECOMMEND B: smaller, keeps the apply on the documented command and inside both halves of the D228 guard, keeps the atomic begin/commit wrapper psql -f does not give, and fixes the class rather than this instance. Whichever is chosen, fold in the two §3a test fixes and the corrected T3.3 instruction, and record the session-versus-box refinement of D221.
=== END ===
```
