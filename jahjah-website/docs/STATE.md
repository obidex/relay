# STATE.md — Where the Project Is Right Now

> **The only volatile file in the canon.** Rewritten at every chunk end. Nothing here is a rule. History: `docs/archive/STATE-history.md` (never loaded in a session).

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0 → P0.1 → P0.2 → P1 → P1.1 → P1.2 → **P2 CLOSED 2026-09-11** (P2a → P2b-1/-1b/-1c → P2b-2 → P2b-3) → **P3 Admin Mode** ★ → P4 accounts → P5 UX (parallel) → **L** → P6 |
| **Next step** | P3 Admin Mode (§5) |
| **`master` HEAD at the last canon update** | `ede9a29` (#91). Normally `master` is one commit ahead of this line: the canon PR itself. A bigger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app`: 22 products, 5 brands, 6 categories. No prices, no login. Astro 7.3.2 + `@astrojs/vercel` 11.0.10. Every page prerendered; on-demand routes: 1, `/api/health` (W158). Vercel Hobby (W090 as amended). |
| **Web DB** | Supabase project #2, schema v2 (W153, W159): 7 tables + the `stock_visible` view, empty but the 6 settings. Anon gets 42501 everywhere; quantity is staff-only. Only `/api/health` reads it. |
| **Content** | Placeholder catalogue (AI names; 1 product has real photos). Deleted when real data enters, never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP): separate canon, **not connected** (W075). |

### CI and the merge gate

- **One job, `ci`**, runs on every PR and on `master` (Node 22, actions SHA-pinned). Steps: `tier3-guard` → `npm ci` → build → page count (`EXPECTED_PAGES`=67; Astro reports 68 with `/admin`) → `verify.sh` → reference drift → gitleaks.
- **Ruleset `master-protection`** (22124934): PR required, squash only, `ci` required and strict, no bypass actors (W100). A direct push fails with `GH013`.
- **`verify.sh`** reads the pages from `dist/client/` (W164), walks the 404 and the heading audit, and in 7d asserts `.vercel/output/`: 1 function, 1 on-demand route, no secret, no HTML under `/api` (F54). The hidden-product guard exits 4 on any Sanity failure (W124, W132); `.env.local` fills gaps only (W136).
- **Dependabot PRs:** `ci` is skipped on them, and GitHub counts that as passing, so only W114 keeps them unmerged. Vercel never builds `dependabot/**` (W149).
- **`review`** (`claude-review.yml`) is `workflow_dispatch`-only, a manual fallback. Codex is the reviewer of record (flag 7). CI does not type-check (F55).

## 2. LIVE FLAGS

1. **Two paths to production:** a merge to `master`, AND a Sanity publish (webhook → deploy hook, ~2 min). The second has no commit; only TRUTH sees it.
2. **Placeholder content:** the owner asked that no photos be uploaded to placeholder products. The watermark was ruled clean (W126).
3. **Launch facts ruled 2026-09-04 (W126):** founded 2010; SUNNY/DSP exclusive for all of Syria; numbers, hours, warranty and brands unchanged. The Damascus `+90` number is correct. Still the owner's: tier names, currency, showroom address, a confirmation pass.
4. **Free Supabase pauses after ~1 week idle (W091)** until the nightly `pg_dump` keeps it awake (F64). `/api/health` touches it only when called.
5. **Project knowledge is a lagging sync; the mirror wins.** A fresh `INDEX.md` does not mean a fresh sibling (W098, W102).
6. **The dispatched executor has never finished a real chunk:** #24 and #36 both died on the shared subscription window (W128). Every chunk since has run interactively. Reports stay dual-published until F26.
7. **Codex (`chatgpt-codex-connector`) speaks on four surfaces:** review, inline, issue comment, and the 👍 reaction (= clean). 👀 is not a verdict.
8. **`/opt/jahjah/web` is trusted (2026-09-02):** `.claude/settings.json`'s allow list is in force.
9. **No session can edit `.claude/settings.json`.** The owner edits it by hand (W138).
10. **The reviewer subagent holds `Read` + `Bash` only.** `Grep`/`Glob` are listed but not granted, and Bash scoping is not enforced, so it is not read-only (F11).
11. **Browser floor iOS/Safari 16,** pinned by `cssTarget`; only the owner raises it (W141). Diff compiled CSS on any build-tool upgrade (W145).
12. **Previews are public and carry no service key (W161):** a DB-backed route answers 503 there by design. Vercel injects a toolbar script on previews only, so byte-compare production. Never use the Vercel MCP's `web_fetch_vercel_url` (W146).

