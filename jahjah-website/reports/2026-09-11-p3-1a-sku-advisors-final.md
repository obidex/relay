P3-1a is done (issue #97, card #93). Every product variant now carries its immutable SKU, the key that Admin Mode and the price and stock tables will join on, and Studio requires it. The web DB advisors are down to the intended set, Astro's `/_image` resize endpoint answers 404, and the Studio packages are at 5.31.2. The run stopped once, because this session was refused `supabase db push`; you ran the push and the chunk finished. Next is P3-B1, the staff session with TOTP (card #95).

=== REPORT: P3-1a-sku-advisors · done ===
HEAD: 1ebd0c0 | tree: clean | branch: master
PRs: #98 71f43c3 (T1) · #99 216d2cc (T2) · #101 b34e9a7 (T3) · #102 b594274 (T4) · #103 1ebd0c0 (T5 canon), all merged · Dependabot #83 closed, comment names #98
CI: master `ci` run 34655046248 green · PROD: deployment ANr1kVQ1kkwDGkCBQZfWZxBFrPr4 READY (22:41:20Z) | live probes: 6/6 (`/`, `/ar/`, `/products/`, `/admin/structure` 200; `/_image` 404; the product `sku` served)
DONE:
- T1: `sanity` and `@sanity/vision` 5.24.0 → 5.31.2. Lockfile only; 29 stripped `libc` entries restored; transitive majors named in #98.
- T2: the advisors migration, under GATE 1 and extracted byte-for-byte.
  - Scratch Postgres first (W163). The session's push was refused, and the owner ran it.
  - After the push: 3 = 3; `db diff` shows platform objects only; smoke unchanged.
  - Advisors: `search_path` 0; definer = 5 helpers + `rls_auto_enable`.
- T3: the dry-run and the one `--write` → `VERIFY OK: 3 unique SKUs`, with 0 already set. Webhook redeploy `dpl_3VZ79SH67yGRXuSLaEXoHpUryF86`. `required()` on; the JSON-LD `sku` is on 1 EN and 1 AR page.
- T4: `image.endpoint.entrypoint` → 404 module; 1 function; 67 pages byte-identical; `/_image` 404 on the preview and on production.
- T5: STATE, ROADMAP, DECISIONS (W165, W166), STRATEGIST and the two archive appends; F71 and F72 added; reference unchanged. Reviewer CLEAN (5 NOTEs).
DEVIATIONS:
- Preflight: a redundant `.gitignore` `.vercel` line written by the Vercel CLI was stashed (`stash@{0}`; `git stash pop` on master restores it).
- The issue carried `chunk:proposed`; the hand-start plus T0 were taken as confirmation (as with #85 and #89).
- A redundant `@codex review` on #98, posted 43 s after its 👍.
- DECISIONS grew 24598 → 25013 (+415) against "net bytes ≤ before". The specified cut saves 104 bytes and W165 + W166 add 519; no other entry was trimmed (append-only).
- STRATEGIST gained one line beyond the plan (§7 self-improvement: the refused push), paid for inside its equal cut (11980 → 11966).
FINDINGS/BLOCKERS:
- `supabase db push` is refused in auto mode while `migration list` and `db diff` run. Recorded in STRATEGIST §1 and the STATE handover.
- `rls_auto_enable` (platform, W153) stays on the definer-function advisor.
- 21 of 22 placeholder products have no variant, so no SKU (F72, raised by Codex on #103): W076 wants ≥ 1 per product, the catalogue is placeholder (W007).
- Stale after P3-1a (F71): `README.md` line 61 on `SANITY_WRITE_TOKEN`, and the comment at `src/utils/sanity.js` lines 75-76. The ROADMAP P3 row still says `next`, and its owner precondition is now met: for the strategist's card sweep.
- `skills` 1.5.25 (indirect, via T1) declares engines `node >=22.20.0`.
CANON: STATE, ROADMAP, DECISIONS, STRATEGIST, `docs/archive/ROADMAP-closed.md`, `docs/archive/STATE-history.md` · NEXT-NEEDED: the strategist rules on the DECISIONS +415 bytes (a cap cut or an accepted size)
=== END ===

HANDOVER is in STATE §5: P3-B1 next, because every Admin Mode write needs an attributable `aal2` staff session first (W080). Pressure-test it against how leading appliance distributors' back offices handle roles, 2FA recovery and shared devices.
