# STATE.md — Where the Project Is Right Now

> **THE ONLY VOLATILE FILE IN THE CANON.** Rewritten at every chunk end by Claude Code. Nothing here is a rule.

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0 canon reset → P0.1 cowork lane → **P0.2 · WORKFLOW V2** ★ this chunk → **P1 launch blockers** → P2 identity + foundation → P3 Admin Mode → P4 customer accounts → P5 public UX/content (parallel) → **L launch** → P6 |
| **Next step** | P1 — launch blockers (ROADMAP §2). Architecture is locked (W074–W082); no decision is pending. **It arrives as a GitHub issue** the strategist opens, labelled `chunk:proposed`; the owner's `chunk:approved` label starts it (W099). |
| **`master` HEAD at the last canon update** | `7821043` — "ci: Dependabot, weekly, grouped minor+patch (#17)". This line is written by the chunk-close PR, the first commit that cannot know its own squash hash — so `master` is normally **one commit ahead of this line**, that PR itself. A larger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app` · 22 products · 5 brands · 6 categories · no prices, no login |
| **Content** | Placeholder catalogue (AI-generated names/copy, 126/192 images placeholder). Deleted when real data enters — never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP) — separate canon, **not connected** (W075). |

### CI shape (`.github/workflows/ci.yml`) and the merge gate

One job, **named `ci`**, on every PR and on `master`, Node 22: `tier3-guard` → `npm ci` → `npm run build` → page-count assert (`EXPECTED_PAGES` = 67 content pages — verify.sh excludes `/admin`, Astro reports 68; update it in the same PR that adds routes) → compiled-output checks → `npm run reference` + `git diff --exit-code docs/reference/` → gitleaks. Every action is pinned to a **commit SHA** with its tag in a trailing comment.

**`ci` IS A HARD MERGE GATE from 2026-09-02T16:20:50Z.** Ruleset `master-protection`, id **22124934**: pull request required, squash only, required check `ci` (strict), no deletion, no non-fast-forward, **no bypass actors** — the admin who created it cannot bypass it either (W100). A direct push is refused by GitHub with `GH013`. W084's "merge-on-green is discipline" is history.

`tier3-guard` fails any PR that touches a Tier-3 path without a body line `Tier-3: authorized by chunk <name>` (W101). `on.pull_request.types` includes `edited`, so adding the line re-runs it.

A second job, **`review`** (`.github/workflows/claude-review.yml`) runs an independent Claude review, judged by `REVIEW.md`, on every PR **except Dependabot's** — the workflow carries `if: github.actor != 'dependabot[bot]'`, so on a bot PR the check reports `skipping`. It is **not** a required check — see live flag 10.

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
2. **CONTENT IS PLACEHOLDER.** 22 products with AI-generated names; 1 product has real photos. Owner: don't upload photos to placeholder items; the friend's audit (2026-09-02) reports a **stock-photo watermark** on the DCEL front-load washer images — unverified by us; TRUTH greps for it from the reset on. First P1 item either way.
3. **`/admin/` IS IN THE SITEMAP** (1 of the sitemap's 67 URLs — counted in `dist/sitemap-0.xml`) while `robots.txt` disallows it. P1 removes it and adds `noindex`.
4. **`/images/placeholder.jpg` RETURNS 404** on the live site; 21 cards reference it.
5. **LAUNCH-FACT GATE OPEN (W088).** Site says founded 2010; other company materials say 2009. Damascus number has a Turkish prefix (`+90…`, correct — phone is physically in Turkey). One person rules before launch.
6. **VERCEL HOBBY.** Non-commercial. Pro is required before the first on-demand route ships (W090), i.e. P2 — not "at launch".
7. **NO WEB DB YET.** Supabase project #2 is created in P2. When it exists: free tier pauses after ~1 week idle (W091) — the nightly backup keeps it awake.
8. **CANON MOVED, AND THE PROJECT IS A SYNC NOT A PASTE (W098).** The claude.ai project knowledge is a GitHub sync of `docs/` + `CLAUDE.md`. It is not stale by definition — it **lags**. Use it for orientation; the mirror and the connector win. And a fresh `INDEX.md` does not mean a fresh sibling (W102): `raw.githubusercontent.com` will serve a current index beside a stale body, cache-buster on both.
9. **A CHUNK STARTS FROM A LABELLED ISSUE (W099) — BUT NO REAL CHUNK HAS DONE SO YET.** The strategist opens it, the owner adds `chunk:approved`, `jahjah-web-dispatch` runs it within 2 minutes. That path is proven only by **two smoke tests** (issues #11 and #12); P0.2's own issue #6 was opened by the executor after the fact and deliberately never dispatched. **P1 is the first real test of the loop.** Until it has run, treat the lane as working-but-unexercised. **The relay is dual-published** — every report goes to the issue *and* the relay — and is retired only after that first real chunk (ROADMAP F26).
10. **THE `review` JOB IS GREEN AND SILENT SO FAR.** The independent review job is installed and no longer errors, but no run has yet posted a finding or a "no issues" summary. It is deliberately not a required check, so it cannot block a merge either way — but do not read a green `review` as approval until one has been seen to produce output. Register row open.
11. **THE EXECUTOR WORKSPACE IS TRUSTED** (this is the state fact `docs/STRATEGIST.md` §8 points here for): `/opt/jahjah/web` was trusted on 2026-09-02, so `.claude/settings.json`'s allow list is in force, not just its deny half.

---

## 3. SESSION / CHUNK LEDGER (last 10)

| Date | Session | Model | Result |
|---|---|---|---|
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

Build 68 pages / 37 s / exit 0; 2 build warnings (chunk size; deprecated `@sanity/image-url` import pattern) · 0 `loading="lazy"`, 0 `srcset` · **two** dead RTL rules — `ProductDetail.*.css` `.product-name` and `Layout.*.css` `.footer-heading` (`[data-astro-cid-…][dir=rtl]` cannot match `<html>`; counted by `npm run verify`) · listing/static pages have no JSON-LD · 18/19 probes 200.

### Owner-side open items (no code)

**Decide whether to mirror the three Sanity values as *Dependabot* secrets (ROADMAP F24).** Six
Dependabot PRs are open right now and **none of them can go green** as things stand — and **three of
the six are security updates** (#14 undici, #15 browserslist, #16 dompurify; #19 is the grouped
minor/patch, #20 and #21 are majors). So the default of leaving them red is not free: it means
declining security fixes for as long as it stands. Mirroring the secrets lets them build; it also
hands a live Sanity read token — which can see drafts — to a run executing a dependency version
nobody has reviewed. The ERP repo's canon (D228) rejects the equivalent outright, but its escape hatch
does not exist here, because our `ci` is one job whose secret-dependent steps *are* the gate. **Nothing
else in the project waits on this, but the security half means it should not sit indefinitely.**

The five older owner-side items, unchanged: Rule on the launch facts (W088) · name the three price tiers (default Tier 1/2/3) · decide currency (W066) before flipping `prices_visible` · obtain ShamCash merchant docs (W064, not urgent) · decide whether the showroom address is published (ROADMAP §4).

---

## 5. NEXT PLANNED STEP

**P1 · launch blockers** — the first chunk of the new loop. The strategist opens it as a **GitHub issue
labelled `chunk:proposed`**; the owner adds `chunk:approved` and it starts within two minutes (W099).
No open decision blocks it. Inputs: this file, ROADMAP §2 P1, `docs/reference/site.md`, TRUTH-weekly.

**Carried:** F1, the watermark claim on the DCEL washer images — still open; the first TRUTH run under
the new canon is Mon 05:30 UTC.

**Closed in P0.2:** F6 (GitHub Pro / branch protection — the ruleset is live, and its creation is what
proved Pro is active) · F10 (CI hardening — all three actions pinned to commit SHAs, gitleaks moved
off the Node-20 `@v2` two weeks before GitHub removes that runtime) · F12 (the four unextractable
facts now restated in `CLAUDE.md` §3) · **F13 (the executor workspace is trusted — live flag 11)** ·
F16, F17, F18, F19, F20, F21, F22 (the P0.1 documentation follow-ups; F18 by W098 and W099, which
supersede rather than edit, since DECISIONS is append-only).

**F11 is NOT closed, and here is exactly why.** The question is whether `.claude/agents/reviewer.md`'s
`tools:` frontmatter actually restricts the agent. Evidence gathered this chunk points to **no**: a
review subagent reported executing `cat`, `sed`, `python3`, `find`, `WebFetch` and `WebSearch`, none of
them in that list, without restriction. But it was invoked from a session whose working directory is
`/root`, not `/opt/jahjah/web` — so the project agent was never registered in the first place, and
every reviewer pass this chunk ran as a general-purpose agent handed `reviewer.md` as its contract.
That narrows the question rather than answering it. **The row stays open until a session started IN
`/opt/jahjah/web` invokes `reviewer` by name and its tool set is observed** — which is now easy,
because a dispatched chunk runs from exactly that directory.

**Opened in P0.2:** ROADMAP **F23–F32** — the three reasons a Dependabot PR is red (F23), **the
owner's decision on Dependabot secrets (F24, also in §4 above)**, the `review` job's silence (F25),
the relay retirement (F26), the P3 import drill (F27), the Actions-minutes watch (F28), the generator
not seeing `.github/dependabot.yml` (F29), `ci.yml`'s stale header (F30), the six Dependabot PRs
already open and unmergeable (F31), and DECISIONS' two conventions for supersession (F32).