## 3. LEDGER (last 5 chunks; older rows in the archive)

| Date | Chunk · issue | Close HEAD | PRs | Result |
|---|---|---|---|---|
| 09-11 | P2b-3 first route · #89 | this PR | #90 #91 + close | F65 hardening under GATE 1; `/api/health` 200 on Hobby; 3 BLOCKED (Hobby + view name, view write path on a scratch DB, preview env → W161); **P2 closed** |
| 09-11 | P2b-2 web DB · #85 | `41de536` | #86 #87 #88 | Supabase #2 linked; schema v1 + RLS + audit under GATE 1; typed readers in `src/lib` |
| 09-10 | P2b-1c canon diet · #79 | `cbc423a` | #80 #81 + close | #77's security group applied; canon 262→70 KB, history archived; efficiency rules |
| 09-10 | P2b-1b bot quiet · #74 | `15cf22c` | #75 #76 #78 | Vercel skips bot branches; security updates grouped (#77); 5 bot updates applied; audit 20→16 |
| 09-10 | P2b-1 Astro 7 + adapter · #61 | `5cdf390` | #62 #67 #68 #69 #73 | Astro 7 with the iOS 16 floor pinned; static adapter; `src/lib` skeleton; auto-mode start rule |

## 4. EPHEMERAL FACTS

| What | Where |
|---|---|
| Live / Studio | `https://jahjah-website.vercel.app` · `/admin` |
| Future domain | `jahjah.net`: owned, NOT connected until the launch bundle (W027) |
| Repo / Vercel / Sanity | `obidex/jahjah-website` (private, `master`) · Vercel project `jahjah-website` · Sanity `pxf1amia`/`production` |
| Executor | tmux `web`, clone `/opt/jahjah/web`, Node 22, Supabase CLI 2.117.0 (linked to project #2), read-only Supabase MCP in `.mcp.json` (W160). The box address is never recorded here: this file is public. |
| Relay | `raw.githubusercontent.com/obidex/relay/main/jahjah-website/{docs,reports}/` |
| Automations | `jahjah-web-truth` Mon 05:30 · `-docs` 30 min · `-backup` 02:30 nightly · `-backup-check` Mon 03:30 · `-dispatch` 2 min (kill: `touch /opt/jahjah/WEB_DISPATCH_OFF`). Registry: `jahjah-internal/docs/runbooks/automations.md` |

**Secret names (never values):** `PUBLIC_SANITY_PROJECT_ID` · `PUBLIC_SANITY_DATASET` · `SANITY_READ_TOKEN` (Vercel, VPS, Actions) · `SANITY_WRITE_TOKEN` (server only; the owner creates it at P3 start) · the deploy-hook URL (a credential) · `SUPABASE_URL`, `SUPABASE_ANON_KEY` (VPS; Vercel Production + Preview) · `SUPABASE_SERVICE_ROLE_KEY`: service key Production-only (W161), plus VPS `.env.local` · `SUPABASE_PROJECT_REF` (VPS) · `SUPABASE_ACCESS_TOKEN`, `SUPABASE_DB_PASSWORD`: CLI-only, exported for one command (W154).

**Plans:** Vercel Hobby → Pro at launch (W090) · Sanity Free · GitHub Pro (~$4/mo, for the ruleset) · Supabase Free, project #2 since 2026-09-11 (ERP is #1) · Claude Max.

**Owner-side open items:** which placeholders to keep (W126) · tier names (F7) · currency (W066) · ShamCash docs (W064) · showroom address · TRUTH build warnings F3/F4 (the Monday run re-measures).

## 5. NEXT STEP

**P3 · Admin Mode** (ROADMAP §2, the P3 block). Owner precondition: create `SANITY_WRITE_TOKEN` (W079) and set it in Vercel Production and the VPS; the strategist gives the keystrokes. First in P3: the SKU backfill + `required()` (F51, F50) with that token, under GATE 1. Also open: Dependabot #83 and #84 in a chunk PR (W114; `@sanity/client` 8 is a major, its own chunk), F64 (ERP-side `pg_dump`), F68 before P4.

**ROTATION NOTE:** the strategist chat rotates after this chunk. The next strategist starts from this file and needs nothing else.

**HANDOVER:** P2 is closed: the web DB is hardened and the first on-demand route is live, proving function + env + DB on Hobby. Next is P3 Admin Mode, because staff editing without a programmer is the owner's top priority (W082); pressure-test its panel against how leading appliance distributors' back offices handle bulk status and tier pricing.
