# STATE.md — Where the Project Is Right Now

> **THE ONLY VOLATILE FILE IN THE CANON.** Rewritten at every chunk end by Claude Code. Nothing here is a rule.

## 1. WHERE WE ARE

| Aspect | Status |
|---|---|
| **Programme** | P0 canon reset → P0.1 cowork lane → P0.2 workflow v2 → P1 launch blockers → P1.1 finish P1 → P1.2 gates + deps → **P2a foundation-lite** ★ this chunk → P2b identity + foundation → P3 Admin Mode → P4 customer accounts → P5 public UX/content (parallel) → **L launch** → P6 |
| **Next step** | **P2b — identity and foundation, and it waits on the owner at a PC three times**: create Supabase project #2, create `SANITY_WRITE_TOKEN` (the SKU backfill, F51, cannot run without it), and turn on **Vercel Pro** (~$20/mo), which W090 requires before the first on-demand route ships. The strategist gives the keystrokes. **No site work is owed from P2a, and nothing about the executor's own tooling blocks**: F46 and F47 closed — the allow list has a working route, the owner's hand (W138). The chunk arrives as a GitHub issue labelled `chunk:proposed`; the owner's `chunk:approved` label starts it (W099). **Dispatch it only if the ERP side has fixed the lane's usage-limit handling (W128) — otherwise run it interactively**; last checked 2026-09-05, when no such fix had landed, and not re-checked by P2a, which ran interactively by design. |
| **`master` HEAD at the last canon update** | `9b0a2ce` — "perf(images,seo): responsive product images and listing JSON-LD (W137) (#59)". This line is written by the chunk-close PR, the first commit that cannot know its own squash hash — so `master` is normally **one commit ahead of this line**, that PR itself. A larger gap means commits landed outside the chunk loop. |
| **Live** | 68 pages EN + AR on `https://jahjah-website.vercel.app` · 22 products · 5 brands · 6 categories · no prices, no login · responsive product images and `ItemList`/`CollectionPage` JSON-LD since P2a (W137) |
| **Content** | Placeholder catalogue (AI-generated names/copy, 126/192 images placeholder). Deleted when real data enters — never polished (W007). |
| **Sister project** | `jahjah-internal` (ERP) — separate canon, **not connected** (W075). |

### CI shape (`.github/workflows/ci.yml`) and the merge gate

One job, **named `ci`**, on every PR and on `master`, Node 22: `tier3-guard` → `npm ci` → `npm run build` → page-count assert (`EXPECTED_PAGES` = 67 content pages — verify.sh excludes `/admin`, Astro reports 68; update it in the same PR that adds routes) → compiled-output checks → `npm run reference` + `git diff --exit-code docs/reference/` → gitleaks. **Since #51 the Verify step receives `PUBLIC_SANITY_PROJECT_ID` and `PUBLIC_SANITY_DATASET` as well as `SANITY_READ_TOKEN`** — its `env` now matches the Build step's, so the hidden-product guard resolves the store from the environment rather than from image URLs in `dist/` (F44, W132). Every action is pinned to a **commit SHA** with its tag in a trailing comment.

**`ci` IS A HARD MERGE GATE from 2026-09-02T16:20:50Z.** Ruleset `master-protection`, id **22124934**: pull request required, squash only, required check `ci` (strict), no deletion, no non-fast-forward, **no bypass actors** — the admin who created it cannot bypass it either (W100). A direct push is refused by GitHub with `GH013`. W084's "merge-on-green is discipline" is history.

`tier3-guard` fails any PR that touches a Tier-3 path without a body line `Tier-3: authorized by chunk <name>` (W101). `on.pull_request.types` includes `edited`, so adding the line re-runs it.

A second job, **`review`** (`.github/workflows/claude-review.yml`), no longer runs on pull requests at all: since chunk P1 it is `workflow_dispatch`-only, a **manual fallback** taking a PR number. The reviewer of record is **Codex** — see live flag 10.

