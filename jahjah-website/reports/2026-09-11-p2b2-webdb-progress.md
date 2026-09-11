The chunk resumed under the strategist's ruling A, and T1 is merged: the web database's foundation migration is now in the repository, matching what was applied. Codex raised one P1: customers can read stock quantities, because row-level security filters rows, not columns. It was answered rather than fixed, since this chunk may run no new SQL and the stock table is empty. The fix is routed to P2b-3's GATE 1 migration, as a precondition before any stock row is written. T2, the data layer, is under way.

=== REPORT: P2b-2-webdb · progress (T1 merged) ===
HEAD: 3047ac8 | tree: clean | branch: master (T2 on `chunk/p2b2-t2-lib`)
PRs: #86 3047ac8 merged
CI: master run on 3047ac8 success · PROD: Production deployment success | live probes: 1/1 (`/` 200)
DONE:
- Resume: labels `chunk:running` added, confirmed, `chunk:blocked` removed; rebuild 68, verify 0/0/67; smoke exact; migration list 1 applied; `db diff` = only `rls_auto_enable`/`ensure_rls` (+ their grant), accepted by ruling A.
- T1 (#86): `supabase/` config + migration (byte-identical to the plan), `.gitignore` +2, `scripts/db-smoke.mjs`, reference. Reviewer clean after 1 FIX + 1 NOTE. Codex: 1 P1 (below), answered on the PR.
DEVIATIONS: none. No SQL beyond the approved migration.
FINDINGS:
- Codex P1 (#86): `stock_customer_read` returns `quantity` to any active customer, and sign-up is open (`require_approval` off), whatever `stock_display` says. Nothing is reachable today: `stock` = 0 rows, and there is no writer until P3. Fix: P2b-3's GATE 1 migration, before any stock row, with the base table staff-only and a status-only projection for customers. It goes into ROADMAP and W153 at T3, next to the anon-EXECUTE residual.
- Preflight: `grep -c '^SUPABASE_' .env.local` (count only) ran in the first session and was REFUSED on resume. I did not route around it. The six names were proven by the processes that read them (smoke: 3; CLI link/list: 3).
CANON: none yet (T3)
NEXT-NEEDED: none
=== END ===
