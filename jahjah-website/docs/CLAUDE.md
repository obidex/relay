# CLAUDE.md — Jahjah Website · Executor Contract

> For Claude Code on the VPS (tmux `web`); binding. The plan is a GitHub issue approved by the owner's `chunk:approved` label (W099), named on the prompt's first line. Unattended: never ask mid-chunk; stop with a BLOCKED report. Code facts: `docs/reference/site.md`. Reasons: `docs/DECISIONS.md`. State: `docs/STATE.md`. Never load `docs/archive/`.

## 1. Identity and environment

| | |
|---|---|
| Client | Jahjah Trading Company: Syrian home-appliance manufacturer, main supplier and distributor. EN/AR site, customer price layer, inline Admin Mode |
| Repo · executor | `obidex/jahjah-website` (private), `master` = production · `/opt/jahjah/web`, Node 22, npm, `gh`, bash |
| Hosting | Vercel builds `master` and per-branch previews, and redeploys on a Sanity publish (deploy hook, no commit) |
| CMS · web DB | Sanity `pxf1amia`/`production`, Studio at `/admin` · Supabase project #2 from P2b-2, independent of the ERP's |
| Live | `https://jahjah-website.vercel.app` (`jahjah.net` at launch, W027) |

## 2. Stack rules

- **Astro, prerendered by default** (W074, §7); `@astrojs/vercel` in, static (W142). No Vercel-only API (KV, Blob, Edge Config, crons): the adapter must swap to `@astrojs/node` (W078). No ignored build step (W087).
- **Public pages carry no prices, stock quantities, tokens or session logic.** Hidden/disabled products never reach HTML, the sitemap, listings or search, and a direct link 404s without a staff session. Enforce it at build AND request time (W077).
- **Two stores (W075):** Sanity = content; the web DB = all commerce and identity. Never a price, stock, customer or role field in a Sanity schema.
- **Front end:** vanilla CSS, tokens in `src/styles/global.css`, logical properties for RTL; no CSS framework. Vanilla JS; no React/Vue/Svelte on pages.
- **TypeScript** for server-side code (middleware, `src/lib/**`, on-demand routes, DB/auth clients). Existing `.astro`, `src/utils/*.js` and `translations.js` stay JS (W086).
- **No new dependency** unless the plan names it.
- **Sanity reads are server-side only.** A token never goes into a client bundle or a `PUBLIC_` variable, and `SANITY_WRITE_TOKEN` lives in server env only (W079).

## 3. Data contract

- **Sanity client:** `apiVersion: '2024-01-01'` (pinned; live-count comparisons use it), `perspective: 'published'`, `useCdn: false`, `SANITY_READ_TOKEN`. Every product/brand/category query carries `!(_id in path("drafts.**"))`. Both guards stay (W012).
- **Not in the reference:** a missing image is `null` and renders `NoImageTile` (inline SVG, no `<img>`), never a file URL (W112); `ProductDetail.astro`'s variant data attributes (read the file); the category projection is `category->{slug, nameEn, nameAr}`.
- **Product shape** (`src/utils/sanity.js`): `specs` is an ARRAY of `{label, value}`; `variants` is always an array; `brand` is Latin, never translated (W010); `categoryName` is localized; Arabic falls back to English field by field; a variant carries `sku`, `modelNumber`, an optional `color` slug, `images[]` and `imagesCard[]`.
- **Images:** `imageUrlBuilder(client).image(src).width(w).dpr(2).auto('format').fit('max')`; cards 400, detail 800.
- **Duplicates and constants:** `COLOR_OPTIONS` is duplicated verbatim in `product.ts` and `sanity.js`, so change both in one commit (W038, W094). `BRAND_ORDER` = DCEL, LAPON, JAHJAH, SUNNY, DSP is a constant (W042). Array `_key`s are index-based (`en-0`).
- **Never touch:** the `product.ts` slug validator, which strips `drafts.` and excludes both IDs (W013); the three `/admin` rewrites in `vercel.json` (W014); the `src/data/products*.js` backups (never deleted); `scripts/migrate-*.mjs` (never re-run).

## 4. Tiers

