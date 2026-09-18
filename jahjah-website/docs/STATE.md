# STATE.md — Where the Project Is Right Now

> The only volatile file in the canon: `think` keeps it current (`canon/*` PRs), and each card adds its ledger row. No rules here. History: `docs/archive/` (never loaded).

## 1. PROGRAMME

| Aspect | Status |
|---|---|
| **Programme** | P0–P2 done → **P3 Admin Mode** ★ → P4 → P5 (parallel) → **L** → P6 · engine v3 (card #112): M0, M1, M2 done → **M3 replay** → M4 pilot → M5 handover |
| **`master` HEAD at the last canon update** | `5fa6858` (#158). `master` is normally one commit ahead (this PR). |
| **Live** | 68 pages EN + AR (22 products, 5 brands, 6 categories); no prices, no login. Astro 7.3.2, Studio 5.31.2; 6 on-demand routes (`/api/*`, W167). |
| **Web DB** | Supabase #2, schema v3 (W153 W159 W166): 7 tables + `stock_visible`, empty but the 6 settings; anon gets 42501. |
| **Content · ERP** | Placeholder catalogue, deleted when real data enters (W007) · `jahjah-internal`: own canon, not connected (W075). |
| **Gate** | Required job `ci` (`tier3-guard`, build, `tsc`, 67 pages, `verify.sh`, reference drift, gitleaks); ruleset `master-protection` (W100). Codex reviews, not a check. |

| Phase | Status | Remaining · exit |
|---|---|---|
| P0–P2 | done | carried: #143 (F64), #145 (F68), Dependabot #84 (a major, W114); "every product looks real" waits on the owner (W126) |
| **P3** Admin Mode (W082) | P3-1a, P3-B1 done; next P3-B2 write routes (card #95), then the build (card #111) | exit: an editor works without a programmer or a Sanity seat; every change attributable; prices entered while `prices_visible` is OFF |
| P4 accounts | — | sign-up, tiers, price island, stock, promotions; exit: tiers 1/2/3/none see exactly their prices |
| P5 public UX | parallel | homepage repositioning, category imagery + pages (W041), search + filters, trust strip, service page, showroom map, Lighthouse; AR batched (W125) |
| L launch (W027) | one event | Vercel Pro (W090), Cloudflare, `jahjah.net`, `site` URL, analytics, search consoles, Business Profile, invites; #134 |
| P6 | after launch | quote list; VPS mirror, DNS cut-over after a clean month (W078); ERP SKU sync (W075); payments last (W064). Later: app, guides/blog, testimonials, translate-on-paste |

**Target** (W074 W075 W081 W082): a price-free catalogue; tiered customers' own prices; staff editing in place. **Rejected, do not reopen:** Next.js, Shopify, WooCommerce, an off-the-shelf ERP (W018) · commerce in Sanity (W075) · a portal or `trade.` subdomain, Sanity visual editing (W082) · an ignored build step (W087) · a host move at launch (W027, W078) · a hosted Studio (W011).

## 2. LIVE FLAGS

1. **Two paths to production:** a merge to `master`, and a Sanity publish (deploy hook, ~2 min, no commit).
2. **Owner rulings (W126):** no photos on placeholders; watermark clean; founded 2010; SUNNY/DSP exclusive; numbers, hours, warranty, brands unchanged.
3. **Free Supabase pauses after ~1 week idle (W091)** until #143 dumps it nightly; `/api/health` touches it only when called.
4. **Cards only.** Work is an issue from `.github/ISSUE_TEMPLATE/card.yml`, run by `/run-card`; `jahjah-web-run` takes the oldest `card:ready` every 2 min (W168, W171). **No real card gets `card:ready` until M3's dry cards pass 12/12.** Reports also go to the relay until M5 (#131).
5. **Codex** speaks on four surfaces (review, inline, comment, `+1` = clean; `eyes` = no verdict). All of M2's PRs got a usage-limit notice instead: check its quota before waiting.
6. **No session edits `.claude/settings.json`:** the owner copies `scripts/dispatch/settings.v3.json` (W138); a file rule is `Edit(path)` (W169).
7. **The reviewer agent is not read-only** (its Bash is unscoped). **Browser floor iOS 16** (W141, W145).
8. **Previews carry no Supabase env** (W161, #146): test DB/auth routes locally, then on production; probe with plain `curl` (W146).
9. **Hooks are live (W168):** `pre-bash` refuses destructive and owner-only commands and any push reaching `master`; `post-edit` type-checks. A refusal is a finding.
10. **`think`** runs in tmux `think` (fable, dontAsk) in its own worktree, reset to `origin/master` at each start; `think.sh --restart` reloads it. Worktrees of this clone are trusted (measured): think writes only issues, STATE/DECISIONS and `canon/*` PRs.

## 3. LEDGER (newest 5; older rows → `docs/archive/STATE-history.md`)

| Date | Work · issue | Close HEAD | PRs | Result |
|---|---|---|---|---|
| 09-18 | docs · AGENTS.md drops relay-era pointers · #166 | this PR | this PR | reworded THE BAR/follow-up/Tier-3/`chunk plan` pointers to `.claude/skills/milestone-review/SKILL.md`, `backlog` issues, #137 and `Tier-3: authorized by card #<n>`; THE BAR and the six checks unchanged |
| 09-17 | Engine · worktree env + clone on master · #164 | this PR | this PR | `dispatch.sh` symlinks the clone's `.env.local` into a card worktree on every path (fresh, reused, resumed), so `verify.sh` and the smoke scripts run there; the unit puts the clone on master and fast-forwards before each tick, and refuses a dirty one. Offline simulation 50/50; its 4 new link checks fail on the old script. Owner re-runs `install.sh` once |
| 09-17 | M2 brain · #124 | this PR | #125 #155–#158 + close | `CLAUDE.md` core (53 lines); skills run-card, migrate-db, milestone-review, strategist (haiku-proven); card template; `tier3-guard` + API routes; ROADMAP → #126–#154; think's worktree; settings narrowed (W171) |
| 09-17 | M1 engine · #114 | `ef36b4c` | #117–#119 #121–#123 | Hooks, `tsc` in CI, settings v3, agents, `jahjah-web-run`, `think` on the phone (W168–W170) |
| 09-17 | M0 baseline · #113 | `a884e8e` | #115 #116 | `scripts/dispatch/metrics.mjs`; numbers on #113 |

## 4. FACTS AND OWNER DECISIONS

| What | Where |
|---|---|
| Repo · Vercel · Sanity | `obidex/jahjah-website` · `jahjah-website` · `pxf1amia`/`production`; `jahjah.net` waits for L (W027) |
| Box | clone `/opt/jahjah/web` (Node 22, Supabase CLI 2.117.0 linked to #2, read-only MCP, W160) · `think` worktree `/opt/jahjah/think` · dispatcher state `/opt/jahjah/run-state`. No addresses. |
| Automations | `jahjah-web-run` 2 min (kill: `scripts/dispatch/STOP` or a `stop` issue) · `-truth` Mon · `-docs` 30 min · `-backup` 02:30 + `-backup-check` Mon · registry in `jahjah-internal` |
| Backlog | open issues labelled `backlog` + `pri:*` + `risk:*` |

**Secret names (never values):** `PUBLIC_SANITY_*`, `SANITY_READ_TOKEN` (Vercel, VPS, Actions) · `SANITY_WRITE_TOKEN` (VPS, Production) · the deploy-hook URL · `SUPABASE_URL`, `SUPABASE_ANON_KEY` (VPS, Production) · `SUPABASE_SERVICE_ROLE_KEY` (Production, VPS) · `SUPABASE_PROJECT_REF` · `SUPABASE_ACCESS_TOKEN`, `SUPABASE_DB_PASSWORD` (CLI, one command, W154).
**Plans:** Vercel Hobby (Pro at L, W090) · Sanity Free · GitHub Pro · Supabase Free · Claude Max.

| Owner decision (open) | Blocks | Default |
|---|---|---|
| Which placeholders to keep (he toggles `published`) | P5 | all visible |
| Launch-fact pass + delivery coverage (W088) | L | as is |
| Currency: SYP / USD / both (W066) | `prices_visible` | OFF |
| Tier names (#128) | P4 | Tier 1/2/3 |
| Publish the showroom address? | P5 map, L profile | unchanged |
| ShamCash merchant API (W064) | P6 ordering | quote list |
| Guides/testimonials: will anyone write them? | Later | skip |

Answered: W126; Dependabot gets no Sanity secrets; workers push `card-*`/`canon/*` under allow rules, not the classifier; canon PRs auto-merge as a ruling, but the `Bash(gh pr merge:*)` deny in `think.settings.json` still overrides the allow, so `think` cannot merge yet — narrowing that deny is an owner edit (chunk M2b-rules, #162).

## 5. NEXT STEP

**M3 replay (card #112):** `think` writes 12 dry cards across the template's paths (auto, owner tap, risk 1–3, a blocked-by chain, a GATE-1 dry run, "no work needed"); 12/12 through `jahjah-web-run` before any real card is ready. Then M4 (one real backlog card vs M0) and M5 (relay retired, #131). Product next: P3-B2 (card #95; P3-1b, card #93, may go first), #145 before P4.

**HANDOVER (to `think`):** M2 is done. Read `CLAUDE.md` (the core) and `/strategist` (yours); procedure lives in `/run-card`, `/migrate-db`, `/milestone-review`, `/ship`. Work is a card from the template; follow-ups are the open `backlog` issues (#159 lists M2's stale pointers). You run in your own worktree, reset to `origin/master` at each start, and edit only STATE and DECISIONS on `canon/*` branches. First: M3's 12 dry cards (none `card:ready` until #160 is settled), then the two new rulings in §4. Read W168–W173.
