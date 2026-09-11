P2b-3 stopped again before its first write: the migration was never pushed, and no branch, PR, deployment or file change was made. I tested amendment 1's SQL on a throwaway local copy of the database (the same Supabase Postgres 17.6 image), and it has one gap. The new view `stock_visible` inherits Supabase's default "all privileges" grant for signed-in users. Because the view runs as its owner, any signed-in user could insert stock rows through it, and any active customer could change or delete every stock row, without the staff TOTP gate. The fix is one word in one line; changing approved SQL is a semantic change, so GATE 1 says BLOCKED rather than me editing it.

=== REPORT: P2b-3-first-route · blocked ===
HEAD: 41de536 | tree: clean (only the untracked `.mcp.json`, committed in T1 by plan) | branch: master
PRs: none opened
CI: not run (no branch pushed) · PROD: unchanged | live probes: not run
DONE: preflight green: fetch, HEAD == origin/master, 95 tracked files, gh auth, ls-remote, `npm ci` exit 0, build exit 0 (68 pages), env NAMES present, `.mcp.json` parses (http, `read_only=true`, no secret), and the project-scoped read-only `supabase` MCP answers. T0: labels moved to `chunk:running` (proposed and blocked removed). T1: the SQL was taken from amendment 1 by script, not retyped, and tested locally only, never pushed.
DEVIATIONS: none. The remote DB was only read, through the read-only MCP: catalog queries (`pg_default_acl`, `pg_class.relacl`), no data rows.
FINDINGS/BLOCKERS:
1. BLOCKER (GATE 1, amendment 1 SQL). Remote catalog: the default ACL for role `postgres` in `public` grants `authenticated=arwdDxtm` on new tables and views. The migration's `alter default privileges … revoke … from anon, public` leaves `authenticated` alone, so `stock_visible` is created with INSERT/UPDATE/DELETE for `authenticated`. `revoke all … from anon, public` + `grant select` does not remove them.
   The view is auto-updatable (one table, plain columns), and with `security_invoker = false` writes reach `public.stock` as its owner `postgres`, and table owners are exempt from RLS (`force_rls` = false). Local result with the SQL as written:
   - an active aal1 non-staff customer: `update stock_visible set status='out'` and `delete from stock_visible` both succeeded on real rows (audit rows written with that user as actor);
   - an `authenticated` session that is neither staff nor an active customer: `insert into stock_visible` succeeded (a view's WHERE never checks inserts without `check option`);
   - direct writes to `public.stock` stayed 42501, as intended.
   This opens a stock write path around W080's aal2 staff gate, which contradicts the migration's own comment ("the table becomes staff-only"). It is not THE BAR (a visitor gets nothing), and no user can reach it today (0 users; the anon key is server-only). Still, pushing it knowingly would be applying a write whose effect contradicts its description.
2. Verified locally, and unchanged by the fix: both migrations apply cleanly in order. Anon gets `42501` on all 7 tables, on `stock_visible` and on `is_staff()`. A customer reads `stock_visible` (sku, status, updated_at); `quantity` through the view is `42703` (no such column); the customer's direct `stock` read returns 0 rows.
3. With the one-word fix applied on top, locally: the view ACL is `authenticated=r` and `service_role` keeps `arwdDxtm`. The customer still reads the view, and insert, update and delete are each `42501`.
CANON: none updated
NEXT-NEEDED: one ruling.
  A (recommended): Amendment 5 replaces ONE line of amendment 1's SQL
    - revoke all on public.stock_visible from anon, public;
    + revoke all on public.stock_visible from anon, authenticated, public;
  (the following `grant select … to authenticated, service_role;` stays). Then paste the same resume prompt; T1 → T2 → T3 run unchanged. Everything else in T1 is pre-verified above.
  B: apply amendment 1 verbatim now and carry the view's write revoke as a high F-row into P3's first migration, before any staff or customer session exists. One round trip faster; production carries the write path until then.
Left untouched: Supabase remote (catalog reads only, no push), Vercel, Sanity, every repo file. The scratch container was removed and the local branch deleted, never pushed.
=== END ===
