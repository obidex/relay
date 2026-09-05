# STATE.md — Where the Project Is Right Now

> **THE ONLY VOLATILE FILE IN THE CANON.** Rewritten at every chunk end by Claude Code. Nothing here is a rule.

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0 canon reset → P0.1 cowork lane → P0.2 workflow v2 → P1 launch blockers → P1.1 finish P1 → **P1.2 gates + deps** ★ this chunk → P2 identity + foundation → P3 Admin Mode → P4 customer accounts → P5 public UX/content (parallel) → **L launch** → P6 |
| **Next step** | **P2 — identity and foundation.** No *site* work is owed from P1, P1.1 or P1.2 — but **two high rows about the executor's own tooling are open**: F46 (`gh issue close` unallowlisted) and F47 (no route to the allow list at all). **The single first decision is VERCEL PRO** (~$20/mo), which W090 requires before the first on-demand route ships — that is P2's item 2, so it is needed early rather than at launch. The chunk arrives as a GitHub issue labelled `chunk:proposed`; the owner's `chunk:approved` label starts it (W099). **Dispatch it only if the ERP side has fixed the lane's usage-limit handling (W128) — otherwise run it interactively**, because the dispatched executor has failed 2 of 2; checked again 2026-09-05 against `jahjah-internal`'s reports index and no such fix has landed. |
| **`master` HEAD at the last canon update** | `0eb0a2a` — "ci(verify): give the hidden-product guard its store from env (#51)". This line is written by the chunk-close PR, the first commit that cannot know its own squash hash — so `master` is normally **one commit ahead of this line**, that PR itself. A larger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app` · 22 products · 5 brands · 6 categories · no prices, no login |
| **Content** | Placeholder catalogue (AI-generated names/copy, 126/192 images placeholder). Deleted when real data enters — never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP) — separate canon, **not connected** (W075). |

### CI shape (`.github/workflows/ci.yml`) and the merge gate

One job, **named `ci`**, on every PR and on `master`, Node 22: `tier3-guard` → `npm ci` → `npm run build` → page-count assert (`EXPECTED_PAGES` = 67 content pages — verify.sh excludes `/admin`, Astro reports 68; update it in the same PR that adds routes) → compiled-output checks → `npm run reference` + `git diff --exit-code docs/reference/` → gitleaks. **Since #51 the Verify step receives `PUBLIC_SANITY_PROJECT_ID` and `PUBLIC_SANITY_DATASET` as well as `SANITY_READ_TOKEN`** — its `env` now matches the Build step's, so the hidden-product guard resolves the store from the environment rather than from image URLs in `dist/` (F44, W132). Every action is pinned to a **commit SHA** with its tag in a trailing comment.

**`ci` IS A HARD MERGE GATE from 2026-09-02T16:20:50Z.** Ruleset `master-protection`, id **22124934**: pull request required, squash only, required check `ci` (strict), no deletion, no non-fast-forward, **no bypass actors** — the admin who created it cannot bypass it either (W100). A direct push is refused by GitHub with `GH013`. W084's "merge-on-green is discipline" is history.

`tier3-guard` fails any PR that touches a Tier-3 path without a body line `Tier-3: authorized by chunk <name>` (W101). `on.pull_request.types` includes `edited`, so adding the line re-runs it.

A second job, **`review`** (`.github/workflows/claude-review.yml`), no longer runs on pull requests at all: since chunk P1 it is `workflow_dispatch`-only, a **manual fallback** taking a PR number. The reviewer of record is **Codex** — see live flag 10.

`verify.sh` now also walks `dist/404.html` explicitly (four assertions, W111), audits heading hierarchy through `scripts/heading-audit.mjs` (0 skips across 67 pages), and asserts through `scripts/hidden-products-check.mjs` that no hidden product reached the build (W110). **That guard can no longer pass without checking (W124, #41; closes F37 and F40).** A `SKIP` at exit 0 is now available only where nothing was promised — no `SANITY_READ_TOKEN`, or no `dist/`. Where a token IS supplied, as in CI, any Sanity failure is exit **4** and `verify.sh` reports **FAIL**: a gate that did not run is not a build without hidden products. It also refuses to guess which store to query — no hardcoded `pxf1amia/production` fallback, the project/dataset pair resolves atomically from one source, and when the environment and the build's own image URLs both answer they must agree.

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
2. **CONTENT IS PLACEHOLDER; THE WATERMARK QUESTION IS CLOSED.** 22 products with AI-generated names; 1 product has real photos. Owner: don't upload photos to placeholder items. The friend's audit (2026-09-02) reported a **stock-photo watermark** on the DCEL front-load washer images. Both halves are now answered: the grep half was clean (3 distinct Sanity CDN assets, no watermark markers, re-checked by TRUTH weekly), and **the owner inspected the three photographs on 2026-09-04 and ruled them CLEAN** (W126). ROADMAP F1 is closed.
3. ~~`/admin/` is in the sitemap~~ — **FIXED in P1** (#30). The sitemap is 66 URLs, `/admin` is not among them, and the Studio surface carries `noindex, nofollow`. `verify.sh` asserts both, independently of each other, so removing one cannot mask the other.
4. ~~`/images/placeholder.jpg` returns 404~~ — **FIXED in P1** (#26, W112). **There are no asset 404s.** A missing image is `null` and renders an inline no-image tile that emits no `<img>` at all, so the browser makes no request that can fail; 0 placeholder references remain in the build, and `verify.sh` counts them every run.
5. **LAUNCH FACTS: PARTLY RULED, AND THE SITE NO LONGER CONTRADICTS ITSELF (W088 → W126).** The owner ruled on 2026-09-04: **founding year is 2010** (the site was right; other company materials are wrong); **SUNNY and DSP are the exclusive agency for all of Syria**; **phone numbers, opening hours, warranty wording and the brand list stay exactly as they stand.** The Damascus number's Turkish prefix (`+90…`) is correct — the phone is physically in Turkey. P1.1 applied the ruling (#42): the last unmade agency claim is gone from the home page's visible copy and from the Organization JSON-LD, which now emits the same string as the page's `<meta name="description">` by construction. **What is still the owner's** is narrower than the original row: the three tier names, the currency (W066), the showroom address, and a confirmation pass on numbers/hours/warranty — all low urgency, none blocking.
6. **VERCEL HOBBY.** Non-commercial. Pro is required before the first on-demand route ships (W090), i.e. P2 — not "at launch".
7. **NO WEB DB YET.** Supabase project #2 is created in P2. When it exists: free tier pauses after ~1 week idle (W091) — the nightly backup keeps it awake.
8. **CANON MOVED, AND THE PROJECT IS A SYNC NOT A PASTE (W098).** The claude.ai project knowledge is a GitHub sync of `docs/` + `CLAUDE.md`. It is not stale by definition — it **lags**. Use it for orientation; the mirror and the connector win. And a fresh `INDEX.md` does not mean a fresh sibling (W102): `raw.githubusercontent.com` will serve a current index beside a stale body, cache-buster on both.
9. **THE ISSUE LANE WORKS. THE DISPATCHED EXECUTOR HAS NEVER FINISHED A REAL CHUNK — 2 real chunks dispatched, 2 dead on the subscription window.** (Two earlier dispatches, the #11 and #12 smoke tests of 2026-09-02, exited 0 in 13 s each; they carried no work.) Keep those two facts apart, because an earlier version of this flag welded them together and got the cause wrong. **The lane itself is sound**: it picked up both approved issues within two minutes, relabelled, started the executor with `CHUNK_ISSUE` set, and every report has reached both the issue and the relay. **The dispatched sessions are what fail.** Chunk #24 exited 1 after 5307 s; chunk #36 exited 1 after 523 s. Each printed exactly one line — `You've hit your session limit · resets <time> (UTC)` — and each left a **53-byte log and no report**, because `claude -p` in its default text mode emits only a final assistant message and a killed run has none: 88 minutes of #24's real work produced 53 bytes. **This flag used to blame the allowlist for #24, and that was false**: the allowlist gap was real and was that chunk's *finding*, reported at 03:17:23Z, after which it kept working and merged two more PRs before the window ran out at 03:56:35Z. Both lanes and every interactive session on this box draw on **one** 5-hour subscription window and the lane cannot see how much of it is left (W128). **INTERACTIVE SESSIONS WORK**: every PR this project has merged since #29 was merged by one, including P1.1's four merged PRs and this chunk-close one. The fix is the dispatcher's, in `jahjah-internal` — capture the transcript, and treat a usage-limit exit as retryable rather than as a chunk failure. **The relay stays dual-published** until the strategist confirms it has read a whole chunk from the issue alone (ROADMAP F26).
10. **THE REVIEWER OF RECORD IS CODEX, THE CLAUDE `review` JOB IS MANUAL-ONLY, AND CODEX SPEAKS ON **FOUR** SURFACES — THE FOURTH IS A REACTION, AND MISSING IT HAS NOW PRODUCED A FALSE CANON CLAIM TWICE.** Codex (the ChatGPT GitHub app, on the owner's ChatGPT plan) posts as `chatgpt-codex-connector`, reading `AGENTS.md`. **Its own boilerplate states the rule this flag kept getting wrong:** *"If Codex has suggestions, it will comment; otherwise it will react with 👍."* So a 👍 **is** the "reviewed, found nothing" verdict, and it is invisible to all three surfaces this flag used to name.

    | Surface | Command | Carries |
    |---|---|---|
    | review | `gh pr view <n> --json reviews` | the review shell; boilerplate only |
    | inline | `gh api …/pulls/<n>/comments` | **the findings**, which the review body does not list |
    | issue comment | `gh api …/issues/<n>/comments` | a re-review answer after `@codex review` |
    | **reaction** | **`gh api …/issues/<n>/reactions`** | **👍 = reviewed, no findings.** 👀 = *looking*, posted within seconds of opening and NOT a verdict — match on the reaction's `content`, never on a reaction merely existing (W134b) |

    **THE CORRECTED RECORD, measured 2026-09-04.** P1.1: Codex answered **4 of 4** — #34 in 2m07s and #41 in 3m26s after explicit `@codex review` comments (issue comment + 👍 each), #37 unprompted with a 👍 in 2m41s, #42 unprompted with a review carrying one inline P1 in 1m52s. **Findings on 1 of 4; silence on none.** P1: it posted **findings** on 4 of 10 (#26 one P2, #27 one P1, #28 two P1, #31 two P2) — but the claim that it was "silent on #25, #29, #30, #32, #33 and #34" is **wrong for five of those six**: #25, #29, #30, #32 and #33 all carry a 👍. Only #34 was genuinely unanswered during P1, and it got its 👍 the next day. **Codex answered 9 of 10 P1 PRs, not 4** — and the two signals are complementary, which corroborates the rule: every P1 PR carrying findings has **no** 👍 (#26, #27, #28), every PR without findings has one, and the single PR with both is **#31**, whose explicit `@codex review` found nothing and added a 👍 on top of its original two findings. Neither Codex nor the Claude `review` job is a required check, so silence could never block a merge — but "silence" has been much rarer than this project believed, and the executor's reviewer subagent remains the gate regardless. `claude-review.yml` is `workflow_dispatch`-only, so its absence from a PR means nothing (F25, F28). **P1.2, measured 2026-09-05: 3 of 3 answered** — #45 an inline P2 in 2m53s (and no 👍, consistent with the rule that findings and 👍 are complementary), #46 a 👍 in 1m02s, #51 a 👍 in 1m50s. Both of P1.2's mid-chunk progress reports published a wrong interval for this, computed from the polling loop's wall clock rather than the PR's `createdAt`; the figures here are re-measured (W134c).
11. **THE EXECUTOR WORKSPACE IS TRUSTED** (this is the state fact `docs/STRATEGIST.md` §8 points here for): `/opt/jahjah/web` was trusted on 2026-09-02, so `.claude/settings.json`'s allow list is in force, not just its deny half.
12. **A DISPATCHED SESSION AND AN INTERACTIVE ONE ARE NOT THE SAME EXECUTOR (W116).** A dispatched (`claude -p`) session **cannot edit `.claude/**` at all** — the harness refuses it whatever the settings say — and runs only what the allowlist names. A plan may not pre-authorize the executor to make an allowlist change, and any new command a chunk depends on must be **dry-run first**. **THIS FLAG USED TO SAY "an allowlist change is *interactive-session work*", AND P1.2 DISPROVED IT** (W133, F47): an interactive session was refused the one-line edit too, by the harness's auto-mode classifier, so there is currently **no route at all** from this repository to its own allow list. Editing `.claude/agents/reviewer.md` and the skills succeeded in the same session, so the boundary is narrower than all of `.claude/**` and nobody has mapped it. `CLAUDE.md` §9 carries the five measured traps, including that a rule ending at a `VAR=` assignment is a universal bypass of the whole deny list.
13. **THE REVIEWER SUBAGENT'S `tools:` FRONTMATTER RESTRICTS IT — BUT NOT TO WHAT IT LISTS (F11, closed 2026-09-04).** Four passes invoked by name from `/opt/jahjah/web` each reported holding exactly **`Read` and `Bash`**: no `Write`, no `Edit`, **no MCP tools** despite MCP instructions arriving in their context — and no `Grep` or `Glob`, which the frontmatter *does* list. The per-command `Bash(git diff:*)` scoping is **not** enforced. **A `tools:` list containing `Bash` is not a read-only guarantee**: Bash grants `node -e`, `python3` and in-repo writes.

---

## 3. SESSION / CHUNK LEDGER (most recent first)

| Date | Session | Model | Result |
|---|---|---|---|
| 2026-09-05 | **P1.2 · Gates + allowlist + deps + CI witness** (**#44**) — the owner's Arabic amendment made real in every reviewer contract; the three indirect Dependabot updates; CI's hidden-product guard given its store from env. **INTERACTIVE BY DESIGN, not by failure** (W116): T1 edits `.claude/**`, which a dispatched session cannot do at all, so the plan forbade `chunk:approved` and the executor moved its own labels. | Claude Code (Opus 5, xhigh) | **PRs #45 `de5a88a`, #46 `7482dde`, #51 `0eb0a2a`** merged (+ this chunk-close PR). Dependabot #38, #39, #40 closed naming #46. **F42 and F44 closed; F46 DID NOT CLOSE, and that is this chunk's headline.** The one-line addition of `"Bash(gh issue close:*)"` to `.claude/settings.json` was **refused by the harness's auto-mode classifier** — a scripted edit and the Edit tool alike — in an **interactive** session, which is the very thing `CLAUDE.md` §9 and W116 both name as the repair path for an allowlist gap. The plan's contingency was followed exactly: split out, left uncommitted, reported, not routed around. **And its consequence was followed too** — T1(f)(2), which would have deleted the skill paragraph warning about this refusal, was withheld, because a cleanup authorized on the strength of a change that did not land must not land either (W133, F47). **Three executor reviewer passes; two changed the shipped file and the third corrected the record.** T1's applied a nit and left a severity-vocabulary import for the strategist. T3's caught a citation of **W132 before W132 existed** — had the chunk died between T3 and T4, `master` would carry a code comment pointing at a decision that was never written. T2's changed no shipped byte: it independently re-derived the lockfile diff, walked every dependency range in the lockfile (2621 by its count, summing `dependencies`, `optionalDependencies` and `peerDependencies`), and **built the site twice to prove all 104 files in `dist/` are byte-identical** across the dependency change — then corrected a factual error in the PR body before it shipped. **Codex answered all three PRs** (W130 holding): #45 a real inline P2 in 2m53s against its own newly-amended contract — the reviewer rule demanded a PR body at a moment when no PR exists — fixed in `75a7f6d` and confirmed by its follow-up; #46 a 👍 in 1m02s; #51 a 👍 in 1m50s. **The two progress reports published mid-chunk carry 3m11s and 1m52s for those two, and both are wrong** — they were computed from the polling loop's wall clock rather than the PR's `createdAt`, caught by re-measuring at write time. The habit W122 prescribes is the only reason the ledger is right. `npm audit` 24 → 21 and the **critical is gone**. Two cheap traps measured and written down (W134): a `**bolded**` Tier-3 authorization line fails `tier3-guard` in 7 s, and Codex posts a **👀** reaction on sight that is not a verdict. |
| 2026-09-04 | **P1.1 · Finish P1** (**#36**) — the four things P1 carried out, plus the owner's rulings of the same day. Merged the held-open AR accessibility PR under the amended Arabic gate; applied the four minor/patch Dependabot updates; made the hidden-product guard unable to pass without checking; applied the W088 launch-fact ruling; canon close. | Claude Code (Opus 5, xhigh) | **PRs #34 `0488dc1`, #37 `fa6df63`, #41 `c336235`, #42 `a85edc1`** merged (+ this chunk-close PR). #27 closed as the stale duplicate of #28; Dependabot #14, #15, #16, #19 closed naming #37. **Ran INTERACTIVELY, the dispatched run of this very issue having died** — #36's own dispatch exited 1 after 523 s on the subscription window, as #24's had after 5307 s, both leaving 53-byte logs and no report (W128). **Six executor reviewer passes** — T1, T2, T3, a T3 re-review, T4 and this chunk-close — and **five changed the shipped result** (the passes leave no artefact in git, so this count is the session's own report of itself, not a measurable): a lockfile-only PR that had silently stripped 29 `libc` fields; the F40 guard fix, where the first attempt closed one of three doors to the same vacuous pass; a partial application of the owner's ruling (two of three positioning elements on the home card); and an Arabic string whose diacritics were inconsistent with the file and with itself. **Codex answered ALL FOUR PRs, and this row's first draft said it was silent on three of them** — W122 recurring in the very chunk that was correcting it. The cause was mechanical and is now canon: there is a **fourth surface**, the 👍 **reaction**, and Codex's own boilerplate says so — *"If Codex has suggestions, it will comment; otherwise it will react with 👍."* Measured: #34 answered in **2m07s** and #41 in **3m26s** after explicit `@codex review` comments (issue comment + 👍 each), #37 with an unprompted 👍 in **2m41s**, and #42 with a review carrying **one inline P1** in 1m52s. So: findings on 1 of 4, response on 4 of 4, nothing silent. The P1 finding was correct against `AGENTS.md`'s copy of the *superseded* Arabic gate, and is the chunk's top follow-up (F42). **One permanent blemish**: #34 was squashed without `--subject`, so `master` carries a commit whose subject and body say the PR must not be merged. |
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

**Dependabot secrets: NOT MIRRORED (owner, 2026-09-04; ROADMAP F24 closed).** A live Sanity read token,
which can see drafts, is never handed to a run executing a dependency version nobody has reviewed. The
consequence is that Dependabot PRs stay red, and **W114 makes that the intended state**: the bot is a
notifier, the executor applies each update in a chunk PR with secrets present, then closes the bot's.

**DONE 2026-09-04 (P1.1 · T2, PR #37, W123).** The four minor/patch PRs — #14 undici, #15 browserslist,
#16 dompurify, #19 grouped — were applied and closed naming #37. `npm audit` over the same tree went from
**30** vulnerabilities (1 critical / 17 high / 10 moderate / 2 low) to **24** (1 / 15 / 6 / 2). **#20 and
#21 stay open and untouched**: they are major bumps of the embedded Studio and are F34's named chunk, and
the remaining critical and most of the highs live in that Studio 5.x tree, unreachable by a range bump.
**The bot opened three more within two minutes of the merge (72–84 s)** as it rescanned the new lockfile — #38
brace-expansion, #39 tar, #40 postcss, all indirect. **DONE 2026-09-05 (P1.2 · T2, PR #46):** all three
applied and closed naming it; `npm audit` **24 → 21** (1 → 0 critical, 15 → 13 high), advisories resolved
brace-expansion/postcss/tar, none new. npm stripped the 29 `libc` fields again and they were restored again
(W123). **And the bot did it a third time, 69–105 s after #46 merged:** #47 svgo and #48 linkify-it (indirect,
minor/patch — ordinary flow) and **#49 sharp + #50 astro 6→7, which are majors and travel together** because
Dependabot says sharp needs the astro major as its ancestor. They have their own row, F48.
**Both of the items that gated P1's exit are now answered (2026-09-04), and neither needed code:**
- **The watermark verdict** (F1) — the owner looked at the three photographs and ruled them **CLEAN**.
- **The launch-fact ruling** (W088) — ruled in part, and the self-inconsistency is gone: the home page's
  visible copy and the Organization JSON-LD no longer make an agency claim the owner never made (#42).
**Still open:** decide **which of the 22 placeholder products to keep** (F35) — W031 wants 8–10 real
flagships, W007 says placeholder records are deleted rather than polished. The machinery to hide them
exists and is proven. **The owner ruled on 2026-09-04 that hiding is never the executor's or the
strategist's** — he does it himself with the existing `published` toggle, in Studio now and Admin Mode
later (W126). So what is open is a curation decision, not a task waiting on a chunk.

The older items, unchanged: name the three price tiers (default Tier 1/2/3) · decide currency (W066)
before flipping `prices_visible` · obtain ShamCash merchant docs (W064, not urgent) · decide whether
the showroom address is published (ROADMAP §4).

---

## 5. NEXT PLANNED STEP

**P2 · Identity and foundation.** No *site* work is still owed from P1, P1.1 or P1.2; what is owed is F46 and F47 below, which are about the executor rather than the site. P2 is Tier 3 throughout: SKU on every sellable variant under GATE 1 (W076) · the
`@astrojs/vercel` adapter, the first on-demand route and a middleware skeleton · Supabase project #2
with schema, RLS and TOTP MFA (W083, W091) · a TypeScript data layer in `src/lib/` (W086).

**The single first decision is VERCEL PRO** (~$20/mo). W090 requires it *before* the first on-demand
route ships, which is P2's second item — so it is needed early in P2, not at launch, and it is the
owner's to make. Nothing else blocks.

**ONE THING BLOCKS THE EXECUTOR ITSELF, AND IT IS NOT P2 WORK (ROADMAP F47, W133).** This repository
currently has **no route to its own allow list.** P1.2 was run interactively for the express purpose of
adding one line to `.claude/settings.json`, and the harness's auto-mode classifier refused it by every
route tried — a scripted edit and the Edit file tool alike. `CLAUDE.md` §9 and W116 both say an
allowlist gap is repaired by an interactive session; that is now known to be false, so **any future
plan whose remedy is "add an allow rule" will stop the same way.** F46 (`gh issue close`) is the
instance. The owner editing the file by hand is the only route anyone has proposed. Note what was NOT
established: edits to `.claude/agents/reviewer.md` and both skills succeeded in the same session, so
the boundary is narrower than all of `.claude/**` and nobody has mapped it.

**Open, and each has a row:** F5 (two dead RTL rules, P5) · F26 (retiring the relay — still **two** chunks
through the dispatch lane, P1 #24 and P1.1 #36; P1.2 was started by hand and never carried
`chunk:approved`, so the lane did not carry it) · F34 (Studio 5→6, its own named chunk) · F35
F36 (the `.vercel.app` host is indexable) · F38 (the dispatcher
leaves `chunk:proposed` in place — ERP-side) · F39 (silent sessions) · F27 · F32 · F43 (the standing AR
mass-review row — **no Arabic string shipped in P1.2**, so it is unchanged at four strings) · F45
(two silent-skips) · **F46** (still open, see above) · **F47 and F48, opened by this chunk**.

**Closed in P1.2:** F42 (every reviewer contract states the amended Arabic gate — PR #45) · F44 (CI's
Verify step resolves the store from env — PR #51). **Closed in P1.1:** F1 · F31 and F33 · F37 and
F40 · F41.

**One question is the strategist's, not the owner's:** the item-4 wording P1.2 shipped verbatim imports
the severity label "P1" into `REVIEW.md`, which uses 🔴/nit and defines P1 nowhere. It was shipped
rather than diverged from because the plan required `AGENTS.md` and `REVIEW.md` to carry identical
wording, and flagged rather than silently fixed.

**And the operational fact that outranks all of them:** the dispatched executor has never finished a
chunk — 2 runs, 2 failures, both on the subscription window (live flag 9, W128). Checked again
2026-09-05 against `jahjah-internal`'s reports index: **the `run-chunk.sh` change has not landed.**
Until it does, **approve P2's issue only if someone is there to run it interactively**, or expect it
to die mid-flight leaving a 53-byte log. P1.2 itself ran interactively — by design rather than by
failure, since it edits `.claude/**` — and its own experience says the interactive lane works: three
PRs, three merges, no session loss.
