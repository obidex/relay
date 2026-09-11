P2b-3 T1 is merged: the web DB's F65 hardening migration (amendment 5's SQL, verbatim) is applied. Visitors now get a permission error on every table, on the new `stock_visible` view and on the helper functions. Signed-in users can only read that view, which carries SKU, status and date but never the quantity. Codex raised one P2: the view ignores `stock_display = 'hidden'`. It is answered on the PR and will get a ROADMAP row at the chunk close, because fixing it needs a new approved migration. T2 (the `/api/health` route) is next.

=== REPORT: P2b-3-first-route · progress (T1) ===
HEAD: 54abd0c | tree: clean | branch: chunk/p2b3-t2-health (T2 in progress)
PRs: #90 54abd0c merged (T1 hardening migration, smoke, .mcp.json)
CI: master ci green on 54abd0c · PROD: production deployment READY | live probes: 1/1 (/ 200)
DONE: T0 labels (chunk:running added and confirmed, then chunk:blocked removed). T1: the SQL was extracted from amendment 5 by script (sha256 d07de1d2…b074). It was tested first on a throwaway local Postgres, then `supabase db push`. `migration list` shows 2 applied, local = remote. `db diff --linked` shows only the platform rls_auto_enable/ensure_rls objects (W153).
  Smoke: service settings=6 staff=0 customers=0 prices=0 promotions=0 stock=0 audit=0 (unchanged). Anon gets 42501 on all 7 tables, on stock_visible and on rpc is_staff. The negative test exits 3.
  Signed-in writes on stock_visible are denied. Remote catalog, via the read-only MCP: authenticated holds SELECT only, and INSERT, UPDATE and DELETE are false. Scratch copy: all four writes got 42501.
DEVIATIONS: the signed-in write check ran on the catalog and the scratch copy, not as a live signed-in request. A live request needs a user, and creating one is a DB write outside GATE 1.
FINDINGS/BLOCKERS:
1. Codex P2 (#90): stock_visible does not honour stock_display='hidden'. Nothing can reach it today: 0 users, and no client holds a session. Routed to the ROADMAP at T3 as a follow-up migration before P4 sign-in.
2. Advisors (security), names only: ERROR security_definer_view stock_visible (intended); WARN function_search_path_mutable ×2 (is_staff_mfa, touch_updated_at); WARN authenticated_security_definer_function_executable ×8. All pre-existing or intended; ROADMAP row at T3.
3. Found for T2: an on-demand route switches Astro to server mode, so the pages move to dist/client/. verify.sh and hidden-products-check.mjs follow them (T2 PR).
CANON: none yet (T3)
NEXT-NEEDED: none
=== END ===
