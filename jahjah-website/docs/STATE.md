# STATE.md — Where the Project Is Right Now

> **The only volatile file in the canon.** Rewritten at every chunk end. Nothing here is a rule. History: `docs/archive/STATE-history.md` (never loaded in a session).

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0 → P0.1 → P0.2 → P1 → P1.1 → P1.2 → P2a → P2b-1 → P2b-1b → P2b-1c canon diet → **P2b-2 web DB + first on-demand route** ★ → P3 Admin Mode → P4 accounts → P5 UX (parallel) → **L** → P6 |
| **Next step** | P2b-2, waiting on the owner at a PC (§5) |
| **`master` HEAD at the last canon update** | `8e80804` (#81). Normally `master` is one commit ahead of this line: the canon PR itself. A bigger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app`: 22 products, 5 brands, 6 categories. No prices, no login. Astro 7.3.2 + `@astrojs/vercel` 11.0.10, every route prerendered, no function (W141, W142). |
| **Content** | Placeholder catalogue (AI names; 1 product has real photos). Deleted when real data enters, never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP): separate canon, **not connected** (W075). |

### CI and the merge gate

- **One job, `ci`**, runs on every PR and on `master` (Node 22, actions SHA-pinned). Steps: `tier3-guard` → `npm ci` → build → page count (`EXPECTED_PAGES`=67; Astro reports 68 with `/admin`) → `verify.sh` → reference drift → gitleaks.
- **Ruleset `master-protection`** (22124934): PR required, squash only, `ci` required and strict, no bypass actors (W100). A direct push fails with `GH013`.
- **`verify.sh`** walks `dist/404.html` and the heading audit. The hidden-product guard resolves its store from env and exits 4 on any Sanity failure (W124, W132). It loads `.env.local` to fill gaps only (W136).
- **Dependabot PRs:** `ci` is skipped on them, and GitHub counts that as passing, so only W114 keeps them unmerged. Vercel never builds `dependabot/**` (W149).
- **`review`** (`claude-review.yml`) is `workflow_dispatch`-only, a manual fallback. Codex is the reviewer of record (flag 7).
- **Not machine-checked:** Vercel deploys `.vercel/output/` (`static/` = `dist/`, no `functions/`), but nothing checks it (F54). CI does not type-check (F55).

## 2. LIVE FLAGS

1. **Two paths to production:** a merge to `master`, AND a Sanity publish (webhook → deploy hook, ~2 min). The second has no commit; only TRUTH sees it.
2. **Placeholder content:** the owner asked that no photos be uploaded to placeholder products. The watermark was ruled clean (W126).
3. **Launch facts ruled 2026-09-04 (W126):** founded 2010; SUNNY/DSP exclusive for all of Syria; numbers, hours, warranty and brands unchanged. The Damascus `+90` number is correct. Still the owner's: tier names, currency, showroom address, a confirmation pass.
4. **Vercel Hobby, adapter already in (static):** Pro before the first on-demand route (W090). No web DB yet. `src/lib/db.ts` factories are unused (W143). Free Supabase pauses when idle (W091).
5. **Project knowledge is a lagging sync; the mirror wins.** A fresh `INDEX.md` does not mean a fresh sibling (W098, W102).
6. **The dispatched executor has never finished a real chunk:** #24 and #36 both died on the shared subscription window (W128). Every chunk since has run interactively. The fix is ERP-side, last checked 2026-09-05. Reports stay dual-published until F26.
7. **Codex (`chatgpt-codex-connector`) speaks on four surfaces:** review, inline, issue comment, and the 👍 reaction (= clean). 👀 is not a verdict. Every PR measured since P1.1 got an answer. Keep the literal `@codex` out of issue comments: it starts a Codex "task".
8. **`/opt/jahjah/web` is trusted (2026-09-02):** `.claude/settings.json`'s allow list is in force.
9. **No session can edit `.claude/settings.json`.** The owner edits it by hand (W138). A dispatched session cannot edit any of `.claude/**` (W116). The classifier's boundary is unmapped.
10. **The reviewer subagent holds `Read` + `Bash` only.** `Grep`/`Glob` are listed but not granted, and Bash scoping is not enforced, so it is not read-only (F11).
11. **Browser floor iOS/Safari 16,** pinned by `cssTarget`; only the owner raises it (W141). Diff compiled CSS on any build-tool upgrade (W145).
12. **Previews are public.** Vercel injects a toolbar script on previews only, so byte-compare production, not a preview. Never use the Vercel MCP's `web_fetch_vercel_url` (W146).

