# CLAUDE.md — Jahjah Website

> Binding for every session in this repo. Reasons are `W###` entries in `docs/DECISIONS.md`; code facts are in `docs/reference/site.md`.

## 1. Identity and goal
- Client: Jahjah Trading Company (`شركة الجحجاح التجارية`), a Syrian home-appliance manufacturer, main supplier and distributor. Goal: an EN/AR catalogue site, then a customer price layer and inline Admin Mode.
- Repo `obidex/jahjah-website` (private); `master` = production. Tooling: Node 22, npm, `gh`, bash.
- Vercel builds `master` and per-branch previews, and redeploys on a Sanity publish (no commit). Live: `https://jahjah-website.vercel.app` (`jahjah.net` at launch, W027).
- Content lives in Sanity `pxf1amia`/`production` (Studio at `/admin`). Commerce and identity live in the web DB, Supabase project #2, which is independent of the ERP's.

## 2. Stack rules
- Astro, prerendered by default (W074), with `@astrojs/vercel` static (W142). No Vercel-only API (KV, Blob, Edge Config, crons): the adapter must swap to `@astrojs/node` (W078). No ignored build step (W087).
- Public pages carry no price, stock quantity, token or session logic. Hidden or disabled products never reach HTML, the sitemap, listings or search, and a direct link 404s without a staff session: enforce this at build AND request time (W077).
- Two stores: Sanity holds content, the web DB holds commerce and identity. Never add a price, stock, customer or role field to a Sanity schema (W075).
- Vanilla CSS with the tokens in `src/styles/global.css` and logical properties for RTL. Vanilla JS, and no CSS or UI framework on pages (W003, W004, W009).
- TypeScript for server-side code (`src/middleware*`, `src/lib/**`, on-demand routes). Existing `.astro` files, `src/utils/*.js` and `translations.js` stay JS (W086).
- Bilingual: every user-facing string goes through `translations.js` + `t(lang, key)`, keyed in `en` and `ar`. Every page has an `/ar/` mirror unless it is EN-only (W023, W051, W056).
- No new dependency unless the card names it.
- Sanity reads are server-side only. A token never goes into a client bundle or a `PUBLIC_` variable, and `SANITY_WRITE_TOKEN` stays in server env (W079).

## 3. Data contract
- Sanity client: `apiVersion: '2024-01-01'` (pinned; live-count comparisons use it), `perspective: 'published'`, `useCdn: false`, `SANITY_READ_TOKEN`. Every product/brand/category query carries `!(_id in path("drafts.**"))` (W012). GROQ filters on `_ref` and never calls a function across a reference (W048).
- Product shape (`src/utils/sanity.js`): `specs` is an array of `{label, value}`. `variants` is always an array (`sku`, `modelNumber`, optional `color` slug, `images[]`, `imagesCard[]`). `brand` stays Latin (W010), `categoryName` is localized, and Arabic falls back to English field by field. The category projection is `category->{slug, nameEn, nameAr}`. For `ProductDetail.astro`'s variant data attributes, read the file.
- Images: `imageUrlBuilder(client).image(src).width(w).dpr(2).auto('format').fit('max')`, 400 for cards and 800 for detail. A missing image is `null` and renders `NoImageTile`, never a file URL (W112).
- `COLOR_OPTIONS` appears in both `product.ts` and `sanity.js`: change both in one commit (W038, W094). `BRAND_ORDER` = DCEL, LAPON, JAHJAH, SUNNY, DSP (W042). Array `_key`s are index-based (`en-0`).
- The company name is exactly `شركة الجحجاح التجارية`, and `JAHJAH` stays Latin (W022). The AR `<title>` suffix uses `companyNameAr` (W023).
- Never touch: the `product.ts` slug validator (W013); the three `/admin` rewrites in `vercel.json` (W014); Studio's `basePath` (without it, Studio shows "Tool not found: admin"); the `src/data/products*.js` backups; `scripts/migrate-*.mjs` (never re-run).

## 4. Risk
| Risk | Files | Handling |
|---|---|---|
| 1 | copy, comment, translation value, callerless rename | hooks + CI + Codex |
| 2 | new page/component/section/key, in-component CSS, an additive Sanity field with no consumers, `Layout.astro` chrome | hooks + CI + Codex; the reviewer agent on request |
| 3 | `astro.config.mjs` · `vercel.json` · `sanity.config.ts` · `src/sanity/schemaTypes/**` · GROQ/signatures in `src/utils/sanity.js` · `Layout.astro` head logic · cascade-sensitive scoped CSS · `src/middleware*` · `src/lib/**` · `src/pages/api/**` and every on-demand route · auth · DB schema/RLS/migrations · Admin Mode writes · env handling · `.claude/**` · `.github/**` · `scripts/dispatch/**` | only when the card names the file; Opus, the reviewer agent always, compiled-output checks; PR line `Tier-3: authorized by card #n` |

Unsure between 2 and 3 → 3. The card's `risk:*` label picks the worker's model (W168); a security review never runs on Sonnet or Fable.

## 5. How work runs
- Work arrives as a card (a GitHub issue); `/run-card` is the contract. Load only the skills the card names (`run-card`, `migrate-db`, `milestone-review`, `strategist`, `verify`, `ship`).
- Where things live: `docs/STATE.md` holds everything volatile (phase, flags, ledger, owner decisions, next step). `docs/DECISIONS.md` is append-only, at most two lines per `W###`. `docs/reference/site.md` is generated by `npm run reference`, never hand-edited. `docs/archive/` is never loaded. Open follow-ups are `backlog` issues. Keep one home per fact: point to it, don't copy it.
- Status line: every session's last message opens with `DONE`, `BLOCKED (what)` or `STOPPED (why)`. Unattended work never asks mid-task: it stops BLOCKED.

## 6. Secrets and reports
- Secrets: processes read `.env*`, and you verify by NAME. A value seen outside its store is burned: report it, never paste it. With interpreters allowed, the `.env` deny rules are advisory; this rule holds anyway (W095).
- PR bodies, issue comments and reports are records others read: no secret, draft copy, price, customer datum or hook URL. Never write close/fix/resolve before a `#number` (W150). Re-measure a claim about an external system when you write it down (W122, W140).

## 7. Hard prohibitions
- `prerender = false` on a route the card did not name; `output: 'server'` at all (W074).
- Pushing to `master`, force-pushing, rebasing `master`, retagging; merging without green CI (and, at risk 3, a clean reviewer); editing the `master-protection` ruleset, adding a bypass actor, running `claude setup-token` (W100).
- `sanity dataset import|delete`; reading, printing or committing `.env*`; touching Vercel/Sanity/Supabase settings (owner-only); Arabic the card did not approve (W125).
- Merging a Dependabot PR: apply its update in a card's PR and close the bot's PR; a major update gets its own card (W114, W123).
- Anything of ours in user-level `~/.claude`; a hook above project level, or one installed without `bash -n` or edited in place (W107, W115); working inside another repo's clone.
- Owner-run only: `.claude/settings.json` edits (the owner copies `scripts/dispatch/settings.v3.json`, W138), `scripts/dispatch/install.sh`, `scripts/dispatch/think.sh`, and destructive `gh api` calls. A refused command is a finding: report the exact command, and never route around it.