| Tier | Touches | Ceremony |
|---|---|---|
| 1 | copy, comment, translation value, no-caller rename | investigate → `/verify` → PR |
| 2 | new page/component/section/key, in-component CSS, additive Sanity field with no consumers, `Layout.astro` chrome | + plan in PR body; reviewer |
| 3 | `astro.config.mjs` · `vercel.json` · `sanity.config.ts` · `src/sanity/schemaTypes/**` · GROQ/signatures in `src/utils/sanity.js` · `Layout.astro` head logic · cascade-sensitive scoped CSS · `src/middleware*` · `src/lib/**` · on-demand routes · auth · DB schema/RLS/migrations · Admin Mode writes · env handling · `.claude/**` · `.github/**` | only when the plan names the file; thorough review; compiled-output checks; self-merge only if the plan says |

Unsure 2 vs 3 → 3. The model is on the chunk's first line; never change it mid-chunk.

## 5. Running a chunk

**Preflight** (a failure = BLOCKED):
`git fetch`; HEAD == `origin/master`; clean tree; `git ls-files | wc -l` recorded and recounted before every push (W092); `gh auth status`, `git ls-remote origin`, `npm ci`, build exit 0; env NAMES `PUBLIC_SANITY_PROJECT_ID`, `PUBLIC_SANITY_DATASET`, `SANITY_READ_TOKEN` + the plan's present; STATE and the reference read, and every contradicting plan assumption listed.

**Per task:** branch `chunk/<name>-t<n>` → investigate ("no work needed" is valid, W029) → implement → `/verify` → reviewer subagent → fix → commit (Conventional Commits) → push → PR (plan, acceptance, preview URL) → CI + Codex → `gh pr merge --squash --delete-branch <n> --subject "…"`. Merge only if the plan pre-authorized it, CI is green and the reviewer is clean. Then `/relay-report progress`, which posts to the issue AND the relay (`obidex/relay` `jahjah-website/reports/`); both are required.

**Codex** (reviewer of record, reads `AGENTS.md`, an external system: W105, W113) speaks on four surfaces: `gh pr view <n> --json reviews`, and `gh api repos/obidex/jahjah-website/` + `pulls/<n>/comments` (the findings), `issues/<n>/comments`, `issues/<n>/reactions` (👍 = clean; 👀 = no verdict, match `+1`; W130, W134). Fix or answer every finding at THE BAR; answer nits. A draft PR gets no review. Never write the literal `@codex` in an issue comment (it starts a task). Time intervals from `createdAt`. Your reviewer stays the gate; the `review` workflow is a manual fallback.

