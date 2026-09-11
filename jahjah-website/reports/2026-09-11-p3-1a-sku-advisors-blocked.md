P3-1a is blocked on issue #97: T1, T3 and T4 are merged and live, but T2's web-DB migration is not applied. The session's permission classifier refused `supabase db push`, and CLAUDE.md §9 forbids routing around a refusal. Every variant now has its SKU, `sku` is required in Studio, and production emits it in the Product JSON-LD; `/_image` answers 404, and the Studio packages are at 5.31.2. The one thing needed from you is to run the push once, as option A below. T5 (canon close) waits for it, because it records advisors v3 as applied.

=== REPORT: P3-1a-sku-advisors · blocked ===
HEAD: b594274 | tree: clean | branch: master
PRs: #98 71f43c3 merged (T1) · #101 b34e9a7 merged (T3) · #102 b594274 merged (T4) · #99 OPEN, not mergeable until the push (T2) · #83 closed, comment names #98
CI: master `ci` run 34637276888 green (b594274) · PROD: deployment FeAVdo9yEc5DLTo18Wj9hbsPjS4L READY (19:09:44Z) | live probes: 8/8 (`/_image` 404 ×2; `/`, `/ar/`, `/products/`, `/admin/structure` 200; the product JSON-LD `sku` on EN and AR, on this deploy)
DONE:
- T0: labels moved to `chunk:running`.
- T1: sanity and @sanity/vision went 5.24.0 → 5.31.2. The change is lockfile-only; 29 `libc` entries were restored and the transitive majors are named in #98.
- T2: the migration is byte-equal to the issue text and passed the scratch-Postgres test (W163). Reviewer CLEAN, CI green, Codex 👍. The push was REFUSED, so nothing was applied.
- T3: the dry-run and the one `--write` both found 0 SKUs already set, and the write returned `VERIFY OK: 3 unique SKUs`. The Sanity webhook redeploy was `dpl_3VZ79SH67yGRXuSLaEXoHpUryF86`. `required()` is on; the JSON-LD `sku` appears on 1 EN and 1 AR page, on production too.
- T4: `image.endpoint.entrypoint` points at a module answering 404, and the function count stays 1. The 67 public pages are byte-identical, and `/_image` returns 404 on the preview and on production.
- T5: NOT STARTED. It waits for T2, because W166 would otherwise record an unapplied migration.
DEVIATIONS:
- Preflight tree dirty: I stashed a redundant `.gitignore` `.vercel` line written by the Vercel CLI. It is kept as `stash@{0}`; `git stash pop` on `master` restores it.
- The issue carried `chunk:proposed`, not `chunk:approved`. The hand-start plus T0 were taken as confirmation, as with #85 and #89.
- On #98 I posted `@codex review` 43 s after its 👍 had already landed.
FINDINGS/BLOCKERS:
- BLOCKER: `supabase db push` was refused. The exact command is in progress 1. The decision is below.
- After T2 lands, the advisor `rls_auto_enable` (platform, W153) will remain beside the 5 intended RLS helpers. The plan's expected set omits it.
- 21 of 22 products have no variants (W076), in the placeholder catalogue (W007).
- `src/utils/sanity.js` lines 75-76 comment is stale ("NO page renders it").
- The comments in `astro.config.mjs` and `src/image-endpoint-off.ts` cite W166, which T5 adds.
- T1 added `skills` 1.5.25, with engines `node >=22.20.0`.
CANON: none (T5 not run) · NEXT-NEEDED: the decision below
=== END ===

**Decision needed: how `supabase db push` for T2 gets run.** The session's auto-mode classifier refused it. The web DB is untouched; PR #99 holds the approved migration (CI green, reviewer CLEAN, Codex 👍) and is unmerged. T5 (canon close, W166 "advisors v3") cannot truthfully land until the push is done, so the chunk stops here with T2 and T5 open.

- **A (recommended): you run the push once from the `web` window.** In the session, type this line. It loads `.env.local` into the process only, never prints a value, and lets you answer the CLI's `Y/n` yourself:
  `! node -e "process.loadEnvFile('.env.local'); process.exit(require('child_process').spawnSync('supabase',['db','push'],{stdio:'inherit',env:process.env}).status)"`
  Then say "resume P3-1a". The session runs `migration list` (3 = 3), `db diff --linked`, `db-smoke`, and advisors, merges #99, runs T5, and posts `final`. This costs one keystroke line and leaves no lasting change to the allow list.
- **B: widen the allow list.** Add an allow rule for that exact command to `.claude/settings.json` by hand (W138), then re-dispatch T2's remainder and T5. This fits future migration chunks, but it permanently lets sessions write to the remote web DB without a prompt.

Untouched: the web DB (still at 2 migrations), PR #99 (open), and the canon (STATE, ROADMAP, DECISIONS, STRATEGIST have no T5 edits).
