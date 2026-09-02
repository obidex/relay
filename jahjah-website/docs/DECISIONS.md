# DECISIONS.md — Judgment Register

> **Append-only, newest LAST.** Numbered `W###`, dated. Never edit an entry — supersede it with a new one that names the old number. Retrieval-only: never loaded whole in a session; cite by number.
> Entries W001–W071 were extracted from the frozen `docs/archive/HISTORY.md` at the 2026-09-02 canon reset (source section in brackets). W072+ are the reset itself and everything after.
> Status tags: **LOCKED** (binding until superseded) · **LESSON** (generalizable rule) · **SUPERSEDED by Wnnn** · **OPEN** (decision not yet made).

## Foundation (late 2025 – 2026-04)

- **W001** [Origin] LOCKED→SUPERSEDED by W074. Astro chosen; site fully static, no SSR. The "wall" reasoning (two visitors, two versions of one page) still describes exactly when a route must go on-demand.
- **W002** [Stage 1] LOCKED. Apple/Muji-inspired aesthetic on white; design tokens in `src/styles/global.css`. Amended by W052: when in doubt choose the *richer* option (Apple, Shopify, Amazon as references), never "minimal" as the recommendation.
- **W003** [Stage 1] LOCKED. Vanilla CSS with custom properties. No Tailwind, shadcn, CSS-in-JS, or any CSS framework.
- **W004** [Stage 1] LOCKED. Vanilla JS for page interactivity. No React/Vue/Svelte for site features; Sanity Studio's React stays inside Studio.
- **W005** [Stage 2] LOCKED. WhatsApp is the primary inquiry channel; no contact form. Public visitors never see prices (W081 defines who does).
- **W006** [Stage 2.5] LOCKED. Product copy is lifestyle-led; specs are a secondary table.
- **W007** [Stage 2.5] LOCKED. Current product names, models and copy are AI-generated placeholders, not inventory. Everything placeholder is deleted when real data enters — never polish placeholder records (2026-08-31 owner call).
- **W008** [Stage 3] LOCKED. Arabic at `/ar/*` with a URL-swapping toggle; never a cookie/header language switch (SEO, shareable links).
- **W009** [Stage 3] LOCKED. RTL through logical CSS properties; direction-specific rules only where logical properties cannot express the layout.
- **W010** [Stage 3] LOCKED. Register is Modern Standard Arabic. Brand names (DCEL, LAPON, DSP, SUNNY, JAHJAH) stay Latin in both languages. Mixed numerals are intentional: international for specs, Arabic-Indic where a product name reads better.
- **W011** [Stage 3.5] LOCKED. Sanity is the CMS (Content Collections, Decap, Sheets, headless WordPress rejected). Studio embedded at `/admin`, same repo, same deploy. `_id` conventions `product-${slug}`, `brand-${name-lc}`, `category-${slug}`. EN/AR as flat sibling fields in one document, never one document per language.

## Production bugs that became rules (2026-05-09)

- **W012** [Stage 5] LOCKED. Sanity client `perspective: 'published'` AND the GROQ guard `!(_id in path("drafts.**"))` on every query. Both stay; removing either lets drafts collide with published slugs and silently drop pages.
- **W013** [Stage 5] LOCKED. The slug uniqueness validator in `product.ts` strips the `drafts.` prefix and excludes both IDs. "Simplifying" it clears the slug on every edit.
- **W014** [Stage 5] LOCKED. `vercel.json` keeps exactly three `/admin` rewrites, all to `/admin/index.html`. Collapsing them 404s the Studio from an iOS home-screen icon.
- **W015** [Stage 5] LOCKED→amended by W090. Vercel Hobby is non-commercial; Pro was bundled into launch.
- **W016** [Stage 5] LESSON. Imports must match filename case exactly — Windows forgave it, Linux did not. Moot with the VPS executor (W073); kept because `git mv` history can still bite.

## Direction (2026-05-10 – 2026-05-14)

