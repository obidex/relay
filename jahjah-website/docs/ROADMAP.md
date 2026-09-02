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
| ~~F6~~ | GitHub Pro for branch protection | 2026-09-02 | — | **closed 2026-09-02** — ruleset `master-protection` id 22124934 is active with no bypass actors (W100). Creating it is also what proved Pro is on: `gh api /user` omits `plan` without the `user` scope, so the plan was never observable from the executor |
| F7 | Name the three price tiers | 2026-09-02 | med | owner names them (default Tier 1/2/3) |
| ~~F8~~ | Confirm `gh` push scope for `jahjah-website` from the VPS | 2026-09-02 | high | **closed 2026-09-02** — preflight passed; PR #1 was pushed and merged from the VPS |
| F9 | `/ar/404/` — the 404 page's Arabic hreflang alternate has no route, so Vercel answers it with the English 404 body. `verify.sh` WARNs it as known-until-P1; `STRICT_P1=1` FAILs it | 2026-09-02 | med | P1 fixes the alternate or adds the route |
| ~~F10~~ | **closed 2026-09-02** (PR #4). All three actions pinned to full commit SHAs with their release tags in trailing comments; gitleaks moved from the Node-20 `@v2` to `@v3.0.0`, two weeks before GitHub removes that runtime. Original text: CI hardening: pin `gitleaks/gitleaks-action` to a commit SHA instead of the mutable `@v2` tag, and move off Node-20 actions before the runner drops them. (`GITLEAKS_LICENSE` is **not** needed — `obidex` is a user account. The permissions half is **done**: `pull-requests: read` was added after CI failed 403 without it — this row previously claimed the permissions were already sufficient, which was wrong.) | 2026-09-02 | med | ci.yml updated in a chunk that names it |
| F11 | Prove empirically whether `.claude/agents/reviewer.md`'s `tools:` frontmatter actually restricts the agent. **Narrowed 2026-09-02, not closed:** a review subagent reported running `cat`, `sed`, `python3`, `find`, `WebFetch`, `WebSearch` — none of them in that list — unrestricted; but it was invoked from a session rooted at `/root`, so the project agent was never registered and every reviewer pass this chunk ran as a general-purpose agent handed `reviewer.md` as its contract | 2026-09-02 | med | a session started **in `/opt/jahjah/web`** invokes `reviewer` by name and its tool set is observed. A dispatched chunk runs from exactly there, so this is now cheap |
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
| **F23** | **Dependabot PRs cannot go green unaided, for three separate reasons** — the Tier-3 guard (deliberate, one body line clears it); no Actions secrets on a bot-actored run, so the build guard fails; and reference drift, because `npm run reference` records `package.json`'s version ranges and Dependabot rewrites them. The third cannot be cleared from the PR body at all | 2026-09-02 | med | the secrets question is decided (below) and the reference drift is automated or accepted |
| **F24** | **Owner decision: mirror the three Sanity values as *Dependabot* secrets, or not.** Doing so hands a live read token — which can see drafts — to a run executing an unreviewed dependency version. `jahjah-internal`'s D228 rejects the equivalent outright; that repo can skip its secret-dependent jobs, and ours cannot, because `ci` is one job whose secret-dependent steps ARE the gate | 2026-09-02 | med | the owner rules; record it as a `W###` either way |
| **F25** | **The `review` job is green and silent.** It no longer errors (W109 raised the turn cap after a measured `error_max_turns` at 41), but no run has yet posted a finding or a "no issues" summary. Cause not established — candidates are the plugin's own step-1 triage gate and slash-command expansion | 2026-09-02 | med | one run is observed to post output, or the cause is found and fixed |
| **F26** | **Retire the public relay** once one full chunk has run end to end through the strategist's GitHub connector. Until then every report is dual-published | 2026-09-02 | low | the strategist confirms it has read a whole chunk from the issue alone |
| **F27** | **Real import drill** — restore a backup into a scratch Sanity dataset and diff it against live. Needs `SANITY_WRITE_TOKEN`, which W079 keeps server-side until P3. Until then `jahjah-web-backup-check` proves count parity + asset presence (W104) | 2026-09-02 | med | P3, once a write token exists |
| **F28** | **Watch what the `review` job costs.** It calls a model on every PR and its spend is in a subagent fan-out that `--max-turns` does not bound; `timeout-minutes: 20` is the only real ceiling. Check Actions minutes per week | 2026-09-02 | low | a weekly figure is known and judged acceptable, or the trigger is narrowed |
| **F29** | `.github/dependabot.yml` is invisible to `npm run reference` — the generator walks `.github/workflows` only — so a new weekly bot is absent from the repo-automation surface. Same class as F12 | 2026-09-02 | low | the generator walks `.github/` or the omission is accepted in writing |
| **F30** | `.github/workflows/ci.yml`'s header still says the job "is the advisory gate W084 describes" and that T2 "ATTEMPTS" the ruleset — written before the ruleset landed, and now stale in a Tier-3 file. T7 did not name that file so it could not be fixed here | 2026-09-02 | low | a chunk that names `ci.yml` corrects the header |
| **F31** | **Six Dependabot PRs are already open and none can go green** — #14, #15, #16 (security) and #19 (grouped minor/patch), #20, #21 (majors). They are blocked by F23 and unblocked by F24. Someone must decide whether to close them, hold them, or merge them one at a time after a human re-run | 2026-09-02 | med | F24 is answered and the six are dispositioned |
| **F32** | `docs/DECISIONS.md` now carries **two conventions for the same relationship**: older superseded entries (W035, W053, W057, W061, W071) were given a "SUPERSEDED by W###" status tag, while W072, W073, W084 and W089 still read as plain LOCKED with no forward pointer — so a reader trusting the tag takes W084's "CI is advisory" as current. Adding tags would mean editing entries in an append-only file, which is why it was not done here | 2026-09-02 | low | the file states which convention wins, or a chunk is authorized to add forward pointers |

---

## 4. OPEN DECISIONS (owner's)

| Decision | Blocks | Default if unanswered |
|---|---|---|
| **Mirror the three Sanity values as *Dependabot* secrets?** (F24) | six open Dependabot PRs going green | leave them red; a human re-runs the ones worth merging |
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
