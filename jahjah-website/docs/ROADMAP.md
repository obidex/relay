# ROADMAP.md — Where This Project Is Going

> **Future only.** What shipped is in `docs/STATE.md`'s ledger and `docs/reference/site.md`; why is in `docs/DECISIONS.md`. Phases are ordered; out-of-order work gets redone.

---

## 1. THE TARGET IN ONE SCREEN

Three audiences, one Astro app, two stores (W074, W075, W082):

| Audience | Sees | Served by |
|---|---|---|
| **Visitor** | company, brands, categories, products, specs, images, WhatsApp inquiry | prerendered pages built from Sanity — no prices, no stock quantity, no tokens |
| **Customer (logged in, tier 1–3)** | + prices for their tier, promotions, stock status — each behind an admin switch | on-demand routes reading the web DB under row-level rules |
| **Staff (admin / editor / sales)** | + Admin Mode: pencil on every field, central panel, customers and tiers, settings, audit | on-demand routes writing content to Sanity (server token) and commercial data to the web DB |

Sanity Studio stays at `/admin` for bulk editing. The ERP is not connected; a later one-way SKU sync is the only planned link (W075). Vercel now, own VPS later by adapter swap (W078).

---

## 2. PHASES

### P0 · Canon reset — *this chunk (2026-09-02)*
Canon into the repo, VPS executor, `.claude/` gates, CI, reference generator, docs mirror, nightly backup. **Exit:** strategist reads live canon from the mirror; `master` reachable only via PR; TRUTH + backup + docs units in HEALTH-daily.

### P1 · Launch blockers (content-facing, no architecture)
1. Verify and remove any watermarked/unlicensed images (DCEL front-load washer reported); replace with owned or supplier-authorized photos or hide the product.
2. Fix `/images/placeholder.jpg` 404 — ship the asset or render a neutral generated placeholder; no broken requests.
3. Hide incomplete products (`published: false`) — 8–10 complete flagship products beat 22 empty ones (W031). Placeholder records are deleted, not polished (W007).
4. `/admin` out of the sitemap + `noindex,nofollow` on the Studio surface.
5. Launch-fact ruling recorded (W088): founding year, numbers, hours, warranty wording, coverage, agency claims, brand list.
6. Accessibility fixes verified by the friend's audit: `aria-pressed` on filters; heading hierarchy (h1→h2); duplicate accessible names on category cards ("Cooling Cooling"); Arabic accessible labels on AR pages (WhatsApp float, header); gallery selected state; LTR isolation for numbers/codes in RTL.
7. Page-specific meta descriptions for About / Brands / Contact (currently shared).
**Exit:** every visible product looks real and owned; no asset 404s; sitemap clean; facts ruled.

### P2 · Identity and foundation (Tier 3 throughout)
1. **SKU** on every sellable variant — unique, immutable, validated in the schema; every product ≥ 1 variant; migration script under GATE 1 (W076).
2. `@astrojs/vercel` adapter; first on-demand route (`/api/health`); middleware skeleton; **Vercel Pro** before it ships (W090).
3. **Web DB**: Supabase project #2 — schema (customers, tiers, staff, prices, promotions, stock, settings, audit), RLS, TOTP MFA, seed; nightly `pg_dump` added to `jahjah-web-backup` (W083, W091).
4. Server-side data layer in TypeScript (`src/lib/`), typed against the DB (W086).
**Exit:** an unauthenticated request cannot obtain a price via HTML, JS, API or build output (prove it in CI); a staff user logs in with MFA.

### P3 · Admin Mode (owner's top priority; W082)
1. Staff session + roles; pencil controls on product pages: name, short description, specs, images (upload / reorder / delete-with-confirm), status chips (available / sold out / incoming), visibility (published / hidden / disabled), price × 3 tiers, promo price + label + dates.
2. Central panel `/admin-mode`: product table with filters (hidden, disabled, sold out, promoted), search by name/model/SKU, bulk status, customers + tier assignment, site settings (`prices_visible`, `promotions_enabled`, `stock_display`, `require_approval`), audit log viewer.
3. "Open full editor" deep-link to Studio; rebuild trigger after content writes (webhook already does it).
**Exit:** an employee with the `editor` role updates content, images and status without a programmer or a Sanity seat; every change attributable; prices entered while `prices_visible` stays OFF.

### P4 · Customer accounts
Sign-up / login, tier (default 1), price island on product pages, stock status, promotion display, `require_approval` path; hidden = 404 without staff session (W077). **Exit:** test customers in tiers 1, 2, 3 and `none` see exactly their prices; visitor sees none; owner flips `prices_visible` when currency is decided (W066).

### P5 · Public UX and content — runs in parallel with P3–P4 (content team, not code-blocked)
Homepage repositioning (manufacturer + supplier + nationwide distribution; drop "authorized dealer" framing) · brand logo strip · real category imagery · featured / new arrivals tagging · search by name/model/SKU + brand filter + category-specific filters · consistent cards with visible model number and badges (New / Promotion / Sold out / Coming soon) · related products · category landing pages `/categories/[slug]` + AR (W041, W068) · "Why Jahjah" trust strip · service & support page · showroom info + map (decision-gated) · lazy loading + `srcset` · View Transitions · Lighthouse pass · dead RTL rule fix · listing/static-page JSON-LD · LocalBusiness data per branch after W088. All AR copy through the reviewer (W025).

### L · Launch bundle — one event (W027)
Vercel Pro already on (P2) → Spend cap + alerts · Cloudflare in front · connect `jahjah.net` · Sanity CORS · `site` URL in `astro.config.mjs` · verify webhook · analytics + WhatsApp-click conversion events · share-cache refresh · Search Console + sitemap · Bing · Google Business Profile · editor invites (Admin Mode accounts, not Sanity seats).