- **W017** [2026-05-10] SUPERSEDED by W089's phase order. Stage-4 phasing A→H.
- **W018** [2026-05-10] LOCKED. Rejected for the public site: Next.js (heavier), Shopify (fee, weak RTL, lock-in), WooCommerce (slow, insecure), off-the-shelf ERP (built in-house instead, W065). Do not reopen.
- **W019** [2026-05-12] LESSON. Astro's CSS scoper injects `[data-astro-cid]` into a leading *attribute* selector but not a leading *type* selector, so `[dir="rtl"] .x` outranks `html.menu-open .x`. Chain on `html`: `html[dir="rtl"] .x`. Verify in compiled `dist/_astro/*.css`. Only inside scoped `<style>` blocks; `global.css` is unaffected.
- **W020** [2026-05-12] LESSON. Cascade/specificity/layout bugs: measure the compiled output first, fix second. Three failed attempts came from skipping this.
- **W021** [2026-05-12] LESSON. Runtime inspection (DevTools, computed styles, screenshots) is a distinct step from code review; today it is the reviewer subagent + TRUTH probes, a browser tool when a bug needs it.
- **W022** [2026-05-13] LOCKED. Company name is exactly `شركة الجحجاح التجارية` (`ال` prefix, `ح` not `ه`). `JAHJAH` in Latin is the cooker brand — a different string; never substitute.
- **W023** [2026-05-13] LOCKED. Arabic is canonical, English serves Arabic. AR `<title>` suffix uses `companyNameAr`.
- **W024** [2026-05-13] LOCKED. Positioning: manufacturer + main supplier + distributor — never just "distributor". Two branches listed: Sarmada (Idlib, HQ) and Damascus (Al-Baramkeh); serves all of Syria.
- **W025** [2026-05-13] LOCKED. Native Arabic review gate: AI drafts → native reviewer → ship, for every meaningful AR string including meta tags (W056). No exceptions.
- **W026** [2026-05-13] LESSON. Split large executor tasks: one prompt hit the 32K output cap. Three files per pass beats twelve.
- **W027** [2026-05-14] LOCKED. Launch is ONE bundled event (domain, Pro, Cloudflare, CORS, site URL, analytics + WhatsApp-click events, Search Console, Bing, Business Profile, share-cache refresh, editor invites). Never piecemeal; never index `vercel.app`.
- **W028** [2026-05-14] LESSON. SEO infrastructure ≠ findability; Google must crawl first. Say so honestly.
- **W029** [2026-05-14] LOCKED. Every task starts with a read-only investigation; "no work needed" is a valid result (Tajawal font, language-toggle cleanup).
- **W030** [2026-05-14] LOCKED. Two-hat workflow: builder pass, then reviewer pass on the diff before commit. Now a reviewer subagent (W084).
- **W031** [2026-05-14] LOCKED→amended by W089 P1. Real product photos gate every visible product polish item. Amendment: ship 8–10 complete flagship products and hide the rest, rather than 22 half-empty records.

## Phase A/B lessons (2026-05-15 – 2026-05-18)

