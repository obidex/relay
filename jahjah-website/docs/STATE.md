# STATE.md — Where the Project Is Right Now

> **THE ONLY VOLATILE FILE IN THE CANON.** Rewritten at every chunk end by Claude Code. Nothing here is a rule.

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0 canon reset → P0.1 cowork lane → P0.2 workflow v2 → **P1 LAUNCH BLOCKERS** ★ this chunk → **P1.1 finish P1** → P2 identity + foundation → P3 Admin Mode → P4 customer accounts → P5 public UX/content (parallel) → **L launch** → P6 |
| **Next step** | **P1.1** — the four things P1 could not do (ROADMAP F33), then P2. Architecture is locked (W074–W082); no decision blocks P1.1. It arrives as a GitHub issue labelled `chunk:proposed`; the owner's `chunk:approved` label starts it (W099). |
| **`master` HEAD at the last canon update** | `a0f8bff` — "feat(seo): page-specific meta descriptions for About, Brands and Contact (#33)". This line is written by the chunk-close PR, the first commit that cannot know its own squash hash — so `master` is normally **one commit ahead of this line**, that PR itself. A larger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app` · 22 products · 5 brands · 6 categories · no prices, no login |
| **Content** | Placeholder catalogue (AI-generated names/copy, 126/192 images placeholder). Deleted when real data enters — never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP) — separate canon, **not connected** (W075). |

### CI shape (`.github/workflows/ci.yml`) and the merge gate

One job, **named `ci`**, on every PR and on `master`, Node 22: `tier3-guard` → `npm ci` → `npm run build` → page-count assert (`EXPECTED_PAGES` = 67 content pages — verify.sh excludes `/admin`, Astro reports 68; update it in the same PR that adds routes) → compiled-output checks → `npm run reference` + `git diff --exit-code docs/reference/` → gitleaks. Every action is pinned to a **commit SHA** with its tag in a trailing comment.

**`ci` IS A HARD MERGE GATE from 2026-09-02T16:20:50Z.** Ruleset `master-protection`, id **22124934**: pull request required, squash only, required check `ci` (strict), no deletion, no non-fast-forward, **no bypass actors** — the admin who created it cannot bypass it either (W100). A direct push is refused by GitHub with `GH013`. W084's "merge-on-green is discipline" is history.

`tier3-guard` fails any PR that touches a Tier-3 path without a body line `Tier-3: authorized by chunk <name>` (W101). `on.pull_request.types` includes `edited`, so adding the line re-runs it.

A second job, **`review`** (`.github/workflows/claude-review.yml`), no longer runs on pull requests at all: since chunk P1 it is `workflow_dispatch`-only, a **manual fallback** taking a PR number. The reviewer of record is **Codex** — see live flag 10.

`verify.sh` now also walks `dist/404.html` explicitly (four assertions, W111), audits heading hierarchy through `scripts/heading-audit.mjs` (0 skips across 67 pages), and asserts through `scripts/hidden-products-check.mjs` that no hidden product reached the build (W110). **That last one exits 0 with a `SKIP` whenever it cannot reach Sanity** — no token, no network, 403 — surfaced as a `WARN`; CI has the secret so it runs there, but the guard that matters most is the one that can silently not run (ROADMAP F37).

### Automations touching this project (registry: `jahjah-internal/docs/runbooks/automations.md`)

| Unit | When | Output |
|---|---|---|
| `jahjah-web-truth` | Mon 05:30 UTC | `relay/jahjah-website/reports/TRUTH-weekly.md` — clean build, compiled greps, live probes |
| `jahjah-web-docs` | every 30 min | `relay/jahjah-website/docs/` — mirror of the canon allowlist (set up in P0) |
| `jahjah-web-backup` | 02:30 UTC nightly | `/root/backups/web/` — Sanity export (+ DB dump from P2), keep 7, VPS-only (set up in P0) |
| `jahjah-web-dispatch` | every 2 min | starts an approved chunk issue on the executor; heartbeat `HEARTBEAT-web-dispatch.md`. Kill: `touch /opt/jahjah/WEB_DISPATCH_OFF` (P0.2) |
| `jahjah-web-backup-check` | Mon 03:30 UTC | unpacks the newest backup, compares counts with live Sanity and checks every referenced image is present; verdict on `HEALTH-daily.md` (P0.2) |

---

## 2. LIVE FLAGS — things that surprise a newcomer

1. **TWO PATHS TO PRODUCTION.** `git push` to `master` AND a Sanity publish (webhook → Vercel deploy hook, ~2 min). The site changes with no commit; only TRUTH sees it.
2. **CONTENT IS PLACEHOLDER, AND THE WATERMARK QUESTION IS HALF ANSWERED.** 22 products with AI-generated names; 1 product has real photos. Owner: don't upload photos to placeholder items. The friend's audit (2026-09-02) reported a **stock-photo watermark** on the DCEL front-load washer images. **The grep half is clean** — 3 distinct Sanity CDN assets on that product, no watermark markers, checked in P1 and re-checked by TRUTH weekly. **The visual half is still open and is the owner's**: someone has to look at three photographs (ROADMAP F1).
3. ~~`/admin/` is in the sitemap~~ — **FIXED in P1** (#30). The sitemap is 66 URLs, `/admin` is not among them, and the Studio surface carries `noindex, nofollow`. `verify.sh` asserts both, independently of each other, so removing one cannot mask the other.
4. ~~`/images/placeholder.jpg` returns 404~~ — **FIXED in P1** (#26, W112). **There are no asset 404s.** A missing image is `null` and renders an inline no-image tile that emits no `<img>` at all, so the browser makes no request that can fail; 0 placeholder references remain in the build, and `verify.sh` counts them every run.
5. **LAUNCH-FACT GATE OPEN (W088).** Site says founded 2010; other company materials say 2009. Damascus number has a Turkish prefix (`+90…`, correct — phone is physically in Turkey). One person rules before launch.
6. **VERCEL HOBBY.** Non-commercial. Pro is required before the first on-demand route ships (W090), i.e. P2 — not "at launch".
7. **NO WEB DB YET.** Supabase project #2 is created in P2. When it exists: free tier pauses after ~1 week idle (W091) — the nightly backup keeps it awake.
8. **CANON MOVED, AND THE PROJECT IS A SYNC NOT A PASTE (W098).** The claude.ai project knowledge is a GitHub sync of `docs/` + `CLAUDE.md`. It is not stale by definition — it **lags**. Use it for orientation; the mirror and the connector win. And a fresh `INDEX.md` does not mean a fresh sibling (W102): `raw.githubusercontent.com` will serve a current index beside a stale body, cache-buster on both.
9. **THE ISSUE LANE HAS NOW CARRIED ONE REAL CHUNK, AND IT DID NOT RUN CLEAN.** P1 (issue #24) is the first real chunk to start from the owner's `chunk:approved` label. What the lane got right: it picked the issue up, relabelled, started the executor with `CHUNK_ISSUE` set, and every report reached both the issue and the relay. What it cost: the **dispatched run exited 1 at 5307 s**, because 9 of 10 commands it needed were refused by an allowlist nobody had exercised — two of them named in `CLAUDE.md` §5's own preflight. That gap was found by the chunk and **repaired by an interactive session** in #29, since a dispatched session cannot edit `.claude/**` at all (W116). The chunk then finished across two interactive resumes, **both of which ended silently** (W117, ROADMAP F39). Treat the lane as *exercised and mended*, not as proven. **The relay stays dual-published** until the strategist confirms it has read a whole chunk from the issue alone (ROADMAP F26).
10. **THE REVIEWER OF RECORD IS CODEX, THE CLAUDE `review` JOB IS MANUAL-ONLY, AND CODEX SPEAKS ON THREE DIFFERENT SURFACES.** Codex (the ChatGPT GitHub app, on the owner's ChatGPT plan) posts as `chatgpt-codex-connector`, reading `AGENTS.md`. **Across P1 it reviewed 4 of the 10 pull requests this chunk opened** — #26 (one P2), #27 (one P1), #28 (two P1), #31 (two P2) — and was silent on #25, #29, #30, #32, #33 and #34. It **answered an explicit `@codex review` on #31 in 4m23s**. **It has labelled findings P1/P2 since #22 and #23 on 2026-09-02**, so that is observed behaviour, not a convention we request. **READ ALL THREE SURFACES OR YOU WILL MISREAD SILENCE:** `gh pr view <n> --json reviews` (the review), `gh api …/pulls/<n>/comments` (the inline findings, which the review body does not list), and `gh api …/issues/<n>/comments` — **a re-review with no findings arrives as an issue comment.** This session initially recorded #31 as silent after its `@codex review` because it read only the first two. The Claude `review` workflow is `workflow_dispatch`-only (F25, F28 closed by that). **Neither is a required check**: silence is never approval, and per W105 Codex is somebody else's system — a dated assumption, not a property of ours (W113).
11. **THE EXECUTOR WORKSPACE IS TRUSTED** (this is the state fact `docs/STRATEGIST.md` §8 points here for): `/opt/jahjah/web` was trusted on 2026-09-02, so `.claude/settings.json`'s allow list is in force, not just its deny half.
12. **A DISPATCHED SESSION AND AN INTERACTIVE ONE ARE NOT THE SAME EXECUTOR (W116).** A dispatched (`claude -p`) session **cannot edit `.claude/**` at all** — the harness refuses it whatever the settings say — and runs only what the allowlist names. So an allowlist change is *interactive-session work*, a plan may not pre-authorize the executor to make one, and any new command a chunk depends on must be **dry-run first**. `CLAUDE.md` §9 carries the five measured traps, including that a rule ending at a `VAR=` assignment is a universal bypass of the whole deny list.
13. **THE REVIEWER SUBAGENT'S `tools:` FRONTMATTER RESTRICTS IT — BUT NOT TO WHAT IT LISTS (F11, closed 2026-09-04).** Four passes invoked by name from `/opt/jahjah/web` each reported holding exactly **`Read` and `Bash`**: no `Write`, no `Edit`, **no MCP tools** despite MCP instructions arriving in their context — and no `Grep` or `Glob`, which the frontmatter *does* list. The per-command `Bash(git diff:*)` scoping is **not** enforced. **A `tools:` list containing `Bash` is not a read-only guarantee**: Bash grants `node -e`, `python3` and in-repo writes.

---

## 3. SESSION / CHUNK LEDGER (last 10)

| Date | Session | Model | Result |
|---|---|---|---|
| 2026-09-03/04 | **P1 · Launch blockers** — the FIRST chunk to start from a labelled issue (**#24**). Watermark grep, no-image tile, hidden-product guard, allowlist repair, `/admin` out of the sitemap, the 404 page's head, accessibility, meta descriptions, canon. | Claude Code (Opus 5, xhigh) | **PRs #25 `c1cd217`, #26 `09bc943`, #28 `6e28bc3`, #29 `6fbe570`, #30 `cc1d89b`, #31 `3c30b37`, #32 `7afc84a`, #33 `a0f8bff`** merged (+ this chunk-close PR). **#34 opened and LEFT OPEN on purpose** — it carries three unreviewed Arabic strings, so GATE 2 excludes it. **The run did not go smoothly and the ledger should say so:** the dispatched run **exited 1 at 5307 s** on an allowlist nobody had exercised, and the chunk finished across **two interactive resumes, both of which ended silently** — the first having pushed T5, gone green and taken the Codex review, but never merging #31 or reporting (W117, F39); the second paused ~5.5 h mid-task, resuming under W058 with a from-scratch rebuild before merging. **Five executor reviewer passes in this session** (T5 fix, T6, T6 re-review, T7, T6b) returning 4, 5, 2, 7 and 4 FIXes — every one applied or answered — plus this chunk-close pass. Four of them changed the shipped result: an accessibility *regression* in T5 (the document root was being flipped to Arabic under English chrome), a flex/`text-align` bug in T6, a false verification committed into source in T7, and an `innerHTML` **injection** that T6b's own localization created. **Codex reviewed 4 of the 10 PRs** — and #28's two P1 findings were never seen, because it was merged 88 s after opening and the review landed 108 s later (ROADMAP F40). |
| 2026-09-02 | **P0.2 · Workflow v2** — GATE 2 machine-enforced (ruleset `master-protection`, no bypass actors); CI job renamed `ci`, every action SHA-pinned, new `tier3-guard`; chunk labels + `/relay-report` to the issue; `jahjah-web-dispatch` (chunk issues → executor) and `jahjah-web-backup-check` (backup integrity) built in the ERP repo; independent `review` job; Dependabot. Chunk issue **#6**. | Claude Code (Opus 5, xhigh) | **PRs #4 `58912bd`, #10 `44bef69`, #13 `b840c3a`, #18 `7ce7932`, #17 `7821043`** merged (+ this chunk-close PR); #5 opened and closed unmerged as the ruleset proof. Cross-repo: `jahjah-internal` **#91 `2c66949`** and **#92 `2f2278b`** merged. Six reviewer passes on the relay-report skill, four on the review workflow, three compliance-panel passes on the lane; one BLOCK and eleven FIXes, all closed. |
| 2026-09-02 | **P0.1 · Cowork lane into canon** — the strategist's own lane written down as `docs/STRATEGIST.md` §8; one row added to STATE §4. No site change. | strategist (Fable) + Claude Code (**Sonnet 5**, medium) | **PR #3** `e33b478` merged. Three reviewer passes; merged on a NOTE-only verdict, flagged at the time. Opened F16–F22, all closed in P0.2. |
| 2026-09-02 | **P0 · Canon reset** — five off-disk docs → repo canon (STRATEGIST/STATE/ROADMAP/DECISIONS W001–W091/reference generator/archive), `.claude/` layer (deny rules, reviewer, `/verify` `/ship` `/relay-report`), CI, VPS executor `web`, docs mirror + backup units. Loss-prevention inventory: 0 binding rules lost. | strategist (Fable) + Claude Code (Opus 5) | **PR #1** `939653a` merged (+ this chunk-close PR). Cross-repo: `jahjah-internal` **PR #85** `743b17e` merged — `jahjah-web-docs`, `jahjah-web-backup`, and a backup-freshness heartbeat in `health.sh`. Three reviewer passes on #1, one infra pass on #85; one BLOCK each, both closed. |
| 2026-08-31 | Weekly read-only audit `jahjah-web-truth` installed on the VPS (ERP platform W1). First report 2026-09-01. | — | ERP commit `8d980d3` |
| 2026-06-11 | Strategic reset: three pillars, ShamCash, docs remake (no code) | strategist | — |
| 2026-05-18 | How to Buy page EN+AR | Claude Code (laptop) | `9a5c1d7` |
| 2026-05-17 | Phase B1 brand detail pages (5 commits) | Claude Code (laptop) | `27467c1` … `216160e` |
| 2026-05-15/16 | Variants (schema, palette, swatches, dots) | Claude Code (laptop) | `a62ae10`, `93a391f` |
| 2026-05-15 | Phase A closed (og:image, breadcrumbs, 404, SEO overrides) | Claude Code (laptop) | `b9df92f` … `7d29c10` |
| 2026-05-12/14 | Hamburger menu, SEO foundation, Product JSON-LD | Claude Code (laptop) | `2649416`, `310e7b6`, `93df5bb` |
| 2026-05-09 | Deployed to Vercel; three production bugs fixed (W012–W014) | — | — |
| ~2026-04 | Migrated to Sanity | — | — |

Commits between `9a5c1d7` and `9bdcb40` (brand infrastructure, bilingual content pages, schema enhancements per TRUTH 2026-09-01) were made outside the strategist loop; their content is in the generated reference, not here.

---

## 4. EPHEMERAL FACTS

### Endpoints and places

| What | Where |
|---|---|
| Live site / Studio | `https://jahjah-website.vercel.app` · `/admin` |
| Future domain | `jahjah.net` (owned, NOT connected — launch bundle, W027) |
| Repo | `github.com/obidex/jahjah-website` (private), default branch **`master`** |
| Vercel project | `jahjah-website` (Hobby); verify deploys via Vercel MCP `list_deployments` |
| Sanity | project `pxf1amia`, dataset `production`, Studio embedded |
| VPS executor | box `germany-vpn` · tmux session **`web`** (`tmux attach -t web`) · clone at `/opt/jahjah/web` · Node 22 (set in P0). **Address and login are not recorded here — this file is mirrored to the public relay** (`jj_redact` scrubs the box IP from everything the fleet publishes; canon must not reintroduce it). |
| Relay | `raw.githubusercontent.com/obidex/relay/main/jahjah-website/{docs,reports}/` |
| Project knowledge | GitHub sync of `docs/` + `CLAUDE.md` (snapshot; mirror wins) |
| Old laptop clone | the Windows clone — optional after P0, never the executor. Path not recorded here (public mirror). |

### Secrets — names only, never values

`PUBLIC_SANITY_PROJECT_ID` · `PUBLIC_SANITY_DATASET` · `SANITY_READ_TOKEN` (Vercel, VPS env file, GitHub Actions secret) · `SANITY_WRITE_TOKEN` (server env only, from P3; W079) · Vercel deploy-hook URL (inside Sanity webhooks; treat as a credential) · from P2: `SUPABASE_URL`, `SUPABASE_ANON_KEY` (browser-safe), `SUPABASE_SERVICE_ROLE_KEY` (server-only, radioactive).

### Plans and bill safety

Vercel Hobby (→ Pro at P2, ~$20/mo) · Sanity Free (3 users, 10k docs, 100k API req/mo — employees use Admin Mode, not seats) · **GitHub Pro** (~$4/mo — active as of 2026-09-02; rulesets on a private repo are what it buys, and creating `master-protection` is what proved it, since `gh api /user` omits `plan` without the `user` scope) · Supabase Free from P2 (2 projects/org: ERP is #1). Claude Max for Claude Code; Fable on owner-bought credits for architecture sessions only.

### TRUTH 2026-09-01 findings still open

Build 68 pages / 37 s / exit 0; 2 build warnings (chunk size; deprecated `@sanity/image-url` import pattern) · 0 `loading="lazy"`, 0 `srcset` · **two** dead RTL rules — `ProductDetail.*.css` `.product-name` and `Layout.*.css` `.footer-heading` (`[data-astro-cid-…][dir=rtl]` cannot match `<html>`; counted by `npm run verify`) · listing/static pages have no JSON-LD. **The probe figure that stood here — "18/19 probes 200" — is dropped rather than carried:** #26, #30 and #31 changed exactly what a probe list hits, so a September-1 count says nothing about today. P1's own reports carry a current per-path table, and the next TRUTH run replaces this block.

### Owner-side open items (no code)

**ANSWERED 2026-09-04 — Dependabot secrets: NOT MIRRORED (ROADMAP F24 closed).** A live Sanity read
token, which can see drafts, is never handed to a run executing a dependency version nobody has
reviewed. The consequence is that Dependabot PRs stay red, and **W114 makes that the intended state**
rather than a problem: the bot is a notifier, the executor applies each update in a chunk PR with
secrets present, then closes the bot's. **Six are still open and three are *labelled* security updates** (#14
undici, #15 browserslist, #16 dompurify; #19 grouped minor/patch, #20 and #21 majors) — but
`gh api /repos/obidex/jahjah-website/dependabot/alerts` is the fuller picture and it is wider than
those three PRs: open advisories on browserslist, nanoid, js-yaml, brace-expansion and undici at
**high**, plus dompurify and postcss at medium. P1 was
forbidden `npm update` and `gh pr close`, so **P1.1 does the four minor/patch ones** — #14, #15,
#16 and #19 (F31). **#20 and #21 are NOT P1.1's**: they are major bumps of the embedded Studio and
get their own named chunk (F34), per W114.

**Two of these now gate P1's own exit, so they are no longer background:**
- **The watermark verdict** (F1). The grep half is clean; someone has to look at three photographs.
- **The launch-fact ruling** (W088) — and it has become self-inconsistent while unanswered: P1
  removed "authorized distributor" from **every meta description** in both languages, but the same
  agency claim still stands in the home page's visible body copy (`home.featureDealer`, "Authorized
  dealer" / "وكيل معتمد") and in the Organization JSON-LD that feeds knowledge panels. **The site
  currently says two different things about itself.** Changing the remaining two needs a new Arabic
  string, so they move together, after the ruling.

**New:** decide **which of the 22 placeholder products to keep** (F35) — W031 wants 8–10 real
flagships, W007 says placeholder records are deleted rather than polished. The machinery to hide
them exists and is proven; nobody has said which.

The older items, unchanged: name the three price tiers (default Tier 1/2/3) · decide currency (W066)
before flipping `prices_visible` · obtain ShamCash merchant docs (W064, not urgent) · decide whether
the showroom address is published (ROADMAP §4).

---

## 5. NEXT PLANNED STEP

**P1.1 · finish P1** — four things, all of them carried out of P1 rather than discovered in it, and
all of them reachable-but-forbidden there (ROADMAP **F33**):

1. **T0d — the FOUR minor/patch Dependabot PRs**: #14, #15, #16 (**security**) and #19. Apply each
   update in a chunk PR with secrets present, then close the bot's PR naming it (W114). P1's own
   plan forbade `npm update` and `gh pr close`. **#20 and #21 are explicitly NOT in P1.1** — they
   are major bumps of the embedded Studio and get their own named chunk (F34).
2. **Close #27**, the stale duplicate of #28. P1 was told not to touch it.
3. **The T1 visual verdict** from the owner (F1) — the grep half is already clean.
4. **The live probe table**, now unblocked: the allowlist repair in #29 made per-path `curl` work,
   and P1's later reports carry a real table.

Then **P2 · identity and foundation**. No decision blocks P1.1.

**Also open, and each has a row:** PR **#34** is deliberately unmerged, holding three unreviewed
Arabic strings for the native reviewer — it is the last thing standing between P1.6 and its own
acceptance line. F35 (which products to keep), F36 (the `.vercel.app` host is indexable and will
still be at launch), F37 (`verify.sh`'s hidden-product guard can silently SKIP), F38 (the dispatcher
leaves `chunk:proposed` in place — ERP-side), F39 (two sessions of this chunk ended silently and
nothing caught it).

**Closed in P1:** F9 (the 404 page — and the register's own explanation of it was wrong, corrected
in place) · F11 (the reviewer subagent's `tools:` frontmatter **does** restrict, but not to what it
lists) · F23 and F24 (Dependabot policy and the owner's secrets ruling) · F25 and F28 (the Claude
`review` job, closed by narrowing its trigger to nothing rather than by explaining its silence) ·
F29 and F30 (the reference generator's blind spot, and `ci.yml`'s stale header).

**Carried:** F1 (visual half), F5 (two dead RTL rules, P5), F31 (four Dependabot PRs → P1.1, two majors → F34),
F26 (retiring the relay — the lane has now carried one real chunk, so this is the strategist's call
to make next).
