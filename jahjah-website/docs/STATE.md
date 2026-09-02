# STATE.md — Where the Project Is Right Now

> **THE ONLY VOLATILE FILE IN THE CANON.** Rewritten at every chunk end by Claude Code. Nothing here is a rule.

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | **P0 · CANON RESET** ★ this chunk → **P1 launch blockers** → P2 identity + foundation → P3 Admin Mode → P4 customer accounts → P5 public UX/content (parallel) → **L launch** → P6 |
| **Next step** | P1 — website strategist's first chunk (see ROADMAP §2). Architecture is locked (W074–W082); no decision is pending for P1. |
| **`master` HEAD at the last canon update** | `939653a` — "docs: canon reset (P0) (#1)". Written by the chunk-close PR, the first commit that could know the squash hash — so `master` is normally **one commit ahead of this line**, that PR itself. A larger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app` · 22 products · 5 brands · 6 categories · no prices, no login |
| **Content** | Placeholder catalogue (AI-generated names/copy, 126/192 images placeholder). Deleted when real data enters — never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP) — separate canon, **not connected** (W075). |

### CI shape (`.github/workflows/ci.yml`)

One job on every PR and on `master`, Node 22: `npm ci` → `npm run build` → page-count assert (`EXPECTED_PAGES` = 67 content pages — verify.sh excludes `/admin`, Astro reports 68; update it in the same PR that adds routes) → compiled-output checks (`/verify` greps) → `npm run reference` + `git diff --exit-code docs/reference/` → gitleaks. **No job is a hard merge gate** (branch protection is Pro-gated on a private repo). Merge-on-green is discipline (W084).

### Automations touching this project (registry: `jahjah-internal/docs/runbooks/automations.md`)

| Unit | When | Output |
|---|---|---|
| `jahjah-web-truth` | Mon 05:30 UTC | `relay/jahjah-website/reports/TRUTH-weekly.md` — clean build, compiled greps, live probes |
| `jahjah-web-docs` | every 30 min | `relay/jahjah-website/docs/` — mirror of the canon allowlist (set up in P0) |
| `jahjah-web-backup` | 02:30 UTC nightly | `/root/backups/web/` — Sanity export (+ DB dump from P2), keep 7, VPS-only (set up in P0) |

---

## 2. LIVE FLAGS — things that surprise a newcomer

1. **TWO PATHS TO PRODUCTION.** `git push` to `master` AND a Sanity publish (webhook → Vercel deploy hook, ~2 min). The site changes with no commit; only TRUTH sees it.
2. **CONTENT IS PLACEHOLDER.** 22 products with AI-generated names; 1 product has real photos. Owner: don't upload photos to placeholder items; the friend's audit (2026-09-02) reports a **stock-photo watermark** on the DCEL front-load washer images — unverified by us; TRUTH greps for it from the reset on. First P1 item either way.
3. **`/admin/` IS IN THE SITEMAP** (1 of the sitemap's 67 URLs — counted in `dist/sitemap-0.xml`) while `robots.txt` disallows it. P1 removes it and adds `noindex`.
4. **`/images/placeholder.jpg` RETURNS 404** on the live site; 21 cards reference it.
5. **LAUNCH-FACT GATE OPEN (W088).** Site says founded 2010; other company materials say 2009. Damascus number has a Turkish prefix (`+90…`, correct — phone is physically in Turkey). One person rules before launch.
6. **VERCEL HOBBY.** Non-commercial. Pro is required before the first on-demand route ships (W090), i.e. P2 — not "at launch".
7. **NO WEB DB YET.** Supabase project #2 is created in P2. When it exists: free tier pauses after ~1 week idle (W091) — the nightly backup keeps it awake.
8. **CANON MOVED.** Anything in a claude.ai project other than the single pointer file is stale by definition. Read the mirror.

---

## 3. SESSION / CHUNK LEDGER (last 10)

| Date | Session | Model | Result |
|---|---|---|---|
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
| Old laptop clone | the Windows clone — optional after P0, never the executor. Path not recorded here (public mirror). |

### Secrets — names only, never values

`PUBLIC_SANITY_PROJECT_ID` · `PUBLIC_SANITY_DATASET` · `SANITY_READ_TOKEN` (Vercel, VPS env file, GitHub Actions secret) · `SANITY_WRITE_TOKEN` (server env only, from P3; W079) · Vercel deploy-hook URL (inside Sanity webhooks; treat as a credential) · from P2: `SUPABASE_URL`, `SUPABASE_ANON_KEY` (browser-safe), `SUPABASE_SERVICE_ROLE_KEY` (server-only, radioactive).

### Plans and bill safety

Vercel Hobby (→ Pro at P2, ~$20/mo) · Sanity Free (3 users, 10k docs, 100k API req/mo — employees use Admin Mode, not seats) · GitHub Free (no branch protection) · Supabase Free from P2 (2 projects/org: ERP is #1). Claude Max for Claude Code; Fable on owner-bought credits for architecture sessions only.

### TRUTH 2026-09-01 findings still open

Build 68 pages / 37 s / exit 0; 2 build warnings (chunk size; deprecated `@sanity/image-url` import pattern) · 0 `loading="lazy"`, 0 `srcset` · **two** dead RTL rules — `ProductDetail.*.css` `.product-name` and `Layout.*.css` `.footer-heading` (`[data-astro-cid-…][dir=rtl]` cannot match `<html>`; counted by `npm run verify`) · listing/static pages have no JSON-LD · 18/19 probes 200.

### Owner-side open items (no code)

Rule on the launch facts (W088) · name the three price tiers (default Tier 1/2/3) · decide currency (W066) before flipping `prices_visible` · obtain ShamCash merchant docs (W064, not urgent) · decide whether the showroom address is published (ROADMAP §4).

---

## 5. NEXT PLANNED STEP

**P1 · launch blockers** — first chunk of the website strategist. No open decision blocks it. Inputs: this file, ROADMAP §2 P1, `docs/reference/site.md`, TRUTH-weekly.

**Carried from P0:** verify the watermark claim (TRUTH grep, **F1 — still open**, first TRUTH run under the new canon is Mon 05:30 UTC).

**Closed in P0:** `gh` push scope confirmed (F8 — preflight passed, PR #1 pushed and merged from the VPS) · the two units registered and merged in the ERP runbook (F2 — `jahjah-internal` PR #85) · Node-22 pin in CI matches `package.json` engines (`node-version: 22` vs `>=22.12.0`, and the box runs 22.23.2).

**Opened in P0:** F9 `/ar/404/` · F10 CI hardening · F11 the reviewer agent's `tools:` field · F12 four facts now in no canon file · **F13 the executor workspace is not trusted, so `.claude/settings.json`'s allow list is ignored** (one interactive `claude` session in `/opt/jahjah/web` clears it; the deny half is in force meanwhile) · F14 `jahjah-web-truth` still reads the old clone · F15 the deferred `web-truth`/`verify.sh` unification.