- **W032** [2026-05-15] SUPERSEDED by W085. Hand-maintained "current inventory" in CLAUDE.md.
- **W033** [2026-05-15] LESSON. At equal specificity source order decides; Astro does not reorder. Shared rules before overrides; grep compiled CSS.
- **W034** [2026-05-15] LESSON. On a static build `Astro.url.pathname` inside `404.astro` is `/404`; visitor-URL logic must run client-side (`is:inline`). Generalizes: frontmatter runs at build (or, on-demand routes, at request) — know which.
- **W035** [2026-05-15] SUPERSEDED by W084. Tiered push authorization by phrase.
- **W036** [2026-05-15] LESSON. Per-variant images with product-level fallback (Amazon pattern). Rule: before choosing "simpler now", walk the next adjacent roadmap item; if it makes the simple choice obviously wrong, choose right now.
- **W037** [2026-05-15] LOCKED. Tier-3 work never bundles the irreversible step. Now: Tier-3 PRs are never pre-authorized for merge inside a chunk unless the chunk plan named the file.
- **W038** [2026-05-16] LOCKED→corrected by W094 (`COLOR_MAP` is only in `sanity.js`, derived from its own `COLOR_OPTIONS`; `product.ts` has none). Variant `color` is a slug from `COLOR_OPTIONS`; `COLOR_MAP` is duplicated in `product.ts` and `sanity.js` and edited together (TS→JS import rejected). The reference generator (W085) diffs the two lists.
- **W039** [2026-05-16] LESSON. Static staleness masquerades as a bug: ask for hard-refresh/incognito before investigating "content isn't showing".
- **W040** [2026-05-16] LESSON. When changing a numeric gate, walk concrete cases through the upstream data (the `>= 2` that was really `> 0`).
- **W041** [2026-05-17] LOCKED. Brand (and category) pages are real routes, not query filters.
- **W042** [2026-05-17] LOCKED. Brand copy lives in Sanity. Brand order DCEL → LAPON → JAHJAH → SUNNY → DSP is a business constant in `BRAND_ORDER`, not a schema field. JAHJAH is least-featured on purpose.
- **W043** [2026-05-17] LOCKED. Listing shows short description; detail shows long (split on `\n\n`).
- **W044** [2026-05-17] LESSON. Decompose multi-part features so every step leaves the site working (fields → data → orphan routes → wire-up → SEO). The chunk-mode equivalent: tasks inside a chunk are independently shippable PRs.
- **W045** [2026-05-17] LESSON. A prompt that contradicts the source ships the contradiction. Re-read every prompt before it goes; the investigation step lists "assumptions that contradict the source".
- **W046** [2026-05-17] LESSON. Head-slot structured data is two halves: frontmatter variables AND the `<Fragment slot="head">` emitting them. Verify in compiled HTML.
- **W047** [2026-05-17] LESSON. Astro emits `<script type="application/ld+json">` with no space before `>`; match the literal or `[^>]*>`.
- **W048** [2026-05-17] LESSON. GROQ cannot call functions across a reference traversal; pre-fetch the `_id`, filter on `_ref`.
- **W049** [2026-05-17] LOCKED. Before deleting any Sanity field, re-fetch live data immediately before applying, not at investigation time.
- **W050** [2026-05-17] LESSON. Copy a production-verified pattern verbatim; adapt data, not structure.
- **W051** [2026-05-17] LESSON. EN/AR mirrors are diffed programmatically (`<style>` blocks and `t()` calls), not eyeballed.
- **W052** [2026-05-17] LOCKED. Owner prefers rich UI; the strategist never recommends the plain option.
- **W053** [2026-05-17] SUPERSEDED by W072. Keystroke-exact file-edit instructions for the owner's manual doc edits — the owner no longer edits canon by hand.
- **W054** [2026-05-18] LOCKED. Content that churns early ships in `translations.js`; migrate to Sanity when editing UX becomes the bottleneck (How to Buy is the example; migration script pattern = `migrate-brand-copy.mjs`).
- **W055** [2026-05-18] LOCKED. Support/policy pages live in the footer; top nav is for what customers shop for.
- **W056** [2026-05-18] LOCKED. Any AR string in a deliverable — including meta descriptions — is reviewer-approved text; duplicating the approved sentence beats drafting an unreviewed one.
- **W057** [2026-05-18] SUPERSEDED by W085/W072. "Shipped table is canonical, remaining lists drift."
- **W058** [2026-05-18] LOCKED. An interrupted session's claims are worthless; the next session re-runs build and verification from scratch.
- **W059** [2026-05-18] LESSON. Defer small pattern-dependent details ("which namespace") to the investigation step; never guess.
- **W060** [2026-05-18] LESSON. Arabic reviewer rejects "built-on-X" constructions, bureaucratic noun phrases (`إليك آلية العمل`) and defensive claims (`نحن شركة حقيقية`). Test: would an established Syrian merchant say it face-to-face? Warranty wording is `كفالة معتمدة من شركة الجحجاح التجارية`; SUNNY/DSP are `الوكيل الحصري`.
- **W061** [2026-05-18] SUPERSEDED by W072. Full-file rewrite delivery of canon as downloadable artifacts.

