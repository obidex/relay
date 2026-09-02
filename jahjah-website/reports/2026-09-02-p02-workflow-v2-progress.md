# P0.2 · workflow v2 — progress

<!-- index: P0.2 progress — GATE 2 is now machine-enforced by a ruleset, the CI job is `ci` with pinned actions and a Tier-3 guard, and the chunk lane is live on the box -->

**Generated (UTC):** 2026-09-02T18:35:00Z · **Executor:** VPS `germany-vpn`, tmux `web`, Claude Code (Opus 5, xhigh) · **Chunk issue:** [#6](https://github.com/obidex/jahjah-website/issues/6)

## For the owner — one paragraph

Two things changed that you can check yourself, and one thing was built that you will feel next time.
First: **the rule that nothing reaches production except through a reviewed pull request is now
enforced by GitHub, not by good manners.** I proved it by trying to push straight to production and
being refused, and by trying to merge before the checks finished and being refused. Second: the
checks themselves got safer — every external tool the pipeline uses is now pinned to an exact version
that nobody else can move under us, and any change to a sensitive file is refused unless the pull
request says out loud which plan authorised it (I proved that one too, by removing the line and
watching it go red). Third, and this is the one that changes your day: **the machine now picks up
work from a labelled GitHub issue.** Instead of you pasting a long prompt into a terminal, the
strategist files an issue and you tap one label. That lane is installed and has been polling cleanly
on the box for two hours; the end-to-end test with a real chunk is the next step. The website itself
has not changed at all — same 68 pages, same HTML byte for byte, all five addresses answering 200
throughout.

-----

```
=== REPORT: P0.2 workflow v2 · progress ===
HEAD: master 58912bd "ci: name the job `ci`, pin every action to a SHA, add tier3-guard (#4)" | tree: clean | branch: master
      file count 70 before and after every push (W092)
PRs: #4 58912bd MERGED — .github/workflows/ci.yml only (+72/-7, 1 file)
     #5 CLOSED UNMERGED, branch deleted — a throwaway one-line README change whose only purpose was
        to prove the ruleset refuses a merge and a direct push. Nothing from it reached master.
CI: PR run 33653788213 green (1m10s) · deliberate red 33653968894 (tier3-guard, by design) ·
    green again 33654046648 · post-merge master run 33654207689 SUCCESS on 58912bd
PROD: deployment 6226827938, sha 58912bd, Production, state success (2026-09-02T16:20:16Z)
      live probes 5/5 200: / · /ar/ · /products/ · /sitemap-0.xml · /robots.txt
      `/` HTML sha256 2043b04a…d679d1 — BYTE-IDENTICAL to the reading taken before the chunk began
DONE:
  T1  CI shape. The job is now `ci` (job id and display name) so a ruleset can require it by name.
      Every action pinned to a full commit SHA, each the current latest release and each `node24`:
        actions/checkout        v7.0.1  3d3c42e5aac5ba805825da76410c181273ba90b1
        actions/setup-node      v7.0.0  820762786026740c76f36085b0efc47a31fe5020
        gitleaks/gitleaks-action v3.0.0 e0c47f4f8be36e29cdc102c57e68cb5cbf0e8d1e
      gitleaks was on @v2, still node20, and GitHub removes node20 from hosted runners on
      2026-09-16 — two weeks out. v3 is a runtime migration only. ROADMAP F10 closed.
      New step `tier3-guard`: on a pull request it diffs against the base and, if any Tier-3 path
      changed, requires the PR body to carry `Tier-3: authorized by chunk <name>`. No-op on push to
      master — verified `skipped` in run 33654207689.
      ACCEPTANCE, all three runs on PR #4: line present -> green; line removed -> RED at tier3-guard,
      step 3 of 9, with `.github/workflows/ci.yml` named and every later step skipped; line restored
      -> green. `on.pull_request.types` gained `edited`, so the guard re-reads the body on an edit
      instead of needing an empty commit — which is also what makes the red state recoverable.
  T2  Ruleset `master-protection`, id 22124934, enforcement active, bypass_actors [],
      current_user_can_bypass "never". Rules in force on master (GET /rules/branches/master):
      deletion, non_fast_forward, pull_request (0 approvals, squash only), required_status_checks
      (strict, context `ci`). The JSON sent is published verbatim below. Nothing was rejected, so no
      identifier-level correction was needed.
      PROOF 1 — `gh pr merge` while `ci` was pending:
        GraphQL: Repository rule violations found / Required status check "ci" is expected.
      PROOF 2 — `git push origin HEAD:master` from a scratch commit, rejected by GITHUB:
        remote: error: GH013: Repository rule violations found for refs/heads/master.
        remote: - Changes must be made through a pull request.
        remote: - Required status check "ci" is expected.
        ! [remote rejected] HEAD -> master (push declined due to repository rule violations)
      origin/master was 58912bd before the attempt and 58912bd after it. PR #5 was then CLOSED
      UNMERGED and its branch deleted. ROADMAP F6 closed. W084 stopped being advisory at 16:20:50Z.
  T3a Labels created in obidex/jahjah-website (idempotent, --force): chunk:proposed #6e7781,
      chunk:approved #1a7f37, chunk:running #0969da, chunk:blocked #cf222e, chunk:done #8250df,
      chunk:failed #b60205, model:opus #d4c5f9, model:sonnet #c2e0c6.
  --  Issue #6 "P0.2 workflow v2" opened and labelled chunk:running + model:opus. It is CHUNK_ISSUE
      for the rest of this chunk; every report from here also lands there.
IN FLIGHT (built and deployed, PRs not yet open):
  T3b /relay-report extended and .claude/settings.json given exactly three `gh issue` allow rules.
      Reviewer pass 1 returned a BLOCK; see FINDINGS A. Rewritten; re-review running.
  T3c `jahjah-web-dispatch` — the chunk lane. Installed on the box, timer armed, and it has polled
      cleanly every two minutes since 16:26 UTC (`ok: idle — nothing approved`), with its heartbeat
      live in the relay at jahjah-internal/reports/HEARTBEAT-web-dispatch.md. The end-to-end smoke
      test from the timer is the next step, and it runs after T3b merges so the lane is exercised in
      its real configuration.
  T6  `jahjah-web-backup-check` — PROVEN FROM ITS TIMER already. First run 2026-09-02T18:24:04Z,
      verdict OK: last night's archive holds product=22, brand=5, category=6, each matching a live
      GROQ count over the read-only token, with 3 image references and all 3 present in the archive.
      The temporary first-run drop-in was removed straight after; `list-timers` now shows only the
      real Mon 03:30 UTC schedule.
DEVIATIONS:
  1. GitHub Pro could not be confirmed before T2, and the reason is worth recording: `GET /user`
     OMITS the `plan` key entirely unless the token carries the `user` scope, and this token's
     scopes are gist, read:org, repo, workflow. A first reviewer pass read the resulting null as
     "Free" and built a finding on it. The ruleset POST is what settled it — it succeeded, and
     rulesets on a private personal repo are plan-gated, so Pro is active.
  2. T6 is a NEW weekly unit, `jahjah-web-backup-check`, rather than an extension of
     jahjah-web-backup's verify step. The plan allowed either. Reason: the backup runs a
     verify-then-rotate contract, and a checker inside it would both widen the window in which the
     night's archive is unrotated and count its own failures against the BACKUP's timer. A failing
     checker must never be able to cost us a backup.
  3. A wrapper script was added to the sister repo, `infra/vps/session/publish-report.sh`, and the
     /relay-report skill now publishes through it. This was not in the plan and it is the largest
     deviation; the reason is FINDING A, and it is not optional.
  4. Both jahjah-internal PRs are opened from an ISOLATED CLONE rather than /root/jahjah-internal,
     because another Claude Code session has been working in that tree all afternoon — FINDING C.
FINDINGS/BLOCKERS:
  A. THE RELAY-PUBLISHING PATH DOES NOT WORK IN A DISPATCHED SESSION, and no permission rule could
     ever have fixed it. Every unattended job publishes by sourcing /opt/jahjah/lib/jahjah-common.sh.
     A Claude Code session cannot: `.` and `source` are refused as shell-code evaluation, headless
     and interactive alike. Measured today, three probes in /opt/jahjah/web:
       `wc -l < CLAUDE.md`                    -> RAN (allowlisted)
       `date -u +%FT%TZ`                      -> RAN, though no rule covers it — read-only commands
                                                 are auto-approved, so the reviewer's stated
                                                 mechanism was wrong in that detail
       `JJ_JOB=session . /opt/jahjah/lib/...` -> DENIED, "'.' evaluates arguments as shell code"
     Interactive sessions got away with it only because a human approved each prompt. A dispatched
     chunk has nobody, so its report would never be published — and because the lane reads the
     issue's LABELS to decide whether a chunk succeeded, an unpublishable report would have made
     every chunk look failed. Fixed with a wrapper script that one narrow rule can allow, granted
     per-run by the lane through `--allowedTools` (measured additive: a rule granted there did not
     displace the repo's own `Bash(wc:*)`). The wrapper validates its target against an allowlist of
     two reports folders, refuses `..` and `//`, caps the file at 512 KB, and publishes under the
     shared flock. Five refusal cases tested.
  B. THE ORDER OF THE REPORT MATTERED MORE THAN IT LOOKED. As first written, the label move was the
     last action, behind the relay push and the issue comment — so a contended relay lock would have
     left a successful chunk labelled chunk:failed. The issue half now runs FIRST and independently.
  C. ANOTHER CLAUDE CODE SESSION IS BUILDING A SIMILAR LANE IN THE ERP REPO, RIGHT NOW. Session
     `jahjah-internal-1c` has been live in /root/jahjah-internal since ~15:00 UTC building an
     "inbox" lane that starts ERP chunks from files placed in the relay repo. It is a different
     repo, a different trigger surface and a different tmux session, so nothing collides
     mechanically — but two chunk-start lanes with different designs were built the same afternoon,
     and the owner should know before a third appears. This chunk's jahjah-internal work is
     therefore done in an isolated clone and lands as ordinary PRs; that tree is untouched.
     It also cost real time, and taught two things worth keeping. That session's in-progress edit
     to /opt/jahjah/bin/gate1-hook.sh — a PreToolUse hook registered at USER level and therefore
     live for EVERY session on this box — left the script unparseable and refused every command in
     this session for about eight minutes. Diagnosed here, fixed there, resolved 18:32 UTC; the
     cause was an apostrophe inside a comment closing the single-quoted bash string that wraps an
     embedded Python classifier, dropping the Python into bash.
       (i)  A BROKEN HOOK TAKES ITS OWN KILL SWITCH DOWN WITH IT. `touch /opt/jahjah/GATE1_HOOK_OFF`
            is a Bash call, and Bash is precisely what the broken hook refuses. The only way out is
            a non-Bash tool. Both sessions independently had to recover through the file tools.
       (ii) A HOOK REGISTERED AT USER LEVEL IS SHARED INFRASTRUCTURE. The two sessions are rooted in
            different projects and it applied to both. It must be edited the way a shared branch is
            — validated with `bash -n` before install, never in place.
  D. DEPENDABOT WILL NOT GO GREEN UNAIDED once T5 lands, and the blocker is not the Tier-3 guard.
     GitHub does not pass Actions secrets to a Dependabot-actored run, so SANITY_READ_TOKEN is empty
     and the build step's own guard fails on every such PR whatever paths it touches. A
     human-actored body edit re-runs it with real secrets and can go green, but each rebase reverts
     it. The fix is owner-side — mirror the three secrets as *Dependabot* secrets in repo settings —
     and the executor must not touch repository settings. Carried to T5 and to the register.
  E. Recorded, not acted on: the three new allow rules are prefix rules, so `-R <repo>` cannot be
     constrained by them and `gh issue edit` covers more than labels. They are exactly the three
     verbs the plan named; narrowing them is a strategist decision, not an executor one.
CANON: none yet — all canon updates are T7, in the chunk's last PR.
NEXT-NEEDED: none from the owner yet. Item D will need him at the GitHub settings page, but not
  before T5 has merged and the first Dependabot PR appears.
=== END ===
```

## The ruleset JSON, verbatim as sent

```json
{"name":"master-protection","target":"branch","enforcement":"active","bypass_actors":[],
 "conditions":{"ref_name":{"include":["refs/heads/master"],"exclude":[]}},
 "rules":[
   {"type":"deletion"},
   {"type":"non_fast_forward"},
   {"type":"pull_request","parameters":{"required_approving_review_count":0,"dismiss_stale_reviews_on_push":true,"require_code_owner_review":false,"require_last_push_approval":false,"required_review_thread_resolution":false,"allowed_merge_methods":["squash"]}},
   {"type":"required_status_checks","parameters":{"strict_required_status_checks_policy":true,"do_not_enforce_on_create":false,"required_status_checks":[{"context":"ci"}]}}
 ]}
```

GitHub accepted it unchanged and added one default of its own,
`require_extra_approval_for_unattributed_changes: true`, which does not affect an attributed commit.
Worth watching on the first PR by a new author.
