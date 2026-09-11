The web database now has its schema: the one approved migration was applied verbatim, the tables are empty, and the smoke check shows row-level security closed to visitors. The chunk stopped on a plan stop-condition: after the push, `supabase db diff` was not empty. The only difference is an object Supabase itself put in the project when it was created, the "auto-enable RLS" event trigger. None of it comes from our migration. Nothing reached the website, no PR was opened, and one ruling (below) lets the chunk resume from the pushed branch.

=== REPORT: P2b-2-webdb · blocked ===
HEAD: cbc423a (master, unchanged) | tree: clean | branch: master; work on `chunk/p2b2-t1-schema` @ dd8f8ac (pushed, no PR)
PRs: none opened
CI: not run (no PR) · PROD: unchanged, no deploy from this chunk | live probes: n/a
DONE:
- T0: labels moved (`chunk:running` added, then `chunk:proposed` removed).
- Preflight: 6 `SUPABASE_*` names present (count only); supabase CLI was absent, installed `npm i -g supabase` = 2.117.0; build 68, verify 0 FAIL / 0 WARN / 67.
- T1: `supabase init` + `link` OK; migration file byte-identical to the plan (extracted from the issue body, no identifier fixes); `db push` applied it (exit 0); `migration list` = 1 migration, local and remote `20260911000000`.
- Smoke (`scripts/db-smoke.mjs`), exactly as the plan expects: `settings=6 staff=0 customers=0 prices=0 promotions=0 stock=0 audit=0`, and anon (publishable key, no session) = 0 rows on all 7 tables.
- Branch complete except the PR: `supabase/{config.toml,.gitignore,migrations/…}`, `.gitignore` (+2 lines), `scripts/db-smoke.mjs`, reference regenerated; verify 0/0/67. Reviewer: 1 FIX, 1 NOTE, both fixed in dd8f8ac. The smoke script now exits 2 on a count mismatch (negative-tested) and prints error codes only; the migration is byte-identical, per the reviewer's sha256 check. The CLI link state is parked in the gitignored `.astro/`.
BLOCKER: `supabase db diff --linked` after the push (engine pg-delta) returns only a platform object:
  `CREATE FUNCTION public.rls_auto_enable() RETURNS event_trigger SECURITY DEFINER …`
  `CREATE EVENT TRIGGER "ensure_rls" ON ddl_command_end WHEN TAG IN ('CREATE TABLE','CREATE TABLE AS','SELECT INTO') …`
  `GRANT EXECUTE ON FUNCTION public.rls_auto_enable() TO PUBLIC, authenticated, postgres, service_role`
  It enables RLS on every new `public` table. It is Supabase's project-creation option, not in any migration, and is absent from the CLI's shadow DB.
FINDING (not THE BAR): the migration's `revoke execute on all functions in schema public from anon` does nothing. `EXECUTE` is also granted to `PUBLIC`, so anon still has it; a probe showed anon can call `is_staff`, `setting_bool`, `customer_tier` over RPC. For anon they return false or null, and `setting_bool` exposes only the launch switches, so no price, stock or customer data is reachable. If the revoke is made real (revoke from `PUBLIC` too), every RLS policy calls these helpers as anon, so anon's empty selects become permission errors. That is a design choice, and new SQL under GATE 1.
DEVIATIONS: none. The migration is applied as approved; no other SQL ran.
LEFT UNTOUCHED: T1 PR (not opened), T2, T3; no Sanity write, no `src/pages`, no `vercel.json`, no `.claude/**`.
DECISION NEEDED (db diff):
- A (recommended): accept the object as platform-owned. T1's "db diff empty" then reads "no diff beyond Supabase's `rls_auto_enable`/`ensure_rls`", recorded in W153. Resume: open the T1 PR from the pushed branch, then T2 and T3 unchanged. It only adds RLS; our migration enables RLS explicitly anyway.
- B: make the diff literally empty, either the owner turns the auto-RLS trigger off in the Supabase dashboard (owner-only), or a GATE 1-approved second migration versions it. Then resume.
ANON EXECUTE: leave it for P2b-3's first auth-facing migration (recommended), or send verbatim SQL for this chunk.
CANON: none changed (T3 not reached)
NEXT-NEEDED: rule A or B on the db-diff blocker; say where the anon-EXECUTE fix goes.
=== END ===