## Strategic reset (2026-06-11)

- **W062** [2026-06-11] LOCKED→amended by W075. Three pillars: public website · internal operations backend (`jahjah-internal`) · future public commerce layer. Amendment: the website is INDEPENDENT of the ERP for now; its commercial layer is its own (W075).
- **W063** [2026-06-11] LOCKED. Build the real backend once; no throwaway MVP that gets rebuilt. Applies to the website's commercial DB too: designed for the real catalog, features dark until lit.
- **W064** [2026-06-11] OPEN. ShamCash merchant API capability (create-payment + webhook vs read-only) unconfirmed. Owner obtains official docs. Payments never run in Astro or the browser.
- **W065** [2026-06-11] LOCKED. The ERP is built in-house as `jahjah-internal`; its detail lives in its own canon. This canon records only the contract between the two.
- **W066** [2026-06-11] LOCKED→amended by W081. Public pricing was "the front gate". Amendment: prices exist behind a global toggle (OFF); the *currency* question (SYP / USD / both) stays OPEN and blocks flipping the toggle.
- **W067** [2026-06-11] LOCKED. Headless commerce packages (Medusa/Saleor) are candidate-if-needed only.
- **W068** [2026-06-11] LOCKED. Category landing pages are in scope; analytics + WhatsApp-click conversion events belong to the launch bundle; Sanity dataset backup is required (now W083).
- **W069** [2026-06-11] LOCKED→delivered by W084. Agentic layer (`.claude/` deny rules, reviewer subagent, skills) approved.
- **W070** [2026-06-11] LOCKED→delivered by W073. Audit the live site before trusting any doc — now the weekly `jahjah-web-truth` report.
- **W071** [2026-06-11] SUPERSEDED by W072. Pointer-form snapshot in ROADMAP, shipped table in PROJECT.

## Canon reset (2026-09-02)

