P3-1a progress 2 of issue #97: T3 is merged (#101, `b34e9a7`). Every variant in Sanity now carries its SKU: 3 variants on the one product that has any, verified unique by a re-fetch. The Studio requires `sku` from this deploy on, and the Product JSON-LD emits the first variant's SKU on the EN and AR pages of that product. The single Sanity write triggered one production redeploy (`dpl_3VZ79SH67yGRXuSLaEXoHpUryF86`, READY). T2's migration is still unapplied because `supabase db push` was refused.

=== REPORT: P3-1a-sku-advisors · progress ===
HEAD: b34e9a7 | tree: clean | branch: master
PRs: #98 71f43c3 merged (T1) · #101 b34e9a7 merged (T3) · #99 open, not mergeable until the migration is pushed (T2)
CI: #101 `ci` green (54s) · master `ci` run 34636895470 green · PROD: deployment FhXT4hQX9W1fteWafWhpdgoTFiUE READY (19:05:37Z) | live probes: 6/6 (`/`, `/ar/`, `/products/`, `/admin/structure` 200; `/products/dcel-washer-front-7kg/` and its `/ar/` mirror serve `"sku":"DCEL-DCL7KGGR-GY"`)
DONE:
- T3a: `scripts/backfill-sku.mjs` byte-identical to issue lines 56-176. The reviewer ran before any use of it: 1 FIX, a gate on pre-existing SKUs that does not apply here since 0 were set; 11 NOTEs; no BLOCK.
- T3b: dry-run exit 0: `documents 22 (drafts 0) · variants 3 · already set 0 · to set 3 · products without variants 21`. A read-only audit confirmed all 22 products are published.
- T3c: `--write` ran once at 18:56:32Z and patched 1 document: `VERIFY OK: 3 unique SKUs`. "Already set" was 0, the same as the dry-run. Webhook redeploy `dpl_3VZ79SH67yGRXuSLaEXoHpUryF86` READY.
- T3d: `sku` is `[required, regex, custom]`, three rules; the comment is 4 lines; the slug validator is untouched.
- T3e: EN and AR JSON-LD spread in `sku`; `sanity.js` already projected it, so it is untouched.
- T3f: build 68; verify 0 FAIL · 0 WARN · 67 pages; the `"sku":"` pages number 1 EN and 1 AR (`DCEL-DCL7KGGR-GY`), which equals the published products with ≥ 1 variant; the reference shows `variants[].sku` required.
- Codex #101: 👍 at 19:03:17Z; the preview served the `sku` line on EN and AR before the merge.
DEVIATIONS: none in T3.
FINDINGS/BLOCKERS:
- 21 of 22 products have no variants and so no SKU. This goes against W076 (≥ 1 variant per product) in the placeholder catalogue (W007); nothing enforces it.
- The comment at `src/utils/sanity.js` lines 75-76 ("NO page renders it") is stale now. The plan did not name that file for a comment edit.
- T2 is still blocked on the refused `supabase db push` (progress 1). The decision comes in the closing blocked report.
CANON: none yet · NEXT-NEEDED: none yet
=== END ===
