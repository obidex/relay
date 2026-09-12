# STATE.md — Where the Project Is Right Now

> **The only volatile file in the canon.** Rewritten at every chunk end. Nothing here is a rule. History: `docs/archive/STATE-history.md` (never loaded in a session).

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0 → P0.1 → P0.2 → P1 → P1.1 → P1.2 → **P2 CLOSED 2026-09-11** (P2a → P2b-1/-1b/-1c → P2b-2 → P2b-3) → **P3 Admin Mode** ★ (P3-1a, P3-B1 done) → P4 accounts → P5 UX (parallel) → **L** → P6 |
| **Next step** | P3-B2 write routes (§5) |
| **`master` HEAD at the last canon update** | `80759a2` (#108). Normally `master` is one commit ahead of this line: the canon PR itself. A bigger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app`: 22 products, 5 brands, 6 categories. No prices, no login. Astro 7.3.2 + `@astrojs/vercel` 11.0.10, Studio 5.31.2. Every page prerendered; on-demand routes: 6 (`/api/health` + `/api/staff/*`, W167); `/_image` 404 (W166); Product JSON-LD `sku` (W165). Vercel Hobby (W090 as amended). |
| **Web DB** | Supabase project #2, schema v3 (W153, W159, W166): 7 tables + the `stock_visible` view, empty but the 6 settings (`audit_log` grows with every write). Anon gets 42501 everywhere; quantity is staff-only; `search_path` pinned. Read by `/api/health` and, as the caller, by `/api/staff/*`. |
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
12. **Previews carry NO Supabase environment at all (W161, measured 2026-09-12):** not just no service key — no `SUPABASE_URL` either, so every DB or auth route answers 503/500 there and cannot be tested on a preview. Test the built function locally, then production. Vercel injects a toolbar script on previews only, so byte-compare production. Never use the Vercel MCP's `web_fetch_vercel_url` (W146).

## 3. LEDGER (last 5 chunks; older rows in the archive)

| Date | Chunk · issue | Close HEAD | PRs | Result |
|---|---|---|---|---|
| 09-12 | P3-B1 staff session · #104 | this PR | #105 #106 #107 #108 + close | `@supabase/ssr` session library; 5 `/api/staff/*` routes to `aal2`; 3 owner-run admin scripts; end-to-end smoke 40/40 on production. 10 Codex P2s and 3 reviewer BLOCKs, all fixed; W161 and W167's recovery line corrected by measurement |
| 09-11 | P3-1a SKU + advisors · #97 | `1ebd0c0` | #98 #99 #101 #102 + close | SKUs backfilled + `required()` + JSON-LD `sku` (GATE 1); advisors v3 (GATE 1); `/_image` 404; Studio 5.31.2; 1 BLOCKED (`db push` refused, owner ran it) |
| 09-11 | P2b-3 first route · #89 | `2203388` | #90 #91 + close | F65 hardening under GATE 1; `/api/health` 200 on Hobby; 3 BLOCKED (Hobby + view name, view write path on a scratch DB, preview env → W161); **P2 closed** |
| 09-11 | P2b-2 web DB · #85 | `41de536` | #86 #87 #88 | Supabase #2 linked; schema v1 + RLS + audit under GATE 1; typed readers in `src/lib` |
| 09-10 | P2b-1c canon diet · #79 | `cbc423a` | #80 #81 + close | #77's security group applied; canon 262→70 KB, history archived; efficiency rules |

## 4. EPHEMERAL FACTS

| What | Where |
|---|---|
| Live / Studio | `https://jahjah-website.vercel.app` · `/admin` |
| Future domain | `jahjah.net`: owned, NOT connected until the launch bundle (W027) |
| Repo / Vercel / Sanity | `obidex/jahjah-website` (private, `master`) · Vercel project `jahjah-website` · Sanity `pxf1amia`/`production` |
| Executor | tmux `web`, clone `/opt/jahjah/web`, Node 22, Supabase CLI 2.117.0 (linked to project #2), read-only Supabase MCP in `.mcp.json` (W160). The box address is never recorded here: this file is public. |
| Relay | `raw.githubusercontent.com/obidex/relay/main/jahjah-website/{docs,reports}/` |
| Automations | `jahjah-web-truth` Mon 05:30 · `-docs` 30 min · `-backup` 02:30 nightly · `-backup-check` Mon 03:30 · `-dispatch` 2 min (kill: `touch /opt/jahjah/WEB_DISPATCH_OFF`). Registry: `jahjah-internal/docs/runbooks/automations.md` |

**Secret names (never values):** `PUBLIC_SANITY_PROJECT_ID` · `PUBLIC_SANITY_DATASET` · `SANITY_READ_TOKEN` (Vercel, VPS, Actions) · `SANITY_WRITE_TOKEN` (VPS + Vercel Production since 2026-09-11) · the deploy-hook URL (a credential) · `SUPABASE_URL`, `SUPABASE_ANON_KEY` (VPS; Vercel Production + Preview) · `SUPABASE_SERVICE_ROLE_KEY`: service key Production-only (W161), plus VPS `.env.local` · `SUPABASE_PROJECT_REF` (VPS) · `SUPABASE_ACCESS_TOKEN`, `SUPABASE_DB_PASSWORD`: CLI-only, exported for one command (W154).

**Plans:** Vercel Hobby → Pro at launch (W090) · Sanity Free · GitHub Pro (~$4/mo, for the ruleset) · Supabase Free, project #2 since 2026-09-11 (ERP is #1) · Claude Max.

**Owner-side open items:** which placeholders to keep (W126) · tier names (F7) · currency (W066) · ShamCash docs (W064) · showroom address · TRUTH build warnings F3/F4 (the Monday run re-measures).

## 5. NEXT STEP

**P3-B2 write routes (card #95); P3-1b `@sanity/client` 8 (card #93) may run before it; design lane card #94 runs in its own chat, D-steps approved there unlock card #96 chunks.** Also open: Dependabot #84 (`@sanity/client` 8, a major, W114), F64 (ERP-side `pg_dump`), F68 before P4.

**HANDOVER:** P3-B1 is done: a staff member signs in, enrols a TOTP authenticator and reaches `aal2`, and the server knows who they are and what role they hold — proven end to end against production, 40 of 40. The write routes can now be attributed and audited (W080), which is what P3-B2 is for; `is_staff_mfa()` already gates every staff write on the `aal2` claim these routes produce. Two canon facts were corrected by measurement rather than assumption: previews have no Supabase environment (W161), and an MFA reset does not sign sessions out (W167, F73) — read both before planning recovery or preview testing.