- **W072** LOCKED. Canon lives in the repo: `CLAUDE.md`, `docs/STRATEGIST.md`, `docs/STATE.md`, `docs/ROADMAP.md`, `docs/DECISIONS.md`, generated `docs/reference/site.md`; frozen `docs/archive/`. The claude.ai project holds ONE pointer file. A VPS job mirrors the allowlisted files to the public relay (`relay/jahjah-website/docs/`); the strategist reads live from there. Claude Code updates canon at chunk end in the same PR; the owner never edits canon by hand.
- **W073** LOCKED. Executor is Claude Code on the VPS, tmux session `web`. Chunk mode: full plan → owner confirms ONCE → executor runs unattended → ONE report to `relay/jahjah-website/reports/`. Owner touches the loop twice per chunk: confirm, veto merge. Laptop is optional.
- **W074** LOCKED (supersedes W001's static-only rule). Prerendered by default; `export const prerender = false` only on routes that need a session or per-visitor data (auth, account, price island, Admin Mode, APIs); every on-demand route is listed in `docs/reference/site.md`. Adapter: `@astrojs/vercel` now, swappable (W078). Public pages carry no prices, no tokens, no session logic.
- **W075** LOCKED. Two stores, one boundary. Sanity = content (names, descriptions, specs, images, brands, categories, SEO, SKU on the variant). Web commercial DB = Supabase project #2 (independent from the ERP's project): auth, staff roles, customers + tiers, prices per SKU × tier, promotions with dates, stock status/quantity, visibility, site settings, audit log. Row-level rules are the security boundary. Never a price, stock or customer field in Sanity, even "temporarily". ERP link = later one-way sync by SKU, never a build or request dependency.
- **W076** LOCKED. One stable SKU per sellable variant: unique, immutable, the only integration key. Every sellable product has ≥ 1 variant. `slug` and `modelNumber` are not keys.
- **W077** LOCKED. Visibility is enforced at request time AND build time: hidden/disabled products never enter static HTML, sitemap, search or listings; direct links 404 without a staff session.
- **W078** LOCKED. Portability: no Vercel-only APIs (KV, Blob, Edge Config, platform sessions/crons). Moving hosts = swap the adapter (`@astrojs/node` behind Caddy, Cloudflare in front). The VPS stands up as a mirror after launch, never during it (W027).
- **W079** LOCKED (replaces "write token local-only"). `SANITY_WRITE_TOKEN` lives in the server environment (Vercel env, later VPS env file) for Admin Mode's server routes only. Never `PUBLIC_`, never in a client bundle, never in a chunk report. Rotate on any exposure. `SANITY_READ_TOKEN` additionally lives on the VPS (for builds/CI) and as a GitHub Actions secret.
- **W080** LOCKED. Staff roles: `admin` (everything incl. settings and users), `editor` (content + status), `sales` (prices, promotions, customers, tiers). TOTP MFA required for staff. Every write records who/what/when/previous value. Destructive actions confirm; deletes prefer soft-delete.
- **W081** LOCKED. Commercial model: `prices_visible` OFF at launch; `promotions_enabled` OFF; `stock_display` = `status` (options hidden/status/quantity; quantity column exists from day one, entered manually until sync); three tiers (1/2/3, names TBD by owner) + `none`; new sign-up → tier 1 active; `require_approval` OFF but present; hidden = invisible to visitors and customers, visible to staff; disabled = kept, not shown, flagged "temporarily off". Currency (SYP/USD/both) OPEN (W066).
- **W082** LOCKED. Admin Mode = pencil on every editable field on the live page + central panel (`/admin-mode`) for tables, filters, bulk status, customers/tiers, settings, audit viewer. Content edits go through a server route to Sanity (publish immediately + audit row; "Open full editor" deep-links into Studio for long content). Commercial edits go to the web DB. Employees never need a Sanity seat. Rejected: Sanity visual editing (sends editors into Studio), a separate portal deployment / `trade.` subdomain (over-built for this catalogue; the DB boundary is the boundary).
- **W083** LOCKED. Backups: `jahjah-web-backup` nightly 02:30 UTC — Sanity dataset export, and from P2 a `pg_dump` of the web DB — into `/root/backups/web`, verified, keep 7, VPS-only, never the relay. The dump's read keeps the free Supabase project un-paused (W091).
- **W084** LOCKED. Gates are machine-shaped where possible: `.claude/settings.json` denies pushes to `master`, force pushes, `.env*` access and destructive Sanity CLI verbs; changes reach `master` only through a PR merged on green CI after the reviewer subagent is clean. Branch protection is unavailable (private repo, GitHub Free) — CI is advisory; merge-on-green is prompt-enforced discipline. GitHub Pro (~$4/mo) revisited with Vercel Pro. Tier-3 files may be edited only when the chunk plan names them.
- **W085** LOCKED (supersedes W032/W057). `npm run reference` generates `docs/reference/site.md` from the source tree; CI fails when the committed file is stale. No hand-written inventory anywhere.
- **W086** LOCKED (replaces "no TypeScript outside schema files"). TypeScript for everything server-side (middleware, API routes, DB/auth clients, `src/lib/**`). Existing `.astro` pages, `src/utils/*.js` and `translations.js` stay as they are; converting them is not a task.
- **W087** LOCKED. No Vercel "ignored build step": its interplay with deploy-hook builds can silently skip a content publish. Docs-only commits rebuild the site (≈40 s); acceptable.
- **W088** LOCKED. Launch-fact gate: one named person rules on founding year (site says 2010, other materials 2009), every phone/WhatsApp number, hours, warranty wording, delivery coverage, agency/exclusivity claims, and the active brand list before launch. Recorded in STATE until ruled.
- **W089** LOCKED. Phase order (see ROADMAP): P1 launch blockers → P2 identity + foundation → P3 Admin Mode → P4 customer accounts → P5 public UX/content (parallel) → L launch bundle → P6 post-launch (quote list, VPS mirror, ERP sync). Adopted from the friend's 2026-09-02 audit with Admin Mode moved ahead of customer login; rejected from it: separate portal, branch protection now, six roles.
- **W090** LOCKED (amends W015). Vercel Pro is a prerequisite of the first on-demand route in production, not a launch item. Unprotected preview deployments (Hobby) are the review surface for every PR.
- **W091** LOCKED. Supabase free projects pause after ~1 week of low activity. The nightly backup query counts as activity; the health report flags a paused project. Recorded as a live flag in STATE until the site is on a paid tier or the VPS.

## P0 canon reset — execution (2026-09-02)

- **W092** [2026-09-02] LOCKED. `.gitignore` tracks the agentic layer. The repo ignored `.claude/`, written when that folder held only local workspace state; as of this reset it holds canon (`settings.json`, `agents/reviewer.md`, three skills), so `git add` silently dropped all five files and the first attempt at the reset branch landed without them. The rule now ignores `.claude/*` and re-includes `settings.json`, `agents/` and `skills/`, keeping `settings.local.json` and `src/.claude/` ignored. The same change widens `.env` / `.env.local` / `.env.production` to `.env*`, because CLAUDE.md §7 bans committing `.env*` while the rule only covered three literal names — `.env.vps` and `.env.development` were not ignored. `.gitignore` is outside the P0 plan's pre-authorized file list; it is recorded here rather than changed silently. No `.env*` file is or was tracked.
- **W093** [2026-09-02] LOCKED (implements W083). The Sanity CLI takes its credential as **`SANITY_AUTH_TOKEN`** — that is the variable `@sanity/cli` reads, not `SANITY_READ_TOKEN`, which is what the website *build* calls the same value. `jahjah-web-backup` exports the read-only token under that name into the export process only. The job runs from `/opt/jahjah/web` so `sanity.cli.ts` supplies project and dataset, which are therefore never duplicated in the job and cannot drift; the unit refuses to start if that clone lacks `sanity.cli.ts` or `node_modules`.
- **W094** [2026-09-02] LOCKED (corrects W038). `COLOR_MAP` is **not** duplicated. It exists only in `src/utils/sanity.js`, derived from that file's `COLOR_OPTIONS` with `Object.fromEntries`; `product.ts` has no `COLOR_MAP`. The invariant that needs guarding is between the **two `COLOR_OPTIONS` literals**, which the reference generator now diffs field by field (slug, nameEn, nameAr, hex) and reports as `EXTRACTOR MISS` rather than `IN SYNC` when it reads neither. Before this reset it read neither and reported `IN SYNC` on two empty lists — a guard that could not fail. W038's rule stands; only its description of where the duplication lives was wrong.
- **W095** [2026-09-02] LOCKED (qualifies W084). **`.claude/settings.json` is a guardrail against accident, not a sandbox.** Its deny list is enforced per command pattern, so a determined path around any file rule always exists — `Read(.env*)` is denied while `Bash(grep:*)` is allowed, and no enumerable list of readers (`grep`, `sed`, `awk`, `node -e`, …) closes that. The gate that actually holds is that only the owner's own plans are executed and every change to `master` goes through a reviewed PR. Where a gap IS enumerable it is closed: the push rules now cover a bare `git push`, `git push origin`, `git push origin HEAD` and `git push --all` run while checked out on `master`, not only the explicit `origin master` spellings. Do not read W084's "denies `.env*` access" as a technical guarantee.