**Session-efficiency rules (the owner's standing rules):**
- A report's plain-language opening is ≤ 5 sentences and its body ≤ 60 lines. A PR body is ≤ 40 lines.
- One executor-reviewer pass per PR, and a second only when the first returned a BLOCK. A chunk-close canon PR gets one pass, which checks IDs and numbers, not prose style.
- Codex: wait 5 min, then post one plain `@codex review` PR comment, wait 5 more, then proceed with the silence recorded.
- Never re-measure a fact the canon already carries unless the task changes it. A "`dist/` byte-identical" proof is made only when the plan asks, and covers the 67 public pages, never `/admin` (F61).
- Relay report filenames are unique per publish (`-blocked-N`, `-progress-N`, `-final`): list the folder first, never overwrite (W162).
- Every session's last message opens with DONE, WAITING FOR YOU (what), or STOPPED (why).

**Tier-3 PR:** the body carries `Tier-3: authorized by chunk <name>`, unemphasised, at column 1 (W101, W134). Editing the body re-runs the guard.

**GATE 1** (Sanity writes, DB migrations): the plan's script or SQL is the approved text. Identifier-only fixes go to a relay file (final text + diff) before applying; a semantic change is BLOCKED. Re-fetch live data before any field deletion (W049). A write ahead of approval stops the chunk and is reported for ratification.

**Interrupted?** Rebuild and re-`/verify` from scratch (W058).

**Chunk end** (last PR): STATE (ledger, HEAD, flags, next step), ROADMAP register, a new `W###`, `npm run reference`. Then `/relay-report final` (REPORT shape, `docs/STRATEGIST.md` §1) closes the issue (W127); `blocked`/`interrupted` leave it open. A cap hit = stop, report `interrupted`.

## 6. Rules for every task

- **Verify compiled output, not source**, and measure it before fixing cascade or layout (W020): `dist/` HTML for JSON-LD (`<script type="application/ld+json">`, W047), hreflang, og:image and noindex; `dist/_astro/*.css` for rule order and specificity (W019, W033); the page count.
- **Head slot:** frontmatter AND `<Fragment slot="head">` (W046).
- **Bilingual:** every user-facing string via `translations.js` + `t(lang, key)`, keyed in `en` and `ar`; every page has an `/ar/` mirror unless EN-only, diffed programmatically (W023, W051, W056).
- **Arabic never blocks a merge.** Strategist-approved AR ships, listed in the PR body under `AR strings shipped (strategist-approved per W125; batched for native review)` with its questions (W125, W131).
- **Names:** company name exactly `شركة الجحجاح التجارية`; `JAHJAH` stays Latin (W022). The AR `<title>` suffix uses `companyNameAr`.
- **GROQ:** no function calls across a reference traversal; filter on `_ref` (W048).
- **One thing at a time:** no unrelated fixes, refactors or cleanup. Copy a production pattern verbatim (W050). Output: what changed, the hash, the verification; no preambles, no emojis.
- **Secrets:** processes read `.env*`; you verify by NAME. A value seen outside its store is burned: report it, never paste it. With interpreters allowed, the `.env` deny rules are advisory; this holds (W095).
- **Reports and PR bodies are public:** no secret, draft copy, price, customer datum, hook URL. Never write close/fix/resolve before a `#number` (W150).
- **External claims:** re-measure when writing one down, whatever a plan says (W122, W140).

## 7. Hard prohibitions

- `prerender = false` on a route the plan did not name; `output: 'server'` at all (W074).
- Pushing to `master`, force-pushing, rebasing `master`, retagging; merging without green CI and a clean reviewer; editing the `master-protection` ruleset, adding a bypass actor, running `claude setup-token` (W100).
- `sanity dataset import|delete`; reading, printing or committing `.env*`; a secret or anything private in a report; touching Vercel/Sanity/Supabase settings (owner-only); Arabic the plan did not approve.
- Merging a Dependabot PR: apply its update in a chunk PR and close the bot's naming yours; a major gets its own chunk (W114, W123).
- Anything of ours in user-level `~/.claude`; a hook above project level, installed without `bash -n` or edited in place (W107, W115); working inside another repo's clone (isolated clone + PR instead).

## 8. Pitfalls

- filename case (W016); `Astro.url.pathname` in a static 404 (W034); cache staleness (W039); `grep -c` counts lines, trailing `grep -q` inverts (W097); "Tool not found: admin" = missing `basePath`.

## 9. Commands and traps

```bash
npm run build                                    # 68 built; verify counts 67 (no /admin)
EXPECTED_PAGES=67 bash scripts/verify.sh         # canonical spelling; its allow rule is literal
gh pr merge --squash --delete-branch <n> --subject "<subject> (#<n>)"   # flags first; always --subject (W129)
curl -s https://jahjah-website.vercel.app/<path> -o /dev/null -w '%{http_code}\n'
```

- **Allow rules:** `:*` breaks at a token boundary; a path rule ends `/*`. Put the constrained token first; a prefix rule scopes only the first URL.
- **A leading `VAR=` is part of the command.** Allow the literal (`Bash(EXPECTED_PAGES=67 bash scripts/verify.sh:*)`) and move it with `ci.yml`. Never `Bash(EXPECTED_PAGES=*)`: it bypasses the deny list.
- **A compound command fails whole, silently, at its strictest segment:** give what matters its own call. `npm run reference` sees only tracked files: stage first (W139).
- **A working-directory sandbox outranks the allow list:** scratch and report files go in `/opt/jahjah/web/.astro/`, never `/tmp`.
- **Owner-shell only:** `.claude/settings.json` edits (no session can; a dispatched one can edit no `.claude/**`) and destructive `gh api` calls. A plan names them as owner preconditions (W116, W138, W139, W144).
- **A refused command is a finding:** report the exact command; never route around it.