`verify.sh` now also walks `dist/404.html` explicitly (four assertions, W111), audits heading hierarchy through `scripts/heading-audit.mjs` (0 skips across 67 pages), and asserts through `scripts/hidden-products-check.mjs` that no hidden product reached the build (W110). **That guard can no longer pass without checking (W124, #41; closes F37 and F40).** A `SKIP` at exit 0 is now available only where nothing was promised — no `SANITY_READ_TOKEN`, or no `dist/`. Where a token IS supplied, as in CI, any Sanity failure is exit **4** and `verify.sh` reports **FAIL**: a gate that did not run is not a build without hidden products. It also refuses to guess which store to query — no hardcoded `pxf1amia/production` fallback, the project/dataset pair resolves atomically from one source, and when the environment and the build's own image URLs both answer they must agree. **Since #58 (P2a) it loads `.env.local` when present, filling gaps only (W136)**, so the three secret-in-`dist/` leak checks run on the executor as well as in CI — and it reads **0 FAIL · 0 WARN**, the F5 dead-RTL warning gone.

### Automations touching this project (registry: `jahjah-internal/docs/runbooks/automations.md`)

| Unit | When | Output |
|---|---|---|
| `jahjah-web-truth` | Mon 05:30 UTC | `relay/jahjah-website/reports/TRUTH-weekly.md` — clean build, compiled greps, live probes |
| `jahjah-web-docs` | every 30 min | `relay/jahjah-website/docs/` — mirror of the canon allowlist (set up in P0) |
| `jahjah-web-backup` | 02:30 UTC nightly | `/root/backups/web/` — Sanity export (+ DB dump from P2b), keep 7, VPS-only (set up in P0) |
| `jahjah-web-dispatch` | every 2 min | starts an approved chunk issue on the executor; heartbeat `HEARTBEAT-web-dispatch.md`. Kill: `touch /opt/jahjah/WEB_DISPATCH_OFF` (P0.2) |
| `jahjah-web-backup-check` | Mon 03:30 UTC | unpacks the newest backup, compares counts with live Sanity and checks every referenced image is present; verdict on `HEALTH-daily.md` (P0.2) |

---

## 2. LIVE FLAGS — things that surprise a newcomer

1. **TWO PATHS TO PRODUCTION.** `git push` to `master` AND a Sanity publish (webhook → Vercel deploy hook, ~2 min). The site changes with no commit; only TRUTH sees it.
2. **CONTENT IS PLACEHOLDER; THE WATERMARK QUESTION IS CLOSED.** 22 products with AI-generated names; 1 product has real photos. Owner: don't upload photos to placeholder items. The friend's audit (2026-09-02) reported a **stock-photo watermark** on the DCEL front-load washer images. Both halves are now answered: the grep half was clean (3 distinct Sanity CDN assets, no watermark markers, re-checked by TRUTH weekly), and **the owner inspected the three photographs on 2026-09-04 and ruled them CLEAN** (W126). ROADMAP F1 is closed.
3. ~~`/admin/` is in the sitemap~~ — **FIXED in P1** (#30). The sitemap is 66 URLs, `/admin` is not among them, and the Studio surface carries `noindex, nofollow`. `verify.sh` asserts both, independently of each other, so removing one cannot mask the other.
4. ~~`/images/placeholder.jpg` returns 404~~ — **FIXED in P1** (#26, W112). **There are no asset 404s.** A missing image is `null` and renders an inline no-image tile that emits no `<img>` at all, so the browser makes no request that can fail; 0 placeholder references remain in the build, and `verify.sh` counts them every run.
5. **LAUNCH FACTS: PARTLY RULED, AND THE SITE NO LONGER CONTRADICTS ITSELF (W088 → W126).** The owner ruled on 2026-09-04: **founding year is 2010** (the site was right; other company materials are wrong); **SUNNY and DSP are the exclusive agency for all of Syria**; **phone numbers, opening hours, warranty wording and the brand list stay exactly as they stand.** The Damascus number's Turkish prefix (`+90…`) is correct — the phone is physically in Turkey. P1.1 applied the ruling (#42): the last unmade agency claim is gone from the home page's visible copy and from the Organization JSON-LD, which now emits the same string as the page's `<meta name="description">` by construction. **What is still the owner's** is narrower than the original row: the three tier names, the currency (W066), the showroom address, and a confirmation pass on numbers/hours/warranty — all low urgency, none blocking.
6. **VERCEL HOBBY.** Non-commercial. Pro is required before the first on-demand route ships (W090), i.e. P2b — not "at launch".
7. **NO WEB DB YET.** Supabase project #2 is created in P2b. When it exists: free tier pauses after ~1 week idle (W091) — the nightly backup keeps it awake.
8. **CANON MOVED, AND THE PROJECT IS A SYNC NOT A PASTE (W098).** The claude.ai project knowledge is a GitHub sync of `docs/` + `CLAUDE.md`. It is not stale by definition — it **lags**. Use it for orientation; the mirror and the connector win. And a fresh `INDEX.md` does not mean a fresh sibling (W102): `raw.githubusercontent.com` will serve a current index beside a stale body, cache-buster on both.
9. **THE ISSUE LANE WORKS. THE DISPATCHED EXECUTOR HAS NEVER FINISHED A REAL CHUNK — 2 real chunks dispatched, 2 dead on the subscription window.** (Two earlier dispatches, the #11 and #12 smoke tests of 2026-09-02, exited 0 in 13 s each; they carried no work.) Keep those two facts apart, because an earlier version of this flag welded them together and got the cause wrong. **The lane itself is sound**: it picked up both approved issues within two minutes, relabelled, started the executor with `CHUNK_ISSUE` set, and every report has reached both the issue and the relay. **The dispatched sessions are what fail.** Chunk #24 exited 1 after 5307 s; chunk #36 exited 1 after 523 s. Each printed exactly one line — `You've hit your session limit · resets <time> (UTC)` — and each left a **53-byte log and no report**, because `claude -p` in its default text mode emits only a final assistant message and a killed run has none: 88 minutes of #24's real work produced 53 bytes. **This flag used to blame the allowlist for #24, and that was false**: the allowlist gap was real and was that chunk's *finding*, reported at 03:17:23Z, after which it kept working and merged two more PRs before the window ran out at 03:56:35Z. Both lanes and every interactive session on this box draw on **one** 5-hour subscription window and the lane cannot see how much of it is left (W128). **INTERACTIVE SESSIONS WORK**: every PR this project has merged since #29 was merged by one, including P1.1's four merged PRs and this chunk-close one. The fix is the dispatcher's, in `jahjah-internal` — capture the transcript, and treat a usage-limit exit as retryable rather than as a chunk failure. **The relay stays dual-published** until the strategist confirms it has read a whole chunk from the issue alone (ROADMAP F26).
10. **THE REVIEWER OF RECORD IS CODEX, THE CLAUDE `review` JOB IS MANUAL-ONLY, AND CODEX SPEAKS ON **FOUR** SURFACES — THE FOURTH IS A REACTION, AND MISSING IT HAS NOW PRODUCED A FALSE CANON CLAIM TWICE.** Codex (the ChatGPT GitHub app, on the owner's ChatGPT plan) posts as `chatgpt-codex-connector`, reading `AGENTS.md`. **Its own boilerplate states the rule this flag kept getting wrong:** *"If Codex has suggestions, it will comment; otherwise it will react with 👍."* So a 👍 **is** the "reviewed, found nothing" verdict, and it is invisible to all three surfaces this flag used to name.

    | Surface | Command | Carries |
    |---|---|---|
    | review | `gh pr view <n> --json reviews` | the review shell; boilerplate only |
    | inline | `gh api …/pulls/<n>/comments` | **the findings**, which the review body does not list |
    | issue comment | `gh api …/issues/<n>/comments` | a re-review answer after `@codex review` |
    | **reaction** | **`gh api …/issues/<n>/reactions`** | **👍 = reviewed, no findings.** 👀 = *looking*, posted within seconds of opening and NOT a verdict — match on the reaction's `content`, never on a reaction merely existing (W134b) |

    **THE CORRECTED RECORD, measured 2026-09-04.** P1.1: Codex answered **4 of 4** — #34 in 2m07s and #41 in 3m26s after explicit `@codex review` comments (issue comment + 👍 each), #37 unprompted with a 👍 in 2m41s, #42 unprompted with a review carrying one inline P1 in 1m52s. **Findings on 1 of 4; silence on none.** P1: it posted **findings** on 4 of 10 (#26 one P2, #27 one P1, #28 two P1, #31 two P2) — but the claim that it was "silent on #25, #29, #30, #32, #33 and #34" is **wrong for five of those six**: #25, #29, #30, #32 and #33 all carry a 👍. Only #34 was genuinely unanswered during P1, and it got its 👍 the next day. **Codex answered 9 of 10 P1 PRs, not 4** — and the two signals are complementary, which corroborates the rule: every P1 PR carrying findings has **no** 👍 (#26, #27, #28), every PR without findings has one, and the single PR with both is **#31**, whose explicit `@codex review` found nothing and added a 👍 on top of its original two findings. Neither Codex nor the Claude `review` job is a required check, so silence could never block a merge — but "silence" has been much rarer than this project believed, and the executor's reviewer subagent remains the gate regardless. `claude-review.yml` is `workflow_dispatch`-only, so its absence from a PR means nothing (F25, F28). **P1.2, measured 2026-09-05: 3 of 3 answered** — #45 an inline P2 in 2m53s (and no 👍, consistent with the rule that findings and 👍 are complementary), #46 a 👍 in 1m02s, #51 a 👍 in 1m50s. Both of P1.2's mid-chunk progress reports published a wrong interval for this, computed from the polling loop's wall clock rather than the PR's `createdAt`; the figures here are re-measured (W134c). **P2a, re-measured 2026-09-10 from each PR's `createdAt`: 5 of 5 answered.** #54 👍 in 2m08s · #56 👍 in 3m25s · #57 👍 in 3m07s · #58 a review carrying one inline P2 in 3m28s — a real security defect in the executor's own `verify.sh` change (W136), with no 👍 beside it — then, after the fix and an explicit `@codex review` at 01:03:03Z, a 👍 4m08s and an issue comment **4m09s** later (the T2 + T3 progress report published 3m55s; the timestamps say 4m08s–4m09s) · #59 👍 unprompted in **6m25s** — which a strategist ruling posted 6h23m later said did not exist (W140). **Codex also appears to answer the text `@codex` on a chunk ISSUE, by running a "task".** Twice on #53 it posted a task summary (01:18:01Z, 07:46:43Z), each 6–8 minutes after one of the only two #53 comments containing that string — the executor's T2 + T3 report and the strategist's ruling — so most likely summoned by it. Each describes a commit of its own (`99b54e2`, `d9b155e`); GitHub returns 422 for both, no branch exists and no PR was opened. They are neither reviews nor changes, and the strategist ruled the first ignorable. Until the mechanism is ruled out, **keep the literal `@codex` out of issue comments** that do not want a task.
11. **THE EXECUTOR WORKSPACE IS TRUSTED** (this is the state fact `docs/STRATEGIST.md` §8 points here for): `/opt/jahjah/web` was trusted on 2026-09-02, so `.claude/settings.json`'s allow list is in force, not just its deny half.
12. **A DISPATCHED SESSION AND AN INTERACTIVE ONE ARE NOT THE SAME EXECUTOR (W116).** A dispatched (`claude -p`) session **cannot edit `.claude/**` at all** — the harness refuses it whatever the settings say — and runs only what the allowlist names. A plan may not pre-authorize the executor to make an allowlist change, and any new command a chunk depends on must be **dry-run first**. **THIS FLAG USED TO SAY "an allowlist change is *interactive-session work*", AND P1.2 DISPROVED IT** (W133): an interactive session is refused the one-line edit too, by the harness's auto-mode classifier — three times across P1.2 and P2a. **The route that works is the owner's keyboard** (W138, measured 2026-09-09): he edits `.claude/settings.json` by hand, outside any session, and the executor commits his diff behind a gate (#56). Edits to `.claude/agents/reviewer.md` and the skills succeeded, and a destructive `gh api` call was refused in the same classifier words (W139), so the boundary is neither all of `.claude/**` nor only that one file, and nobody has mapped it. `CLAUDE.md` §9 carries the seven measured traps, including that a rule ending at a `VAR=` assignment is a universal bypass of the whole deny list.
13. **THE REVIEWER SUBAGENT'S `tools:` FRONTMATTER RESTRICTS IT — BUT NOT TO WHAT IT LISTS (F11, closed 2026-09-04).** Four passes invoked by name from `/opt/jahjah/web` each reported holding exactly **`Read` and `Bash`**: no `Write`, no `Edit`, **no MCP tools** despite MCP instructions arriving in their context — and no `Grep` or `Glob`, which the frontmatter *does* list. The per-command `Bash(git diff:*)` scoping is **not** enforced. **A `tools:` list containing `Bash` is not a read-only guarantee**: Bash grants `node -e`, `python3` and in-repo writes.

---

## 3. SESSION / CHUNK LEDGER (most recent first)

| Date | Session | Model | Result |
|---|---|---|---|
| 2026-09-05 → 09-10 | **P2a · Foundation-lite** (**#53**) — the allow-list route test (F46/F47), the SKU field (W076), the deferred cleanups (F45, F5), and responsive product images + listing JSON-LD pulled forward from P5. **INTERACTIVE BY DESIGN, in sessions started by hand** — Session A intending default permission mode for the F47 measurement, Session B `acceptEdits` — and `chunk:approved` was never applied. Session B itself took three: the first was abandoned before merging anything (it left the named stash `abandoned-session-B-T2-draft` and an empty `chunk/p2a-t2-sku`, both accounted for in the T1b report, and T2 was rewritten fresh rather than applied from it); the second merged T1b–T3, reported after opening #59 and ended; the third rebuilt and re-verified from scratch (W058), merged #59 and closed the chunk. | Claude Code (Opus 5, xhigh) | **PRs #54 `4b45dbc`, #56 `df9174d`, #57 `a10426a`, #58 `13e7ec9`, #59 `9b0a2ce`** merged (+ this chunk-close PR). **F47's answer: no session can edit `.claude/settings.json`, and the owner's hand can.** T1's third refusal came from a session started intending default mode, with no prompt (#54); the owner then added `"Bash(gh issue close:*)"` by hand and T1b committed his diff behind a four-part gate (#56, W138). F45, F46, F47 and F5 closed, and `npm run verify` reads **0 FAIL · 0 WARN** for the first time. **The executor's reviewer changed the shipped result in T1, T1b, T2 and T3** — among them two unreachable validator messages and the first-keystroke `readOnly` lock in T2, and a BLOCK in T3 for a reference not regenerated. **Codex found a real security defect in T3 that the executor had introduced**: `verify.sh` sourcing `.env.local` over the caller's environment, which would have had the leak check search `dist/` for the wrong token (W136); fixed in `923ce0b`. **Codex answered 5 of 5 PRs** (live flag 10) — and #59's 👍, 6m25s after opening, was said not to exist by a strategist ruling posted 6h23m later (W140). The plan's `readOnly` line shipped verbatim in T2 with its defect reported, and came out in T5 on the strategist's ruling together with the Product JSON-LD's `"sku": product.slug` (W135). **The plan was wrong twice about the harness prompting**: the settings edit did not prompt in T1, and `gh api -X DELETE` was refused outright in T3 — the owner deleted `chunk/p1-t0` himself (W139). T4's `lazy ≥ 100` was arithmetically unreachable — the site emits 10 `<img>`, because one of 22 products has an image — and was ratified as such. No Sanity write of any kind (GATE 1). |
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

`PUBLIC_SANITY_PROJECT_ID` · `PUBLIC_SANITY_DATASET` · `SANITY_READ_TOKEN` (Vercel, VPS env file, GitHub Actions secret) · `SANITY_WRITE_TOKEN` (server env only, from P2b for the F51 backfill; W079) · Vercel deploy-hook URL (inside Sanity webhooks; treat as a credential) · from P2b: `SUPABASE_URL`, `SUPABASE_ANON_KEY` (browser-safe), `SUPABASE_SERVICE_ROLE_KEY` (server-only, radioactive).

### Plans and bill safety

Vercel Hobby (→ Pro at P2b, ~$20/mo) · Sanity Free (3 users, 10k docs, 100k API req/mo — employees use Admin Mode, not seats) · **GitHub Pro** (~$4/mo — active as of 2026-09-02; rulesets on a private repo are what it buys, and creating `master-protection` is what proved it, since `gh api /user` omits `plan` without the `user` scope) · Supabase Free from P2b (2 projects/org: ERP is #1). Claude Max for Claude Code; Fable on owner-bought credits for architecture sessions only.

### TRUTH 2026-09-01 findings still open

Build 68 pages / exit 0; the 2 build warnings (chunk size; deprecated `@sanity/image-url` import pattern — F4, F3) are still open. **Closed by P2a, measured in its own build:** `loading="lazy"` 0 → **8** and `srcset` 0 → **10** (the whole site emits 10 `<img>`, W137) · the two dead RTL rules 2 → **0** (F5) · both product listings and every brand page now carry `ItemList` JSON-LD and the brand indexes `CollectionPage`; the static pages still have none. The next Monday TRUTH run re-measures all of it independently. **The probe figure that stood here — "18/19 probes 200" — is dropped rather than carried:** #26, #30 and #31 changed exactly what a probe list hits, so a September-1 count says nothing about today. P1's own reports carry a current per-path table, and the next TRUTH run replaces this block.

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
Dependabot says sharp needs the astro major as its ancestor. They have their own row, F48. **A seventh arrived during P2a: #55**, `anthropics/claude-code-action`
1.0.213 → 1.0.216 (actions-minor-patch group) — a `.github/**` change, so Tier 3 and named when
applied; it rides with #47/#48 in the next dependency task (strategist ruling 2026-09-10). P2a touched
none of the seven. **The stale branch `chunk/p1-t0` is gone** — deleted by the owner after the executor's
`gh api -X DELETE` was refused by the classifier (W139). That is stated in the closing session's start
prompt on 2026-09-10 and the ref now returns 404; the repository's event feed carries no DeleteEvent
for it, so when is not measured.
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

**P2b · Identity and foundation — waiting on the owner at a PC, three times.** Create Supabase
project #2 · create `SANITY_WRITE_TOKEN` (server env only, W079) · turn on **Vercel Pro** (~$20/mo;
W090 requires it before the first on-demand route ships). The strategist gives the keystrokes. Then
P2b is Tier 3 throughout: the **SKU backfill + `required()`** under GATE 1 (F51 — the field shipped in
P2a, W135) · **astro 7** (F48: #49 + #50 travel together) with the `@astrojs/vercel` adapter, the
first on-demand route and a middleware skeleton · Supabase schema, RLS and TOTP MFA (W083, W091) · a
TypeScript data layer in `src/lib/` (W086).

**Nothing blocks the executor itself any more.** F46 and F47 closed in P2a: the allow list has a
working route — the owner edits `.claude/settings.json` by hand and the executor commits his diff
behind a gate (W138) — so a plan that needs a new allow rule names that hand edit as a
**precondition**. Two things stay unmeasured: whether a **dispatched** run's `gh issue close` goes
through (F52), and where the classifier's line actually falls — it let `.claude/` skill and reviewer
edits through, and refused both `settings.json` and a destructive `gh api` call (W139).

**Open, and each has a row:** F3, F4 (build warnings) · F26 (retiring the relay — P2a was started by
hand, so the lane has still carried only P1 #24 and P1.1 #36) · F27 · F32 · F34 (Studio 5→6, its own
named chunk) · F36 (the `.vercel.app` host is indexable) · F38 (the dispatcher leaves
`chunk:proposed` in place — ERP-side) · F39 (silent sessions) · F43 (the standing AR mass-review row —
**no Arabic string shipped in P2a**, so it is unchanged at four strings) · F48 (now with #55) ·
**F49–F52, opened by this chunk**: the slug validator's unreachable message (W013 — do not touch it
casually), the Product JSON-LD `sku` returning with the backfill, the backfill itself, and the
dispatched-lane close.

**Closed in P2a:** F5 and F45 (PR #58) · F46 and F47 (PR #56 and this chunk's final, W138).
**Closed in P1.2:** F42 · F44.

**One question is the strategist's, not the owner's, carried unchanged from P1.2:** the item-4 wording
P1.2 shipped verbatim imports the severity label "P1" into `REVIEW.md`, which uses 🔴/nit and defines
P1 nowhere.

**And the operational fact that still outranks the rest:** the dispatched executor has never finished
a chunk — 2 runs, 2 failures, both on the subscription window (live flag 9, W128). Last checked
2026-09-05 against `jahjah-internal`'s reports index, when the `run-chunk.sh` change had not landed;
**P2a did not re-check.** Until it lands, approve P2b's issue only if someone is there to run it
interactively. P2a's own record says the interactive lane works: six PRs and six merges counting its close, across
four sessions — one of them abandoned early, its draft stashed and rewritten rather than applied.