## 3. LEDGER (last 5 chunks; older rows in the archive)

| Date | Chunk · issue | Close HEAD | PRs | Result |
|---|---|---|---|---|
| 09-10 | P2b-1c canon diet · #79 | this PR | #80 #81 + close | #77's security group applied, audit 16→11; canon 262→70 KB, history archived; efficiency rules; F61 ratified |
| 09-10 | P2b-1b bot quiet · #74 | `15cf22c` | #75 #76 #78 | Vercel skips bot branches; security updates grouped (#77); 5 bot updates applied; audit 20→16 |
| 09-10 | P2b-1 Astro 7 + adapter · #61 | `5cdf390` | #62 #67 #68 #69 #73 | Astro 7 with the iOS 16 floor pinned; static adapter; `src/lib` skeleton; auto-mode start rule |
| 09-05→10 | P2a foundation-lite · #53 | `8d037e5` | #54 #56–#60 | SKU field; owner-edited allow rule; F45/F5 closed; responsive images + listing JSON-LD |
| 09-05 | P1.2 gates + deps · #44 | `3c8d03b` | #45 #46 #51 #52 | Amended Arabic gate in every reviewer file; 3 bot updates; CI guard gets env store |

## 4. EPHEMERAL FACTS

| What | Where |
|---|---|
| Live / Studio | `https://jahjah-website.vercel.app` · `/admin` |
| Future domain | `jahjah.net`: owned, NOT connected until the launch bundle (W027) |
| Repo / Vercel / Sanity | `obidex/jahjah-website` (private, `master`) · Vercel project `jahjah-website` (Hobby) · Sanity `pxf1amia`/`production` |
| Executor | tmux `web`, clone `/opt/jahjah/web`, Node 22. The box address is never recorded here: this file is public. |
| Relay | `raw.githubusercontent.com/obidex/relay/main/jahjah-website/{docs,reports}/` |
| Automations | `jahjah-web-truth` Mon 05:30 · `-docs` 30 min · `-backup` 02:30 nightly · `-backup-check` Mon 03:30 · `-dispatch` 2 min (kill: `touch /opt/jahjah/WEB_DISPATCH_OFF`). Registry: `jahjah-internal/docs/runbooks/automations.md` |

**Secret names (never values):** `PUBLIC_SANITY_PROJECT_ID` · `PUBLIC_SANITY_DATASET` · `SANITY_READ_TOKEN` (Vercel, VPS, Actions) · `SANITY_WRITE_TOKEN` (server only, from P2b-2) · the deploy-hook URL (a credential) · `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY` (server-only). The Supabase values do not exist until P2b-2.

**Plans:** Vercel Hobby (Pro ~$20/mo at P2b-2) · Sanity Free · GitHub Pro (~$4/mo, needed for the ruleset) · Supabase Free from P2b-2 (ERP is project #1) · Claude Max.

**Owner-side open items:**
- Which of the 22 placeholders to keep (the owner hides them himself, W126).
- Tier names (F7).
- Currency (W066).
- ShamCash docs (W064).
- Showroom address.
- **TRUTH:** build warnings F3/F4 are still open. The next Monday run re-measures everything.

## 5. NEXT STEP

**P2b-2 · web DB + first on-demand route.** It waits on the owner at a PC for three things:
- create Supabase project #2;
- create `SANITY_WRITE_TOKEN` (W079);
- turn on Vercel Pro (W090).

The strategist gives the keystrokes. Then everything is Tier 3, under GATE 1 wherever it writes:
- the schema, RLS, TOTP MFA and nightly `pg_dump` (W083, W091);
- the SKU backfill + `required()` (F51) and the JSON-LD `sku` (F50);
- the first on-demand route, named by the plan (W074);
- whatever Dependabot opens (W114).

The code side is ready (W141–W143). Run it by hand as `claude --permission-mode auto` unless the dispatcher's usage-limit fix is confirmed (W128, W144).
