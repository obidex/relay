# STATE.md — Where the Project Is Right Now

> The only volatile file in the canon: threads keep it current on the coordinator's cards; §3 is rebuilt at each milestone review from the cards' closing comments. No rules here. History: `docs/archive/` (never loaded).

## 1. PROGRAMME

| Aspect | Status |
|---|---|
| **Programme** | P0–P2 done → **P3 Admin Mode** ★ → P4 → P5 (parallel) → **L** → P6 · engine: M0–M5 done; the Claude Code Project `jahjah-website` (coordinator + threads) runs the work (W181) · next: the P3 build from #111 via threads |
| **`master` HEAD at the last canon update** | `892c06c` (#198). `master` is normally one commit ahead (this PR). |
| **Live** | 68 pages EN + AR (22 products, 5 brands, 6 categories); no prices, no login. Astro 7.3.2, Studio 5.31.2; 6 on-demand routes (`/api/*`, W167). |
| **Web DB** | Supabase #2, schema v4 (W153 W159 W166 W174): 7 tables + `stock_visible`, which honours `stock_display` so a customer sees no status while it is `hidden` (F68); empty but the 6 settings; anon gets 42501. |
| **Content · ERP** | Placeholder catalogue, deleted when real data enters (W007) · `jahjah-internal`: own canon, not connected (W075). |
| **Gate** | Required job `ci` (`tier3-guard`, build, `tsc`, 67 pages, `verify.sh`, reference drift, gitleaks); ruleset `master-protection` (W100). Codex reviews, not a check. |

| Phase | Status | Remaining · exit |
|---|---|---|
| P0–P2 | done | carried: #143 (F64), Dependabot #84 (a major, W114); "every product looks real" waits on the owner (W126) |
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
4. **Cards only.** Work is an issue from `.github/ISSUE_TEMPLATE/card.yml`; the coordinator starts a thread that runs it with `/run-card` (W171, W181). No dispatch labels.
5. **Codex** was silent on every M4 pilot PR: threads check it once before merging and never wait (`/run-card` §7).
6. **No session edits `.claude/settings.json`:** the owner copies `scripts/dispatch/settings.v3.json` (W138); a file rule is `Edit(path)` (W169).
7. **The reviewer agent is not read-only** (its Bash is unscoped). **Browser floor iOS 16** (W141, W145).
8. **Previews carry no Supabase env** (W161, #146): test DB/auth routes locally, then on production; probe with plain `curl` (W146).
9. **Hooks are live (W168):** `pre-bash` refuses destructive and owner-only commands and any push reaching `master`; `post-edit` type-checks. A refusal is a finding.
10. **Threads** run Opus 5.5, effort from the `risk:*` label (W180); `supabase db push` stays owner-run until the W179 card lands.

## 3. LEDGER (newest 5; older rows → `docs/archive/STATE-history.md`)

| Date | Work · issue | Close HEAD | PRs | Result |
|---|---|---|---|---|
| 09-20 | review · M3 replay milestone review (R12) · #176 | this PR | this PR | 12/12 replay cards PASS (table on #112); THE BAR clean; 11 merged `card-*` branches await an owner delete (#203) |
| 09-19 | db · db-smoke asserts the hidden `stock_display` case (R11) · #175 | `892c06c` | #198 | step 3 reads `stock_visible` as a throwaway customer: 0 rows at `hidden`, 1 at `status` (fails on the pre-#186 view); setting restored; live run exit 0 |
| 09-18 | engine · a failing tick is never silent (R13) · #178 | `c228817` | #193 | `tick.sh` takes the unit's `ExecStartPre` lines: a dirty clone or diverged `master` leaves a `tick.log` line, `$STATE/clone-blocked` and at most one `alert` issue a day; sim 81/81; owner re-runs `install.sh` once |
| 09-18 | cleanup · relay-era pointers (#159) · #174 | `e69b6e1` | #190 | 12 files' stale pointers now name the card, a skill or a backlog issue; verify's `dist/` paths move to `dist/client/`; `tier3-guard` matches `scripts/dispatch/`; 67 pages byte-identical |
| 09-18 | deps · @sanity/client 7 → 8 (R08, #84) · #172 | `75c9c13` | #187 | v8's breaking changes miss our `createClient`/`fetch`/`patch` use; `libc` lockfile entries restored (F53); 67 pages byte-identical |

## 4. FACTS AND OWNER DECISIONS

| What | Where |
|---|---|
| Repo · Vercel · Sanity | `obidex/jahjah-website` · `jahjah-website` · `pxf1amia`/`production`; `jahjah.net` waits for L (W027) |
| Box | clone `/opt/jahjah/web` (Node 22, Supabase CLI 2.117.0 linked to #2, read-only MCP, W160). No addresses. |
| Automations | `jahjah-web-truth` Mon · `-docs` 30 min · `-backup` 02:30 + `-backup-check` Mon · registry in `jahjah-internal` |
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

Answered: W126; Dependabot gets no Sanity secrets; threads push `card-*` under allow rules; a thread's own merge can still be refused as "Merge Without Review" (W175).

## 5. NEXT STEP

**P3 build (card #111) via threads.** Open first: the W179 card (thread-run `supabase db push`), #202 (Actions minutes), #203 (branch delete). Product: P3-B2 (card #95; P3-1b, card #93, may go first), then P4.

**HANDOVER (to the coordinator):** M5 is done: the VPS engine and the relay are gone, and the Project runs the work. Read `CLAUDE.md` (it imports `AGENTS.md`) and `/strategist`; procedure lives in `/run-card`, `/migrate-db`, `/milestone-review`, `/ship`. Follow-ups are the open `backlog` issues. Read W179–W181.
