P3-1a progress 3 of issue #97: T4 is merged (#102, `b594274`). Astro's `/_image` endpoint now answers 404 on production, which closes F67's compute exposure. No public page changed: all 67 are byte-identical to the build before T4. T2 (the advisors migration) is still unapplied because `supabase db push` was refused, so T5 (the canon close) cannot land yet. The closing blocked report follows.

=== REPORT: P3-1a-sku-advisors · progress ===
HEAD: b594274 | tree: clean | branch: master
PRs: #98 71f43c3 (T1) · #101 b34e9a7 (T3) · #102 b594274 (T4), all merged · #99 open (T2)
CI: #102 `ci` green (46s, head `018e290`) · master `ci` run 34637276888 green · PROD: deployment FeAVdo9yEc5DLTo18Wj9hbsPjS4L READY (19:09:44Z) | live probes: 6/6 (`/_image?href=%2Ffavicon.svg&w=32` 404, `/_image` 404, `/`, `/ar/`, `/products/`, `/admin/structure` 200)
DONE:
- T4 investigation (W029): Astro 7.3.2 accepts `image.endpoint.entrypoint`, so the preferred shape shipped (`astro.config.mjs` plus the new `src/image-endpoint-off.ts`, where `GET` and `ALL` return 404). `src/middleware.ts` and `scripts/verify.sh` are untouched.
- Acceptance:
  - build 68; verify 0 FAIL · 0 WARN · 67 pages, with 7d unchanged (1 function, 1 on-demand route)
  - `/_image` references in HTML: 0; `.vercel/output/functions`: 1
  - locally, the built handler answered 404
  - preview (head `018e290`): `/_image?href=%2Ffavicon.svg&w=32` 404, `/_image` 404, `/`, `/ar/`, `/products/` 200
  - production: `/_image?href=%2Ffavicon.svg&w=32` 404, `/_image` 404
  - 67 public pages byte-identical to the build before T4: 67 identical, 0 differ
- Reviewer: CLEAN. Codex #102: 👍 at 19:07:53Z.
DEVIATIONS: none in T4. The new comments cite W166, which T5 adds, so until T5 lands `master` points at an ID that is not recorded yet.
FINDINGS/BLOCKERS: T2's refused `supabase db push` (progress 1) remains the only blocker.
CANON: none yet · NEXT-NEEDED: the T2 decision (blocked report)
=== END ===