### P6 · After launch
Saved quote / inquiry list sent to sales or WhatsApp (before any ordering) · VPS mirror: same build, `@astrojs/node`, Caddy, same webhook receiver, staging hostname → after a month of clean publishes, cutover by DNS (W078) · ERP one-way sync by SKU with cached fallback (W075) · orders/payments only after price, stock and sync are trustworthy (W064).

### Later
Mobile app (after commerce is stable; Sanity + web DB are already API-shaped) · buying guides/blog if someone will write them · testimonials in Sanity · translate-on-paste Studio plugin.

---

## 3. FOLLOW-UP REGISTER

| # | Item | Opened | Priority | Closes when |
|---|---|---|---|---|
| F1 | Verify watermark claim on DCEL washer images | 2026-09-02 | high | TRUTH grep result recorded in STATE |
| ~~F2~~ | Register `jahjah-web-docs` + `jahjah-web-backup` in `jahjah-internal/docs/runbooks/automations.md` | 2026-09-02 | high | **closed 2026-09-02** — `jahjah-internal` PR #85 `743b17e` |
| F3 | Deprecated `@sanity/image-url` import pattern (build warning) | 2026-09-01 | low | warning gone |
| F4 | Chunk-size build warning (Studio bundle) | 2026-09-01 | low | accepted or split |
| F5 | **Two** dead RTL rules — `ProductDetail.*.css` `.product-name`, `Layout.*.css` `.footer-heading` | 2026-09-01 | low | P5 |
| F6 | GitHub Pro for branch protection | 2026-09-02 | low | revisit at P2 with Vercel Pro (W084) |
| F7 | Name the three price tiers | 2026-09-02 | med | owner names them (default Tier 1/2/3) |
| ~~F8~~ | Confirm `gh` push scope for `jahjah-website` from the VPS | 2026-09-02 | high | **closed 2026-09-02** — preflight passed; PR #1 was pushed and merged from the VPS |
| F9 | `/ar/404/` — the 404 page's Arabic hreflang alternate has no route, so Vercel answers it with the English 404 body. `verify.sh` WARNs it as known-until-P1; `STRICT_P1=1` FAILs it | 2026-09-02 | med | P1 fixes the alternate or adds the route |
| F10 | CI hardening: pin `gitleaks/gitleaks-action` to a commit SHA instead of the mutable `@v2` tag, and move off Node-20 actions before the runner drops them. (`GITLEAKS_LICENSE` is **not** needed — `obidex` is a user account. The permissions half is **done**: `pull-requests: read` was added after CI failed 403 without it — this row previously claimed the permissions were already sufficient, which was wrong.) | 2026-09-02 | med | ci.yml updated in a chunk that names it |
| F11 | Prove empirically whether `.claude/agents/reviewer.md`'s `tools:` frontmatter actually restricts the agent. The documentation says that field takes permission-rule syntax, and the file parses (the agent is listed), but no run has yet confirmed the Bash scoping binds | 2026-09-02 | med | a session started in `/opt/jahjah/web` invokes `reviewer` by name and its tool set is observed |
| F12 | Four facts left the old `CLAUDE.md` and are extracted by nothing, so they are in no canon file: Sanity `apiVersion: '2024-01-01'`, the `/images/placeholder.jpg` fallback, `ProductDetail.astro` variant data attributes, and the `category->{slug,nameEn,nameAr}` projection | 2026-09-02 | med | the generator extracts them, or `CLAUDE.md` §3 restates them |
| F13 | The executor workspace is not trusted, so Claude Code ignores `.claude/settings.json`'s **allow** list (the deny half still applies) and every session prompts for routine commands. Owner action, deliberately not automated — it is a human trust decision | 2026-09-02 | med | one interactive `claude` session in `/opt/jahjah/web` accepts the trust dialog |
| F14 | `jahjah-web-truth` still reports git facts about `/root/jahjah-website`, which is no longer the executor clone. Either point it at `/opt/jahjah/web` or say in the report that the section describes a spare copy | 2026-09-02 | med | the unit is updated in a chunk that names it |
| F15 | Optional, deferred from P0: have `jahjah-web-truth` run `bash scripts/verify.sh` from its clean checkout after its own build, so the verification ritual has one home. Not done in P0 — it is a proven job whose next run is Monday, so a change could not be re-proven inside the chunk | 2026-09-02 | low | web-truth runs verify.sh and stays green |

---

## 4. OPEN DECISIONS (owner's)

| Decision | Blocks | Default if unanswered |
|---|---|---|
| **Currency for prices** — SYP / USD / both (W066) | flipping `prices_visible` | keep OFF |
| Publish the showroom address publicly? | P5 showroom section, Business Profile at L | contact page unchanged |
| Founding year 2009 vs 2010 + phone numbers + claims (W088) | L | site stays as is; flagged |
| ShamCash merchant API capability (W064) | P6 ordering | quote list only |
| Buying guides / testimonials — will anyone write/collect them? | Later | skip |

---

## 5. REJECTED — DO NOT REOPEN

Next.js for the public site · Shopify · WooCommerce · off-the-shelf ERP (W018) · prices/stock/customers in Sanity (W075) · a separate portal deployment or `trade.` subdomain (W082) · Sanity visual editing as the admin experience (W082) · Vercel ignored build step (W087) · combining host migration with launch (W027, W078) · a hosted (non-embedded) Studio (W011).

---

## 6. HOW TO USE THIS FILE

"What's next?" → STATE §5, then the phase here. "Should I do X yet?" → find X in §2; if out of phase say what comes first. A proposal that locks the project out of the VPS move, the ERP sync, or the Arabic gate is a bad proposal however clever. When a phase closes, an item ships, or a decision is answered: update here **and** write the `W` entry — in the same PR.
