P2b-3 stopped at T2's preview check, by the plan's own STOP rule. The `/api/health` route is built, reviewed and green on CI and Codex (👍), but on the preview it returns 503 `{"ok":false}`. Its runtime log says `SUPABASE_URL` is not set in that deployment's server environment. T1 (the F65 hardening) is merged and live, and PR #91 stays open and unmerged. The fix is an owner step in the Vercel dashboard, then a resume that redeploys the preview, merges #91 and runs T3.

=== REPORT: P2b-3-first-route · blocked ===
HEAD: 54abd0c (master) | tree: clean | branch: chunk/p2b3-t2-health (8d63a84, pushed)
PRs: #90 54abd0c merged (T1) · #91 8d63a84 OPEN, not merged (T2 /api/health)
CI: #91 ci green · master ci green on 54abd0c · PROD: READY (54abd0c) | live probes: / 200
DONE: T0 labels. T1 merged: migration applied under GATE 1, smoke green (see the T1 progress report).
  T2 built and reviewed. health.ts, verify.sh 7d (1 function, 1 on-demand route, no secret in .vercel/output, no HTML under /api) and the reference (on-demand 1).
  Local build: 68 HTML files, verify 0 FAIL / 0 WARN / 67 pages, negative test FAILs, client-bundle supabase grep 0. `/`, `/ar/`, `/products/` and `/admin` are byte-identical to production.
  The built function, called locally with the VPS env, returns 200 {"ok":true,"db":"ok"} with no-store and JSON. With no env it returns 503 {"ok":false}.
  Executor reviewer CLEAN. Codex 👍 on #91 (13:04Z, 4 min after the PR opened).
DEVIATIONS:
1. An on-demand route switches Astro to server mode, and the pages move from dist/ to dist/client/ (measured; the plan assumed dist/). verify.sh reads the pages there.
2. scripts/hidden-products-check.mjs (not in T2's list, not Tier 3) got a one-line change so the sitemap stays in the W077 check. The reviewer ruled it justified.
FINDINGS/BLOCKERS:
1. BLOCKER (plan STOP: env NAME missing on Vercel). Preview dpl_BBesr6swTNETNCGdEti6ZWVeEePw: GET and HEAD /api/health return 503 {"ok":false}, with cache-control no-store and content-type application/json. The runtime log (Vercel MCP) reads "SUPABASE_URL is not set in the server environment". The route reads SUPABASE_URL first, so SUPABASE_SERVICE_ROLE_KEY is unverified. The Vercel tools do not list env names, so whether Production has them is unknown too.
2. Astro also routes its own /_image and /_server-islands/* to the function. On the preview, /_image?href=/og-default.jpg&w=64&f=webp returns 200: visitors can make it resize the site's own images. It is not a data path. Turning it off is an astro.config.mjs change, which no plan has named yet. ROADMAP row at T3.
3. Carried from T1: Codex P2 on #90 (stock_visible ignores stock_display='hidden') needs a follow-up migration before P4. Advisors: 1 intended ERROR (security_definer_view), 10 WARNs.
CANON: none updated (T3 not reached)
NEXT-NEEDED: one owner step, then a resume.
  A (recommended): in Vercel, open jahjah-website → Settings → Environment Variables. Make SUPABASE_URL, SUPABASE_ANON_KEY and SUPABASE_SERVICE_ROLE_KEY each exist for BOTH Production and Preview, with the service key marked Sensitive. Then paste "RESUME P2b-3-first-route at T2: PR #91 open". The executor pushes an empty commit to #91's branch for a fresh preview, re-probes, merges and runs T3 unchanged.
  B: set them for Production only, so public previews never run with the service key (flag 12). The strategist then amends T2 to accept a post-merge production probe instead of the preview probe. That keeps previews keyless but merges without the preview proof.
Left untouched: Vercel settings (read-only lookups and runtime logs only), Sanity, the web DB after T1's approved push (catalog reads via the read-only MCP only), #91 unmerged, master (only #90 merged).
=== END ===
