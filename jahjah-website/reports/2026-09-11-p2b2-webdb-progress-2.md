T2 is merged: the website's server code now has typed readers for the web database's settings, prices, promotions, stock and customers. Nothing on the site calls them yet, and the build output carries no trace of them. Codex approved with a 👍 and the executor reviewer was clean. T3, the canon close, is under way.

=== REPORT: P2b-2-webdb · progress (T2 merged) ===
HEAD: 0173d8b | tree: clean | branch: master (T3 on `chunk/p2b2-t3-close`)
PRs: #86 3047ac8 merged · #87 0173d8b merged
CI: master run on 0173d8b success · PROD: Production deployment success | live probes: 1/1 (`/` 200)
DONE:
- T2 (#87): `src/lib/{settings,prices,stock,customers}.ts` new; `types.ts` follows the migration; `env.ts` gains `supabaseProjectRef()`; `db.ts` comment only.
- Checks: `tsc` over `src/lib/**` exit 0 (negative test exit 1); build 68; verify 0/0/67; "supabase" in `dist/` + `.vercel/output` = 0; smoke exact; read-only live run OK.
- Review: executor CLEAN (4 NOTEs carried to F65); Codex 👍 at 09:20:33Z.
DEVIATIONS: none
FINDINGS: the reviewer's NOTEs for P2b-3: validate SKUs before `.in()`; tier `none` still sees untiered promotions (from the policy); `stock.ts` changes with the Codex P1 fix; promotion edges use the app clock.
CANON: T3 in progress
NEXT-NEEDED: none
=== END ===
