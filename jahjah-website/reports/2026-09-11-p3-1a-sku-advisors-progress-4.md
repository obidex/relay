P3-1a progress 4 of issue #97: T2 is merged (#99, `216d2cc`) after you ran the migration push. The web DB is at schema v3, and every post-push check matches the plan. `search_path` is pinned on every function, and the two trigger functions are no longer callable by signed-in users. Only the five intended RLS helpers and Supabase's own `rls_auto_enable` remain on the definer-function advisor. T5 (the canon close) is next, then the final report.

=== REPORT: P3-1a-sku-advisors · progress ===
HEAD: 216d2cc | tree: clean | branch: master
PRs: #98 71f43c3 (T1) · #101 b34e9a7 (T3) · #102 b594274 (T4) · #99 216d2cc (T2), all merged
CI: #99 `ci` green on `27faa1d` (1m7s) · master `ci` run 34649338711 green · PROD: deployment DLPyrFTVM7GkemAiw3kXRK3aSJJz READY (21:26:25Z) | live probes: 6/6 (`/`, `/ar/`, `/products/`, `/admin/structure` 200; `/_image` 404; product `sku` served)
DONE:
- Resume: the owner ran `supabase db push` from the `web` window; labels moved from `chunk:blocked` to `chunk:running`.
- T2 remote checks:
  - `supabase migration list` shows 3 = 3.
  - `supabase db diff --linked` shows only the platform `rls_auto_enable` function, its `ensure_rls` event trigger and their grants (W153).
  - `node scripts/db-smoke.mjs` is unchanged (`settings=6`, the rest 0; anon `42501` on all 7 tables, `stock_visible` and `rpc:is_staff`).
- T2 advisors (security), after the push:
  - `function_search_path_mutable` 0
  - `authenticated_security_definer_function_executable`: `customer_tier`, `is_active_customer`, `is_staff`, `setting_bool`, `staff_role` (intended, W155) plus `rls_auto_enable` (platform, W153; outside the plan's set, named)
  - `security_definer_view` `stock_visible` (intended, W159)
- #99's body now carries these results. The branch was updated with master; build 68; verify 0 FAIL · 0 WARN · 67 pages; reviewer CLEAN; Codex 👍.
DEVIATIONS: none since the blocked report.
FINDINGS/BLOCKERS: none new.
CANON: T5 in progress · NEXT-NEEDED: none
=== END ===
