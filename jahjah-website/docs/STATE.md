# STATE.md — Where the Project Is Right Now

> **The only volatile file in the canon.** Rewritten at every chunk end. Nothing here is a rule. History: `docs/archive/STATE-history.md` (never loaded in a session).

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0–P2 done → **P3 Admin Mode** ★ → P4 accounts → P5 UX (parallel) → **L** → P6 (phase table below; ROADMAP retired to issues by M2) · engine v3 (card #112): M0, M1 done, M2 running → M3 replay → M4 pilot → M5 handover |
| **Next step** | M2 brain (card #112), then P3-B2 write routes (§5) |
| **`master` HEAD at the last canon update** | `96d69e1` (#122). Normally `master` is one commit ahead of this line: the canon PR itself. A bigger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app`: 22 products, 5 brands, 6 categories. No prices, no login. Astro 7.3.2 + `@astrojs/vercel` 11.0.10, Studio 5.31.2. Every page prerendered; on-demand routes: 6 (`/api/health` + `/api/staff/*`, W167); `/_image` 404 (W166); Product JSON-LD `sku` (W165). Vercel Hobby (W090 as amended). |
| **Web DB** | Supabase project #2, schema v3 (W153, W159, W166): 7 tables + the `stock_visible` view, empty but the 6 settings (`audit_log` grows with every write). Anon gets 42501 everywhere; quantity is staff-only; `search_path` pinned. Read by `/api/health` and, as the caller, by `/api/staff/*`. |
| **Content** | Placeholder catalogue (AI names; 1 product has real photos). Deleted when real data enters, never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP): separate canon, **not connected** (W075). |

### Phases (run in order; out-of-order work gets redone)

| Phase | Status | Remaining · exit |
|---|---|---|
| P0–P2 | done (P2 closed 2026-09-11) | carried: #143 (F64), #145 (F68), Dependabot #84 (W114, a major); P1's "every visible product looks real" waits on the owner's curation (W126) |
| **P3** Admin Mode (W082) | in progress: P3-1a, P3-B1 done; P3-B2 write routes next (card #95); build per card #111 | exit: an editor works without a programmer or a Sanity seat; every change attributable; prices entered while `prices_visible` is OFF |
| P4 accounts | — | sign-up/login, tier (default 1), price island, stock, promotions, `require_approval`; hidden = 404 without staff (W077). Exit: tiers 1/2/3/none see exactly their prices |
| P5 public UX (parallel to P3–P4) | — | homepage repositioning, brand strip, category imagery and pages (W041), featured/new, search + filters, badges, related, trust strip, service page, showroom map (decision-gated), View Transitions, Lighthouse, static JSON-LD, LocalBusiness after W088; AR by batched review (W125) |
| L launch bundle (W027) | one event | Vercel Pro (W090), spend cap, Cloudflare, `jahjah.net`, CORS, `site` URL, webhook check, analytics + WhatsApp events, share-cache refresh, Search Console, Bing, Business Profile, editor invites; #134 (F36) |
| P6 after launch | — | quote list to sales/WhatsApp; VPS mirror (`@astrojs/node`, Caddy), cut over by DNS after a clean month (W078); ERP SKU sync (W075); orders/payments last (W064). Later: mobile app, guides/blog, testimonials, translate-on-paste plugin |

**Target (W074 W075 W081 W082):** visitors see the catalogue with no prices; tiered customers see their own prices and stock; staff edit in place through Admin Mode; Studio stays at `/admin`. **Rejected, do not reopen:** Next.js, Shopify, WooCommerce, an off-the-shelf ERP (W018) · commerce in Sanity (W075) · a separate portal or `trade.` subdomain, Sanity visual editing as Admin Mode (W082) · an ignored build step (W087) · migrating host at launch (W027, W078) · a hosted Studio (W011).

### CI and the merge gate

- **One job, `ci`**, runs on every PR and on `master` (Node 22, actions SHA-pinned). Steps: `tier3-guard` → `npm ci` → build → `tsc --noEmit` (after build, which writes the Astro types; F76 lists the excluded files) → page count (`EXPECTED_PAGES`=67; Astro reports 68 with `/admin`) → `verify.sh` → reference drift → gitleaks.
- **Ruleset `master-protection`** (22124934): PR required, squash only, `ci` required and strict, no bypass actors (W100). A direct push fails with `GH013`.
- **`verify.sh`** reads the pages from `dist/client/` (W164), walks the 404 and the heading audit, and in 7d asserts `.vercel/output/`: 1 function, 1 on-demand route, no secret, no HTML under `/api` (F54). The hidden-product guard exits 4 on any Sanity failure (W124, W132); `.env.local` fills gaps only (W136).
- **Dependabot PRs:** `ci` is skipped on them, and GitHub counts that as passing, so only W114 keeps them unmerged. Vercel never builds `dependabot/**` (W149).
- **`review`** (`claude-review.yml`) is `workflow_dispatch`-only, a manual fallback. Codex is the reviewer of record (flag 7).

## 2. LIVE FLAGS

1. **Two paths to production:** a merge to `master`, AND a Sanity publish (webhook → deploy hook, ~2 min). The second has no commit; only TRUTH sees it.
2. **Placeholder content:** the owner asked that no photos be uploaded to placeholder products. The watermark was ruled clean (W126).
3. **Launch facts ruled 2026-09-04 (W126):** founded 2010; SUNNY/DSP exclusive for all of Syria; numbers, hours, warranty and brands unchanged. The Damascus `+90` number is correct. Still the owner's: tier names, currency, showroom address, a confirmation pass.
4. **Free Supabase pauses after ~1 week idle (W091)** until the nightly `pg_dump` keeps it awake (#143, F64). `/api/health` touches it only when called.
5. **Project knowledge is a lagging sync; the mirror wins.** A fresh `INDEX.md` does not mean a fresh sibling (W098, W102).
6. **The v3 dispatcher (`jahjah-web-run`, W168) has closed one no-op card (#120) and no real card yet.** It runs live workers, but `/run-card` arrives in M2: label nothing `card:ready` before then. The relay-era lane `-dispatch` is disabled; #24 and #36 had died on the shared window (W128). Reports stay dual-published until F26.
7. **Codex (`chatgpt-codex-connector`) speaks on four surfaces:** review, inline, issue comment, and the 👍 reaction (= clean). 👀 is not a verdict.
8. **`/opt/jahjah/web` is trusted (2026-09-02):** `.claude/settings.json`'s allow list is in force.
9. **No session can edit `.claude/settings.json`** (a deny rule since settings v3). The owner copies `scripts/dispatch/settings.v3.json` into place by hand (W138); a file rule is `Edit(path)`, never `Write(path)` (W169).
10. **The reviewer subagent holds `Read` + `Bash` only.** `Grep`/`Glob` are listed but not granted, and Bash scoping is not enforced, so it is not read-only (F11).
11. **Browser floor iOS/Safari 16,** pinned by `cssTarget`; only the owner raises it (W141). Diff compiled CSS on any build-tool upgrade (W145).
12. **Previews carry NO Supabase environment at all (W161, measured 2026-09-12):** not just no service key — no `SUPABASE_URL` either, so every DB or auth route answers 503/500 there and cannot be tested on a preview. Test the built function locally, then production. Vercel injects a toolbar script on previews only, so byte-compare production. Never use the Vercel MCP's `web_fetch_vercel_url` (W146).
13. **Hooks are live (settings v3, W168):** `pre-bash` refuses a recursive rm outside the tree, writes under `/etc`, `/root` and `~/.claude`, `supabase db push|reset`, `sanity dataset`, `vercel` and any push reaching `master`; `post-edit` type-checks `.ts` and syntax-checks `.js`/`.mjs`. A refusal is a finding.
14. **`think` runs in tmux `think`** (Remote Control, `claude-fable-5-1`, `think.settings.json`, dontAsk), started by the owner with `bash scripts/dispatch/think.sh`. It shares this clone, so a branch switch here moves its checkout too (#152, F77).

## 3. LEDGER (last 5 chunks; older rows in the archive)

| Date | Chunk · issue | Close HEAD | PRs | Result |
|---|---|---|---|---|
| 09-17 | M1 engine · #114 | this PR | #117 #118 #119 #121 #122 + close | Hooks + `tsc` in CI; settings v3; builder/reader agents; `jahjah-web-run` dispatcher (no-op card #120 closed in 2 min); `think` answering from the phone. 1 BLOCKED (`tsc` red on master, ruling A); 2 owner-reported start failures fixed (W169, W170); 1 incident: the executor started `think` for 20 s |
| 09-17 | M0 baseline · #113 | `a884e8e` | #115 + close | `scripts/dispatch/metrics.mjs`; numbers on the issue |
| 09-12 | P3-B1 staff session · #104 | `96aa8a3` | #105 #106 #107 #108 + close | `@supabase/ssr` session library; 5 `/api/staff/*` routes to `aal2`; 3 owner-run admin scripts; end-to-end smoke 40/40 on production. 10 Codex P2s and 3 reviewer BLOCKs, all fixed; W161 and W167's recovery line corrected by measurement |
| 09-11 | P3-1a SKU + advisors · #97 | `1ebd0c0` | #98 #99 #101 #102 + close | SKUs backfilled + `required()` + JSON-LD `sku` (GATE 1); advisors v3 (GATE 1); `/_image` 404; Studio 5.31.2; 1 BLOCKED (`db push` refused, owner ran it) |
| 09-11 | P2b-3 first route · #89 | `2203388` | #90 #91 + close | F65 hardening under GATE 1; `/api/health` 200 on Hobby; 3 BLOCKED (Hobby + view name, view write path on a scratch DB, preview env → W161); **P2 closed** |

## 4. EPHEMERAL FACTS

| What | Where |
|---|---|
| Live / Studio | `https://jahjah-website.vercel.app` · `/admin` |
| Future domain | `jahjah.net`: owned, NOT connected until the launch bundle (W027) |
| Repo / Vercel / Sanity | `obidex/jahjah-website` (private, `master`) · Vercel project `jahjah-website` · Sanity `pxf1amia`/`production` |
| Executor | tmux `web`, clone `/opt/jahjah/web`, Node 22, Supabase CLI 2.117.0 (linked to project #2), read-only Supabase MCP in `.mcp.json` (W160). The box address is never recorded here: this file is public. |
| Relay | `raw.githubusercontent.com/obidex/relay/main/jahjah-website/{docs,reports}/` |
| Automations | `jahjah-web-truth` Mon 05:30 · `-docs` 30 min · `-backup` 02:30 nightly · `-backup-check` Mon 03:30 · `jahjah-web-run` 2 min (kill: `scripts/dispatch/STOP` or an open issue labelled `stop`; state `/opt/jahjah/run-state`; W168) replaces `-dispatch` (disabled) · tmux `think` (owner-started). Registry: `jahjah-internal/docs/runbooks/automations.md` (it still lists `-dispatch`; the ERP side adds `-run` in its own canon, card #112) |

**Secret names (never values):** `PUBLIC_SANITY_PROJECT_ID` · `PUBLIC_SANITY_DATASET` · `SANITY_READ_TOKEN` (Vercel, VPS, Actions) · `SANITY_WRITE_TOKEN` (VPS + Vercel Production since 2026-09-11) · the deploy-hook URL (a credential) · `SUPABASE_URL`, `SUPABASE_ANON_KEY` (VPS; Vercel Production + Preview) · `SUPABASE_SERVICE_ROLE_KEY`: service key Production-only (W161), plus VPS `.env.local` · `SUPABASE_PROJECT_REF` (VPS) · `SUPABASE_ACCESS_TOKEN`, `SUPABASE_DB_PASSWORD`: CLI-only, exported for one command (W154).

**Plans:** Vercel Hobby → Pro at launch (W090) · Sanity Free · GitHub Pro (~$4/mo, for the ruleset) · Supabase Free, project #2 since 2026-09-11 (ERP is #1) · Claude Max.

### Owner decisions (open; the default holds until he rules)

| Decision | Blocks | Default |
|---|---|---|
| Which of the 22 placeholders to keep (he toggles `published` himself, W126) | P5 curation | all 22 visible |
| Launch-fact confirmation pass (numbers, hours, warranty) + delivery coverage (W088) | L | site as is |
| Currency: SYP / USD / both (W066) | flipping `prices_visible` | OFF |
| Tier names (#128, F7) | P4 (`settings.tier_names`) | Tier 1/2/3 |
| Publish the showroom address? | P5 showroom, L profile | unchanged |
| ShamCash merchant API (W064) | P6 ordering | quote list only |
| Guides/testimonials: will anyone write them? | Later | skip |

Answered: watermark, hiding, founding year, exclusivity, numbers/hours/warranty/brands (W126); Dependabot never gets the Sanity secrets (F24, 2026-09-04). Also his: TRUTH build warnings #126/#127 (F3/F4; the Monday run re-measures).

## 5. NEXT STEP

**M2 brain (card #112):** canon dissection, the card template, and the skills strategist / run-card / milestone-review / migrate-db. After it: P3-B2 write routes (card #95); P3-1b `@sanity/client` 8 (card #93) may run before it; design lane card #94 runs in its own chat, D-steps approved there unlock card #111 (P3-U build) chunks. Also open: Dependabot #84 (`@sanity/client` 8, a major, W114), #143 (F64, ERP-side `pg_dump`), #145 (F68) before P4. The follow-up register is now `backlog` issues (`pri:*`, `risk:*`).

**HANDOVER:** M1 is done: the machinery of v3 runs, but work is still described the relay-era way. `jahjah-web-run` ticks every 2 minutes in live mode and has proven the loop with a no-op card; `think` answers from the owner's phone; hooks type-check and guard every session; CI type-checks every PR. M2 gives the engine its brain: until `/run-card` and the card template exist, nobody labels an issue `card:ready`. Read W168-W170 first. Settings edits, `install.sh` and `think.sh` stay owner-run. `think` shares this clone and inherits part of the project allow list (#152, F77), which M2 should settle before `think` writes canon.
