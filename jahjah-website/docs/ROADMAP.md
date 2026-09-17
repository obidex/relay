# ROADMAP.md — Where This Project Is Going

> **Future only.** Shipped work is in `docs/STATE.md`'s ledger and the reference; reasons are in `docs/DECISIONS.md`. Phases run in order, and out-of-order work gets redone. Closed rows and done-phase prose: `docs/archive/ROADMAP-closed.md` (never loaded in a session).

## 1. THE TARGET

| Audience | Sees | Served by |
|---|---|---|
| **Visitor** | company, brands, categories, products, specs, images, WhatsApp inquiry | prerendered pages from Sanity: no prices, stock quantity or tokens |
| **Customer (tier 1–3)** | + own-tier prices, promotions, stock status, each behind an admin switch | on-demand routes reading the web DB under RLS |
| **Staff (admin/editor/sales)** | + Admin Mode: pencil on every field, panel, customers, tiers, settings, audit | on-demand routes writing content to Sanity and commerce to the web DB |

Studio stays at `/admin` for bulk editing. The ERP stays unconnected until a later one-way SKU sync (W075). Vercel now, own VPS later by an adapter swap (W078).

## 2. PHASES

| Phase | Status | Remaining |
|---|---|---|
| P0 · P0.1 · P0.2 | done 2026-09-02 | The v3 dispatcher closed a no-op card from a label (#120, W168); a real card waits on M2; see F26 |
| P1 · P1.1 · P1.2 | done 2026-09-04/05 | Only "every visible product looks real", which waits on the owner's curation (W126) |
| **P2** (P2a → P2b-3) | **closed** 2026-09-11 | Web DB schema v2 under RLS (W153, W159); the first on-demand route `/api/health` on Hobby (W158). Carried: F64, F68, Dependabot #84 (W114; a major) |
| **P3** Admin Mode (owner's top priority, W082) | in progress | The P3 block below. **Exit:** an editor works without a programmer or a Sanity seat; every change is attributable; prices entered while `prices_visible` is OFF |
| **P4** customer accounts | — | Sign-up/login, tier (default 1), price island, stock, promotions, `require_approval`; hidden = 404 without staff (W077). **Exit:** tiers 1/2/3/none see exactly their prices |
| **P5** public UX (parallel to P3–P4) | — | Homepage repositioning, brand strip, category imagery and `/categories/[slug]` (W041), featured/new, search + filters, badge cards, related products, trust strip, service page, showroom map (decision-gated), View Transitions, Lighthouse, static-page JSON-LD, LocalBusiness after W088. AR through batched review (W125) |
| **L** launch bundle (W027) | one event | Vercel Pro (W090), spend cap, Cloudflare, `jahjah.net`, CORS, `site` URL, webhook check, analytics + WhatsApp events, share-cache refresh, Search Console, Bing, Business Profile, editor invites |
| **P6** after launch | — | Quote list to sales/WhatsApp. VPS mirror (`@astrojs/node`, Caddy), cut over by DNS after a clean month. ERP SKU sync. Orders/payments last (W064) |
| Later | — | Mobile app · guides/blog · testimonials · translate-on-paste plugin |

**The P3 block (W082):**
- **Owner precondition:** `SANITY_WRITE_TOKEN` (W079), created at P3 start; server env only.
- **Staff session:** done P3-B1 (#104) — roles admin/editor/sales, TOTP enrolment to `aal2` (W080, W167); the five `/api/staff/*` routes named by that plan (W074).
- **Server write routes:** content to Sanity with `SANITY_WRITE_TOKEN`, commerce to the web DB under RLS; every write audited (W080).
- **Pencil** on the live page: name, description, specs, images, status, visibility, 3 tier prices, promo.
- **`/admin-mode` panel:** filters, search, bulk status, customers + tiers, settings; Studio deep-link.
- **Audit viewer:** `audit_log` by actor, table, date.
- **First:** F51/F50 done (P3-1a); P3-B1 done; next P3-B2 write routes, after M2.

**Engine v3 (card #112):** M0 baseline done (#113); M1 engine done (#114, W168); **M2 brain next**; then M3 replay, M4 pilot, M5 handover. **Owner-run commands (rule):** settings edits (`cp scripts/dispatch/settings.v3.json .claude/settings.json`, W138), systemd (`bash scripts/dispatch/install.sh`) and the `think` start (`bash scripts/dispatch/think.sh`) are typed by the owner; the scripts refuse without a terminal or inside a Claude session, and a plan names them as owner points.

## 3. FOLLOW-UP REGISTER (open rows only)

| # | Pri | Item | Closes when |
|---|---|---|---|
| F3 | low | Deprecated `@sanity/image-url` import pattern (build warning) | warning gone |
| F4 | low | Chunk-size build warning (Studio bundle) | accepted or split |
| F7 | med | Name the three price tiers | owner names them (default Tier 1/2/3) |
| F14 | med | `jahjah-web-truth` reports git facts about `/root/jahjah-website`, not the executor clone | a chunk naming the unit points it at `/opt/jahjah/web` or labels the section |
| F15 | low | `jahjah-web-truth` should run `verify.sh` after its own build | web-truth runs it and stays green |
| F26 | low | Retire the public relay | the strategist confirms it has read a whole chunk from the issue alone |
| F27 | med | Real import drill into a scratch dataset (W104) | once a write token exists |
| F34 | med | Studio 5 → 6 (`sanity`, `@sanity/vision`), its own chunk; the bot ignores these majors (W149, W151) | both upgraded, the two `ignore` entries deleted, `/admin` loads from an iOS home-screen link |
| F36 | med | `jahjah-website.vercel.app` is indexable and will compete with `jahjah.net` | handled in L (redirect or `X-Robots-Tag`), or ruled a non-issue |
| F38 | low | The dispatcher leaves `chunk:proposed` on a running issue (ERP-side) | the lane removes both labels |
| F39 | med | A silent interactive session looks the same as a finished one (W117) | silence is distinguishable from completion without opening the issue |
| F43 | med | **AR mass review, standing since 2026-09-04:** `nav.breadcrumbLabel`, `products.viewImage`, `products.variantLabel` (#34) and `home.featureDealer` (#42). The questions are in those PR bodies | the native reviewer rules on the batch; the row resets |
| F49 | low | The slug validator reports "Slug is required" for a duplicate (chained `.error()`); W013 locks it | a chunk naming it splits the rules, with the W013 exclusion unchanged |
| F53 | low | This box's npm strips `libc` from the lockfile on every install. Since #117, 47 of the 49 gnu/musl entries carry it (29 restored, 18 from the registry; the 2 `arm-gnueabihf` builds publish none), so every dependency task must restore it | npm emits the field |
| F56 | low | The reference generator misses `export type`/`interface` | a chunk naming the generator adds them |
| F58 | low | The relay-report skill says a hand-started chunk approves the publish once, which contradicts W144 | the next chunk naming the skill rewrites it |
| F63 | low | `claude-review.yml`'s header says REVIEW.md carries five always-checks; since P2b-1c REVIEW.md points at `AGENTS.md`'s six | the next chunk naming the workflow rewords that comment |
| F64 | med | Web-DB `pg_dump` added to `jahjah-web-backup`: an ERP-side unit, needs the DB connection string in the VPS env (W083). Until then the free project pauses after ~1 week idle (W091) | the nightly backup dumps the web DB and `-backup-check` verifies it |
| F66 | low | `grep -c '^SUPABASE_' .env.local` (a count-only preflight step) ran in P2b-2's first session and was refused on resume. Names were then proven by the processes that read them | a plan's env check uses a process-based check, or the owner adds an allow rule |
| F68 | high | `stock_visible` ignores `stock_display = 'hidden'` (Codex P2, #90); 0 users today. From F65: `getStockStatusForSkus()` on the view, SKUs validated before `.in()`, tier `none` vs untiered promotions (W081) | a GATE 1 migration + the reader, before sign-in (P4) |
| F69 | med | **No preview Supabase environment at all**, measured 2026-09-12 (P3-B1): not just the service key — `SUPABASE_URL` is unset on Preview too, so every DB and auth route answers 503/500 there and no preview can exercise one (W161) | the owner adds `SUPABASE_URL` + `SUPABASE_ANON_KEY` to Preview, or 503/500 on previews is ruled permanent |
| F72 | med | 21 of the 22 products have no variant, so they carry no SKU and no price or stock row can key on them. W076 wants ≥ 1 variant per product; the Studio still allows none, and the catalogue is placeholder (W007). Raised by Codex on #103 | real products replace the placeholders with ≥ 1 variant each, or a chunk amends W076 |
| F73 | high | An MFA reset does not end the account's sessions (P3-B1, measured): after `staff-mfa-reset`, the existing access token keeps `aal2` until it expires and the refresh token is not revoked, though it renews at `aal1`. A lost device keeps write access for up to the token lifetime, and read-level session access after that; `staff-remove` + re-add is the only full cut-off | a session-revocation path exists (a GoTrue admin sign-out, or a shorter access-token lifetime the owner sets), or the residual is ruled acceptable |
| F74 | med | `tier3-guard`'s path regex does not cover `src/pages/api/**`, so a Tier-3 route change is not machine-checked for the authorization line (raised by the reviewer on #106) | the workflow's regex covers on-demand routes, or the gap is ruled acceptable |
| F75 | low | TOTP enrolment passes no `issuer`, so authenticator apps label the account with the project default rather than Jahjah. Changing it later means re-enrolling everyone; nobody has enrolled yet | the screens chunk (card #96) sets `issuer` before the first real enrolment |
| F76 | low | The type-check (`tsconfig.json`, CI, the post-edit hook) excludes `astro.config.mjs`, `sanity.config.ts`, `sanity.cli.ts` and `src/sanity/**`: 5 pre-existing errors (2 in `astro.config.mjs`, 3 `client.fetch<number>` in `product.ts`), so edits there get no type-check (ruling A, #114) | a card naming those files fixes them and drops the exclude; the W013 slug-validator exclusion stays unchanged |
| F77 | med | `think` shares the executor's clone (a branch switch in either moves both, and the timer reads `dispatch.sh` from it). `--settings` adds to the project settings, so `think` still inherits `git add`, `git switch`, `git branch`, `gh pr close`/`comment`; whether the phone can change its permission mode is unmeasured | M2 gives `think` its own clone or worktree and settles its rules |
| F78 | med | `Bash(gh api repos/obidex/jahjah-website/*)` allows any HTTP method (a ruleset DELETE matches); `Bash(bash scripts/dispatch/*)` pre-approves the owner-run scripts, which only their terminal/CLAUDECODE guard stops; `Bash(gh label:*)` allows deleting labels | the owner narrows the rules, or pre-bash refuses the writes |
| F79 | low | Dispatcher gaps (#121 review): no `git fetch` before a worktree, so a card can start on stale `master`; a half-failed relabel stalls the queue with no alert; `tick.log` never rotates; `scripts/dispatch/STOP` is untracked (a second STOP lives in the state dir) | a card naming `dispatch.sh` closes them |

## 4. OPEN DECISIONS (owner's)

| Decision | Blocks | Default |
|---|---|---|
| Which of the 22 placeholders to keep (the owner toggles `published` himself, W126) | P5 curation | all 22 visible |
| Launch-fact confirmation pass (numbers, hours, warranty) + delivery coverage (W088) | L | site as is |
| Currency: SYP / USD / both (W066) | flipping `prices_visible` | OFF |
| Tier names (F7) | P4 (they live in `settings.tier_names`) | Tier 1/2/3 |
| Publish the showroom address? | P5 showroom, L profile | unchanged |
| ShamCash merchant API (W064) | P6 ordering | quote list only |
| Guides/testimonials: will anyone write them? | Later | skip |

**Answered:**
- Watermark, hiding, founding year, exclusivity, numbers/hours/warranty/brands (W126).
- Dependabot never gets the Sanity secrets (F24, 2026-09-04).

## 5. REJECTED — DO NOT REOPEN

Next.js · Shopify · WooCommerce · off-the-shelf ERP (W018) · commerce in Sanity (W075) · a separate portal or `trade.` subdomain (W082) · Sanity visual editing as Admin Mode (W082) · an ignored build step (W087) · migrating host at launch (W027, W078) · a hosted Studio (W011).

## 6. HOW TO USE

"What's next?": STATE §5, then the phase here. "Should I do X yet?": find X in §2; if it is out of phase, say what comes first. A proposal that locks out the VPS move, the ERP sync or the Arabic gate is a bad proposal, however clever. When a phase closes, an item ships or a decision is answered, update this file **and** DECISIONS in the same PR.
