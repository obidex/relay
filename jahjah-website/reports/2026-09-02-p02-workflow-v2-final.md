# P0.2 · workflow v2 — final

<!-- index: P0.2 final — GATE 2 is machine-enforced, a chunk now starts from a labelled issue, and two new VPS units are proven from their timers; the site is byte-identical -->

**Generated (UTC):** 2026-09-02T20:20:00Z · **Executor:** VPS `germany-vpn`, tmux `web`, Claude Code (Opus 5, xhigh) · **Chunk issue:** [#6](https://github.com/obidex/jahjah-website/issues/6)

## For the owner — one paragraph

Three things changed, and the website itself did not: same 68 pages, and the home page is **byte-for-byte
identical** to the reading I took before starting. First, **the rule that nothing reaches production
except through a reviewed pull request is now enforced by GitHub rather than by good manners** — I
proved it by trying to push straight to production and being refused, and by trying to merge before
the checks finished and being refused. Second, the checks got safer: every external tool the pipeline
uses is pinned to an exact version nobody else can move under us, and any change to a sensitive file
is refused unless the pull request says out loud which plan authorised it. Third, and this is the one
that changes your day: **the machine now picks work up from a labelled GitHub issue.** Instead of you
pasting a long prompt into a terminal, your strategist files an issue and you tap one label — from
anywhere, including your phone — and the work starts within two minutes. I tested that end to end
twice with a throwaway job and watched the whole trail happen without touching anything. Two smaller
things also went in: your dependency updates now arrive as one tidy pull request a week, and there is
a new weekly check that opens last night's backup and confirms it really contains the whole catalogue
rather than merely being a well-formed file. **Nothing needs you right now.** One decision will need
you soon and it is written up below; it is not urgent and it is not a chore.

-----

```
=== REPORT: P0.2 workflow v2 · done ===
HEAD: master 5eaeb7c "docs: close P0.2 — canon for the issue-driven loop (#22)" | tree: clean | branch: master
      file count 73 tracked, recounted before every push (W092)
      jahjah-internal main 2f2278b; zero drift between that repo and /opt/jahjah
PRs (obidex/jahjah-website):
  #4  58912bd MERGED — ci.yml: job named `ci`, three actions SHA-pinned, tier3-guard
  #5  CLOSED UNMERGED, branch deleted — the throwaway that proved the ruleset. Nothing reached master
  #10 44bef69 MERGED — /relay-report to the chunk issue; three `gh issue` allow rules
  #13 b840c3a MERGED — independent `review` job + REVIEW.md
  #18 7ce7932 MERGED — review turn cap 40 -> 120, after a measured failure
  #17 7821043 MERGED — Dependabot
  #22 5eaeb7c MERGED — this canon close
PRs (obidex/jahjah-internal):
  #91 2c66949 MERGED — jahjah-web-dispatch + session/publish-report.sh
  #92 2f2278b MERGED — jahjah-web-backup-check
CI: every PR green on `ci` before merge; post-merge master run on 5eaeb7c SUCCESS (33678422772).
    One deliberate red recorded: 33653968894, tier3-guard, to prove the guard bites.
PROD: deployment 6231039311, sha 5eaeb7c, Production, state success
      live probes 5/5 200: / · /ar/ · /products/ · /sitemap-0.xml · /robots.txt
      `/` HTML sha256 2043b04a…d679d1 — BYTE-IDENTICAL to the pre-chunk reading
RULESET: master-protection, id 22124934, enforcement active, bypass_actors [],
      current_user_can_bypass "never". Rules on master: deletion, non_fast_forward,
      pull_request (0 approvals, squash only), required_status_checks (strict, `ci`).
DONE:
  T1  CI shape. Job id and display name are both `ci`, so a ruleset can require it. Actions pinned:
      actions/checkout v7.0.1 3d3c42e5aac5ba805825da76410c181273ba90b1
      actions/setup-node v7.0.0 820762786026740c76f36085b0efc47a31fe5020
      gitleaks/gitleaks-action v3.0.0 e0c47f4f8be36e29cdc102c57e68cb5cbf0e8d1e
      All three the current latest release, all three node24. gitleaks was on @v2, still node20,
      which GitHub removes from hosted runners on 2026-09-16. New `tier3-guard` step, proven on its
      own PR in both directions: line present -> green (33653788213); line removed -> RED at step 3
      of 9 with the offending path named and every later step skipped (33653968894); line restored
      -> green (33654046648). No-op on push to master, verified `skipped`. ROADMAP F10 closed.
  T2  Ruleset created; the exact JSON was published in the progress report. Nothing was rejected, so
      no identifier correction was needed. PROOF 1 — `gh pr merge` while `ci` pending: "Repository
      rule violations found / Required status check "ci" is expected." PROOF 2 — `git push origin
      HEAD:master` from a scratch commit: "GH013 ... Changes must be made through a pull request",
      rejected by GitHub, not by settings.json. origin/master 58912bd before and after. ROADMAP F6
      closed — and the POST succeeding is what proved GitHub Pro is active, since `gh api /user`
      omits `plan` entirely without the `user` scope, so the plan was never observable from here.
  T3a Eight labels created. Issue #6 opened as CHUNK_ISSUE for the rest of the chunk.
  T3b /relay-report writes the issue FIRST and independently of the relay, because the lane reads
      LABELS to judge success and a relay problem must never make a successful chunk look failed.
      Six reviewer passes; pass 1 returned a BLOCK. Three findings changed the design and all three
      came from measurement rather than argument — see FINDINGS A.
  T3c jahjah-web-dispatch, in jahjah-internal. PROVEN FROM ITS TIMER TWICE, never by hand:
        18:59:17 issue #11 opened chunk:approved + model:sonnet
        19:00:05 TIMER fired; chunk:running added
        19:00:07 chunk:approved removed        <- two calls, two seconds apart
        19:00:08 "dispatched ... on the executor"; tmux web:chunk-11 opened and survived the poll exit
        19:00:18 the dispatched session replied "SMOKE OK" and "44bef69"
        19:00:22 "dispatcher: exit 0, 13s"
        19:00:24 chunk:failed added; 19:00:25 chunk:running removed
        19:02:04 next poll booked "chunk #11 finished, exit 0, 13s"; marker cleared
      chunk:failed is CORRECT there — a smoke test posts no final report, so the lane rightly
      concluded none was posted. That is the fallback working. Issue #12 repeated the whole trail
      after KillMode=process was removed, which is what proved that removal safe.
  T4  claude-review.yml + REVIEW.md. Four defects found in review, each of which would have shipped
      silently — see FINDINGS C. Its acceptance is PARTIALLY met and I am not claiming otherwise:
      the skip path is proven, the run path is proven not to error, the OUTPUT path is unproven.
  T5  Dependabot, npm + github-actions, Mondays 06:00 UTC, minor+patch grouped, majors separate,
      limit 3 per entry. Alerts: GET 404 before, PUT 204, GET 204 after; automated-security-fixes
      PUT 204. Three separate reasons a Dependabot PR is red are written into the file itself.
  T6  jahjah-web-backup-check, in jahjah-internal. PROVEN FROM ITS TIMER: first run 18:24:04Z from a
      temporary additive OnCalendar drop-in, removed straight after; list-timers now shows only the
      real Mon 03:30 UTC schedule. Verdict OK: archive holds product=22, brand=5, category=6, each
      matching a live GROQ count, 3 image references all present. Review found a FALSE-OK vector —
      see FINDINGS D.
  T7  This canon PR: STRATEGIST §1/§2/§8 + READING MAP + §4 template; CLAUDE.md §1/§3/§5/§7;
      STATE §1/CI-shape/automations/flags/ledger/§5; ROADMAP §2 and the register; DECISIONS
      W098-W109 appended; npm run reference (no change).
DEVIATIONS:
  1. `pull-requests: write` on the review job, not the plan's `read`. The plan's own acceptance
     ("posted a review") requires it. My first reasoning for it was WRONG and the reviewer corrected
     me: Claude's inline comments go through the App token and would work under `read`. The real
     reason is that the action's final composite step falls back to the workflow token when the App
     token is empty, so under `read` the findings are lost exactly when something has already gone
     wrong. The file records the true reason.
  2. `--max-turns 120`, not the plan's 40. Measured: the first run that could execute failed with
     "subtype": "error_max_turns", "num_turns": 41, red, having posted nothing (W109).
  3. `interrupted` reports move the issue to chunk:blocked. The plan named only final->done and
     BLOCKED->blocked; without this an interrupted chunk keeps chunk:running and the lane reports it
     as a crash. chunk:blocked now carries two meanings and only the report body separates them.
  4. T6 is a NEW weekly unit rather than an extension of jahjah-web-backup's verify step (the plan
     allowed either). A checker inside the backup would widen the window in which the night's
     archive is unrotated, and would count its own failures against the BACKUP's timer.
  5. A wrapper, jahjah-internal's infra/vps/session/publish-report.sh, was added and /relay-report
     now publishes through it. Not in the plan, and not optional — FINDINGS A.
  6. Both jahjah-internal PRs were made from an ISOLATED CLONE, not /root/jahjah-internal, because
     another Claude Code session worked in that tree all afternoon. Its tree was never touched.
  7. One extra website PR (#18) beyond the plan's task list, for deviation 2.
FINDINGS/BLOCKERS:
  A. THE RELAY-PUBLISHING PATH COULD NEVER HAVE WORKED IN A DISPATCHED SESSION, and no permission
     rule could have fixed it. Claude Code refuses `.` and `source` as shell-code evaluation, in
     headless and interactive sessions alike — that is not a permission decision, so no settings
     entry reaches it. Every unattended job publishes by sourcing jahjah-common.sh. Interactive
     sessions only ever got away with it because a human approved each prompt; a dispatched chunk has
     nobody. And because the lane reads the issue's LABELS to judge success, an unpublishable report
     would have marked EVERY dispatched chunk failed. Fixed with a wrapper one narrow rule can allow,
     granted per run through --allowedTools (measured additive). W103a-b.
  B. `gh issue edit` IS NOT ATOMIC. Given a bad --add-label it applies the --remove-label anyway and
     then fails, leaving an issue with NO state label — worse than a wrong one, because the lane then
     sees no chunk:running and books the chunk finished. Every label move in the lane and in the
     skill now ADDS before it REMOVES, as two calls. This found a live silent-queue-loss bug in
     already-deployed code: the combined form could strip chunk:approved without applying
     chunk:running, and the issue would then match no label the poll searches for — out of the queue
     permanently, with nothing anywhere saying so. W103c.
  C. THE REVIEW JOB IS GREEN AND SILENT, and I am reporting it as unfinished rather than done. It no
     longer errors — the turn-cap failure is fixed and proven — but no run has yet posted a finding
     or a "no issues" summary, on two PRs. Cause not established; candidates are the plugin's own
     step-1 triage gate and slash-command expansion. It is deliberately NOT a required check, so it
     cannot block a merge either way. ROADMAP F25. Related and already fixed in review: REVIEW.md was
     inert (nothing in the plugin opens it), --allowedTools was too narrow to read a PR or post a
     summary, and the slash command had drifted off index 0 of the prompt, which would have silently
     disabled the whole pipeline with nothing on that PR able to reveal it.
  D. THE BACKUP CHECK HAD A FALSE-OK VECTOR. `sanity dataset export` includes drafts by default while
     the live GROQ query excludes them, so counting the archive unfiltered both cries wolf on any
     open draft AND lets draft inflation offset a real loss — an export that genuinely dropped
     products would have reported OK, which is the exact failure the job exists to catch. Fixed and
     verified both ways: 0 drafts in today's archive, and a synthetic draft line counts 23 unfiltered
     against 22 filtered. W104.
  E. DEPENDABOT PRs CANNOT GO GREEN UNAIDED, for three separate reasons, and only the first is
     deliberate: the Tier-3 guard (one body line clears it); no Actions secrets on a bot-actored run,
     so the build guard fails; and reference drift, because `npm run reference` records package.json's
     version RANGES and Dependabot rewrites them — which nothing in the PR body can clear.
     ROADMAP F23. The obvious fix for the second is an OWNER DECISION with a real cost — see
     NEXT-NEEDED.
  F. A CONTROL RESTING ON METADATA OR ON ANOTHER SYSTEM'S ROLE MODEL IS A CONVENTION, NOT A BOUNDARY.
     The lane's gate was written as "chunk:approved requires repository write" — false: GitHub's
     Triage role can label with no push access. Recorded instead as a dated assumption the job cannot
     enforce, and verified: obidex/jahjah-website is user-owned and private, its only collaborator is
     `obidex` (admin), zero pending invitations. The ERP's lane reached the same rule from commit
     metadata the same afternoon. W105.
  G. TWO CHUNK-START LANES WERE DESIGNED ON THIS BOX THE SAME AFTERNOON. The ERP session built a
     relay-inbox lane; its own review panel found a ROOT shell injection in it (an issue-derived
     field escaping into a tmux command string, running before `claude` and outside every hook) and
     it was stopped and disabled at three layers before it ever ran a real chunk. Mine had the same
     SHAPE with no exploitable value, and is now hardened: $num validated as digits at the source,
     $model asserted, and values passed as `tmux -e VAR=value` so there is no shell string to inject
     into. W108. Mine is the only chunk-start lane live on the box.
  H. A USER-LEVEL PreToolUse HOOK IS SHARED INFRASTRUCTURE. A sister session's in-progress edit to
     one left it unparseable and refused EVERY Bash call in this session for about eight minutes. The
     documented kill switch is itself a Bash call, so it was unreachable; recovery needed a non-Bash
     file tool. W107.
  I. F22 REPRODUCED ITSELF WHILE BEING CLOSED. raw.githubusercontent.com served a stale health page
     beside a current index, cache-buster on both; only the local relay clone showed the truth. On
     the box, read the clone. W102.
  J. THE SUPERSEDED-FACT DEFECT RECURRED THREE TIMES INSIDE THE CHUNK THAT EXISTS TO END IT — and
     every one was caught by review, not by me. STATE still said "GitHub Free (no branch protection)"
     twenty-one lines above the row recording that the ruleset closed F6; STATE's enumeration of the
     new register rows stopped at F29 after F30-F32 were added; and live flag 9 asserted the loop as
     operative when no real chunk has ever started from a label. That is the same class as F16-F22,
     committed inside the file the chunk rewrote, three times. The lesson is not "be careful": it is
     that a chunk introducing a superseding fact must name every file holding the old one, and that
     an adversarial pass over the canon is not optional ceremony.
  K. MY OWN GATE LIED TO ME ONCE. `npm run reference >/dev/null 2>&1` plus an unchecked `git diff`
     reported a clean reference when the generator had added a row for the new workflow — which would
     have turned `ci` red on the one required context. Caught by review. W106.
CANON: CLAUDE.md · docs/STRATEGIST.md · docs/STATE.md · docs/ROADMAP.md · docs/DECISIONS.md
  (W098-W109 appended; W072 and W073 superseded rather than edited, since DECISIONS is append-only)
  · docs/reference/site.md regenerated, no change.
NEXT-NEEDED: ONE decision, not urgent. Mirror the three Sanity values as *Dependabot* secrets, or
  not? Doing so lets Dependabot PRs build; it also hands a live read token — which can see drafts —
  to a run executing a dependency version nobody has reviewed. The ERP repo's own canon (D228)
  rejects the equivalent outright, but its escape hatch is unavailable to us because our `ci` is one
  job whose secret-dependent steps ARE the gate. ROADMAP F24; record the answer as a W### either way.
=== END ===
```

## Handover note

**Left off:** the loop is rebuilt and the gates are machine-enforced. A chunk now starts from a
GitHub issue the owner labels, runs on the executor, and reports back onto that issue; `master` is
reachable only by a squash-merged PR on green `ci`, with no bypass actors.

**The single next step** is P1 — launch blockers — opened as an issue labelled `chunk:proposed`.
Do it that way rather than by paste: **the very first thing the new lane needs is to carry one real
chunk**, and until it has, the relay cannot be retired (F26) and the loop is unproven on anything
larger than a smoke test.

**Worth pressure-testing while you are in there.** Every finding in this chunk that mattered came from
measuring a claim somebody had already written down — that a permission rule could allow sourcing a
library, that `gh issue edit` was atomic, that `KillMode=process` was necessary, that both sides of a
count excluded drafts, that a turn cap of 40 was enough. Four of those were in code already deployed
or already reviewed. The cheap version of that discipline is: when a plan states a mechanism, ask what
would be observed if it were false, and go and look.

**Two things are deliberately unfinished** and are in the register rather than hidden: the `review`
job is installed and silent (F25), and Dependabot PRs need the owner's decision before they can go
green (F23, F24).
