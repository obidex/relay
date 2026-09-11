# DECISIONS.md — Judgment Register

> Append-only, newest last. A `W###` number is never reused or removed. Change a decision by appending one that names the old number. Each entry is at most two lines: the rule, then a short why.
> Full narratives of W001–W151: `docs/archive/DECISIONS-full.md` (never loaded in a session). They were compressed by chunk P2b-1c (issue #79).
> Tags: **LOCKED** (binding) · **LESSON** (generalizable rule) · **OPEN** · *superseded by Wnnn* (history only; read the successor) · *amended by Wnnn* (binding as amended).

## Foundation (late 2025 – 2026-04)

- **W001** superseded by W074.
- **W002** LOCKED. Apple/Muji-inspired aesthetic on white, tokens in `src/styles/global.css`; when in doubt choose the richer option (W052).
- **W003** LOCKED. Vanilla CSS with custom properties; no Tailwind, shadcn, CSS-in-JS or any CSS framework.
- **W004** LOCKED. Vanilla JS on pages; no React/Vue/Svelte for site features (Studio's React stays inside Studio).
- **W005** LOCKED. WhatsApp is the inquiry channel, no contact form; public visitors never see prices (W081).
- **W006** LOCKED. Product copy is lifestyle-led; specs are a secondary table.
- **W007** LOCKED. Current products are AI placeholders: deleted when real data enters, never polished.
- **W008** LOCKED. Arabic at `/ar/*` with a URL-swapping toggle, never a cookie/header switch, for SEO and shareable links.
- **W009** LOCKED. RTL through logical CSS properties; direction-specific rules only where logical ones cannot express it.
- **W010** LOCKED. Modern Standard Arabic; DCEL, LAPON, DSP, SUNNY, JAHJAH stay Latin in both languages; international numerals for specs, Arabic-Indic where a name reads better.
- **W011** LOCKED. Sanity CMS, Studio embedded at `/admin` in this repo; `_id`s `product-${slug}`/`brand-${name-lc}`/`category-${slug}`; EN/AR sibling fields in one document.

## Production bugs that became rules (2026-05-09)

- **W012** LOCKED. `perspective: 'published'` AND `!(_id in path("drafts.**"))` on every query; without either, drafts collide with slugs and pages vanish.
- **W013** LOCKED. The `product.ts` slug validator strips `drafts.` and excludes both IDs; "simplifying" it clears the slug on every edit.
- **W014** LOCKED. `vercel.json` keeps exactly three `/admin` rewrites to `/admin/index.html`; collapsing them 404s Studio from an iOS home-screen icon.
- **W015** amended by W090. Vercel Hobby is non-commercial.
- **W016** LESSON. Imports match filename case exactly (Linux is case-sensitive); rename via `git mv` through a temp name.

## Direction (2026-05-10 – 05-14)

- **W017** superseded by W089.
- **W018** LOCKED. Rejected for the public site: Next.js, Shopify, WooCommerce, an off-the-shelf ERP. Do not reopen.
- **W019** LESSON. Astro's scoper injects into a leading attribute selector inside scoped `<style>`, so chain RTL rules on `html[dir="rtl"]`; check `dist/_astro/*.css`.
- **W020** LESSON. Cascade and layout bugs: measure the compiled output first, fix second.
- **W021** LESSON. Runtime inspection is a step separate from code review (reviewer + TRUTH probes; a browser when a bug needs it).
- **W022** LOCKED. Company name exactly `شركة الجحجاح التجارية`; Latin `JAHJAH` is the cooker brand, a different string.
- **W023** LOCKED. Arabic is canonical and English serves it; the AR `<title>` suffix uses `companyNameAr`.
- **W024** LOCKED. Manufacturer + main supplier + distributor, never just "distributor"; Sarmada (Idlib, HQ) and Damascus (Al-Baramkeh); all of Syria.
- **W025** blocking clause superseded by W125. Every meaningful AR string, meta included, is native-reviewed (now batched).
- **W026** LESSON. Split large executor tasks: one prompt hit the 32K output cap.
- **W027** LOCKED. Launch is ONE bundled event (domain, Pro, Cloudflare, CORS, site URL, analytics, search consoles, Business Profile, invites); never piecemeal, never index `vercel.app`.
- **W028** LESSON. SEO infrastructure is not findability; Google must crawl first. Say so honestly.
- **W029** LOCKED. Every task starts with a read-only investigation; "no work needed" is a valid result.
- **W030** LOCKED. Builder pass, then a reviewer pass on the diff before commit (the reviewer subagent, W084).
- **W031** amended by W089 and W126. Real photos gate product polish; 8–10 complete flagships beat 22 half-empty ones; only the owner hides.

## Phase A/B lessons (2026-05-15 – 05-18)

- **W032** superseded by W085.
- **W033** LESSON. At equal specificity, source order decides and Astro does not reorder: shared rules before overrides.
- **W034** LESSON. `Astro.url.pathname` in a static `404.astro` is `/404`: visitor-URL logic runs client-side (`is:inline`).
- **W035** superseded by W084.
- **W036** LESSON. Per-variant images fall back to the product's; before choosing "simpler now", walk the next roadmap item.
- **W037** LOCKED. Tier-3 work never bundles the irreversible step; a Tier-3 PR merges in a chunk only if the plan named the file.
- **W038** amended by W094. `COLOR_OPTIONS` is duplicated in `product.ts` and `sanity.js` and edited together (TS→JS import rejected).
- **W039** LESSON. Static staleness masquerades as a bug: check the newest Vercel deployment and hard-refresh before investigating.
- **W040** LESSON. Changing a numeric gate: walk concrete cases through the upstream data.
- **W041** LOCKED. Brand and category pages are real routes, not query filters.
- **W042** LOCKED. Brand copy lives in Sanity; DCEL → LAPON → JAHJAH → SUNNY → DSP is the `BRAND_ORDER` constant; JAHJAH least-featured on purpose.
- **W043** LOCKED. Listings show the short description, detail the long one (split on `\n\n`).
- **W044** LESSON. Every step of a feature leaves the site working; each chunk task is an independently shippable PR.
- **W045** LESSON. A prompt that contradicts the source ships the contradiction; list the assumptions that contradict it.
- **W046** LESSON. Head-slot data is two halves, frontmatter AND `<Fragment slot="head">`; verify in compiled HTML.
- **W047** LESSON. Astro emits `<script type="application/ld+json">` with no space before `>`: match the literal or `[^>]*>`.
- **W048** LESSON. GROQ cannot call functions across a reference traversal: fetch the `_id`, filter on `_ref`.
- **W049** LOCKED. Re-fetch live data immediately before deleting any Sanity field.
- **W050** LESSON. Copy a production-verified pattern verbatim; adapt data, not structure.
- **W051** LESSON. EN/AR mirrors are diffed programmatically (`<style>`, `t()` calls), not eyeballed.
- **W052** LOCKED. The owner prefers rich UI; the strategist never recommends the plain option.
- **W053** superseded by W072.
- **W054** LOCKED. Early-churning content ships in `translations.js`, migrating to Sanity when editing becomes the bottleneck.
- **W055** LOCKED. Support/policy pages live in the footer; top nav is for what customers shop for.
- **W056** LOCKED. Every AR string, meta included, is approved text; reusing an approved sentence beats drafting a new one.
- **W057** superseded by W085 and W072.
- **W058** LOCKED. An interrupted session's claims are worthless; the next re-runs build and verification from scratch.
- **W059** LESSON. Defer pattern-dependent details to the investigation step; never guess.
- **W060** LESSON. Arabic: no "built-on-X", bureaucratic noun phrases or defensive claims; would a Syrian merchant say it face to face? Warranty `كفالة معتمدة من شركة الجحجاح التجارية`; SUNNY/DSP `الوكيل الحصري`.
- **W061** superseded by W072.

## Strategic reset (2026-06-11)

- **W062** amended by W075. Three pillars: public website, internal ERP, future commerce layer.
- **W063** LOCKED. Build the real backend once, no throwaway MVP; features ship dark until lit.
- **W064** OPEN. ShamCash merchant API capability unconfirmed (owner obtains docs); payments never run in Astro or the browser.
- **W065** LOCKED. The ERP is `jahjah-internal`, with its own canon; this canon records only the contract between them.
- **W066** amended by W081. Currency (SYP/USD/both) is OPEN and blocks flipping `prices_visible`.
- **W067** LOCKED. Headless commerce packages (Medusa/Saleor) are candidate-if-needed only.
- **W068** LOCKED. Category landing pages in scope; analytics + WhatsApp-click events are launch items; Sanity backup required (W083).
- **W069** delivered by W084. The agentic layer (`.claude/` deny rules, reviewer, skills).
- **W070** delivered by W073. Audit the live site before trusting a doc (weekly `jahjah-web-truth`).
- **W071** superseded by W072.

## Canon reset (2026-09-02)

- **W072** LOCKED, amended by W098. Canon lives in the repo, mirrored to the relay; Claude Code updates it at chunk end, never the owner.
- **W073** LOCKED, amended by W099 and W144. Executor = Claude Code on the VPS (tmux `web`); a plan confirmed once runs unattended; the owner may veto merges.
- **W074** LOCKED. Prerendered by default; `prerender = false` only for session/per-visitor routes, each in the reference; public pages carry no prices, tokens or session logic.
- **W075** LOCKED. Sanity = content; Supabase #2 = identity + commerce, RLS the boundary; never a price/stock/customer field in Sanity; ERP = later one-way SKU sync, never a build or request dependency.
- **W076** LOCKED. One SKU per sellable variant, unique and immutable, the only integration key; ≥ 1 variant per product; slug and modelNumber are not keys.
- **W077** LOCKED. Hidden/disabled products never reach HTML, sitemap, search or listings (build AND request time); links 404 without staff.
- **W078** LOCKED. No Vercel-only APIs: moving hosts is an adapter swap (`@astrojs/node`, Caddy, Cloudflare); the VPS mirror comes after launch.
- **W079** LOCKED. `SANITY_WRITE_TOKEN` server env only, never `PUBLIC_`, a bundle or a report; rotate on exposure. `SANITY_READ_TOKEN` also on the VPS and in Actions.
- **W080** LOCKED. Staff roles admin/editor/sales with TOTP MFA; every write audited with the previous value; destructive actions confirm; soft-delete preferred.
- **W081** LOCKED. `prices_visible`/`promotions_enabled` OFF; `stock_display` = status; tiers 1/2/3 + none, sign-up → 1; `require_approval` OFF; hidden = staff-only, disabled = kept, flagged off.
- **W082** LOCKED. Admin Mode = pencil on the live page + `/admin-mode` panel; content via a server route to Sanity with an audit row, commerce to the web DB; no Sanity seats, no portal.
- **W083** LOCKED. Nightly backup 02:30 UTC (Sanity export + web-DB `pg_dump`) to `/root/backups/web`, verified, keep 7, never the relay.
- **W084** LOCKED, amended by W095 and W100. Gates machine-shaped where possible; `master` only via PR, green CI and a clean reviewer.
- **W085** LOCKED. `npm run reference` generates `docs/reference/site.md`, CI fails when stale; no hand-written inventory.
- **W086** LOCKED. TypeScript for server-side code; existing `.astro` pages, `src/utils/*.js` and `translations.js` stay JS.
- **W087** LOCKED. No Vercel "ignored build step": it can silently skip a deploy-hook content build.
- **W088** LOCKED, partly ruled by W126. One named person rules on founding year, numbers, hours, warranty, delivery, agency claims and brands before launch.
- **W089** LOCKED; its branch-protection rejection reversed by W100. Phase order P1 → P2 → P3 Admin Mode → P4 → P5 → L → P6.
- **W090** LOCKED. Vercel Pro precedes the first on-demand route in production; unprotected previews are the PR review surface.
- **W091** LOCKED. Free Supabase pauses after ~1 week idle; the nightly backup keeps it awake and health flags a pause.

## P0 execution (2026-09-02)

- **W092** LOCKED. `.gitignore` re-includes `.claude/{settings.json,agents,skills}` and ignores `.env*`; count tracked files before every push.
- **W093** LOCKED. The Sanity CLI reads `SANITY_AUTH_TOKEN`; the backup runs from the clone so `sanity.cli.ts` names the store.
- **W094** LOCKED. `COLOR_MAP` is only in `sanity.js`; the reference diffs the two `COLOR_OPTIONS` literals, `EXTRACTOR MISS` if unread.
- **W095** LOCKED. `.claude/settings.json` guards against accident, not a sandbox; the real gate is approved plans and reviewed PRs.
- **W096** LOCKED. Backup liveness is archive freshness (> 30 h = attention), because a skipped systemd unit never fails; archives stamped to the second.
- **W097** LESSON. A trailing `grep -q` under `pipefail` inverts its test (SIGPIPE); use `grep -c` or capture the output.
- **W098** LOCKED. claude.ai project knowledge is a GitHub sync of `docs/` + `CLAUDE.md`; it lags, and the mirror wins.
- **W099** LOCKED, amended by W144. A chunk starts from an issue: the owner's `chunk:approved` confirms; the lane runs it with `CHUNK_ISSUE`; reports are issue comments.
- **W100** LOCKED. Ruleset `master-protection` (22124934): PR, squash, required `ci`, no bypass; never edit it or run `claude setup-token`.
- **W101** LOCKED. `tier3-guard` needs `Tier-3: authorized by chunk <name>` on a Tier-3 PR; a floor, not a ceiling; `edited` re-runs it.
- **W102** LESSON. A fresh `INDEX.md` does not mean a fresh sibling: trust its "Mirrored commit" line; on the box read the clone.
- **W103** LESSON. No rule allows `.`/`source` (publish via the wrapper); `--allowedTools` is additive; add a label before removing it; no `KillMode=process` under tmux; first run from the timer.
- **W104** LOCKED. A backup is proven by drafts-excluded count parity and asset presence until a write token allows a restore drill; a mismatch is a finding.
- **W105** LESSON. A control resting on metadata or another system's roles is a convention: record it as a dated assumption.
- **W106** LESSON. Never suppress a gate's output and its exit status together.
- **W107** LESSON. A broken user-level hook refuses the Bash its kill switch needs: `bash -n` first, replace atomically, recover with a file tool.
- **W108** LOCKED. Pass values to `tmux new-window` as `-e VAR=value`, never inside the command string; validate external fields on entry.
- **W109** LESSON. A turn cap below a job's need bounds its output, not its cost; say so where a limit is untested.

## P1 – P1.2 execution (2026-09-04 – 09-05)

- **W110** LOCKED. `published == true` is the fail-closed content filter (`!= false` admits unset); a plan may name a file, never weaken a guard.
- **W111** LOCKED. The 404 is `noindex`, has no canonical or hreflang, toggles to the other home, keeps `lang="en"` with per-block `lang`/`dir`; `verify.sh` walks it.
- **W112** LOCKED. A missing image is `null`, rendered as `NoImageTile` with no `<img>`, never a static-file URL.
- **W113** LOCKED, amended by W130. Codex is the reviewer of record, the executor's reviewer the gate, `review` a manual fallback; silence is never approval.
- **W114** LOCKED, amended by W149. Dependabot notifies: apply its update in a chunk PR, close the bot's naming it; never merge a bot PR; a major gets its own chunk.
- **W115** LOCKED. Nothing of ours in `~/.claude`, hooks project-level only after `bash -n`; cross-repo work is an isolated clone plus a PR; publish via the wrapper.
- **W116** LESSON, remedy replaced by W138. A dispatched session cannot edit `.claude/**`; dry-run the allowlist before relying on it.
- **W117** LESSON. An interactive session can end silently: merge before summarising; the final label move belongs to the skill.
- **W118** LESSON. Anchor a guard grep on the forbidden assignment, not the identifier, because shipped comments match too.
- **W119** LESSON. Bidi isolation goes on an inner `<bdi>`, not the layout box; `<bdi>` is `dir="auto"`, falling back to LTR.
- **W120** LESSON. An editor-supplied string makes every interpolation an injection site: use `createElement`/`setAttribute`.
- **W121** LESSON. An audit grep sees only its own shape: report sweeps as what they are, and uncatchable checks as vacuous.
- **W122** LESSON. Re-measure a claim about an external system when writing it down; chunk-close review fact-checks.
- **W123** LOCKED. `npm update <named packages>`, never bare; `package.json` byte-identical; expect newer than the bot named; name majors; restore stripped `libc`.
- **W124** LOCKED. With a token supplied, any Sanity failure is exit 4 = FAIL (3 leak, 1 crash); the store resolves atomically and env must agree with `dist/`.
- **W125** LOCKED (owner; supersedes W025's blocking clause). Arabic never blocks a merge: strategist-approved Arabic ships; native review is batched.
- **W126** LOCKED (owner rulings). Watermark clean; only the owner hides products; founded 2010; SUNNY/DSP exclusive; issues close by `final`.
- **W127** LOCKED. `/relay-report final` moves `chunk:running` → `chunk:done` and closes the issue (the owner never closes one by hand); `blocked`/`interrupted` leave it open.
- **W128** LESSON. A dispatched run dies on the shared 5-hour window leaving 53 bytes; the dispatcher must capture `stream-json` and retry it (ERP-side).
- **W129** LESSON. `gh pr merge --squash` takes the branch's first commit message: always pass `--subject`.
- **W130** LESSON (amends W113). Codex's 👍 reaction means "reviewed, nothing found": read all four surfaces; believe a system's own convention.
- **W131** LOCKED. PR-body heading `AR strings shipped (strategist-approved per W125; batched for native review)`; a missing list is a nit; a rule names when its artefact exists.
- **W132** LOCKED. CI's Verify step gets `PUBLIC_SANITY_*`, trading a content-edit red for a possible vacuous green.
- **W133** LESSON, remedy replaced by W138. A cleanup authorized on a change that did not land must not land either.
- **W134** LESSON. The Tier-3 line is never emphasised; Codex's 👀 is not a verdict (match `+1`); time intervals from artefact timestamps.

## P2a execution (2026-09-10)

- **W135** LOCKED. `variants[].sku` pattern `^[A-Z0-9]{3,24}(-[A-Z0-9]{1,8}){0,3}$`, unique (both ids excluded), two rules not a chain; not required until backfill; no `readOnly`.
- **W136** LOCKED. Every JSON-LD block goes through `jsonLd()` (escapes `<`, `>`, `&`, U+2028/9); `verify.sh` loads `.env.local` filling gaps only.
- **W137** LOCKED. Product `<img>`: box `width`/`height`, `w`-varied `srcset` without `dpr`, lazy except LCP/first row; drop `srcset` before reassigning `src`.
- **W138** LOCKED. The owner edits `.claude/settings.json` by hand; the executor commits his diff only if porcelain lists that one file, the JSON parses and the rule appears once. An allow rule is a plan precondition.
- **W139** LESSON. A compound command fails whole at its strictest segment; `npm run reference` sees only tracked files; deletions are the owner's.
- **W140** LESSON. A ruling's claim about an external system is re-measured when acted on.

## P2b-1 and P2b-1b execution (2026-09-10)

- **W141** LOCKED. Browser floor iOS/Safari 16 via `vite.build.cssTarget`; raising it is the owner's call; `compressHTML: true` pinned.
- **W142** LOCKED. `@astrojs/vercel`, static, no `functions/`; Vercel applies `vercel.json`'s `/admin` rewrites on top.
- **W143** LOCKED. `src/lib/env.ts` reads literal `process.env.X` at call time and throws when unset, never `import.meta.env`; middleware sets `locals.lang` only.
- **W144** LOCKED. A hand-started chunk runs `claude --permission-mode auto`; the owner is never a permission gate; the done-marker ends `final`/`blocked`.
- **W145** LESSON. A new CSS minifier can raise the browser floor silently: diff compiled CSS rule by rule on any build-tool upgrade.
- **W146** LESSON. A tool fetching a protected URL may mint a credential; probe previews with plain `curl`.
- **W147** LESSON. A silent exit 0 proves nothing until coverage and a negative test show the check ran (`npx -p typescript tsc`).
- **W148** LESSON. Dependabot security updates ignore version-update groups; they need `applies-to: security-updates`.
- **W149** LOCKED (amends W114). Bot branches are never Vercel-built or CI-run; F34's majors are ignored at the bot until it deletes the ignores.
- **W150** LESSON. Never write close/fix/resolve before a `#number` in a PR body or commit: GitHub closes it. Scan with `grep -noiE '(close[sd]?|fix(e[sd])?|resolve[sd]?)[[:space:]:]+#[0-9]+'`.
- **W151** LESSON. An `ignore` rule's `update-types` does not hold back security updates; when docs are silent, read the source.

## P2b-1c execution (2026-09-10)

- **W152** LOCKED. The canon diet (#81) cut the mirrored canon from 262,210 to 70,072 bytes. Live files hold rules only; history lives verbatim in `docs/archive/`, never mirrored or loaded.
  The owner's session-efficiency rules (`CLAUDE.md` §5, STRATEGIST §1) cap reports, PR bodies, reviewer passes and Codex waits. "`dist/` byte-identical" means the 67 public pages (F61).

## P2b-2 execution (2026-09-11)

- **W153** LOCKED. Web DB schema v1 is `supabase/migrations/20260911000000_p2b2_foundation.sql` as applied (#86). It has seven tables (`staff`, `customers`, `settings` with six keys: W081's four, plus `currency` (W066) and `tier_names` (F7), `prices`, `promotions`, `stock`, `audit_log`), an audit trigger on six of them, and the sign-up trigger (W081).
  RLS rests on three principles:
  - Every table denies by default.
  - The service key is server-only and bypasses RLS (W079), so the caller of a `src/lib` reader is the gate.
  - Staff writes need `aal2` (TOTP, W080).
  Supabase's `rls_auto_enable()` and `ensure_rls` are platform-owned (the owner's "Enable automatic RLS") and are never versioned, so "`db diff` empty" means empty beyond them (ruling 2026-09-11).
  Two residuals stand until F65: anon can EXECUTE the helpers through `PUBLIC`, and customers' `stock` reads return `quantity`.
- **W154** LOCKED. Every web-DB change is a file in `supabase/migrations/`, applied by `supabase db push` from the executor's linked clone. GATE 1 shows every migration verbatim in the plan, and the file is extracted from the issue body, never retyped.
  A push is proven three ways: `migration list` (local = remote), `db diff --linked` (W153), and `scripts/db-smoke.mjs` (counts only; exit 2 on a mismatch, 3 on an anon row).
  The CLI's access token and DB password are exported for one command and never reach the runtime or `src/lib/env.ts`.
- **W155** LESSON. On Supabase, `revoke execute … from anon` is inert while `PUBLIC` holds EXECUTE. RLS policies call their helpers with the caller's privileges, so a real revoke turns anon's empty reads into permission errors. Probe with the publishable key before trusting a revoke.
- **W156** LESSON. RLS filters rows, never columns: a policy that lets a role read a table hands that role every column. Hide a column with a view, an RPC or column grants (Codex P1, #86).
- **W157** LESSON. A plan's "expect: no diff" must allow for what the platform provisions at project creation. Measure a fresh project's `db diff` before writing the expectation.
