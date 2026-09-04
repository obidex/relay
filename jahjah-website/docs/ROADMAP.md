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

### P0 · Canon reset — *done (2026-09-02)*
Canon into the repo, VPS executor, `.claude/` gates, CI, reference generator, docs mirror, nightly backup. **Exit met.**

### P0.1 · Cowork lane into canon — *done (2026-09-02)*
The strategist's own lane written down as `docs/STRATEGIST.md` §8. Opened F16–F22.

### P0.2 · Workflow v2 — *shipped 2026-09-02, exit half met*
GATE 2 machine-enforced by a ruleset; CI job named `ci`, actions SHA-pinned, `tier3-guard`; chunk
issues + `jahjah-web-dispatch`; `/relay-report` to the issue; independent `review` job; Dependabot;
`jahjah-web-backup-check`.
**Exit: HALF MET, and the half that is not is worth naming.** Nothing reaches `master` except a
squash-merged PR on green `ci` — that is enforced by GitHub and proven both ways. But **no real chunk
has yet started from a label**: the lane is proven only by two smoke tests (issues #11 and #12), and
this chunk's own issue #6 was opened by the executor after the fact and deliberately never dispatched.
**P1 is the first real test of the loop**, and until it has run, F26 (retiring the relay) cannot close.

### P1 · Launch blockers — *shipped 2026-09-04, exit PARTLY met*
PRs #25, #26, #28, #29, #30, #31, #32, #33 merged; #34 open on purpose. Per-item status:
1. Watermarked/unlicensed images — **HALF DONE.** The grep half is clean (3 distinct Sanity CDN assets on the washer, no watermark markers). **The visual verdict is the owner's and is still open** (F1).
2. `/images/placeholder.jpg` 404 — **DONE** (#26, W112). A missing image is `null` and renders an inline no-image tile; 0 placeholder references remain, 0 asset 404s.
3. Hide incomplete products — **MECHANISM DONE, THE ITEM IS NOT** (#28, W110). Every product query was already fail-closed on `published == true`, and nothing needed hiding (22 visible, 0 false, 0 unset), so what shipped is the missing *check*: `scripts/hidden-products-check.mjs`, asserting no hidden product reaches `dist/` or the sitemap. **Nothing was hidden and nothing was curated.** The item as written — "8–10 complete flagship products beat 22 empty ones" — needs the owner to say which 8–10 (**F35**), and then a chunk to apply it. Two Codex P1 findings on that PR are also still unaddressed (**F40**).
4. `/admin` out of the sitemap + `noindex` — **DONE** (#30).
5. Launch-fact ruling (W088) — **NOT DONE. Owner's, and still open** (§4).
6. Accessibility — **DONE except the half that needs Arabic** (#32). Filters and gallery thumbs announce `aria-pressed`; 0 skipped heading levels across all 67 pages, guarded by `scripts/heading-audit.mjs`; one accessible name per card; five of seven Arabic chrome labels fixed by reusing existing strings; LTR isolation for model numbers, spec values, phone numbers and the footer copyright. The last two labels need **new Arabic** and are in **PR #34, open for the native reviewer**.
7. Page-specific meta descriptions — **DONE** (#33). Three distinct EN descriptions (150/138/148 chars); each AR mirror byte-identical to an existing reviewed string; the unruled "authorized distributor" agency claim gone from every meta description in both languages.
**Exit — PARTLY MET.** No asset 404s ✅ · sitemap clean ✅ · no hidden product *can* reach the build ✅ · accessibility ✅ bar the Arabic strings. **Not met:** "every visible product looks real and owned" — the catalogue is untouched, so this needs F35's ruling then a chunk, plus F1's visual verdict — and "facts ruled" (W088, owner). Neither is code.

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
| F1 | Verify watermark claim on DCEL washer images. **Narrowed 2026-09-04, not closed:** the grep half is clean — 3 distinct Sanity CDN assets on that product, no watermark markers, recorded in STATE. What remains is a **visual** judgement nobody but a person can make | 2026-09-02 | high | the owner looks at the three images and rules |
| ~~F2~~ | Register `jahjah-web-docs` + `jahjah-web-backup` in `jahjah-internal/docs/runbooks/automations.md` | 2026-09-02 | high | **closed 2026-09-02** — `jahjah-internal` PR #85 `743b17e` |
| F3 | Deprecated `@sanity/image-url` import pattern (build warning) | 2026-09-01 | low | warning gone |
| F4 | Chunk-size build warning (Studio bundle) | 2026-09-01 | low | accepted or split |
| F5 | **Two** dead RTL rules — `ProductDetail.*.css` `.product-name`, `Layout.*.css` `.footer-heading` | 2026-09-01 | low | P5 |
| ~~F6~~ | GitHub Pro for branch protection | 2026-09-02 | — | **closed 2026-09-02** — ruleset `master-protection` id 22124934 is active with no bypass actors (W100). Creating it is also what proved Pro is on: `gh api /user` omits `plan` without the `user` scope, so the plan was never observable from the executor |
| F7 | Name the three price tiers | 2026-09-02 | med | owner names them (default Tier 1/2/3) |
| ~~F8~~ | Confirm `gh` push scope for `jahjah-website` from the VPS | 2026-09-02 | high | **closed 2026-09-02** — preflight passed; PR #1 was pushed and merged from the VPS |
| ~~F9~~ | `/ar/404/` had no route, so Vercel answered it with the English 404 body | 2026-09-02 | — | **closed 2026-09-04** — PR #31. **The cause in this row was wrong and is corrected here rather than repeated:** the `/ar/404/` the link checker saw was the language **toggle's own `href`**, which is root-relative and therefore all that check can match; an hreflang alternate is an ABSOLUTE URL and was never visible to it. What closed F9 is `Layout`'s `oppositeUrl` fallback — on a `noindex` page the toggle goes to the other language's HOME, because `/404` has no `/ar/` counterpart. Suppressing the canonical and all three alternates on a `noindex` page is correct on its own merits and shipped in the same PR, but it is not what was failing. `verify.sh` no longer WARNs it and `STRICT_P1` no longer governs it: the link is an unconditional FAIL, and four positive assertions now walk `dist/404.html`, which the page-count check (`<dir>/index.html` only) never reached |
| ~~F10~~ | **closed 2026-09-02** (PR #4). All three actions pinned to full commit SHAs with their release tags in trailing comments; gitleaks moved from the Node-20 `@v2` to `@v3.0.0`, two weeks before GitHub removes that runtime. Original text: CI hardening: pin `gitleaks/gitleaks-action` to a commit SHA instead of the mutable `@v2` tag, and move off Node-20 actions before the runner drops them. (`GITLEAKS_LICENSE` is **not** needed — `obidex` is a user account. The permissions half is **done**: `pull-requests: read` was added after CI failed 403 without it — this row previously claimed the permissions were already sufficient, which was wrong.) | 2026-09-02 | med | ci.yml updated in a chunk that names it |
| ~~F11~~ | Does `.claude/agents/reviewer.md`'s `tools:` frontmatter restrict the agent? | 2026-09-02 | — | **closed 2026-09-04 — and the answer is "partly", not the "no" this row expected.** Four reviewer passes were invoked by name from a session rooted in `/opt/jahjah/web`, and each reported its own tool set: exactly **`Read` and `Bash`**, with no `Write`, no `Edit`, and **no MCP tools at all** despite MCP server instructions being injected into their context. So the frontmatter *does* restrict the tool set — but **not to what it lists**: `Grep` and `Glob` are named in it and were **not** granted, and the per-command `Bash(git diff:*)` scoping is **not** enforced — the agents ran `gh`, `npm`, `node -e`, `curl` and `python3` freely. **The operational consequence is the one that matters: a `tools:` list containing `Bash` is not a read-only guarantee**, since Bash grants interpreters and in-repo writes. One reviewer noted it could have written files while holding no `Write` tool, and used `.astro/` for scratch probes and cleaned up after itself |
| ~~F12~~ | Four facts extracted by nothing: Sanity `apiVersion`, the `/images/placeholder.jpg` fallback, `ProductDetail.astro` variant data attributes, the `category->{slug,nameEn,nameAr}` projection | 2026-09-02 | — | **closed 2026-09-02** — restated in `CLAUDE.md` §3 as their own bullet |
| ~~F13~~ | The executor workspace is not trusted, so the allow list is ignored | 2026-09-02 | — | **closed 2026-09-02** — the owner trusted `/opt/jahjah/web`; the allow list is in force (STATE live flag 11) |
| F14 | `jahjah-web-truth` still reports git facts about `/root/jahjah-website`, which is no longer the executor clone. Either point it at `/opt/jahjah/web` or say in the report that the section describes a spare copy | 2026-09-02 | med | the unit is updated in a chunk that names it |
| F15 | Optional, deferred from P0: have `jahjah-web-truth` run `bash scripts/verify.sh` from its clean checkout after its own build, so the verification ritual has one home. Not done in P0 — it is a proven job whose next run is Monday, so a change could not be re-proven inside the chunk | 2026-09-02 | low | web-truth runs verify.sh and stays green |
| ~~F16~~ | `docs/STRATEGIST.md`:20 said the claude.ai project holds one pointer file | 2026-09-02 | — | **closed 2026-09-02** — rewritten; W098 supersedes W072's clause |
| ~~F17~~ | STATE live flag 8 said project knowledge is "stale by definition" | 2026-09-02 | — | **closed 2026-09-02** — a sync lags, it is not stale by definition |
| ~~F18~~ | W072 still LOCKED as "ONE pointer file"; DECISIONS is append-only so it needs a superseding entry | 2026-09-02 | — | **closed 2026-09-02** — W098 and W099 supersede rather than edit |
| ~~F19~~ | F13 refuted by a fact §8 recorded | 2026-09-02 | — | **closed 2026-09-02** — F13 closed |
| ~~F20~~ | A state fact ("done for the executor clone") sat in STRATEGIST, whose header forbids dated content | 2026-09-02 | — | **closed 2026-09-02** — moved to STATE live flag 11; §8 points there |
| ~~F21~~ | "Every preflight counts files" bound the implementer but appeared only in §8, so no preflight executed it | 2026-09-02 | — | **closed 2026-09-02** — now in `CLAUDE.md` §5 and the §4 mega-prompt template |
| ~~F22~~ | `?v=` does not guarantee a fresh body; INDEX can be current beside a stale sibling | 2026-09-02 | — | **closed 2026-09-02** — W102, folded into the READING MAP and §8. It recurred while being closed, from the CDN side |
| ~~F23~~ | **Dependabot PRs cannot go green unaided**, for three compounding reasons — the Tier-3 guard (deliberate, one body line clears it); no Actions secrets on a bot-actored run, so the build guard fails; and reference drift, because `npm run reference` records `package.json`'s version ranges and Dependabot rewrites them, which cannot be cleared from the PR body at all | 2026-09-02 | — | **closed 2026-09-04 as policy, not as a fix — W114.** They are not meant to go green. Dependabot is a notifier; the executor applies the update in a chunk PR with secrets present, then closes the bot's PR naming that one. A major version gets its own named chunk |
| ~~F24~~ | **Owner decision: mirror the three Sanity values as *Dependabot* secrets, or not** | 2026-09-02 | — | **closed 2026-09-04 — the owner ruled NOT MIRRORED.** A live read token, which can see drafts, is never handed to a run executing an unreviewed dependency version. The consequence is that Dependabot PRs stay red, and W114 makes that the intended state rather than a problem |
| ~~F25~~ | **The `review` job is green and silent** — no run ever posted a finding or a "no issues" summary, cause never established | 2026-09-02 | — | **closed 2026-09-04 by removing the question, not by answering it** (#25, W113). The job no longer runs on pull requests at all: it is `workflow_dispatch`-only, the manual fallback for when Codex is down or silent. A job that never speaks is not a gate, and paying a model on every PR for it was the actual cost (F28). If it is ever dispatched and stays silent, reopen this |
| **F26** | **Retire the public relay** once one full chunk has run end to end through the strategist's GitHub connector. Until then every report is dual-published | 2026-09-02 | low | the strategist confirms it has read a whole chunk from the issue alone |
| **F27** | **Real import drill** — restore a backup into a scratch Sanity dataset and diff it against live. Needs `SANITY_WRITE_TOKEN`, which W079 keeps server-side until P3. Until then `jahjah-web-backup-check` proves count parity + asset presence (W104) | 2026-09-02 | med | P3, once a write token exists |
| ~~F28~~ | **Watch what the `review` job costs** — a model call on every PR, spend in a subagent fan-out `--max-turns` does not bound | 2026-09-02 | — | **closed 2026-09-04 — the trigger was narrowed to nothing** (#25). `workflow_dispatch`-only means it costs nothing unless someone asks for it. The eight P1 PRs ran it zero times |
| ~~F29~~ | `.github/dependabot.yml` invisible to `npm run reference` (the generator walked `.github/workflows` only) | 2026-09-02 | — | **closed 2026-09-04** (#25) — the generator now walks `.github` whole, so the weekly bot is on the automation surface |
| ~~F30~~ | `ci.yml`'s header still called the job "the advisory gate W084 describes" and said T2 "ATTEMPTS" the ruleset — stale since the ruleset landed | 2026-09-02 | — | **closed 2026-09-04** (#25). Measured comments-only: 0 non-comment lines changed |
| **F31** | **Six Dependabot PRs are open and none can go green** — #14, #15, #16 (**security**) and #19 (grouped minor/patch), #20, #21 (majors). Policy is now settled (W114) and the secrets question is answered (F24: not mirrored), so what remains is the doing: apply each update in a chunk PR, then close the bot's PR naming it. **P1 could not do it** — its own plan forbids `npm update` and `gh pr close`. **Three of the six are security updates, so this should not sit.** **The six split two ways:** #14, #15, #16 and #19 are minor/patch and are **P1.1 · T0d**'s; #20 and #21 are **major** bumps of the embedded Studio and are **not** P1.1's — they belong to the named chunk in **F34**, per W114 | 2026-09-02 | **high** | the four minor/patch PRs are applied and closed by P1.1 · T0d, and #20/#21 are dispositioned by F34's chunk |
| **F32** | `docs/DECISIONS.md` now carries **two conventions for the same relationship**: older superseded entries (W035, W053, W057, W061, W071) were given a "SUPERSEDED by W###" status tag, while W072, W073, W084 and W089 still read as plain LOCKED with no forward pointer — so a reader trusting the tag takes W084's "CI is advisory" as current. Adding tags would mean editing entries in an append-only file, which is why it was not done here | 2026-09-02 | low | the file states which convention wins, or a chunk is authorized to add forward pointers |
| **F33** | **P1.1 — the chunk that finishes P1.** Scope, all of it carried out of P1 rather than discovered: **T0d** (apply the four minor/patch Dependabot updates — #14, #15, #16, #19 — in chunk PRs and close the bot's, per W114; three of them are security. The two **majors**, #20 and #21, are **not** P1.1's: F34); **close #27**, the stale duplicate of #28; **the T1 visual verdict** from the owner (F1); and **the probe table**, now unblocked since the allowlist repair in #29 made per-path `curl` work. Everything here was reachable-but-forbidden in P1, not unknown | 2026-09-04 | **high** | P1.1 runs and its issue closes |
| **F34** | **Studio 5 → 6 needs its own named chunk.** Dependabot #20 (`sanity`) and #21 (`@sanity/vision`) are **major** bumps of the embedded Studio, which is the `/admin` surface, three `vercel.json` rewrites (W014), `basePath`, and the schema types. W114 says a major gets its own chunk; this is that chunk, and it is not P1.1's | 2026-09-04 | med | a named chunk upgrades both together and `/admin` still loads on an iOS home-screen link |
| **F35** | **The owner's hide-list — and the chunk that applies it.** P1 proved no product is hidden and that none *can* leak if one is (W110), but nobody has said **which** of the 22 placeholder products should be hidden or deleted. W031 wants 8–10 real flagships; W007 says placeholder records are deleted, not polished. The machinery is ready and idle | 2026-09-04 | med | the owner names the keepers; a chunk applies it under GATE 1 |
| **F36** | **`jahjah-website.vercel.app` is indexable and will still be at launch.** `robots.txt` allows it and there is no host-level `noindex`, so the preview domain can accumulate index entries that compete with `jahjah.net` the moment the domain connects (W027). Decide before L: canonical host redirect, or `X-Robots-Tag` on the `.vercel.app` host | 2026-09-04 | med | handled in the **L** launch bundle, or ruled a non-issue in writing |
| **F37** | **`verify.sh` §7 and §7b are asymmetric about credentials, and the weaker one is the real gate.** **Codex found this first**, as a P1 on PR #28 that nobody read (F40); it is repeated here because it is true, not because it was discovered here. §7's leak checks are pure greps over `dist/` and always run. §7b — the hidden-products check, which is the W077 guard — needs `SANITY_READ_TOKEN` and **exits 0 with a `SKIP` on any failure**: no token, no network, expired token, 403, wrong dataset. That is deliberate, so a developer clone stays green, and the SKIP is surfaced as a `WARN`. But it means **the guard that matters most is the one that can silently not run**, and only a human reading the WARN notices. CI has the secret today, so it does run there | 2026-09-04 | med | CI asserts the check actually ran (fail if SKIPped when secrets are present), or the asymmetry is accepted in writing |
| **F38** | **The dispatcher removes `chunk:approved` but leaves `chunk:proposed` in place** — so a running chunk's issue carries a label saying it is still waiting on the owner. Cosmetic today because the poll keys off `chunk:approved`, and misleading to anyone reading the issue. **ERP-side**, in `jahjah-web-dispatch`, not this repo | 2026-09-04 | low | the lane removes both labels |
| **F39** | **Two sessions of this chunk ended silently, and nothing anywhere caught it.** The dispatched run exited 1 at 5307 s. The first interactive resume then pushed T5, went green, received the Codex review, printed its task summary and **ended without merging PR #31 or publishing any report** — from outside, a finished task looked like a stall. A later interactive session was paused mid-task for ~5.5 h between #33 going green and its merge. The lane's `chunk:failed` fallback fires only when a *dispatched* process exits, so an interactive session sitting on a permission prompt produces no signal at all. W117 records the executor-side rules (merge before summarising; the label move belongs to the skill); this row is the **reliability** question that outlives them | 2026-09-04 | med | an awaited chunk's silence is distinguishable from its completion without a person opening the issue |
| **F40** | **Two Codex P1 findings on PR #28 were never seen, never answered, and are still unfixed — and the reason is a §5 breach.** #28 opened 2026-09-03T03:47:34Z, was **merged 88 seconds later** at 03:49:02Z, and Codex's review arrived **108 seconds after the merge** at 03:50:50Z. CLAUDE.md §5 says wait for it; that session did not. Both findings are on `scripts/hidden-products-check.mjs` and both are real: **(a)** the check exits 0 with a `SKIP` on *any* Sanity failure — no token, network, 403, wrong dataset — so `ci` stays green without ever checking hidden products (this is the same defect independently rediscovered as **F37**, and Codex found it first); **(b)** when the build emits no Sanity image URL — a valid output, since images are optional — `fromDist` is null and the script **silently falls back to `pxf1amia/production`**, so a hidden product in a differently-configured store is missed while the check reports success against an unrelated default. The same finding (b) sits unanswered on **#27** too. **Neither meets THE BAR** (no leak is demonstrated), so nothing is being bypassed by recording rather than fixing — but they must be answered, not absorbed silently into a follow-up row with no attribution | 2026-09-04 | **high** | both are fixed or answered on the PRs, and the SKIP-when-credentialed case fails CI |
| **F41** | **`.claude/skills/relay-report/SKILL.md` points at a "stall protocol (`docs/STRATEGIST.md` §1)" that no longer exists.** T8 replaced the MEGA/MID/SMALL cadence table, and the only definition of that protocol — "silent > 40 min on a SMALL job" — went with it. §1 still names **stall** as one of the three mid-chunk message kinds, so the concept survives and only its threshold is gone. `.claude/**` is Tier 3 and T8 does not name it, so this PR could not fix it | 2026-09-04 | low | a chunk that names `.claude/**` either restates the threshold or drops the phrase |

---

## 4. OPEN DECISIONS (owner's)

| Decision | Blocks | Default if unanswered |
|---|---|---|
| **The watermark verdict on the DCEL washer images** (F1) — look at three photos and say yes or no | P1's exit; L | the product stays as it is, flagged |
| **Which of the 22 placeholder products to keep** (F35) — W031 wants 8–10 real flagships | P1's "every visible product looks real", P5 | all 22 stay visible |
| **Launch-fact ruling** (W088): founding year 2009 vs 2010, every phone/WhatsApp number, hours, warranty wording, delivery coverage, **agency/exclusivity claims**, the active brand list | L | site stays as is; flagged. **Now partly urgent**: P1 removed "authorized distributor" from every meta description, and the claim still stands in home body copy and in the Organization JSON-LD, so the site currently says two different things about itself |
| **Currency for prices** — SYP / USD / both (W066) | flipping `prices_visible` | keep OFF |
| **Name the three price tiers** (F7) | P2 schema, P4 display | default Tier 1 / 2 / 3 |
| Publish the showroom address publicly? | P5 showroom section, Business Profile at L | contact page unchanged |
| ShamCash merchant API capability (W064) | P6 ordering | quote list only |
| Buying guides / testimonials — will anyone write/collect them? | Later | skip |

**Answered 2026-09-04:** *mirror the three Sanity values as Dependabot secrets?* — **no** (F24). A live read token that can see drafts is never handed to a run executing an unreviewed dependency version. Dependabot PRs therefore stay red by design (W114).

---

## 5. REJECTED — DO NOT REOPEN

Next.js for the public site · Shopify · WooCommerce · off-the-shelf ERP (W018) · prices/stock/customers in Sanity (W075) · a separate portal deployment or `trade.` subdomain (W082) · Sanity visual editing as the admin experience (W082) · Vercel ignored build step (W087) · combining host migration with launch (W027, W078) · a hosted (non-embedded) Studio (W011).

---

## 6. HOW TO USE THIS FILE

"What's next?" → STATE §5, then the phase here. "Should I do X yet?" → find X in §2; if out of phase say what comes first. A proposal that locks the project out of the VPS move, the ERP sync, or the Arabic gate is a bad proposal however clever. When a phase closes, an item ships, or a decision is answered: update here **and** write the `W` entry — in the same PR.
